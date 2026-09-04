# Subsistemas transversais

## E-mail de entrada e saída

Entradas: `POST /api/tickets.email`, pipe CLI e coleta IMAP/POP por cron. O
fetcher seleciona mailbox ativa, processa mensagens via
`TicketApiController('cli')->processEmail()` e, após sucesso, move/exclui/mantém
conforme configuração (`include/class.mailfetch.php:60-270`;
`include/api.tickets.php:181-233`).

O parser emite `mail.received` antes e `mail.decoded` depois da decodificação
(`include/class.mailparse.php:60-79`). Persistência envolve `email` e
`email_account` (`setup/inc/streams/core/install-mysql.sql:246-291`).

Na saída, `osTicket\Mail\Mailer` prioriza SMTP do remetente, depois MTA padrão,
outra identidade configurada e, por fim, `mail()`
(`include/class.mailer.php:30-60,580-639`). Message-ID assinado, referências,
token de thread, cabeçalhos de supressão e CID compõem o threading/entrega.

Na homologação da Onda 7, não há conta SMTP em `email_account`; o fallback do
PHP aponta somente para `localhost:25`, normalmente sem listener, e não há
`sendmail_path` ou `mail.log`. Os ensaios instalaram somente um coletor SMTP
local efêmero e sem relay, encerrado após cada passagem.

Esse coletor foi executado temporariamente na Onda 7, sem relay. Durante uma
abertura Web, recebeu uma mensagem com um destinatário e retornou sucesso SMTP;
o ticket foi persistido e não houve `Mailer Error`. O coletor foi encerrado ao
fim do ensaio. Como ele não armazenou endereços ou conteúdo, esse primeiro
checkpoint não classificou o tipo; a passagem seguinte o fez pela lateralidade
do destinatário e pela configuração persistida.

Uma segunda passagem classificou apenas o domínio do destinatário em memória.
O envio foi para o lado interno, não para o solicitante `example.com`. O estado
persistido confirmou `ticket_autoresponder=0`, alerta global de novo ticket
ativo, alerta administrativo ativo, alerta de gerente habilitado porém sem
gerente no departamento e alerta a membros desabilitado. O comportamento
observado corresponde aos ramos de `Ticket::onNewTicket()` em
`include/class.ticket.php:1679-1771`.

Uma resposta staff com `reply-to=user` percorreu o mesmo fallback local e gerou
uma mensagem para o domínio reservado do proprietário, sem destinatário
interno. A entrada `R` foi persistida antes da confirmação do resultado e o
ticket permaneceu aberto/respondido. O coletor não armazenou endereço,
cabeçalho ou corpo e foi encerrado ao final.

Uma mensagem Web publicada por colaborador produziu uma única notificação ao
proprietário `example.com`. Não houve autoresposta ao autor porque
`message_autoresponder=0`; o aviso aos demais participantes permaneceu efetivo
pelo padrão verdadeiro de `message_autoresponder_collabs`. O alerta interno
estava ativo, mas não tinha destinatário concreto: o ticket não estava
atribuído, não possuía respondente staff e o departamento não tinha gerente.
Essa separação confirma que autoresposta, aviso a colaboradores e alerta staff
são ramos distintos do mesmo `postMessage()`.

O ensaio de atividade interna separou mais dois ramos. A atribuição temporária
não enviou mensagem, embora `assigned_alert_active=1` e o agente esteja marcado
como destinatário, porque `Ticket::onAssign()` exige membros elegíveis para
alerta no departamento. A nota interna também não enviou: a chave mestre
`note_alert_active=0` encerra `onActivity()` antes de considerar atribuído ou
último respondente. A ausência foi medida com o coletor ativo e não inferida de
logs.

Na entrada HTTP, um RFC 822 mínimo passou por `ApiEmailDataParser`, persistiu um
ticket `source=Email` e vinculou os cabeçalhos à entrada inicial. A mensagem não
foi classificada como bounce ou autoresposta. Como o `To` fictício não
correspondia a uma identidade configurada, o ensaio não caracteriza a recepção
por mailbox real nem seus avisos; o coletor de saída permaneceu sem mensagens.

O cron HTTP autorizado foi executado somente após dump integral restaurado e
comparado. O inventário anterior identificou 12 sessões expiradas e nenhum outro
candidato. A resposta foi `200 Completed`; depois, sessões expiradas estavam
zeradas e locks, drafts, resets, logs antigos e arquivos órfãos continuaram
zerados. Tickets, entradas e arquivos foram preservados. Isso confirma que
`Cron::run()` compõe fetch, monitoramento e limpezas, mas os efeitos concretos
dependem do estado elegível em cada execução.

## Arquivos, storage e anexos

`AttachmentFile` representa conteúdo/metadados, `Attachment` associa o arquivo
polimorficamente e `FileStorageBackend` permite backends. O padrão `D` guarda
blocos de 500 KiB em `file_chunk` (`include/class.file.php:45-55,760-1031`;
`include/class.attachment.php:19-43`).

`AttachmentFile::create()` detecta MIME, deduplica por assinatura/tamanho e
grava no backend; depois `GenericAttachments::upload()` cria a associação
(`include/class.file.php:352-539`; `include/class.attachment.php:134-184`).
Cron remove arquivos temporários órfãos com mais de um dia.

