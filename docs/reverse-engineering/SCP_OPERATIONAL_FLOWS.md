# SCP — Fluxos operacionais

[Mapa principal e limites](SCP_FLOW_MAP.md). Baseline `v1.18.4`; releitura estática
em 2026-09-05, entrada `33d40fb7`. **Fato observado:** as cadeias abaixo foram
relidas; nenhum teste comportamental foi executado nesta unidade.

## Como ler

`STAFF` é a cadeia detalhada em T01–T03 do
[mapa transversal](SCP_TRANSVERSAL_FLOWS.md), com condições e exceções próprias.
Não substitui permissão de objeto/ação. Caminhos de classes e AJAX sem prefixo
pertencem a `include/`; templates a `include/staff/`. Números após caminhos são
linhas da baseline. Cada ID agrupa um fluxo e suas variantes expressamente
listadas; a decomposição literal das rotas está no [catálogo AJAX](AJAX_ROUTE_CATALOG.md).
Dependências indicam o que precisa ser preservado, não módulos aprovados.

## Painel e diretório

| ID | Entrada e guardas | Cadeia, efeitos e apresentação | Dependência de extração |
| --- | --- | --- | --- |
| O45 | `scp/dashboard.php`, `scp/directory.php`; STAFF | `OverviewReport(start,period)` → `getPlotData/getTabularData` (`class.report.php:85,149`), HTML/CSV; diretório usa `getDeptAgents`, filtros q/did, ordenação e `Pagenate` em `directory.inc.php` | Consulta também está no template; quatro rotas AJAX de relatórios não têm implementação, mas dashboard tradicional existe |

## Usuários e organizações

| ID | Entrada e guardas | Cadeia, efeitos e apresentação | Dependência de extração |
| --- | --- | --- | --- |
| O34 | `scp/users.php:17,189`; `User::PERM_DIRECTORY`; lista/busca/ordem/detalhe/tickets/PJAX/export | `User::lookup`, query em `users.inc.php`, dados dinâmicos e sessão; `user-view`, `templates/tickets`; `Export::saveUsers/saveTickets`; AJAX `exportTickets` delega `queueExport` | Query em sessão e agregações no template não são contrato independente |
| O35 | `UsersAjaxAPI::search/searchStaff/lookup/selectUser/getUser/preview/addRemoteUser`; STAFF, permissões por caminho | Diretórios locais/remotos `AuthenticationBackend`, seleção e `to_json`; searchStaff consulta agentes; preview exige diretório ou edição; remoto exige criação | Seleção, prévia e detalhe têm finalidades diferentes; integração externa depende de backend/configuração |
| O36 | `users.php` create/update; AJAX `addUser/editUser/updateUser` | `UserForm` → `User::fromForm` (`class.user.php:269`); `UserAccount::update` e `User::updateInfo` (`:540`); JSON201 ou `user.tmpl.php` | Perfil, criação e conta são persistências distintas; preservar guardas de cada entrada |
| O37 | `users.php` confirmlink/pwreset; AJAX `register/manage` (`ajax.users.php:188,217`) | `sendConfirmEmail/sendResetEmail`, `UserAccount::register` (`class.user.php:1380`)/`update`; gestão AJAX exige `PERM_MANAGE`; modais de conta/registro | Envio, confirmação e autenticação são etapas diferentes |
| O38 | `users.php:75`; massa lock/unlock/delete/reset/register/setorg; AJAX delete | `lock/unlock`, e-mails, `UserAccount::register`, `setOrganization`; exclusão pode chamar `deleteAllTickets` antes de `User::delete`; AJAX exige `PERM_DELETE`; resultado parcial | `User::delete` recusa tickets existentes; remove conta/e-mails/dados; não há garantia universal de atomicidade |
| O39 | `users.php:167`; AJAX importUsers/updateOrg/createNote/manageForms/updateForms | `User::importFromPost` (`class.user.php:533`), `Organization::fromForm/setOrganization`, nota U{id}; forms tipo U ordenados, adicionados/removidos | Importar, vincular, anotar e configurar forms são efeitos distintos; notas compartilhadas em T32 |
| O40 | `scp/orgs.php`; lista/busca/detalhe/tickets/PJAX/export; AJAX busca/lookup/seleção | `Organization::lookup`, query/sessão em `orgs.inc.php`, `org-view`; `Export::saveOrganizations/saveTickets`, export AJAX delega `queueExport` | Tickets agregados via usuário; alvo AJAX `getOrg` ausente não elimina detalhe tradicional |
| O41 | `OrgsAjaxAPI::addOrg/editOrg/updateOrg` (`ajax.orgs.php:226,65,85`); create/edit | `OrganizationForm` → `Organization::fromForm/updateProfile/update` (`class.organization.php:386,419`); templates org-lookup/org-profile/org; JSON201 | Perfil dinâmico e propriedades operacionais separados |
| O42 | `orgs.php` import/remove-users/mass delete; AJAX addUser/importUsers/delete/note/forms | `User::importFromPost/setOrganization`, `Organization::removeUser/delete` (`class.organization.php:304,548`); notas O{id}, forms O; modais/contagens | Exclusão desassocia usuários e remove dados; importação/vínculo têm guardas próprias |

