# Arquitetura de persistência

## Estado físico da baseline

| Evidência | Resultado estático |
| --- | ---: |
| `CREATE TABLE` no schema de instalação | 67 |
| SQL históricos de upgrade | 99: 80 patches e 19 limpezas |
| declarações `static $meta` | 72, distribuídas em 38 arquivos PHP |
| constantes terminadas em `_TABLE` | 72 |
| `FOREIGN KEY`/`REFERENCES` executáveis | 0 |

Existe apenas um comentário `references form_entry.id` em
`setup/inc/streams/core/install-mysql.sql:165`. Logo, relações, integridade e
cascatas são predominantemente lógicas/aplicacionais; os ERDs e metadados ORM
não representam FKs criadas pelo schema.

## ORM e transações

`VerySimpleModel::$meta` descreve tabela, ordenação, PK e relações
(`include/class.orm.php:305-310`). `ModelMeta::processJoin()` transforma joins e
reversos usados na consulta/lazy lookup, mas não cria constraints físicas
(`include/class.orm.php:152-213`).

`VerySimpleModel::save()` salva primeiro objetos estrangeiros novos, executa
`INSERT`/`UPDATE` e emite `model.created`, `model.updated` e `object.edited`.
`delete()` genérico apaga apenas a linha local e emite `model.deleted`
(`include/class.orm.php:613-722`); não há cascade genérico.

A conexão `mysqli` ativa autocommit (`include/mysqli.php:21-90`). Foram
localizados wrappers para alternar autocommit e rollback, não um wrapper de
commit (`include/mysqli.php:93-103`). `Sequence::__next()` é uma exceção
transacional explícita: desativa autocommit, usa `SELECT ... FOR UPDATE` e o
reativa (`include/class.sequence.php:153-169`).

O schema declara InnoDB explicitamente somente para `sequence`, `event` e
`thread_referral`; as demais tabelas herdam o padrão do servidor
(`setup/inc/streams/core/install-mysql.sql:80-82,775,808`). A engine efetiva
deverá ser inventariada
após a instalação.

## Relações centrais lógicas

| Origem | Relação observada | Evidência |
| --- | --- | --- |
| Organização | 1:N usuários | `include/class.organization.php:21-40`; `include/class.user.php:48-86` |
| Usuário | 1:N e-mails; 0..1 conta lógica; 1:N tickets | `include/class.user.php:48-86`; `setup/inc/streams/core/install-mysql.sql:1027-1066` |
| Ticket | N:1 usuário, status, dept, SLA, agente, equipe e tópico; 1:N tarefas/formulários | `include/class.ticket.php:39-106`; `setup/inc/streams/core/install-mysql.sql:715-755` |
| Thread | associação polimórfica a ticket (`T`) ou tarefa (`A`); 1:N entries, colaboradores, referrals e eventos | `include/class.thread.php:22-55`; `include/class.model.php:16-45` |
| ThreadEntry | N:1 thread; autores opcionais; 1:N anexos | `include/class.thread.php:798-837`; `setup/inc/streams/core/install-mysql.sql:665-711` |
| Colaborador | N:1 thread e N:1 usuário; par único | `include/class.collaborator.php:19-35`; `setup/inc/streams/core/install-mysql.sql:842-854` |
| Agente | N:1 departamento/papel; M:N departamentos e equipes | `include/class.staff.php:25-45,1451-1467`; `include/class.team.php:17-33,372-385` |
| Departamento/tópico | auto-hierarquias por `pid` | `include/class.dept.php:19-53`; `include/class.topic.php:20-61` |
| Plugin | 1:N instâncias | `include/class.plugin.php:543-553,1006-1017`; `setup/inc/streams/core/install-mysql.sql:896-919` |

**Restrição lógica relevante:** `user_account.user_id` não é único no schema,
embora o ORM trate `User.account` como 0..1. Essa cardinalidade depende do fluxo
aplicacional, não de uma unique key.

## Associações polimórficas

- `thread(object_type, object_id)`: ticket `T` ou tarefa `A`.
- `form_entry(object_type, object_id)`: ticket `T`, tarefa `A`, usuário `U` ou
  organização `O`.
- `attachment(type, object_id)`: draft `D` ou thread entry `H`.
- `task.object_id`: join opcional com ticket; o metadado observado não inclui
  constraint de `object_type` (`include/class.task.php:20-67`).

O último caso é uma lacuna de invariantes a rastrear, não um defeito confirmado.

## Dados dinâmicos e `__cdata`

`form_entry_values` usa PK composta `(entry_id, field_id)` e responde às
entradas polimórficas (`setup/inc/streams/core/install-mysql.sql:150-171`;
`include/class.dynamic_forms.php:974-990,1416-1432`).

`DynamicForm::ensureDynamicDataViews()` cobre ticket, tarefa, usuário e
organização. `buildDynamicDataView()` materializa tabela física com `CREATE
TABLE ... AS` e PK no identificador do objeto
(`include/class.dynamic_forms.php:300-358`). As quatro constantes `*_CDATA_TABLE`
não correspondem a `CREATE TABLE` estático porque essas estruturas são
derivadas dinamicamente.

## Exclusões e integridade manual

| Agregado | Limpeza explícita observada | Transação global localizada |
| --- | --- | --- |
| Ticket | desassocia filhos; remove thread, forms, drafts e cdata | não |
| Thread | remove anexos, colaboradores, referrals e entries; zera eventos | não |
| Tarefa | remove linha, thread, drafts e forms | não |
| Usuário | recusa se houver tickets; remove conta, e-mails e forms | não |
| Organização | remove linha; zera usuários e remove dados dinâmicos | não |
| Agente/equipe | limpa ownership e tabelas de associação | não |

Evidências: `include/class.ticket.php:3601-3658`,
`include/class.thread.php:743-764`, `include/class.task.php:1584-1597`,
`include/class.user.php:647-668`, `include/class.organization.php:548-566`,
`include/class.staff.php:935-964` e `include/class.team.php:278-295`.

**Risco sustentado:** sem FKs e sem uma transação envolvendo cada cascade
manual completa, uma falha intermediária pode deixar estado parcial. Qualquer
integração futura deverá usar os serviços/invariantes do domínio, não escrita
direta em tabelas.

## Evolução e discrepâncias

O upgrader usa assinatura de stream; a assinatura atual está em
`include/upgrader/streams/core.sig`. O instalador grava `schema_signature`, e
cada patch avança a cadeia (`setup/inc/class.installer.php:262,280`). Os scripts
históricos não devem ser tratados como migrações independentes aplicáveis fora
de ordem.

- `PRIORITY_TABLE` é alias de `TICKET_PRIORITY_TABLE`; 71 constantes restantes
  mapeiam nomes diretos.
- `TIMEZONE_TABLE` não possui criação estática localizada.
- `group` existe no schema, mas não possui constante/modelo atual localizado;
  referências `GROUP_TABLE` aparecem em tarefas históricas de upgrade.

Os dois últimos pontos permanecem divergências a confirmar na instalação real,
sem classificação de tabela órfã ou erro nesta fase.

## Uso dos ERDs oficiais

Os 14 ERDs incorporados foram usados como índice auxiliar. Como a referência
documental corresponde à série 1.17.7 e a baseline é 1.18.4, schema, metadados
ORM e código da baseline prevalecem como evidência.
