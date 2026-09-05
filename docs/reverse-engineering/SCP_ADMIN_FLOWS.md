# SCP — Fluxos administrativos

[Mapa principal](SCP_FLOW_MAP.md). Baseline `v1.18.4`, entrada `33d40fb7`,
releitura em 2026-09-05. **Fato observado:** mapa estático, sem novas execuções.
`ADM` indica `scp/staff.inc.php` → `scp/admin.inc.php`, condições T01/T02 do
[mapa transversal](SCP_TRANSVERSAL_FLOWS.md). AJAX STAFF não implica ADM.
Classes/AJAX sem prefixo ficam em `include/`; templates em `include/staff/`.
As famílias são organizadas por finalidade humana, não módulos já escolhidos.

## Configurações

| ID | Entrada e guardas | Mecanismo e efeitos originais | Apresentação/dependências |
| --- | --- | --- | --- |
| A01 | `scp/admin.php`; ADM | Location settings.php e require da página | Redirecionamento, sem comando próprio |
| A02 | `scp/settings.php?t=system`; ADM | `OsticketConfig::updateSettings/updateSystemSettings` (`class.config.php:1228,1265`) → `Config::updateAll`, CONFIG_TABLE; helpdesk/departamento/ACL/HTTPS/logs/idioma/fuso/armazenamento | settings-system; referências/validação, inclusive proteção contra bloqueio do IP administrativo |
| A03 | `settings.php?t=tickets#settings`; ADM | `updateTicketsSettings` (`class.config.php:1410`); numeração/status/prioridade/SLA/tópico/limite/lock/colaboradores/fila | settings-tickets; referências válidas, GD se CAPTCHA; anexo via field-config, sequências e filas separados |
| A04 | Mesmo POST tickets, abas autoresp/alerts; ADM | updateTicketsSettings chama updateAutoresponderSettings/updateAlertsSettings antes da validação agregada; destinatários de alertas | settings-autoresp/settings-alerts; abas não são comandos HTTP independentes |
| A05 | Mesmo POST tickets, queues/ordenação; ADM | CustomQueue::queues → lookup → sort/save; QUEUE_TABLE e config de fila padrão | queues-ticket; ordenar e escolher padrão distinto de editar fila |
| A06 | `settings.php?t=tasks`; settings/alerts, ADM | updateTasksSettings (`class.config.php:1476`); numeração/prioridade/políticas/destinatários | settings-tasks; anexo via modal separado |
| A07 | `settings.php?t=agents`; ADM | updateAgentsSettings (`class.config.php:1354`); nomes/acesso/senha/sessão/MFA | settings-agents; modelos textuais passam por ContentAjaxAPI |
| A08 | `settings.php?t=users`; ADM | updateUsersSettings (`class.config.php:1384`); registro/login/senha/sessão/verificação | settings-users; páginas traduzidas de registro/login/recuperação separadas |
| A09 | `settings.php?t=pages`; ADM, companhia/IDs/uploads válidos | updatePagesSettings (`class.config.php:1619`), AttachmentFile::uploadLogo/uploadBackdrop, company getForm/save, remove imagens selecionadas, updateAll | settings-pages; config + formulário da companhia + arquivos, $_POST/$_FILES |
| A10 | `settings.php?t=kb`; ADM | updateKBSettings: enable_kb/restrict_kb/enable_premade | settings-kb; exposição KB/respostas prontas |

## Gerenciar — tópicos, filtros, SLA e agendas