## Tarefas

| ID | Entrada e guardas | Cadeia, efeitos e apresentação | Dependência de extração |
| --- | --- | --- | --- |
| O25 | `scp/tasks.php:18,143`; listar/detalhar/buscar/ordenar; `checkStaffPerm` no detalhe | `Task::lookup`; `tasks.inc.php` constrói query/filtros/paginação/sessão; abertas/minhas/atrasadas/concluídas; `task-view.inc.php`; AJAX lookup JSON | Visibilidade da lista, detalhe e prévia não presumida uniforme |
| O26 | `TasksAjaxAPI::add` (`ajax.tasks.php:91`); livre/derivada | `TaskForm`, formulário interno/anexos → `Task::create` (`class.task.php:1458`), verifica `PERM_CREATE`, salva tarefa/dados/TaskThread/descrição/evento/atribuição/alertas/sinal; modal task, 201 ID | Derivação depende de sessão; notas cruzadas podem ocorrer depois da criação |
| O27 | `TicketsAjaxAPI::addTask/task` (`ajax.tickets.php:1833,1936`); acesso + `Task::PERM_CREATE` na criação | `Task::create` com pai ticket, campos/prazo; notas cruzadas; detalhe modal confirma ticket/tarefa; `task-view.tmpl.php` | Contexto e prazo herdados não são iguais à tarefa livre |
| O28 | `tasks.php:53`; postnote/postreply; addcc exige `Ticket::PERM_EDIT` no papel da tarefa (:111) | `Task::postNote/postReply` (`class.task.php:971,1004`) → addNote/addResponse, possível task:status, `onActivity`, alertas; limpa drafts/widgets; CC via addCollaborator/flag | Atualização da tarefa não equivale à resposta pública de ticket |
| O29 | `TasksAjaxAPI::edit/editField` (`ajax.tasks.php:192,222`); `PERM_EDIT` | `DynamicFormEntry::forObject(...,'A')` → `Task::update/updateField` (`class.task.php:1322,1368`); task-edit/field-edit, 201/JSON | Formulário interno e tipos dinâmicos |
| O30 | `TasksAjaxAPI::assign/claim/transfer` (`ajax.tasks.php:673,722,629`) | Guardas e formulários próprios → `Task::assign/claim/transfer` (`class.task.php:750,698,884`); modais/201 | Nomes iguais aos de ticket não provam contratos iguais |
| O31 | `TasksAjaxAPI::changeStatus/close/reopen/delete` (`ajax.tasks.php:823,885,881,780`) | Close: `PERM_CLOSE/isCloseable`; reopen: `PERM_CREATE`; `Task::setStatus/delete` (`class.task.php:570,1584`); task-status/delete, 201 | Estados/exclusão com guardas próprias |
| O32 | `TasksAjaxAPI::massProcess` (`ajax.tasks.php:298`) | claim/assign/transfer/reopen/close/delete; verifica tarefa, chama domínio, agrega resultados/notas | Sucesso parcial identificável por item; não transação integral |
| O33 | `TasksAjaxAPI::preview/task/triggerThreadAction` (`ajax.tasks.php:181,889,61`); tasks.php print/export | Prévia/detalhe; ações verificam tipo A e registro; `Task::pdfExport`; `Export::saveTasks` com sessão :Q:tasks | Prévia tem política distinta documentada; ações T47–T52; PDF/export são saídas separadas |

## Tickets — localizar, abrir e atender

