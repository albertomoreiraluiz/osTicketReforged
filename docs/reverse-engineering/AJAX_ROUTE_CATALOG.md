# Catálogo de rotas AJAX

## Método reproduzível

A extração correta usa tokens PHP e árvore balanceada:

1. executar `token_get_all` no entrypoint;
2. construir nós para `patterns`, `url`, `url_get`, `url_post` e
   `url_delete`, equilibrando parênteses;
3. classificar `url*` como agrupador apenas quando o segundo argumento é
   `patterns(...)`;
4. propagar prefixo regex e controller herdado recursivamente;
5. mapear helpers para GET/POST/DELETE/ANY e concatenar regex literalmente;
6. resolver controller/método, herança, guardas, entidades e resposta;
7. emitir linha estável
   `entrypoint|linha|verbo|regex|controller|definição|guarda|efeito|resposta|estado`.

Invariantes confirmados:

| Arquivo | Chamadas | Agrupadores | Folhas | Verbos |
| --- | ---: | ---: | ---: | --- |
| `ajax.php` | 16 | 4 | 12 | 4 GET, 7 POST, 1 DELETE |
| `scp/ajax.php` | 256 | 27 | 229 | 101 GET, 60 POST, 5 DELETE, 63 ANY |

ANY significa qualquer método, não apenas GET/POST. POST pode emular PUT, PATCH
e DELETE por `_method` (`include/class.dispatcher.php:29-37,87-104,191-205`).

## Cliente — 12 folhas

| Verbo | Regex efetiva | Alvo |
| --- | --- | --- |
| GET | `/config/client` | `ConfigAjaxAPI::client` |
| POST/DELETE | `/draft/{id}` | `updateDraftClient` / `deleteDraftClient` |
| POST | `/draft/{id}/attach` | `uploadInlineImageClient` |
| POST | `/draft/{namespace}/attach` | `uploadInlineImageEarlyClient` |
| GET/POST | `/draft/{namespace}` | `getDraftClient` / `createDraftClient` |
| GET | `/form/help-topic/{id}` | `getClientFormsForHelpTopic` |
| POST | três padrões `/form/upload/...` | `upload` ou `attach` |
| GET | `/i18n/{lang}/{tag}` | `i18nAjaxAPI::getLanguageFile` |

Declarações: `ajax.php:23-43`. Definições:
`include/ajax.config.php:58`, `include/ajax.draft.php:147-252`,
`include/ajax.forms.php:50,378-392` e `include/ajax.i18n.php:21`.

## Staff — contagem por família

| Família | Folhas | Família | Folhas |
| --- | ---: | --- | ---: |
| KB | 3 | Conteúdo | 7 |
| Config | 3 | Form | 8 |
| Filtro | 1 | Agenda | 6 |
| Listas | 11 | Plugins | 4 |
| Relatórios | 4 | Usuários | 28 |
| Organizações | 22 | Locks | 3 |
| Tickets | 53 | Tarefas | 20 |
| Threads | 8 | Drafts | 7 |
| Export | 1 | Notas | 4 |
| Sequências | 3 | Upgrader | 1 |
| Ajuda | 2 | i18n | 5 |
| Admin | 7 | Staff | 8 |
| Filas | 7 | E-mail | 3 |

Todas passam por ACL `staff`, sessão válida e CSRF para métodos mutáveis
(`scp/staff.inc.php:24-112`). Atividade/offline/upgrade têm condições e exceções
administrativas explícitas, não uma guarda uniforme. `Controller::access()` é chamado, mas o padrão é
permissivo. Plugins e e-mail exigem admin no controller; Admin verifica admin em
cada método; upgrader exige admin no método. Demais controles são distribuídos.

## Alvos não callable localizados

Treze folhas apontam para alvo ausente na baseline:

- lista: `massManageListItems` (`scp/ajax.php:86`);
- plugins: `actions` (`scp/ajax.php:95`);
- quatro relatórios e o arquivo `include/ajax.reports.php` ausente
  (`scp/ajax.php:99-102`);
- organização: `getOrg` (`scp/ajax.php:137`);
- ticket: `changeUser` (`scp/ajax.php:165`);
- busca: `deleteSearch` (`scp/ajax.php:211`);
- filas em busca: `deleteQueues`, `disableQueues`, `undisableQueues`
  (`scp/ajax.php:215-217`);
- fila: `addColumn` (`scp/ajax.php:311`).

Para os nove métodos ausentes em controllers carregáveis, o dispatcher produz
500 após `access()` (`include/class.dispatcher.php:139-155`). As quatro rotas de
relatório falham antes, no `include_once`/construção do controller ausente
(`include/class.dispatcher.php:163-175`). A Onda 7 confirmou resposta `500` nas
quatro rotas desse grupo. A ausência dos 13 alvos é fato estático.

### Confirmação comportamental parcial — Onda 7

Em 2026-09-04, uma sessão administrativa confirmou resposta `500` nos treze
alvos ausentes. As rotas mutáveis foram acionadas somente com identificadores
fictícios e CSRF válido; a falha de resolução do método antecedeu qualquer
persistência. Rotas válidas de configuração responderam `200`; parâmetros
obrigatórios ausentes produziram `400`; acesso anônimo a uma rota de
configuração produziu `403`; e POST autenticado sem CSRF produziu `400`.

Três regex de filas concatenam `search` diretamente ao ID, sem barra, e
`/admin/role/{id}/perms` é irmã de `quick-add`. Esses detalhes comprovam por
que extração por indentação não é confiável.

## Matriz linha a linha

Legenda: `STAFF` resume a cadeia condicional acima, sessão válida e CSRF condicionado ao
método HTTP. `hasPerm`, `acesso-*` e `dono-*` resumem controles adicionais
observados; não são nomes canônicos de permissões.

