# APIs, AJAX e integrações

## API HTTP nativa

| Método/caminho | Controller | Controle | Resposta observada |
| --- | --- | --- | --- |
| `POST /api/tickets.json` | `TicketApiController::create` | API key + IP + `can_create_tickets` | 201 e número em texto |
| `POST /api/tickets.xml` | mesmo | mesmo | 201 e número em texto |
| `POST /api/tickets.email` | mesmo | mesmo; tenta correlacionar thread | 201 e número em texto |
| `POST /api/tasks/cron` | `CronApiController::execute` | API key + IP + `can_exec_cron` | 200 `Completed` |
| `/api/auth/ext` | closure registrada pelo sinal `api` | sessão/backend externo; sem API key explícita | dependente do backend |

Evidências: `api/http.php:16-29`, `include/class.auth.php:787-798`,
`include/class.api.php:68-78,188-215`, `include/api.tickets.php:113-174` e
`include/api.cron.php:5-20`.

A extensão do caminho seleciona o parser; não há negociação por `Accept` nem
versionamento na URL. Campos são filtrados por listas em
`include/api.tickets.php:11-64`, mas a validação semântica final pertence a
`Ticket::create()`.

Corpo HTTP é lido de `php://input` e CLI de `php://stdin`. JSON, XML e e-mail
são aceitos; erros de formato/parsing usam 415, 501 ou 400
(`include/class.api.php:175-186,238-264`). Respostas e erros são
predominantemente texto/HTML, sem envelope JSON uniforme
(`include/class.api.php:314-373`; `include/class.http.php:16-50`).

## Cron e pipe locais

`api/cron.php` e `api/pipe.php` exigem CLI e não usam API key. O cron busca
e-mails, processa vencimento/locks, limpa logs, sessões, tokens, drafts e órfãos
e pode otimizar tabelas (`include/class.cron.php:23-123`). O pipe converte
respostas em exit codes de MTA (`include/api.tickets.php:239-269`).

**Lacuna:** cron remoto devolve `Completed` mesmo se `Cron::run()` retornar cedo
por upgrade pendente; a semântica operacional exige confirmação.

## AJAX do cliente

Foram normalizadas 12 rotas-folha em `ajax.php:22-51`: configuração cliente,
criação/leitura/atualização/exclusão de drafts, anexos inline, formulários do
tópico, uploads e arquivo de idioma. A superfície carrega `client.inc.php`, mas
não exige login global; CSRF é exigido para métodos mutáveis.

O sinal `ajax.client` permite rotas adicionais e também preenche
`apps/dispatcher.php`. Não há listener literal nativo localizado.

## AJAX da equipe

`scp/ajax.php:36-322` contém 256 chamadas sintáticas `url*()`: 27 agrupadores
e 229 folhas. A revisão por árvore balanceada corrigiu dois supercounts da
tabela anterior: Tickets possui 53, e Admin 7.

| Grupo | Folhas | Grupo | Folhas |
| --- | ---: | --- | ---: |
| KB | 3 | Conteúdo | 7 |
| Configuração | 3 | Formulários | 8 |
| Filtros | 1 | Agenda | 6 |
| Listas | 11 | Plugins | 4 |
| Relatórios | 4 | Usuários | 28 |
| Organizações | 22 | Locks | 3 |
| Tickets | 53 | Tarefas | 20 |
| Threads | 8 | Drafts | 7 |
| Exportação | 1 | Notas | 4 |
| Sequências | 3 | Upgrader | 1 |
| Ajuda | 2 | i18n | 5 |
| Admin | 7 | Staff | 8 |
| Filas | 7 | E-mail | 3 |

A decomposição confirmada indica 101 GET, 60 POST, 5 DELETE e 63 declarações sem
restrição de método. Toda a superfície exige agente válido e CSRF mutável via
`scp/staff.inc.php`; plugins, e-mail e upgrader adicionam requisito de
administrador.
O [catálogo AJAX](AJAX_ROUTE_CATALOG.md) cruza cada uma das 229 folhas com
verbo, regex efetiva, alvo, definição, guarda resumida e efeito/resposta.

As respostas misturam HTML, texto e JSON. No SCP, somente string iniciada por
`{` recebe `application/json`; no cliente o retorno é impresso diretamente
(`scp/ajax.php:324-333`; `ajax.php:50-51`).

## Semântica e achados

- `url()` sem helper de verbo aceita qualquer método; POST pode emular PUT,
  PATCH e DELETE por `_method` (`include/class.dispatcher.php:29-37,87-104`).
- primeira regex compatível vence e várias não possuem âncora final `$`;
- `Controller::access()` é permissivo por padrão;
- controllers no formato arquivo/classe são carregados sob demanda.

Achados e validações futuras:

1. **fato observado:** treze folhas apontam para alvo ausente; nos nove métodos
   ausentes de controllers carregáveis, requisição que passe `access()` alcança
   o 500 explícito; nas quatro rotas de relatório, a falha antecede o teste de
   callable durante carregamento/construção; runtime confirmará alcance e
   resposta real;
2. a documentação histórica diz que API keys servem à API HTTP sem configuração
   especial, enquanto o código exige flags por operação;

**Inferência sustentada:** a API nativa é orientada a comandos, não CRUD. AJAX é
interno, sem versão e acoplado a templates; não está classificado como contrato
público estável.