| ID | Entrada e guardas | Cadeia, efeitos e apresentação | Dependência de extração |
| --- | --- | --- | --- |
| O01 | `scp/index.php:17`, `scp/tickets.php:28`; STAFF | index inclui tickets; `CustomQueue::getHierarchicalQueues`, `SavedQueue::lookup/AdhocSearch::load`, `getQuery(false,filter)`; sessão/fila; `templates/queue-tickets.tmpl.php` | Hierarquia, buscas salvas, preferências e apresentação repartidas |
| O02 | `tickets.php:48–145`; busca simples, uid/e-mail/organização/estado/clear_filter | Critérios advsearch em sessão, fila ad hoc; número/e-mail/palavras; organização pela relação do usuário; teste `<4` limita palavras | Não reduzir a `Ticket::lookup`; busca avançada e preferências em T23–T27 |
| O03 | `tickets.php:34–43,515`; ID/número, `Ticket::checkStaffPerm` | `Ticket::lookup` → ticket-view; AJAX previewTicket/viewUser; abas ticket-tasks/ticket-relations | Escopo do ticket antes de detalhe, prévia e agregações |
| O04 | `TicketsAjaxAPI::lookup/lookupByNumber/lookupByEmail` (`ajax.tickets.php:26,99,151`) | QuerySets e visibilidade → autocomplete JSON | Seleção distinta da pesquisa por fila/detalhe |
| O05 | `tickets.php:410`; GET/POST abrir; `PERM_CREATE` | `Ticket::open` (`class.ticket.php:4593`) → `Ticket::create(...,'staff',false)`; origem/usuário/atribuição, resposta/nota opcionais, notificações; limpa temporários, regenera sessão, redirect | Tópico/departamento/forms/SLA/numeração/thread/anexos/e-mail; criação composta |
| O06 | `tickets.php:169`; resposta, `PERM_REPLY`, lock conforme config | Valida corpo/destinatários/lock, ajusta colaboradores → `Ticket::postReply` (`class.ticket.php:3345`) → addResponse; estado/autoatribuição/onResponse/sinal/envio; limpa draft/widget, redirect | Coordenação precede método; só postReply não reproduz todo o fluxo |
| O07 | `tickets.php:253`; nota, acesso e lock | `Ticket::postNote` (`class.ticket.php:3509`), anexos, possível estado; limpa temporários; detalhe/fila | Nota, estado e alertas são efeitos separados |
| O08 | `tickets.php:308,519`; editar/atualizar, `PERM_EDIT` | `DynamicFormEntry::forTicket/addMissingFields` no preparo; `Ticket::update` (`class.ticket.php:3675`); reavalia acesso/limpa draft; ticket-edit/detalhe | Preparação de edição não pode ser presumida leitura pura |
| O09 | `TicketsAjaxAPI::editField/viewField` (`ajax.tickets.php:682,775`); acesso/edição | Campo/formulário validado → `Ticket::updateField` (`class.ticket.php:3809`); tipos/formatadores; field-edit/field-view, JSON/HTML | Arquivos, datas/fuso, departamento e fechamento |
| O10 | `manageForms/updateForms` (`ajax.tickets.php:380,395`); edição | Ordena/salva entradas, addForm, remove retiradas; form-manage/201 | Retirar formulário pode excluir dados, não apenas ocultar componente |
| O11 | `tickets.php:373,385`; solicitante/CC, edição | `User::lookup` → `Ticket::changeOwner/addCollaborator`, flags/save; changeUserForm/updateUser usam formulário | Perfil de usuário, propriedade e colaboração são operações diferentes |

## Tickets — encaminhar, relacionar e processar