| Linha | Verbo | Regex | Alvo | Definição | Guarda | Efeito/Resposta |
| ---: | --- | --- | --- | --- | --- | --- |
| 39 | GET | `/kb/canned-response/(?P<id>\d+).(?P<format>json\|txt)` | `KbaseAjaxAPI::cannedResp` | `ajax.kbase.php:21` | STAFF | KB; HTML/JSON |
| 40 | ANY | `/kb/faq/(?P<id>\d+)/access` | `KbaseAjaxAPI::manageFaqAccess` | `ajax.kbase.php:67` | STAFF+hasPerm | KB; HTML/JSON |
| 41 | GET | `/kb/faq/(?P<id>\d+)` | `KbaseAjaxAPI::faq` | `ajax.kbase.php:35` | STAFF+hasPerm | KB; HTML/JSON |
| 44 | GET | `/content/log/(?P<id>\d+)` | `ContentAjaxAPI::syslog` | `ajax.content.php:22` | STAFF+ADMIN | conteúdo; HTML/JSON |
| 45 | GET | `/content/context` | `ContentAjaxAPI::context` | `ajax.content.php:215` | STAFF | conteúdo; HTML/JSON |
| 46 | GET | `/content/ticket_variables` | `ContentAjaxAPI::ticket_variables` | `ajax.content.php:48` | STAFF | conteúdo; HTML/JSON |
| 47 | GET | `/content/signature/(?P<type>\w+)(?:/(?P<id>\d+))?` | `ContentAjaxAPI::getSignature` | `ajax.content.php:119` | STAFF | conteúdo; HTML/JSON |
| 48 | GET | `/content/(?P<id>\d+)/(?:(?P<lang>\w+)/)?manage` | `ContentAjaxAPI::manageContent` | `ajax.content.php:147` | STAFF+ADMIN | conteúdo; HTML |
| 49 | GET | `/content/(?P<id>[\w-]+)/(?:(?P<lang>\w+)/)?manage` | `ContentAjaxAPI::manageNamedContent` | `ajax.content.php:173` | STAFF+ADMIN | conteúdo; HTML |
| 50 | POST | `/content/(?P<id>\d+)(?:/(?P<lang>\w+))?` | `ContentAjaxAPI::updateContent` | `ajax.content.php:187` | STAFF+ADMIN | conteúdo; status |
| 53 | GET | `/config/scp` | `ConfigAjaxAPI::scp` | `ajax.config.php:23` | STAFF | config; JSON/JS |
| 54 | GET | `/config/links` | `ConfigAjaxAPI::templateLinks` | `ajax.config.php:89` | STAFF | config; JSON |
| 55 | GET | `/config/date-format` | `ConfigAjaxAPI::dateFormat` | `ajax.config.php:121` | STAFF | config; texto escapado |
| 58 | GET | `/form/help-topic/(?P<id>\d+)` | `DynamicFormsAjaxAPI::getFormsForHelpTopic` | `ajax.forms.php:17` | STAFF+Referer+tópico existente | forms; sessão :form-data; JSON com HTML e media |
| 59 | GET | `/form/field-config/(?P<id>\d+)` | `DynamicFormsAjaxAPI::getFieldConfiguration` | `ajax.forms.php:54` | STAFF | campo; HTML |
| 60 | POST | `/form/field-config/(?P<id>\d+)` | `DynamicFormsAjaxAPI::saveFieldConfiguration` | `ajax.forms.php:59` | STAFF | campo; status/HTML |
| 61 | DELETE | `/form/answer/(?P<entry>\d+)/(?P<field>\d+)` | `DynamicFormsAjaxAPI::deleteAnswer` | `ajax.forms.php:94` | STAFF | resposta; status |
| 62 | POST | `/form/upload/(\d+)?` | `DynamicFormsAjaxAPI::upload` | `ajax.forms.php:378` | STAFF | arquivo; JSON |
| 63 | POST | `/form/upload/(\w+)?` | `DynamicFormsAjaxAPI::attach` | `ajax.forms.php:392` | STAFF | arquivo; JSON |
| 64 | POST | `/form/upload/(?P<object>ticket\|task)/(\w+)` | `DynamicFormsAjaxAPI::attach` | `ajax.forms.php:392` | STAFF | arquivo; JSON |
| 65 | GET | `/form/(?P<id>\d+)/fields/view` | `DynamicFormsAjaxAPI::getAllFields` | `ajax.forms.php:415` | STAFF | campos; JSON com HTML |
| 68 | GET | `/filter/action/(?P<type>\w+)/config` | `FilterAjaxAPI::getFilterActionForm` | `ajax.filter.php:7` | STAFF | filtro; HTML |
| 71 | ANY | `/schedule/add` | `ScheduleAjaxAPI::add` | `ajax.schedule.php:6` | STAFF | agenda; HTML/status |
| 72 | ANY | `/schedule/(?P<id>\d+)/clone` | `ScheduleAjaxAPI::cloneSchedule` | `ajax.schedule.php:49` | STAFF | agenda; status |
| 73 | ANY | `/schedule/(?P<id>\d+)/diagnostic` | `ScheduleAjaxAPI::diagnostic` | `ajax.schedule.php:100` | STAFF | agenda; HTML |
| 74 | POST | `/schedule/(?P<id>\d+)/delete-entries` | `ScheduleAjaxAPI::deleteEntries` | `ajax.schedule.php:113` | STAFF | agenda; status |
| 75 | ANY | `/schedule/(?P<id>\d+)/entry/add` | `ScheduleAjaxAPI::addEntry` | `ajax.schedule.php:53` | STAFF | agenda; HTML/status |
| 76 | ANY | `/schedule/(?P<sid>\d+)/entry/(?P<eid>\d+)/update` | `ScheduleAjaxAPI::updateEntry` | `ajax.schedule.php:77` | STAFF | agenda; HTML/status |
| 79 | GET | `/list/(?P<list>\w+)/items` | `DynamicFormsAjaxAPI::getListItems` | `ajax.forms.php:125` | STAFF | lista; JSON |
| 80 | GET | `/list/(?P<list>\w+)/items/search` | `DynamicFormsAjaxAPI::searchListItems` | `ajax.forms.php:222` | STAFF | lista; JSON |
| 81 | GET | `/list/(?P<list>\w+)/item/(?P<id>\d+)/update` | `DynamicFormsAjaxAPI::getListItem` | `ajax.forms.php:109` | STAFF | item; HTML |
| 82 | POST | `/list/(?P<list>\w+)/item/(?P<id>\d+)/update` | `DynamicFormsAjaxAPI::saveListItem` | `ajax.forms.php:152` | STAFF | item; status/HTML |
| 83 | GET | `/list/(?P<list>\w+)/items/(?P<id>\d+)/preview` | `DynamicFormsAjaxAPI::previewListItem` | `ajax.forms.php:138` | STAFF | item; HTML |
| 84 | ANY | `/list/(?P<list>\w+)/item/add` | `DynamicFormsAjaxAPI::addListItem` | `ajax.forms.php:250` | STAFF | item; HTML/status |
| 85 | ANY | `/list/(?P<list>\w+)/import` | `DynamicFormsAjaxAPI::importListItems` | `ajax.forms.php:283` | STAFF | itens; HTML/status |
| 86 | ANY | `/list/(?P<list>\w+)/manage` | `DynamicFormsAjaxAPI::massManageListItems` | AUSENTE | STAFF; ALVO-AUSENTE | 500 após `access()` |
| 87 | POST | `/list/(?P<list>\w+)/delete` | `DynamicFormsAjaxAPI::deleteItems` | `ajax.forms.php:357` | STAFF | itens; status |
| 88 | POST | `/list/(?P<list>\w+)/disable` | `DynamicFormsAjaxAPI::disableItems` | `ajax.forms.php:311` | STAFF | itens; status |
| 89 | POST | `/list/(?P<list>\w+)/enable` | `DynamicFormsAjaxAPI::undisableItems` | `ajax.forms.php:334` | STAFF | itens; status |
| 92 | GET | `/plugins/(?P<id>\d+)/instances` | `PluginsAjaxAPI::getInstances` | `ajax.plugins.php:28` | STAFF+ADMIN | instância; HTML |
| 93 | ANY | `/plugins/(?P<id>\d+)/instances/(?P<iid>\d+)/update` | `PluginsAjaxAPI::updateInstance` | `ajax.plugins.php:34` | STAFF+ADMIN | instância; HTML/JSON201 |
| 94 | ANY | `/plugins/(?P<id>\d+)/instances/add` | `PluginsAjaxAPI::addInstance` | `ajax.plugins.php:47` | STAFF+ADMIN | instância; HTML/JSON201 |
| 95 | POST | `/plugins/(?P<id>\d+)/instances/(\w+)` | `PluginsAjaxAPI::actions` | AUSENTE | STAFF+ADMIN; ALVO-AUSENTE | 403 não-admin; admin alcança erro callable |
| 99 | GET | `/report/overview/graph` | `OverviewReportAjaxAPI::getPlotData` | AUSENTE | STAFF; ALVO-AUSENTE | falha no loading; status dinâmico TBD |
| 100 | GET | `/report/overview/table/groups` | `OverviewReportAjaxAPI::enumTabularGroups` | AUSENTE | STAFF; ALVO-AUSENTE | falha no loading; status dinâmico TBD |
| 101 | GET | `/report/overview/table/export` | `OverviewReportAjaxAPI::downloadTabularData` | AUSENTE | STAFF; ALVO-AUSENTE | falha no loading; status dinâmico TBD |
| 102 | GET | `/report/overview/table` | `OverviewReportAjaxAPI::getTabularData` | AUSENTE | STAFF; ALVO-AUSENTE | falha no loading; status dinâmico TBD |
| 105 | GET | `/users` | `UsersAjaxAPI::search` | `ajax.users.php:27` | STAFF | usuário; JSON |
| 106 | GET | `/users/local` | `UsersAjaxAPI::search` | `ajax.users.php:27` | STAFF | usuário; JSON |
| 107 | GET | `/users/remote` | `UsersAjaxAPI::search` | `ajax.users.php:27` | STAFF | usuário; JSON |
| 108 | GET | `/users/(?P<id>\d+)` | `UsersAjaxAPI::getUser` | `ajax.users.php:282` | STAFF+hasPerm | usuário; JSON |
| 109 | POST | `/users/(?P<id>\d+)` | `UsersAjaxAPI::updateUser` | `ajax.users.php:166` | STAFF+hasPerm | usuário; status |
| 110 | GET | `/users/(?P<id>\d+)/preview` | `UsersAjaxAPI::preview` | `ajax.users.php:119` | STAFF+hasPerm | usuário; HTML |
| 111 | GET | `/users/(?P<id>\d+)/edit` | `UsersAjaxAPI::editUser` | `ajax.users.php:148` | STAFF+hasPerm | usuário; HTML |
| 112 | ANY | `/users/lookup` | `UsersAjaxAPI::getUser` | `ajax.users.php:282` | STAFF+hasPerm | usuário; JSON |
| 113 | GET | `/users/lookup/form` | `UsersAjaxAPI::lookup` | `ajax.users.php:300` | STAFF | usuário; HTML |
| 114 | POST | `/users/lookup/form` | `UsersAjaxAPI::addUser` | `ajax.users.php:304` | STAFF+hasPerm | usuário; JSON/status |
| 115 | GET | `/users/add` | `UsersAjaxAPI::addUser` | `ajax.users.php:304` | STAFF+hasPerm | usuário; HTML |
| 116 | ANY | `/users/import` | `UsersAjaxAPI::importUsers` | `ajax.users.php:352` | STAFF+hasPerm | usuário; HTML/status |
| 117 | GET | `/users/select` | `UsersAjaxAPI::selectUser` | `ajax.users.php:378` | STAFF | usuário; HTML |
| 118 | GET | `/users/select/(?P<id>\d+)` | `UsersAjaxAPI::selectUser` | `ajax.users.php:378` | STAFF | usuário; HTML |
| 119 | GET | `/users/select/auth:(?P<bk>[\w.]+):(?P<id>.+)` | `UsersAjaxAPI::addRemoteUser` | `ajax.users.php:329` | STAFF+USER_CREATE | usuário remoto; HTML/status |
| 120 | GET | `/users/(?P<id>\d+)/register` | `UsersAjaxAPI::register` | `ajax.users.php:188` | STAFF+USER_MANAGE | registro; HTML |
| 121 | POST | `/users/(?P<id>\d+)/register` | `UsersAjaxAPI::register` | `ajax.users.php:188` | STAFF+USER_MANAGE | registro; status |
| 122 | GET | `/users/(?P<id>\d+)/delete` | `UsersAjaxAPI::delete` | `ajax.users.php:252` | STAFF+USER_DELETE | usuário; HTML |
| 123 | POST | `/users/(?P<id>\d+)/delete` | `UsersAjaxAPI::delete` | `ajax.users.php:252` | STAFF+USER_DELETE | usuário; status |
| 124 | GET | `/users/(?P<id>\d+)/manage(?:/(?P<target>\w+))?` | `UsersAjaxAPI::manage` | `ajax.users.php:217` | STAFF+USER_MANAGE | usuário; HTML |
| 125 | POST | `/users/(?P<id>\d+)/manage(?:/(?P<target>\w+))?` | `UsersAjaxAPI::manage` | `ajax.users.php:217` | STAFF+USER_MANAGE | usuário; status |
| 126 | GET | `/users/(?P<id>\d+)/org(?:/(?P<orgid>\d+))?` | `UsersAjaxAPI::updateOrg` | `ajax.users.php:431` | STAFF+USER_EDIT | vínculo org; HTML |
| 127 | POST | `/users/(?P<id>\d+)/org` | `UsersAjaxAPI::updateOrg` | `ajax.users.php:431` | STAFF+USER_EDIT | vínculo org; status |
| 128 | GET | `/users/staff` | `UsersAjaxAPI::searchStaff` | `ajax.users.php:411` | STAFF+ADMIN | agente; JSON |
| 129 | POST | `/users/(?P<id>\d+)/note` | `UsersAjaxAPI::createNote` | `ajax.users.php:488` | STAFF | nota usuário; status |
| 130 | GET | `/users/(?P<id>\d+)/forms/manage` | `UsersAjaxAPI::manageForms` | `ajax.users.php:497` | STAFF | forms usuário; HTML |
| 131 | POST | `/users/(?P<id>\d+)/forms/manage` | `UsersAjaxAPI::updateForms` | `ajax.users.php:503` | STAFF+USER_EDIT | forms usuário; status |
| 132 | ANY | `/users/(?P<id>\d+)/tickets/export` | `UsersAjaxAPI::exportTickets` | `ajax.users.php:542` | STAFF | tickets usuário; export |
| 135 | GET | `/orgs` | `OrgsAjaxAPI::search` | `ajax.orgs.php:24` | STAFF | organização; JSON |
| 136 | GET | `/orgs/search` | `OrgsAjaxAPI::search` | `ajax.orgs.php:24` | STAFF | organização; JSON |
| 137 | GET | `/orgs/(?P<id>\d+)` | `OrgsAjaxAPI::getOrg` | AUSENTE | STAFF; ALVO-AUSENTE | 500 após `access()` |
| 138 | POST | `/orgs/(?P<id>\d+)` | `OrgsAjaxAPI::updateOrg` | `ajax.orgs.php:85` | STAFF+ORG_EDIT | organização; status |
| 139 | POST | `/orgs/(?P<id>\d+)/profile` | `OrgsAjaxAPI::updateOrg` | `ajax.orgs.php:85` | STAFF+ORG_EDIT | perfil org; status |
| 140 | ANY | `/orgs/(?P<id>\d+)/tickets/export` | `OrgsAjaxAPI::exportTickets` | `ajax.orgs.php:337` | STAFF | tickets org; export |
| 141 | GET | `/orgs/(?P<id>\d+)/edit` | `OrgsAjaxAPI::editOrg` | `ajax.orgs.php:65` | STAFF+ORG_EDIT | organização; HTML |
| 142 | GET | `/orgs/lookup/form` | `OrgsAjaxAPI::lookup` | `ajax.orgs.php:248` | STAFF | organização; HTML |
| 143 | POST | `/orgs/lookup` | `OrgsAjaxAPI::lookup` | `ajax.orgs.php:248` | STAFF | organização; JSON |
| 144 | GET | `/orgs/add` | `OrgsAjaxAPI::addOrg` | `ajax.orgs.php:226` | STAFF+ORG_CREATE | organização; HTML |
| 145 | POST | `/orgs/add` | `OrgsAjaxAPI::addOrg` | `ajax.orgs.php:226` | STAFF+ORG_CREATE | organização; status |
| 146 | GET | `/orgs/select` | `OrgsAjaxAPI::selectOrg` | `ajax.orgs.php:252` | STAFF | organização; HTML |
| 147 | GET | `/orgs/select/(?P<id>\d+)` | `OrgsAjaxAPI::selectOrg` | `ajax.orgs.php:252` | STAFF | organização; HTML |
| 148 | GET | `/orgs/(?P<id>\d+)/add-user(?:/(?P<userid>\d+))?` | `OrgsAjaxAPI::addUser` | `ajax.orgs.php:136` | STAFF+USER_EDIT | membro; HTML |
| 149 | GET | `/orgs/(?P<id>\d+)/add-user(?:/auth:(?P<userid>.+))?` | `OrgsAjaxAPI::addUser` | `ajax.orgs.php:136` | STAFF+USER_EDIT | membro remoto; HTML |
| 150 | POST | `/orgs/(?P<id>\d+)/add-user` | `OrgsAjaxAPI::addUser` | `ajax.orgs.php:136` | STAFF+USER_EDIT | membro; status |
| 151 | ANY | `/orgs/(?P<id>\d+)/import-users` | `OrgsAjaxAPI::importUsers` | `ajax.orgs.php:197` | STAFF+ORG_CREATE | membros; HTML/status |
| 152 | GET | `/orgs/(?P<id>\d+)/delete` | `OrgsAjaxAPI::delete` | `ajax.orgs.php:115` | STAFF+ORG_DELETE | organização; HTML |
| 153 | DELETE | `/orgs/(?P<id>\d+)/delete` | `OrgsAjaxAPI::delete` | `ajax.orgs.php:115` | STAFF+ORG_DELETE | organização; status |
| 154 | POST | `/orgs/(?P<id>\d+)/note` | `OrgsAjaxAPI::createNote` | `ajax.orgs.php:266` | STAFF | nota org; status |
| 155 | GET | `/orgs/(?P<id>\d+)/forms/manage` | `OrgsAjaxAPI::manageForms` | `ajax.orgs.php:292` | STAFF | forms org; HTML |
| 156 | POST | `/orgs/(?P<id>\d+)/forms/manage` | `OrgsAjaxAPI::updateForms` | `ajax.orgs.php:298` | STAFF+ORG_EDIT | forms org; status |
| 159 | POST | `/lock/ticket/(?P<tid>\d+)` | `TicketsAjaxAPI::acquireLock` | `ajax.tickets.php:195` | STAFF+acesso-ticket | lock; JSON/status |
| 160 | POST | `/lock/(?P<id>\d+)/ticket/(?P<tid>\d+)/renew` | `TicketsAjaxAPI::renewLock` | `ajax.tickets.php:231` | STAFF+dono-lock | lock; JSON/status |
| 161 | POST | `/lock/(?P<id>\d+)/release` | `TicketsAjaxAPI::releaseLock` | `ajax.tickets.php:268` | STAFF+dono-lock | lock; status |
| 164 | GET | `/tickets/(?P<tid>\d+)/change-user` | `TicketsAjaxAPI::changeUserForm` | `ajax.tickets.php:351` | STAFF+acesso-ticket | usuário ticket; HTML |
| 165 | POST | `/tickets/(?P<tid>\d+)/change-user` | `TicketsAjaxAPI::changeUser` | AUSENTE | STAFF; ALVO-AUSENTE | 500 após `access()` |
| 166 | GET | `/tickets/(?P<tid>\d+)/user` | `TicketsAjaxAPI::viewUser` | `ajax.tickets.php:297` | STAFF+acesso-ticket | usuário; HTML |
| 167 | POST | `/tickets/(?P<tid>\d+)/user` | `TicketsAjaxAPI::updateUser` | `ajax.tickets.php:323` | STAFF+acesso-ticket | usuário; status |
| 168 | GET | `/tickets/(?P<tid>\d+)/preview` | `TicketsAjaxAPI::previewTicket` | `ajax.tickets.php:287` | STAFF+acesso-ticket | ticket; HTML |
| 169 | GET | `/tickets/(?P<tid>\d+)/forms/manage` | `TicketsAjaxAPI::manageForms` | `ajax.tickets.php:380` | STAFF+TICKET_EDIT | forms ticket; HTML |
| 170 | POST | `/tickets/(?P<tid>\d+)/forms/manage` | `TicketsAjaxAPI::updateForms` | `ajax.tickets.php:395` | STAFF+TICKET_EDIT | forms ticket; status |
| 171 | GET | `/tickets/(?P<tid>\d+)/merge` | `TicketsAjaxAPI::mergeTickets` | `ajax.tickets.php:437` | STAFF+TICKET_EDIT | merge; HTML |
| 172 | POST | `/tickets/(?P<tid>\d+)/merge` | `TicketsAjaxAPI::updateMerge` | `ajax.tickets.php:466` | STAFF+MERGE | merge; status |
| 173 | GET | `/tickets/(?P<tid>\d+)/link` | `TicketsAjaxAPI::mergeTickets` | `ajax.tickets.php:437` | STAFF+TICKET_EDIT | link; HTML |
| 174 | POST | `/tickets/(?P<tid>\d+)/link` | `TicketsAjaxAPI::updateMerge` | `ajax.tickets.php:466` | STAFF+LINK | link; status |
| 175 | GET | `/tickets/(?P<tid>\d+)/merge/preview` | `TicketsAjaxAPI::previewMerge` | `ajax.tickets.php:505` | STAFF+acesso-ticket | merge; HTML |
| 176 | GET | `/tickets/(?P<tid>\d+)/relations` | `TicketsAjaxAPI::relations` | `ajax.tickets.php:1823` | STAFF+acesso-ticket | relações; HTML |
| 177 | GET | `/tickets/(?P<tid>\d+)/canned-resp/(?P<cid>\w+).(?P<format>json\|txt)` | `TicketsAjaxAPI::cannedResponse` | `ajax.tickets.php:520` | STAFF+acesso-ticket | resposta pronta; JSON/texto |
| 178 | GET | `/tickets/(?P<tid>\d+)/status/(?P<status>\w+)(?:/(?P<sid>\d+))?` | `TicketsAjaxAPI::changeTicketStatus` | `ajax.tickets.php:1359` | STAFF+role-perm | status; HTML |
| 179 | POST | `/tickets/(?P<tid>\d+)/status` | `TicketsAjaxAPI::setTicketStatus` | `ajax.tickets.php:1404` | STAFF+role-perm | status; JSON/status |
| 180 | ANY | `/tickets/(?P<tid>\d+)/thread/(?P<thread_id>\d+)/(?P<action>\w+)` | `TicketsAjaxAPI::triggerThreadAction` | `ajax.tickets.php:1706` | STAFF+acesso-ticket | thread; status |
| 181 | GET | `/tickets/status/(?P<status>\w+)(?:/(?P<sid>\d+))?` | `TicketsAjaxAPI::changeSelectedTicketsStatus` | `ajax.tickets.php:1505` | STAFF+role-perm | tickets; HTML |
| 182 | POST | `/tickets/status/(?P<state>\w+)` | `TicketsAjaxAPI::setSelectedTicketsStatus` | `ajax.tickets.php:1539` | STAFF+manage/role | tickets; status |
| 183 | GET | `/tickets/(?P<tid>\d+)/tasks` | `TicketsAjaxAPI::tasks` | `ajax.tickets.php:1813` | STAFF+acesso-ticket | tarefas; HTML |
| 184 | ANY | `/tickets/(?P<tid>\d+)/add-task` | `TicketsAjaxAPI::addTask` | `ajax.tickets.php:1833` | STAFF+TASK_CREATE | tarefa; HTML/status |
| 185 | GET | `/tickets/(?P<tid>\d+)/tasks/(?P<id>\d+)/view` | `TicketsAjaxAPI::task` | `ajax.tickets.php:1936` | STAFF+acesso-task | tarefa; HTML |
| 186 | POST | `/tickets/(?P<tid>\d+)/tasks/(?P<id>\d+)` | `TicketsAjaxAPI::task` | `ajax.tickets.php:1936` | STAFF+acesso-task | tarefa; status |
| 187 | GET | `/tickets/lookup` | `TicketsAjaxAPI::lookup` | `ajax.tickets.php:26` | STAFF | ticket; JSON |
| 188 | GET | `/tickets/number-lookup` | `TicketsAjaxAPI::lookupByNumber` | `ajax.tickets.php:99` | STAFF | ticket; JSON |
| 189 | ANY | `/tickets/mass/(?P<action>\w+)(?:/(?P<what>\w+))?` | `TicketsAjaxAPI::massProcess` | `ajax.tickets.php:981` | STAFF+perm-por-ação | tickets; HTML/status |
| 190 | ANY | `/tickets/(?P<tid>\d+)/transfer` | `TicketsAjaxAPI::transfer` | `ajax.tickets.php:559` | STAFF+TICKET_TRANSFER | ticket; HTML/status |
| 191 | ANY | `/tickets/(?P<tid>\d+)/field/(?P<fid>\d+)/edit` | `TicketsAjaxAPI::editField` | `ajax.tickets.php:682` | STAFF+TICKET_EDIT | campo; HTML/status |
| 192 | ANY | `/tickets/(?P<tid>\d+)/field/(?P<field>\w+)/edit` | `TicketsAjaxAPI::editField` | `ajax.tickets.php:682` | STAFF+TICKET_EDIT | campo; HTML/status |
| 193 | ANY | `/tickets/(?P<tid>\d+)/field/(?P<fid>\d+)/view` | `TicketsAjaxAPI::viewField` | `ajax.tickets.php:775` | STAFF+acesso-ticket | campo; HTML |
| 194 | ANY | `/tickets/(?P<tid>\d+)/field/(?P<field>\w+)/view` | `TicketsAjaxAPI::viewField` | `ajax.tickets.php:775` | STAFF+acesso-ticket | campo; HTML |
| 195 | ANY | `/tickets/(?P<tid>\d+)/assign(?:/(?P<to>\w+))?` | `TicketsAjaxAPI::assign` | `ajax.tickets.php:801` | STAFF+TICKET_ASSIGN | ticket; HTML/status |
| 196 | ANY | `/tickets/(?P<tid>\d+)/release` | `TicketsAjaxAPI::release` | `ajax.tickets.php:922` | STAFF+TICKET_RELEASE/manager | ticket; status |
| 197 | ANY | `/tickets/(?P<tid>\d+)/mark/(?P<action>\w+)` | `TicketsAjaxAPI::markAs` | `ajax.tickets.php:1642` | STAFF+MARKANSWERED/manager | ticket; status |
| 198 | ANY | `/tickets/(?P<tid>\d+)/refer(?:/(?P<to>\w+))?` | `TicketsAjaxAPI::refer` | `ajax.tickets.php:604` | STAFF+TICKET_ASSIGN | referência; HTML/status |
| 199 | ANY | `/tickets/(?P<tid>\d+)/referrals` | `TicketsAjaxAPI::referrals` | `ajax.tickets.php:600` | STAFF+acesso-ticket | referências; HTML |
| 200 | ANY | `/tickets/(?P<tid>\d+)/claim` | `TicketsAjaxAPI::claim` | `ajax.tickets.php:862` | STAFF+TICKET_ASSIGN | ticket; status |
| 201 | ANY | `/tickets/export/(?P<id>\d+)` | `TicketsAjaxAPI::export` | `ajax.tickets.php:2005` | STAFF+fila-acessível | tickets; export |
| 202 | ANY | `/tickets/export/adhoc,(?P<key>[\w=/+]+)` | `TicketsAjaxAPI::export` | `ajax.tickets.php:2005` | STAFF+fila-acessível | tickets; export |
| 204 | GET | `/tickets/search` | `SearchAjaxAPI::getAdvancedSearchDialog` | `ajax.search.php:25` | STAFF | busca; HTML |
| 205 | POST | `/tickets/search` | `SearchAjaxAPI::doSearch` | `ajax.search.php:87` | STAFF | busca; JSON/status |
| 206 | GET | `/tickets/search/(?P<id>\d+)` | `SearchAjaxAPI::editSearch` | `ajax.search.php:46` | STAFF+dono-busca | busca; HTML |
| 207 | GET | `/tickets/search/adhoc,(?P<key>[\w=/+]+)` | `SearchAjaxAPI::getAdvancedSearchDialog` | `ajax.search.php:25` | STAFF | busca; HTML |
| 208 | GET | `/tickets/search/create` | `SearchAjaxAPI::createSearch` | `ajax.search.php:160` | STAFF | busca; HTML |
| 209 | POST | `/tickets/search/(?P<id>\d+)/save` | `SearchAjaxAPI::saveSearch` | `ajax.search.php:176` | STAFF+dono-busca | busca; status |
| 210 | POST | `/tickets/search/save` | `SearchAjaxAPI::saveSearch` | `ajax.search.php:176` | STAFF | busca; status |
| 211 | DELETE | `/tickets/search/(?P<id>\d+)` | `SearchAjaxAPI::deleteSearch` | AUSENTE | STAFF; ALVO-AUSENTE | 500 após `access()` |
| 212 | GET | `/tickets/search/field/(?P<id>[\w_!:]+)` | `SearchAjaxAPI::addField` | `ajax.search.php:58` | STAFF | campo busca; HTML |
| 213 | ANY | `/tickets/search/column/edit/(?P<id>\d+)` | `SearchAjaxAPI::editColumn` | `ajax.search.php:236` | STAFF | coluna; HTML/status |
| 214 | ANY | `/tickets/search/sort/edit/(?P<id>\d+)` | `SearchAjaxAPI::editSort` | `ajax.search.php:259` | STAFF | ordenação; HTML/status |
| 215 | POST | `/tickets/search(?P<id>\d+)/delete` | `SearchAjaxAPI::deleteQueues` | AUSENTE | STAFF; ALVO-AUSENTE | 500 após `access()` |
| 216 | POST | `/tickets/search(?P<id>\d+)/disable` | `SearchAjaxAPI::disableQueues` | AUSENTE | STAFF; ALVO-AUSENTE | 500 após `access()` |
| 217 | POST | `/tickets/search(?P<id>\d+)/enable` | `SearchAjaxAPI::undisableQueues` | AUSENTE | STAFF; ALVO-AUSENTE | 500 após `access()` |
| 221 | GET | `/tasks/(?P<tid>\d+)/preview` | `TasksAjaxAPI::preview` | `ajax.tasks.php:181` | STAFF+acesso-task | tarefa; HTML |
| 222 | GET | `/tasks/(?P<tid>\d+)/edit` | `TasksAjaxAPI::edit` | `ajax.tasks.php:192` | STAFF+TASK_EDIT | tarefa; HTML |
| 223 | POST | `/tasks/(?P<tid>\d+)/edit` | `TasksAjaxAPI::edit` | `ajax.tasks.php:192` | STAFF+TASK_EDIT | tarefa; status |
| 224 | ANY | `/tasks/(?P<tid>\d+)/field/(?P<fid>\d+)/edit` | `TasksAjaxAPI::editField` | `ajax.tasks.php:222` | STAFF+TASK_EDIT | campo; HTML/status |
| 225 | ANY | `/tasks/(?P<tid>\d+)/field/(?P<field>\w+)/edit` | `TasksAjaxAPI::editField` | `ajax.tasks.php:222` | STAFF+TASK_EDIT | campo; HTML/status |
| 226 | GET | `/tasks/(?P<tid>\d+)/transfer` | `TasksAjaxAPI::transfer` | `ajax.tasks.php:629` | STAFF+TASK_TRANSFER | tarefa; HTML |
| 227 | POST | `/tasks/(?P<tid>\d+)/transfer` | `TasksAjaxAPI::transfer` | `ajax.tasks.php:629` | STAFF+TASK_TRANSFER | tarefa; status |
| 228 | ANY | `/tasks/(?P<tid>\d+)/assign(?:/(?P<to>\w+))?` | `TasksAjaxAPI::assign` | `ajax.tasks.php:673` | STAFF+TASK_ASSIGN | tarefa; HTML/status |
| 229 | ANY | `/tasks/(?P<tid>\d+)/claim` | `TasksAjaxAPI::claim` | `ajax.tasks.php:722` | STAFF+TASK_ASSIGN | tarefa; status |
| 230 | GET | `/tasks/(?P<tid>\d+)/delete` | `TasksAjaxAPI::delete` | `ajax.tasks.php:780` | STAFF+TASK_DELETE | tarefa; HTML |
| 231 | POST | `/tasks/(?P<tid>\d+)/delete` | `TasksAjaxAPI::delete` | `ajax.tasks.php:780` | STAFF+TASK_DELETE | tarefa; status |
| 232 | ANY | `/tasks/(?P<tid>\d+)/close` | `TasksAjaxAPI::close` | `ajax.tasks.php:885` | STAFF+TASK_CLOSE | tarefa; status |
| 233 | ANY | `/tasks/(?P<tid>\d+)/reopen` | `TasksAjaxAPI::reopen` | `ajax.tasks.php:881` | STAFF+TASK_CREATE | tarefa; status |
| 234 | GET | `/tasks/(?P<tid>\d+)/view` | `TasksAjaxAPI::task` | `ajax.tasks.php:889` | STAFF+acesso-task | tarefa; HTML |
| 235 | POST | `/tasks/(?P<tid>\d+)` | `TasksAjaxAPI::task` | `ajax.tasks.php:889` | STAFF+acesso-task | tarefa; status |
| 236 | ANY | `/tasks/(?P<tid>\d+)/thread/(?P<thread_id>\d+)/(?P<action>\w+)` | `TasksAjaxAPI::triggerThreadAction` | `ajax.tasks.php:61` | STAFF+acesso-task | thread; status |
| 237 | ANY | `/tasks/add` | `TasksAjaxAPI::add` | `ajax.tasks.php:91` | STAFF+TASK_CREATE | tarefa; HTML/status |
| 238 | ANY | `/tasks/(?P<tid>\d+)/add` | `TasksAjaxAPI::add` | `ajax.tasks.php:91` | STAFF+TASK_CREATE | subtarefa; HTML/status |
| 239 | ANY | `/tasks/lookup` | `TasksAjaxAPI::lookup` | `ajax.tasks.php:26` | STAFF | tarefa; JSON |
| 240 | ANY | `/tasks/mass/(?P<action>\w+)(?:/(?P<what>\w+))?` | `TasksAjaxAPI::massProcess` | `ajax.tasks.php:298` | STAFF+perm-por-ação | tarefas; HTML/status |
| 243 | GET | `/thread/(?P<tid>\d+)/collaborators/(?P<manage>\d+)/preview` | `ThreadAjaxAPI::previewCollaborators` | `ajax.thread.php:176` | STAFF+acesso-thread | colaboradores; HTML |
| 244 | GET | `/thread/(?P<tid>\d+)/collaborators/(?P<manage>\d+)` | `ThreadAjaxAPI::showCollaborators` | `ajax.thread.php:162` | STAFF+acesso-thread | colaboradores; HTML |
| 245 | POST | `/thread/(?P<tid>\d+)/collaborators` | `ThreadAjaxAPI::updateCollaborators` | `ajax.thread.php:211` | STAFF+acesso-thread | colaboradores; HTML/status |
| 246 | GET | `/thread/(?P<tid>\d+)/add-collaborator/(?P<type>\w+)/(?P<uid>\d+)` | `ThreadAjaxAPI::addCollaborator` | `ajax.thread.php:81` | STAFF+acesso-thread | colaborador; HTML |
| 247 | GET | `/thread/(?P<tid>\d+)/add-collaborator/(?P<type>\w+)/auth:(?P<bk>[\w.]+):(?P<id>.+)` | `ThreadAjaxAPI::addRemoteCollaborator` | `ajax.thread.php:59` | STAFF+acesso-thread | colaborador remoto; HTML |
| 248 | ANY | `/thread/(?P<tid>\d+)/add-collaborator/(?P<type>\w+)` | `ThreadAjaxAPI::addCollaborator` | `ajax.thread.php:81` | STAFF+acesso-thread | colaborador; HTML/status |
| 249 | GET | `/thread/(?P<tid>\d+)/collaborators/(?P<cid>\d+)/view` | `ThreadAjaxAPI::viewCollaborator` | `ajax.thread.php:145` | STAFF+acesso-thread | colaborador; HTML |
| 250 | POST | `/thread/(?P<tid>\d+)/collaborators/(?P<cid>\d+)` | `ThreadAjaxAPI::updateCollaborator` | `ajax.thread.php:120` | STAFF+acesso-thread | colaborador; HTML/status |
| 253 | POST | `/draft/(?P<id>\d+)` | `DraftAjaxAPI::updateDraft` | `ajax.draft.php:284` | STAFF+dono-draft | draft; JSON/status |
| 254 | DELETE | `/draft/(?P<id>\d+)` | `DraftAjaxAPI::deleteDraft` | `ajax.draft.php:326` | STAFF+dono-draft | draft; status |
| 255 | POST | `/draft/(?P<id>\d+)/attach` | `DraftAjaxAPI::uploadInlineImage` | `ajax.draft.php:297` | STAFF+dono-draft | arquivo; JSON |
| 256 | POST | `/draft/(?P<namespace>[\w.]+)/attach` | `DraftAjaxAPI::uploadInlineImageEarly` | `ajax.draft.php:310` | STAFF+namespace | arquivo; JSON |
| 257 | GET | `/draft/(?P<namespace>[\w.]+)` | `DraftAjaxAPI::getDraft` | `ajax.draft.php:268` | STAFF+dono/namespace | draft; JSON |
| 258 | POST | `/draft/(?P<namespace>[\w.]+)` | `DraftAjaxAPI::createDraft` | `ajax.draft.php:255` | STAFF+namespace | draft; JSON/status |
| 259 | GET | `/draft/images/browse` | `DraftAjaxAPI::getFileList` | `ajax.draft.php:339` | STAFF; canAccess condiciona inclusão de imagens da thread | arquivos; JSON |
| 262 | ANY | `/export/(?P<id>\w+)/check` | `ExportAjaxAPI::check` | `ajax.export.php:6` | STAFF+sessão-export | export; HTML; POST JSON 200/201 |
| 265 | GET | `/note/(?P<id>\d+)` | `NoteAjaxAPI::getNote` | `ajax.note.php:9` | STAFF+dono/ADMIN | nota; HTML/205 |
| 266 | POST | `/note/(?P<id>\d+)` | `NoteAjaxAPI::updateNote` | `ajax.note.php:23` | STAFF+dono/ADMIN | nota; HTML/status |
| 267 | DELETE | `/note/(?P<id>\d+)` | `NoteAjaxAPI::deleteNote` | `ajax.note.php:43` | STAFF+dono/ADMIN | nota; status |
| 268 | POST | `/note/attach/(?P<ext_id>\w\d+)` | `NoteAjaxAPI::createNote` | `ajax.note.php:59` | STAFF; contexto O/U ou ADMIN | nota rápida; HTML/status |
| 271 | GET | `/sequence/(?P<id>\d+)` | `SequenceAjaxAPI::current` | `ajax.sequence.php:24` | STAFF | sequência; valor escalar formatado |
| 272 | GET | `/sequence/manage` | `SequenceAjaxAPI::manage` | `ajax.sequence.php:54` | STAFF | sequência; HTML |
| 273 | POST | `/sequence/manage` | `SequenceAjaxAPI::manage` | `ajax.sequence.php:54` | STAFF | sequência; status |
| 275 | POST | `/upgrader` | `UpgraderAjaxAPI::upgrade` | `ajax.upgrader.php:22` | STAFF+ADMIN | upgrade; texto/status |
| 277 | GET | `/help/tips/(?P<namespace>[\w_.]+)` | `HelpTipAjaxAPI::getTipsJson` | `ajax.tips.php:23` | STAFF | ajuda; JSON |
| 278 | GET | `/help/(?P<lang>[\w_]+)?/tips/(?P<namespace>[\w_.]+)` | `HelpTipAjaxAPI::getTipsJsonForLang` | `ajax.tips.php:48` | STAFF | ajuda; JSON |
| 281 | GET | `/i18n/langs/all` | `i18nAjaxAPI::getConfiguredLanguages` | `ajax.i18n.php:104` | STAFF | idiomas; JSON |
| 282 | GET | `/i18n/langs` | `i18nAjaxAPI::getSecondaryLanguages` | `ajax.i18n.php:132` | STAFF | idiomas; JSON |
| 283 | GET | `/i18n/translate/(?P<tag>\w+)` | `i18nAjaxAPI::getTranslations` | `ajax.i18n.php:41` | STAFF | traduções; JSON |
| 284 | POST | `/i18n/translate/(?P<tag>\w+)` | `i18nAjaxAPI::updateTranslations` | `ajax.i18n.php:56` | STAFF | traduções; status |
| 285 | GET | `/i18n/(?P<lang>[\w_]+)/(?P<tag>\w+)` | `i18nAjaxAPI::getLanguageFile` | `ajax.i18n.php:21` | STAFF | idioma; arquivo |
| 289 | ANY | `/admin/quick-add/department` | `AdminAjaxAPI::addDepartment` | `ajax.admin.php:22` | STAFF+ADMIN | departamento; HTML/status |
| 290 | ANY | `/admin/quick-add/team` | `AdminAjaxAPI::addTeam` | `ajax.admin.php:69` | STAFF+ADMIN | equipe; HTML/status |
| 291 | ANY | `/admin/quick-add/role` | `AdminAjaxAPI::addRole` | `ajax.admin.php:116` | STAFF+ADMIN | papel; HTML/status |
| 292 | ANY | `/admin/quick-add/staff` | `AdminAjaxAPI::addStaff` | `ajax.admin.php:160` | STAFF+ADMIN | agente; HTML/status |
| 293 | ANY | `/admin/quick-add/queue-column` | `AdminAjaxAPI::addQueueColumn` | `ajax.admin.php:194` | STAFF+ADMIN | coluna fila; HTML/status |
| 294 | ANY | `/admin/quick-add/queue-sort` | `AdminAjaxAPI::addQueueSort` | `ajax.admin.php:219` | STAFF+ADMIN | ordenação; HTML/status |
| 296 | GET | `/admin/role/(?P<id>\d+)/perms` | `AdminAjaxAPI::getRolePerms` | `ajax.admin.php:147` | STAFF+ADMIN | permissões; JSON |
| 299 | ANY | `/staff/(?P<id>\d+)/set-password` | `StaffAjaxAPI::setPassword` | `ajax.staff.php:21` | STAFF+ADMIN | senha agente; HTML/status |
| 300 | ANY | `/staff/(?P<id>\d+)/change-password` | `StaffAjaxAPI::changePassword` | `ajax.staff.php:74` | STAFF+próprio | senha; HTML/status |
| 301 | GET | `/staff/(?P<id>\d+)/perms` | `StaffAjaxAPI::getAgentPerms` | `ajax.staff.php:127` | STAFF+ADMIN | permissões; JSON |
| 302 | ANY | `/staff/reset-permissions` | `StaffAjaxAPI::resetPermissions` | `ajax.staff.php:140` | STAFF+ADMIN | permissões; status |
| 303 | ANY | `/staff/change-department` | `StaffAjaxAPI::changeDepartment` | `ajax.staff.php:171` | STAFF+ADMIN | departamento; status |
| 304 | ANY | `/staff/(?P<id>\d+)/avatar/change` | `StaffAjaxAPI::setAvatar` | `ajax.staff.php:213` | STAFF+próprio/ADMIN | avatar; JSON com img HTML e code |
| 305 | ANY | `/staff/(?P<id>\d+)/2fa/configure(?:/(?P<mfid>.+))?` | `StaffAjaxAPI::configure2FA` | `ajax.staff.php:236` | STAFF+próprio | 2FA; HTML/status |
| 306 | ANY | `/staff/(?P<id>\d+)/reset-2fa` | `StaffAjaxAPI::reset2fA` | `ajax.staff.php:309` | STAFF+ADMIN | 2FA; status |
| 309 | ANY | `/queue/(?P<id>\d+/)?preview` | `SearchAjaxAPI::previewQueue` | `ajax.search.php:328` | STAFF+existência se ID; sem checkAccess explícito no método | fila; HTML |
| 310 | GET | `/queue/(?P<id>\d+)` | `SearchAjaxAPI::getQueue` | `ajax.search.php:281` | STAFF+fila-acessível | fila; JSON |
| 311 | GET | `/queue/addColumn` | `SearchAjaxAPI::addColumn` | AUSENTE | STAFF; ALVO-AUSENTE | 500 após `access()` |
| 312 | GET | `/queue/condition/add` | `SearchAjaxAPI::addCondition` | `ajax.search.php:351` | STAFF | condição; HTML |
| 313 | GET | `/queue/condition/addProperty` | `SearchAjaxAPI::addConditionProperty` | `ajax.search.php:379` | STAFF | condição; HTML |
| 314 | GET | `/queue/counts` | `SearchAjaxAPI::collectQueueCounts` | `ajax.search.php:394` | STAFF | contagens; JSON |
| 315 | ANY | `/queue/(?P<id>\d+)/delete` | `SearchAjaxAPI::deleteQueue` | `ajax.search.php:296` | STAFF+dono-fila | fila; HTML/status |
| 318 | POST | `/email/(?P<id>\d+)/stash` | `EmailAjaxAPI::stashFormData` | `ajax.email.php:11` | STAFF+ADMIN | filtra POST em sessão `:email`; sem DB; 201 |
| 319 | POST | `/email/(?P<id>\d+)/auth/config/(?P<type>\w+)/delete` | `EmailAjaxAPI::deleteToken` | `ajax.email.php:46` | STAFF+ADMIN | exclui config `email.*.account.*`; 201 se remove |
| 320 | ANY | `/email/(?P<id>\d+)/auth/config/(?P<type>\w+)/(?P<auth>.+)` | `EmailAjaxAPI::configureAuth` | `ajax.email.php:18` | STAFF+ADMIN | GET/POST inválido: HTML; POST basic: config/account + 201 texto; OAuth2: config/account + 201 JSON redirect quando autoriza |