| ID | Entrada e guardas | Mecanismo e efeitos originais | Apresentação/dependências |
| --- | --- | --- | --- |
| A36 | `scp/helptopics.php`; create/update, ADM | Topic::create/update (`class.topic.php:425`), updateForms (:552); TOPIC/TOPIC_FORM | helptopic(s); hierarquia/roteamento/SLA/agente/equipe/forms/FAQ |
| A37 | helptopics massa enable/disable/archive/delete/sort; ADM | Protege padrão e mínimo ativo conforme ramo; flags/FilterAction::setFilterFlags/sinais; Topic::delete limpa filhos/FAQ/tópico de tickets; setTopicSortMode/setSortOrder | Invariantes de lote no controlador; config/filtros/tickets/FAQ |
| A22 | `scp/filters.php` add/update; ADM; AJAX STAFF | Filter::update (`class.filter.php:391`), save_rules/save_actions; FilterAction::lookupByType/getConfigurationForm (`ajax.filter.php:7`) | filter(s), HTML de ação dinâmica; banlist redireciona, registry de ações e referências |
| A23 | filters massa enable/disable/delete; ADM | ORM isactive/save; Filter::delete (`class.filter.php:464`), protege sistema | Resultados parciais; avisos de referência inativa no controlador |
| A38 | `scp/slas.php` add/update; ADM | SLA::create/update (`class.sla.php:155`); SLA_TABLE, agenda/flags | sla(s); prazo depende do calendário |
| A39 | slas massa enable/disable/delete; ADM, padrão protegido | ORM flags; SLA::delete (`class.sla.php:222`) limpa depts/tópicos e substitui SLA em tickets | Lote combina ORM/SQL na cadeia original |
| A40 | `scp/schedules.php` lista/ID/update/massa delete; ADM | Schedule::update/delete (`class.schedule.php:279,332`), remove entradas; SCHEDULE/SCHEDULE_ENTRY | schedule(s); proteção de agenda em uso permanece TODO apesar da mensagem da página |
| A41 | AJAX schedule add/clone/entry add/update/delete-entries/diagnostic; STAFF | ScheduleAjaxAPI (`ajax.schedule.php:6,49,53,77,100,113`), basicForm/create/save/cloneEntries/addEntry, ScheduleEntry::update; vínculo agenda/entrada; BusinessHoursSchedule | schedule-add/entry/diagnostic; HTML/201/JSON; tipo e timezone |

## Gerenciar — API, páginas, formulários, listas e plugins