| ID | Entrada e guardas | Cadeia, efeitos e apresentação | Dependência de extração |
| --- | --- | --- | --- |
| O12 | `tickets.php:326`; claim/overdue/ban/unban; edição/gerência/banlist conforme ramo | claim antigo; `markOverdue/logActivity`, `BanList::add/remove` | Claim tradicional tem assinatura incompatível; não equiparar ao modal |
| O13 | `TicketsAjaxAPI::assign/claim` (`ajax.tickets.php:801,862`); `PERM_ASSIGN`, aberto/atribuição | getAssignmentForm/getClaimForm → `Ticket::assign/claim` (`class.ticket.php:2847,2775`); eventos/alertas; assign/201 | Elegibilidade de agente/equipe pertence ao formulário/domínio |
| O14 | `transfer` (`ajax.tickets.php:559`); `PERM_TRANSFER` | getTransferForm → `Ticket::transfer` (`class.ticket.php:2655`); departamento/efeitos; transfer/201 | Revalidar acesso, atribuição e políticas após transição |
| O15 | `release` (`ajax.tickets.php:922`); liberação/gerência | `Ticket::release` (`class.ticket.php:2938`); controller registra released/nota opcional; release/201 | Evento/nota não estão somente no método de domínio |
| O16 | `refer/referrals` (`ajax.tickets.php:600,604`); `PERM_ASSIGN` | getReferralForm → `Ticket::refer` (`class.ticket.php:2949`); gestão também remove referências; refer.tmpl.php | Referência, atribuição e colaboração não são sinônimos |
| O17 | `changeTicketStatus/setTicketStatus` (`ajax.tickets.php:1359,1404`) | Papel por estado/isCloseable → `TicketStatus::lookup`, `Ticket::setStatus` (`class.ticket.php:1483`); fechar/reabrir/excluir/filhos; ticket-status | Tarefas bloqueantes e relações participam da transição |
| O18 | `markAs` (`ajax.tickets.php:1642`); marcação ou gerente | `markAnswered/markUnAnswered` (`class.ticket.php:2419,2423`), nota/logActivity; mark-as/201 | Marcação não publica resposta |
| O19 | `massProcess` (`ajax.tickets.php:981`); assign/claim/transfer/delete | Formulário/seleção/guardas por item → domínio; contagens sucesso/falha/templates | Resultado parcial explícito; seleção/alternar todos são controles UI, não comando de domínio |
| O20 | `changeSelectedTicketsStatus/setSelectedTicketsStatus` (`ajax.tickets.php:1505,1539`) | Estado/permissão global/acesso por ticket → setStatus; resultados agregados | Lote de estado distinto de atribuição/transferência |
| O21 | `mergeTickets/updateMerge/previewMerge` (`ajax.tickets.php:437,466,505`), massa merge/link (`:1030`) | merge-tickets → `Ticket::merge` (`class.ticket.php:2562`) ou unlink; prévia/relações | Pai/filho, threads, colaboradores e opções de combinação preservados |
| O22 | `triggerThreadAction` (`ajax.tickets.php:1706`) | Acesso, entrada na thread T, ação registrada → `ThreadEntry::triggerAction` | Derivação/edição/histórico/reenvio detalhados em T47–T52 |
| O23 | `tickets.php:529–547`; print/ZIP/auditoria | pdfExport/zipExport; PDF exige mbstring; auditoria depende de auditPlugin/PHAR, `Export::audits` | Binário/download/plugin não é JSON nem funcionalidade de plugin comprovada |
| O24 | `export/queueExport` (`ajax.tickets.php:2005,2019`); queue checkAccess | Sessão de campos/nome/delimitador, opcional updateExports; `CsvExporter::ack` → queue export → finalize → email → delete; queue-export | Salva preferência/pode enviar e-mail; ack não comprova arquivo entregue |

## Base de Conhecimento

| ID | Entrada e guardas | Cadeia, efeitos e apresentação | Dependência de extração |
| --- | --- | --- | --- |
| O43 | `scp/kb.php`, `scp/faq.php`; categoria/busca/detalhe/print/CRUD/publicação/acesso | `Category::lookup`, `FAQ::create/update/publish/unpublish/delete/printPdf` (`class.faq.php:368`); mutações `PERM_MANAGE`; `FaqAccessMgmtForm` em KbaseAjaxAPI::manageFaqAccess (`ajax.kbase.php:67`) grava ispublished/save no controller; faq-* / quick-add | Idiomas/anexos/tópicos/visibilidade/drafts; preview AJAX HTML |
| O44 | `scp/categories.php`, `scp/canned.php`; CRUD/massas | Categoria exige FAQ gestão: Category create/update, público/privado por QuerySet, delete percorre FAQ; canned habilitado+gestão/acesso: Canned create/update/delete, enable/disable; anexos/drafts/sinais no controller | KbaseAjaxAPI cannedResp difere de TicketsAjaxAPI cannedResponse contextualizada por ticket/variáveis |

## Persistência, evidências e lacunas

Entidades primárias: TICKET, TASK, USER/USER_EMAIL/USER_ACCOUNT, ORGANIZATION,
FAQ/FAQ_CATEGORY/FAQ_TOPIC, CANNED e respectivos cdata. Threads, eventos,
anexos e formulários participam dos comandos compostos. Consultar
[ORM](ORM_CATALOG.md) e [modelo de dados](DATA_MODEL_MAP.md) para tabelas;
esta lista não pretende esgotar todas as tabelas tocadas por cada variante.

- **Fato observado:** `scp/tickets.php:336` chama claim sem argumentos;
  `include/class.ticket.php:2775` exige `ClaimForm` e erros por referência.
  O modal `include/ajax.tickets.php:900` fornece ambos. **Inferência:** caminhos
  não equivalentes; ramo antigo não executado nesta unidade.
- **Fato observado:** relatórios AJAX ausentes não eliminam `OverviewReport`;
  changeUser/getOrg ausentes não eliminam páginas tradicionais correspondentes.
- **Inferência:** viabilidade de reutilização de regras presas a controladores
  permanece pendente, não se resolve copiando código ou invocando HTML legado.
- **Herdado, não reexecutado:** a [matriz visual](INTERFACE_COVERAGE_MATRIX.md)
  correlaciona tickets com BHV-008/010/013/014/017/022/026–034, tarefas com
  BHV-009/031, usuários/organizações com BHV-020/021/023 e KB com BHV-015/016.
  Exclusões observadas não provam execução destrutiva; BHV-034 conserva a
  divergência de materialização da exportação. Não há nova evidência visual.
