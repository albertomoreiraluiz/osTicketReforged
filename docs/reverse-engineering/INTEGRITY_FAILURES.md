# Falhas, atomicidade e órfãos

## Fronteira transacional

**Fato observado:** a conexão habilita autocommit e expõe wrappers de rollback,
mas os fluxos de ticket, tarefa, thread, forms, attachments e drafts não chamam
autocommit, rollback, `START TRANSACTION`, `COMMIT` ou `ROLLBACK`
(`include/mysqli.php:21-103`). Cada `save()`, `delete()` ou operação de
`QuerySet` é independente. O ORM converte apenas `OrmException` em `false` em
`save()`/`delete()`; outros `Error`/exceções propagam
(`include/class.orm.php:613-722,1530-1544`).

## Criação de ticket e tarefa

| Fluxo | Ordem crítica | Falha/retorno | Estado parcial possível |
| --- | --- | --- | --- |
| ticket | ticket → thread → forms → evento → colaboradores → mensagem → filtros → status/assignment → alertas | thread é testada, mas ticket não é compensado; forms/mensagem e vários efeitos têm retorno ignorado | ticket sem thread; thread sem forms/mensagem; assignment/status incompleto (`include/class.ticket.php:4055-4589`) |
| tarefa | task → form → thread → descrição → evento → assignment → alertas | somente primeiro save é testado; thread não é testada antes de dereferenciar | task/forms sem thread; `Error`; thread sem descrição; assignment ausente (`include/class.task.php:1458-1518,1811-1821`) |

Na API, attachments podem ser criados durante validação, antes de
`Ticket::create()`; falha posterior deixa arquivo para coleta tardia
(`include/api.tickets.php:70-107`).

`DynamicFormEntry::saveAnswers()` salva a entrada e respostas sequencialmente,
ignorando falhas individuais (`include/class.dynamic_forms.php:1327-1379`).
`ThreadEntry::create()` pode criar arquivo para corpo grande antes de salvar a
entry e não testa `createAttachments()` nem `saveEmailInfo()` depois dela
(`include/class.thread.php:1639-1823`).

## Atualizações e conversação

- `Ticket::update()` salva campos básicos antes de nota, forms, respostas,
  eventos, SLA e segundo save; retornos intermediários podem deixar estado
  parcial (`include/class.ticket.php:3675-3806`).
- `Task::update()` salva forms, nota e evento antes do save final e emite
  `model.updated`; falha final pode retornar `false` com efeitos persistidos
  (`include/class.task.php:1322-1365`).
- `Ticket::setStatus()` e `Task::setStatus()` salvam o status antes de referral,
  nota, evento, drafts e sincronização relacionada
  (`include/class.ticket.php:1483-1602`; `include/class.task.php:570-645`).
- mensagens, respostas e notas são persistidas antes de housekeeping, status,
  eventos e alertas (`include/class.ticket.php:3081-3269,3345-3459,3509-3559`).

## Exclusão e referências órfãs

Ticket e Task removem primeiro sua linha, depois thread, evento `deleted`,
drafts, forms e CDATA. Apenas a primeira exclusão é verificada
(`include/class.ticket.php:3601-3658`; `include/class.task.php:1584-1611`). Isso
permite linha principal removida com thread/forms/drafts remanescentes e pode
causar dereferência nula depois da remoção.

`Thread::delete()` remove a thread, limpa headers, attachments, colaboradores e
referrals, apaga entries em massa e zera eventos (`include/class.thread.php:743-764`).
Não apaga `thread_entry_email` nem `thread_entry_merge`; o schema não possui FK
ou cascata (`setup/inc/streams/core/install-mysql.sql:665-711`) e não há coletor
cron dessas tabelas. **Inferência forte:** os metadados podem apontar para
entries inexistentes.

Ticket e Task chamam `thread->delete()` antes de `logEvent('deleted')`. O broker
injeta a chave relacional e `ThreadEvents::log()` salva imediatamente
(`include/class.ticket.php:3633-3639`; `include/class.task.php:1587-1597`;
`include/class.thread.php:2322-2395`; `include/class.orm.php:2131-2145`).
**Inferência forte:** o novo evento pode guardar ID de thread já removida.

## Drafts e arquivos

`Draft::delete()` remove attachments antes da linha, mas `Draft::cleanup()` usa
DELETE direto apenas em `draft` (`include/class.draft.php:125-128,185-190`). A
coleta de arquivo seleciona somente files temporários antigos sem linha em
`attachment` (`include/class.file.php:700-721`). **Inferência forte:** purge de
draft expirado pode deixar attachment dangling que mantém o file fora da
coleta indefinidamente.

A coleta ocorre aproximadamente a cada dez crons, interrompe no primeiro
delete falso e ainda retorna sucesso. `AttachmentFile::delete()` remove o
metadado antes do `unlink()` e ignora o retorno deste
(`include/class.cron.php:105-119`; `include/class.file.php:162-170,709-721`).
Conteúdo físico pode permanecer sem metadado.

## Matriz de compensação

| Falha | Estado já persistido | Compensação localizada |
| --- | --- | --- |
| thread do ticket | ticket | nenhuma |
| form ou mensagem do ticket | ticket + thread + efeitos anteriores | nenhuma |
| thread da tarefa | tarefa + possível form | nenhuma; possível `Error` |
| resposta de form | entry + respostas anteriores | nenhuma |
| attachment/metadado de entry | entry + anexos anteriores | nenhuma |
| segundo save de update | campos/forms/notas/eventos anteriores | nenhuma |
| delete de thread/forms/draft/cdata | ticket/task já removido | nenhuma |
| unlink do backend | linha `file` já removida | nenhuma |
| callback/sinal lança | tudo salvo anteriormente | nenhuma localizada |

## Validação futura

Os estados acima são fatos de ordenação ou inferências estáticas, não defeitos
comportamentais confirmados. Após instalação descartável: injetar falhas em
cada etapa; medir órfãos de e-mail/merge e drafts; conferir `thread_id` do
evento deleted; simular falha de storage; validar engine/autocommit; e testar
exceções de sinais após persistência.