| ID | Entrada e guardas | Mecanismo e efeitos originais | Apresentação/dependências |
| --- | --- | --- | --- |
| A18 | `scp/apikeys.php` lista/ID/add/update; ADM | API::add/update/save (`class.api.php:80,124`), API_KEY_TABLE | apikey(s); IP/capacidades/credencial com exposição restrita |
| A19 | apikeys massa enable/disable/delete; ADM, seleção | Ativação SQL no controller; API::lookup/delete | Contagens/validação de lote não são serviço único |
| A47 | `scp/pages.php` add/update/massa enable/disable/delete; ADM | Page::create/update (`class.page.php:242`), traduções, disable/delete; protege padrão/em uso; limpa drafts | page(s), editor rico/idiomas/defaults; PAGE/traduções/drafts |
| A48 | AJAX manageContent/manageNamedContent/updateContent; STAFF+ADM | ContentAjaxAPI (`ajax.content.php:147,173,187`), Page::lookup/lookupByType/update; banner-staff admite vazio | HTML multilíngue/201; divergência visual herdada da Página de Login não foi resolvida |
| A42 | `scp/forms.php` add/update/campos/exclusão de dados; ADM | DynamicForm create/set/save; DynamicFormField create/setRequirementMode/isValid/save/delete; opcional DynamicFormEntryAnswer delete | dynamic-form(s); validações/nome/propriedades/deletabilidade no controller; gravações/deletes antes do resultado final |
| A43 | forms massa delete; AJAX field-config/fields-view | ADM tradicional, STAFF AJAX; DynamicForm::delete; getFieldConfiguration/saveFieldConfiguration (`ajax.forms.php:54,59`), preserva flags, setConfiguration/save; getAllFields | dynamic-field-config/fields-view; config 201/HTML, consulta fields JSON com HTML; ver T19 |
| A56 | AJAX DELETE form/answer/{entry}/{field}; STAFF | DynamicFormsAjaxAPI::deleteAnswer (`ajax.forms.php:94`), lookup da resposta por entrada/campo → DynamicFormEntryAnswer::delete | Exclusão de resposta de formulário, distinta de campo/form inteiro; não inferir guarda ADM pela posição do menu |
| A44 | `scp/lists.php` ID/type/add/update/ordenação; ADM | CustomListHandler::forList; DynamicList::add/update/getForm(true), campos DynamicFormField, itens save; LIST/LIST_ITEM/forms | dynamic-list(s); tipos especiais, inclusive estados de ticket; validações no controller |
| A45 | lists import-items/massa delete/PJAX; ADM | DynamicList::importFromPost (`class.list.php:597`), upload ou texto; delete; list-items.tmpl.php | Importação distinta de consulta; redirect #items |
| A46 | AJAX itens listar/search/preview/add/update/import/enable/disable/delete; STAFF | DynamicFormsAjaxAPI (`ajax.forms.php:109–375`), getListItem/getListItems/searchListItems/previewListItem/saveListItem/addListItem/importListItems/disableItems/undisableItems/deleteItems; verifica lista/vínculo/seleção | HTML ou JSON com HTML; massManageListItems registrado mas ausente |
| A53 | `scp/plugins.php` lista/add/ID/install/update/massa enable/disable/delete; ADM | PluginManager::install (`class.plugin.php:370`), Plugin::update, ORM isactive, uninstall/pre_uninstall, cache; remove instâncias/config mas preserva arquivos | plugin(s)/plugin-add; compatibilidade/defunto, código em disco/registry/config separados |
| A54 | plugins add-instance/update-instance/instances-actions enable/disable/delete; ADM, vínculo/nome/form válidos | Plugin::addInstance (`class.plugin.php:813`), PluginInstance::update/delete (:1136,1167); flags/config; instância salva antes da atualização completa | plugin-instance; formulário depende do plugin; não prova todo runtime externo |
| A55 | AJAX plugin instances/list/add/update/actions; STAFF+ADM no access | PluginsAjaxAPI getInstances/addInstance/updateInstance (`ajax.plugins.php:28,34,47`), métodos de plugin/instância | plugin-instances/plugin-instance-modal; HTML/PJAX/JSON201 redirect; alvo actions ausente |

## E-mails