Contagem da matriz: 229 linhas; 101 GET, 60 POST, 5 DELETE, 63 ANY; 13 definições ausentes.

Releitura estática de 2026-09-05: formatos/guardas de configuração, formulários,
colaboradores, imagens, notas, exportação, sequências, permissões e avatar foram
precisados. A prévia de fila não tem checkAccess explícito no método. Nenhuma
rota foi executada novamente. O [mapa SCP](SCP_FLOW_MAP.md) relaciona estas
declarações aos fluxos tradicionais e dependências de extração.

## Confirmação funcional de respostas prontas — Onda 7

Uma resposta pronta ativa e global foi criada pelo formulário administrativo.
As rotas 39 e 177 foram exercitadas em `.json` e `.txt`: todas retornaram `200`
e o conteúdo esperado. As respostas JSON possuíam `files`, `id`, `response` e
`title`; os contratos `.txt` devolveram HTML para inserção no editor. A rota
contextual do ticket aplicou o mesmo conteúdo à fixture acessível sem persistir
uma resposta.

## Confirmação funcional de atribuição — Onda 7

As rotas 195 e 196 foram exercitadas pelo administrador em um ciclo reversível:
liberar, atribuir ao agente e reatribuir ao administrador. Cada POST respondeu
`201`; a visibilidade do agente `assigned_only` acompanhou a atribuição e o banco
registrou os eventos correspondentes. O estado final restaurou o administrador
como agente do ticket e manteve a equipe vazia.