Achados observados e pontos ainda estáticos:

- `file.php` valida URL HMAC, mas não demonstra o vínculo do usuário ao objeto;
- a Onda 7 confirmou TXT associado à thread, download forçado como attachment,
  login para anônimo e `404` após adulterar assinatura; o acesso cruzado foi
  confirmado depois com cliente sem visibilidade do objeto pai;
- a configuração instalada não restringe `allowed_filetypes`; TXT e conteúdo
  textual inerte nomeado `.php` foram aceitos nos canais Web de equipe e cliente;
- com allowlist `.txt` temporária e rollback verificado, o cliente rejeitou
  `.php`, mas staff aceitou por `ajaxUpload(true)`; a rejeição declarada como 415
  tornou-se HTTP 500 pela tabela incompleta de `Http::header_code_verbose()`;
- com limite temporário de 256 KiB, o cliente aceitou 1 KiB e rejeitou 262.145
  bytes, enquanto staff aceitou 262.145 bytes pelo mesmo bypass; o `413`
  declarado também se tornou HTTP 500 e as configurações voltaram a `NULL`;
- cliente sem visibilidade da tarefa baixou seu anexo interno ao receber URL
  staff assinada, confirmando capability sem autorização contra o objeto pai;
- `include/class.file.php:493-495` contém condição
  `if (!$bks[0]->getBkChar() !== 'D')`, cuja precedência aparenta sempre
  adicionar fallback; não classificada como defeito.

## Busca e indexação

`SearchBackend` é registry e `SearchInterface` escolhe backend configurado ou
`mysql` (`include/class.search.php:28-68,202-228`). O padrão indexa thread,
ticket, usuário, organização e FAQ por sinais.

A tabela `${TABLE_PREFIX}_search` é criada dinamicamente, com PK composta e
FULLTEXT (`include/class.search.php:443-474`). Reindexação incremental ocorre por
cron ou `manage.php cron search` (`include/class.search.php:477-646`;
`include/cli/modules/cron.php:20-26`). A busca pública da KB usa consultas
`contains`, separadas desse índice.

`include/class.search.php:150` passa `$false` não definido a `getDynamicData()`;
o efeito precisa ser confirmado com diagnóstico `E_ALL`.

## Cron e manutenção

Há cron HTTP com API key, cron CLI, autocron do SCP e comandos CLI de fetch e
search. `Cron::run()` coleta e-mail, trata vencimentos/locks e limpa logs,
sessões, resets, drafts, órfãos e eventualmente tabelas
(`include/class.cron.php:21-123`).

O autocron é um GIF 1×1, limita chamadas por agente/sessão, fecha a sessão antes
do trabalho e condiciona coleta de e-mail à configuração
(`scp/autocron.php:17-68`). Não foi localizada trava global de sobreposição em
`Cron::run()`; concorrência será testada depois.

## Filtros

Filtros persistem ordem, escopo, regras e ações em `filter`, `filter_rule` e
`filter_action`. Antes da criação, dados do ticket/usuário/organização/forms são
enriquecidos, banlist e regras são avaliadas, e ações são aplicadas; envio de
e-mail pode ocorrer depois da criação (`include/class.ticket.php:3981-4047`;
`include/class.filter.php:272-348,779-886`).

Ações incluem rejeitar, usar Reply-To, desativar autoresposta, anexar resposta
pronta, atribuir departamento/prioridade/SLA/equipe/agente/tópico/status e enviar
e-mail (`include/class.filter_action.php:206-739`).

## Formulários dinâmicos

`DynamicForm`, campo, entrada e resposta mapeiam `form`, `form_field`,
`form_entry` e `form_entry_values`. Entradas polimórficas atendem ticket, tarefa,
usuário e organização (`include/class.dynamic_forms.php:29-32,598-601,974-990,
1416-1432`).

Persistência instancia entry/answers, valida editabilidade, converte por tipo e
salva valor/ID. Uploads ficam serializados em JSON. Alterações alimentam as
projeções `__cdata` e mudanças estruturais podem reconstruí-las
(`include/class.dynamic_forms.php:300-576,1315-1535`).

## Base de conhecimento

O subsistema atual usa `FAQ`, `Category`, relação FAQ–Help Topic e `Canned`.
Somente FAQ publicada em categoria pública é renderizada; landing lista
categorias ou pesquisa query/categoria/tópico (`kb/kb.inc.php:16-24`,
`kb/faq.php:16-35`, `include/client/knowledgebase.inc.php:6-35`).

FAQs admitem traduções, anexos e relação N:N com tópicos e entram na busca
global. Respostas prontas podem ter escopo departamental e integrar respostas e
ações de filtro. `include/class.knowledgebase.php` aparenta implementação legada
sem chamador atual localizado; carregamento dinâmico/plugins ainda impedem
declará-la morta.

## Lacunas dinâmicas adiadas

- OAuth/IMAP/POP, threading, deduplicação, SMTP e bounce reais;
- MIME real, deduplicação e backends de storage alternativos;
- reconstrução do índice/cdata e busca com volume/acentos;
- concorrência/idempotência do cron;
- desempenho da busca pública da KB.