| ID | Entrada e guardas | Mecanismo e efeitos originais | Apresentação/dependências |
| --- | --- | --- | --- |
| A12 | `scp/emails.php` lista/ID/add/create/update; ADM, endereço/referências válidos | Email::create/update (`class.email.php:289`); EMAIL_TABLE, dept/prioridade/tópico/autoresposta/notas; criação sinaliza object.created no controller | emails/email#account; restoreErrors/restoreNotice recompõem estado |
| A13 | Mesmo POST emails, mailbox; ADM | Email::update → getMailBoxAccount/update → MailBoxAccount::configure/save (`class.email.php:1150,1246`); EMAIL_ACCOUNT; valida protocolo/host/porta/frequência/pastas/autenticação, pode conectar | email-mailbox; IMAP/POP, manter/arquivar/excluir após coleta, backend de autenticação |
| A14 | Mesmo POST emails, smtp; ADM | getSmtpAccount/update/configure/save (`class.email.php:1336,1389`); host/porta/auth/reuso OAuth/mailbox; pode validar conexão | email-smtp; reuso de credencial/spoofing; salvar pode produzir rede |
| A15 | AJAX email stash/auth config/delete; emails.php do=autho | EmailAjaxAPI::access ADM; stashFormData/configureAuth/deleteToken → sessão ou conta getAuthConfigForm/saveAuth/destroyConfig (`ajax.email.php:4,11,18,46`); OAuth2AuthorizationBackend getBackend/triggerEmailAuth | Conta/provedor existentes; basic/OAuth, 201 ou JSON redirect; salvar principal e coordenar modal/retorno são etapas diferentes |
| A16 | emails massa delete; ADM, padrão/alertas protegidos | Email::delete (`class.email.php:249`) remove contas/config, substitui remetente departamental/limpa autoresposta, sinal | Contagem parcial; efeitos além de EMAIL_TABLE |
| A11 | `scp/emailsettings.php`; ADM | updateSettings → updateEmailsSettings (`class.config.php:1552`); remetentes/templates/coleta/autocron/respostas/destinatários/MTA | settings-emails; configuração global distinta da conta |
| A20 | `scp/banlist.php` add/update; ADM | Banlist::getFilter/includes, Filter::addRule(email,equal), FilterRule::update; FILTER/FILTER_RULE | banlist/banrule; filtro de sistema, e-mail válido/não duplicado |
| A21 | banlist massa enable/disable/delete; ADM | SQL flags restrito a filter_id; exclusão confere getFilterId e FilterRule::delete | Banlist não é tabela independente; seleção/resultados no controller |
| A49 | `scp/templates.php` tpl_id/id/default_for/add/manage/implement; POST add/update/updatetpl/implement; ADM | EmailTemplateGroup::add/update, EmailTemplate::add/update (`class.template.php`); padrão/implementação de mensagem herdada, limpa drafts | templates/template/tpl; grupo vs mensagem, assunto/corpo/variáveis/ajuda |
| A50 | templates massa enable/disable/delete; ADM | SQL EMAIL_TEMPLATE_GRP, disable/delete, isInUse, sinais/contagens | Config/departamentos dependentes; preservar efeitos do controller |
| A17 | `scp/emailtest.php`; ADM, origem/destinatário/assunto/corpo válidos | Email::send com reply-tag false, corpo sanitizado; sucesso remove Draft namespace email.diag | Form inline; envio real distinto de confirmação de entrega |

## Agentes, equipes, papéis e departamentos

| ID | Entrada e guardas | Mecanismo e efeitos originais | Apresentação/dependências |
| --- | --- | --- | --- |
| A27 | `scp/staff.php` lista/ID/add/create/update/export; ADM | Staff::create/update (`class.staff.php:1219`), updateAccess/updateTeams/export; sessão new-agent-passwd ou welcome email | staffmembers/staff; conta/acesso/perms/teams, STAFF/STAFF_DEPT/TEAM_MEMBER/config/e-mail |
| A28 | staff massa enable/disable/delete; ADM, protege próprio agente | ORM ativação; Staff::delete (`class.staff.php:935`) libera tickets, preserva autor em threads, remove memberships/acessos/sinal | Efeitos em tickets/thread além do cadastro; parcialidade |
| A29 | staff massa permissions/department; ADM, dept/papel válidos | updatePerms/save, setDepartmentId/role_id/save/dept_access saveAll; StaffAjaxAPI::getAgentPerms (`ajax.staff.php:127`) retorna getPermissionInfo JSON; resetPermissions/changeDepartment (`ajax.staff.php:140,171`) retornam dados para POST principal | 201 do modal não comprova aplicação ao lote de agentes |
| A30 | Senha/MFA de terceiro; STAFF+ADM | StaffAjaxAPI::setPassword/reset2fA (`ajax.staff.php:21,309`), checkPassword, sessão para novo agente ou sendResetEmail/setPassword/save | Modais/estado provisório; próprios senha/MFA em T12/T13 |
| A31 | `scp/teams.php` create/update; ADM | Team::create/update (`class.team.php:172`), updateMembers; TEAM/TEAM_MEMBER, sinal de criação | team(s); líder/membros/alertas/disponibilidade |
| A32 | teams massa enable/disable/delete; ADM | Flags ORM/SqlExpression; Team::delete (`class.team.php:278`) remove membros e limpa atribuição de tickets | Não inferir limpeza de tarefas pelo comportamento de tickets |
| A33 | `scp/roles.php` add/update; ADM; AJAX perms ADM | Role::create/update (`class.role.php:158`), catálogo de permissões; AdminAjaxAPI::getRolePerms/getPermissionInfo | role(s); domínio registra permissões, ROLE_TABLE |
| A34 | roles massa enable/disable/delete; ADM, isDeleteable | ORM flags; Role::delete (`class.role.php:191`) | Vínculos de uso impedem exclusão simples |
| A24 | `scp/departments.php` create/update/export; ADM | Dept::create/update/updateAccess (`class.dept.php:779`), export (:607); DEPT/STAFF_DEPT | department(s); árvore/membros/acesso estendido/gerente/SLA/e-mails/template/assinatura |
| A25 | departments massa make_public/enable/disable/archive; ADM, padrão protegido antes do switch | SQL público; flags/save/FilterAction::setFilterFlags/object.edited | Dependências de filtros também mudam |
| A26 | departments massa delete; ADM, padrão/agentes protegidos | Dept::delete (`class.dept.php:500`) move tickets/tarefas ao padrão, limpa referências em tópicos/e-mails/acesso/sinal | Contador incrementa sem testar retorno individual; mensagem não prova cada persistência |
| A35 | AJAX quick-add department/team/role/staff; STAFF+ADM por método | AdminAjaxAPI::addDepartment/addTeam/addRole/addStaff (`ajax.admin.php:22,69,116,160`); forms → create/update → 201 id/name | Defaults/form simplificado, não equivalência total ao editor completo |

## Filas e componentes reutilizados pela configuração

| ID | Entrada e guardas | Mecanismo e efeitos originais | Apresentação/dependências |
| --- | --- | --- | --- |
| A51 | `scp/queues.php` ID/clone/sub/create/update/massa enable/disable/delete; ADM, padrão protegido na exclusão | CustomQueue::create/update/save (`class.queue.php:1221`); flags/hierarquia/critérios/colunas/sort/export; GET copy ou filho prepara editor; massa redirect settings | queue.inc.php; QUEUE/colunas/config/sort/export; GET clone não comprova gravação |
| A52 | AJAX colunas/ordem/fila/condições; STAFF, quick-add ADM | AdminAjaxAPI::addQueueColumn/addQueueSort (`ajax.admin.php:194,219`), QueueColumn/QueueSort update/save; SearchAjaxAPI editColumn/editSort/getQueue/deleteQueue/previewQueue/addCondition/addConditionProperty | HTML/JSON/201; guardas distintas em T25–T27; addColumn ausente não é capacidade implementada |

## Implicações verificáveis

- **Fato observado:** settings escolhe tela por REQUEST t, mas updateSettings
  despacha pelo t do POST. Abas não delimitam a unidade de gravação.
- **Fato observado:** AJAX forms/listas/agenda/filtro usa STAFF onde a página
  exige ADM. O mapa registra guardas existentes, sem aceitar riscos ou contorná-las.
- **Fato observado:** regras de lote, padrões protegidos e sinais também estão
  no controlador. Não basta chamar save do objeto para reproduzir o fluxo.
- **Inferência:** forms, Email::update, updateTicketsSettings e addInstance
  exigem tratamento de resultado parcial; há efeitos antes do sucesso agregado.
  Não houve ensaio de falha nem prova de transação.
- **Herdado, não reexecutado:** a [matriz visual](INTERFACE_COVERAGE_MATRIX.md)
  mantém a sequência administrativa, coleta/SMTP, modais e os 19 editores de
  mensagens. Este mapa não comprova novamente esses testes, OAuth externo ou
  execução de código de plugins ausentes.

Persistência detalhada: [catálogo ORM](ORM_CATALOG.md), [configuração e dados](DATA_MODEL_MAP.md).
Detalhes de contrato, bootstrap, globais, sessão e efeitos externos seguem
pendentes de discussão dentro do ADR 0004, não autorização para copiar regras.
