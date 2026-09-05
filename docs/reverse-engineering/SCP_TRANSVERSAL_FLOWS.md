# SCP — Fluxos transversais

[Mapa principal](SCP_FLOW_MAP.md). **Fato observado:** releitura estática em
2026-09-05, baseline `v1.18.4`, entrada `33d40fb7`. Nenhuma execução nova.
Classes/AJAX sem prefixo ficam em `include/`; templates em `include/staff/`.
As dependências abaixo não são contratos ou módulos aprovados.

## Entrada, identidade e perfil

| ID | Entrada e guardas | Cadeia, efeitos e saída | Limite de extração |
| --- | --- | --- | --- |
| T01 | Páginas → `scp/staff.inc.php` | Validator::check_acl(staff), StaffAuthenticationBackend::getUser, isValid, refreshSession, checkCSRFToken; idioma/nav/avisos/login/upgrade/perfil obrigatório | STAFF neste mapa é cadeia condicional: atividade/offline/upgrade têm exceções administrativas; sessão com MFA pendente não é sessão plenamente autorizada |
| T02 | Administração → `scp/admin.inc.php` | STAFF + isAdmin; configuração/upgrade; AdminNav/avisos/redirecionamentos | Autorização independente de existência do menu |
| T03 | `scp/ajax.php` | STAFF, redefine staffLoginPage para403; Dispatcher::resolve/Controller::access; sinal ajax.scp; 229 folhas; status/HTML/JSON, detecção de string iniciando por { | Sem envelope uniforme; ANY não é só GET/POST, dispatcher interpreta _method; defaults de access permissivos |
| T04 | `scp/apps/dispatcher.php` | STAFF, prefixo admin carrega ADMIN; acesso direto recusado; apps.scp/apps.admin recebem dispatcher vazio | Rotas reais dependem de handlers; não inventariar plugin ausente como implementado |
| T05 | `scp/login.php`; inicial/submissão | Bootstrap público, CSRF POST, Validator::is_userid; StaffAuthenticationBackend::process → backends → authenticate/login; sessão/eventos/auditoria/MFA; HTML ou HTTP200 com status/message/show_reset/redirect em JSON | Status HTTP diferente de status interno; usar mecanismos originais, não copiar senha/políticas |
| T06 | login do=2fa | CSRF, identidade is2FAPending/backend; getInputForm/isValid/validate; ExpiredOTP faz logout; HTML/JSON | Estados intermediários, desafio e autenticação concluída separados |
| T07 | login externo/SSO | getBackend/triggerAuth/processSignOn; sessão/redirect/sinais | Protocolo/callback extensível; não prova de integração externa |
| T08 | `scp/pwreset.php` sendmail | CSRF, identificador/Staff::lookup/backend local; sendResetEmail, Config(pwreset), conteúdo, auth.pwreset.email/log/envio; HTML/espera mínima | Resposta não comprova entrega de e-mail |
| T09 | pwreset token/newpasswd | Config(pwreset); CSRF POST, StaffAuthenticationBackend::processSignOn; formulário/redirect | newpasswd não altera senha diretamente; continuidade via backend e troca obrigatória |
| T10 | `scp/logout.php` | STAFF, validateLinkToken; logOut/limpa sessão/cookie/Lock::removeStaffLocks; redirect login | Preservar logout backend/locks; controle de fluxo da guarda de link requer reconciliação, sem presumir proteção conclusiva |
| T11 | `scp/profile.php` consultar/atualizar | STAFF, ID próprio; Staff::updateProfile, Config(staff.id), idioma/salvamento; HTML erros/avisos | Contato/assinatura/preferências/identidade/MFA; sem garantia de atomicidade |
| T12 | StaffAjaxAPI::changePassword (`ajax.staff.php:74`) | STAFF/ID próprio/PasswordChangeForm/contexto de recuperação; setPassword/save/cancela tokens; HTML/201/JSON redirect | Senha não é campo genérico; respeitar backend/recuperação |
| T13 | StaffAjaxAPI::configure2FA (`ajax.staff.php:236`) | STAFF/identidade própria/Staff2FABackend; validate/verify, configuração/desafio/verificação; 2fas.tmpl.php | Contrato futuro deve representar estados; escolha padrão passa pelo perfil |
| T14 | StaffAjaxAPI::setAvatar (`ajax.staff.php:213`) | STAFF, próprio ou admin, avatar existente; getAvatar/toggle; JSON img HTML/code | ANY; seleção distinta de upload |
| T41 | `scp/logo.php`; logo/backdrop | main.inc com NOOP_SESSION, sem STAFF; getStaffLogo/getStaffLoginBackdrop/display(false,86400); imagem ou redirect asset, cache privado | Recurso anterior ao login; não proteger indistintamente com sessão staff |

## Editor, arquivos, formulários e locks

| ID | Entrada e guardas | Cadeia, efeitos e saída | Limite de extração |
| --- | --- | --- | --- |
| T15 | DraftAjaxAPI::createDraft/getDraft | STAFF, namespace; Draft::lookupByNamespaceAndStaff/create/save; JSON draft_id/body, ausência205 | Corpo viewableImages não é texto simples; namespace é contexto |
| T16 | updateDraft/deleteDraft | STAFF/draft/dono; _findDraftBody, setBody/delete; JSON/vazio/status | Nomes dinâmicos; erros variam entre criar/atualizar |
| T17 | uploadInlineImage/uploadInlineImageEarly | STAFF/dono, arquivo único/imagem segura/conteúdo/tamanho; cria draft antecipado/attachments upload; JSON CID/draft_id/URL | Arquivo temporário não equivale à publicação |
| T18 | DraftAjaxAPI::getFileList (`ajax.draft.php:339`) | STAFF, AttachmentFile e isInlineSafeImage; expansão thread exige canAccess; JSON thumb/url/title | Guarda por objeto condiciona expansão, não todos resultados; URL não substitui autorização de arquivo |
| T19 | DynamicFormsAjaxAPI::getFormsForHelpTopic/getAllFields (`ajax.forms.php:17,415`) | STAFF, tópico/form; primeiro exige Referer, filtra formas visíveis e altera sessão :form-data; JSON media/html ou success/html | JSON com render legado não satisfaz ADR 0004; metadados/validação abaixo do render precisam ser identificados |
| T20 | DynamicFormsAjaxAPI::upload/attach (`ajax.forms.php:378,392`) | STAFF, campo FileUploadField por ID, configuração ticket/task/thread; FileUploadField::ajaxUpload → JSON id | Upload temporário distinto de associação final; revisão própria das políticas herdadas |
| T21 | TicketsAjaxAPI::acquireLock/renewLock/releaseLock (`ajax.tickets.php:195,231,268`) | STAFF, modo/config, acesso aquisição/renovação, coerência/dono/expiração; Ticket::acquireLock, Lock::renew/release; JSON/escalares | Locks compartilhados entre painéis; renovação revalida acesso; retentativa não é idempotência universal |
| T28 | ContentAjaxAPI::context/ticket_variables/getSignature | STAFF, raiz/tipo/entidade por ação; VariableReplacer::getContextForRoot JSON, variáveis/assinatura HTML | Catálogo hardcoded e conteúdo contextual têm origens distintas |
| T32 | NoteAjaxAPI (`ajax.note.php`) | STAFF; ler/editar/excluir dono ou admin; criar contexto O/U ou admin; QuickNote sanitizado/ext_id/save/delete; HTML/status | Nota rápida diferente da thread; guardas de criação/manutenção diferentes |

Configuração de campo e exclusão de resposta estão em A43/A56 do
[mapa administrativo](SCP_ADMIN_FLOWS.md); não foram esquecidas nem duplicadas.

## Busca, filas e preferências

| ID | Entrada e guardas | Cadeia, efeitos e saída | Limite de extração |
| --- | --- | --- | --- |
| T22 | ConfigAjaxAPI::scp/templateLinks/dateFormat | STAFF, format obrigatório; JSON config/links, data em texto escapado | Idioma/paginação/editor/links acoplados ao SCP; não uniformemente JSON |
| T23 | SearchAjaxAPI::getAdvancedSearchDialog/addField/doSearch | STAFF/campos/form válido; AdhocSearch/critérios na sessão; HTML/JSON html+redirect tickets queue adhoc | Critérios válidos distintos de componentes HTML/destino |
| T24 | SearchAjaxAPI::createSearch/editSearch/saveSearch | STAFF; edição SavedSearch::checkAccess; update/save(true), contagens/recentes na sessão; HTML | Propriedade/acesso por ação; sessão+persistência não necessariamente atômicas |
| T25 | SearchAjaxAPI::getQueue/previewQueue/deleteQueue/collectQueueCounts | STAFF; get/delete checkAccess; preview existência se ID; CustomQueue::delete, SavedQueue::counts; JSON nome/critérios, HTML preview/confirmação | Não atribuir ao preview checkAccess explícito que não contém |
| T26 | SearchAjaxAPI::editColumn/editSort | STAFF/objeto/form; QueueColumn/QueueSort update/save; HTML/201 | Objeto compartilhado; sem guarda ADM adicional explícita nesse controller |
| T27 | SearchAjaxAPI::addCondition/addConditionProperty | STAFF/parâmetros/campos suportados; templates, sem save explícito | IDs de componentes não são necessariamente IDs de domínio |
| T33 | SequenceAjaxAPI::current/manage (`ajax.sequence.php`) | STAFF/validação; Sequence/RandomSequence; valor escalar ou gerência create/update/delete/save; HTML/205 | Não presumir JSON, ADM ou transação no lote |

## Colaboradores e ações de entradas

| ID | Entrada e guardas | Cadeia, efeitos e saída | Limite de extração |
| --- | --- | --- | --- |
| T42 | ThreadAjaxAPI::showCollaborators/previewCollaborators (`ajax.thread.php:162,176`) | STAFF, Thread::lookup/objeto/checkStaffPerm; HTML lista/prévia ou form vazio | manage visual não substitui acesso ao objeto |
| T43 | addCollaborator (`ajax.thread.php:81`) | STAFF/acesso/Threadable; GET form; POST usuário ou User::fromForm → objeto addCollaborator; HTML/erros | Pode criar usuário antes de associar; etapas distintas |
| T44 | addRemoteCollaborator (`ajax.thread.php:59`) | STAFF/acesso/backend/ID; StaffAuthenticationBackend::getBackend/lookup → UserForm/HTML | GET prepara seleção, não comprova vínculo; backend externo condicionado |
| T45 | viewCollaborator/updateCollaborator (`ajax.thread.php:145,120`) | STAFF/acesso, Collaborator lookup por ID+thread; GET perfil, POST User::updateInfo; HTML, inclusive erros405/406 | Altera usuário, não apenas vínculo |
| T46 | updateCollaborators (`ajax.thread.php:211`) | STAFF/acesso; Thread::updateCollaborators (`class.thread.php:203`) del/cid/flags/tempos/eventos/cache; HTML | Operação por participantes, não transação presumida |
| T47 | TicketsAjaxAPI/TasksAjaxAPI::triggerThreadAction (`ajax.tickets.php:1706`, `ajax.tasks.php:61`) | STAFF/acesso/entrada no objeto e tipo T/A; ThreadEntry::getActions/isVisible/triggerAction/isEnabled → handler | Registro extensível; nome da ação não basta para autorização |
| T48 | TEA_ShowEmailRecipients/TEA_ShowEmailHeaders (`class.thread_actions.php`) | T47, dados existentes; headers exige admin em isVisible; Mail_Parse e modais | Destinatários/cabeçalhos capacidades distintas, acesso deve ser preservado |
| T49 | TEA_EditThreadEntry (`class.thread_actions.php`) | T47; não sistêmica, não resposta R; autoria/gerência/ThreadEntry::PERM_EDIT conforme objeto; updateEntry/revisão/anexos/editor/flags/oculta original/substitui revisão; JSON201 com entry HTML | Editar não é só update do corpo; IDs/histórico/anexos mudam; sem prova append-only/atomicidade |
| T50 | TEA_OrigThreadEntry (`class.thread_actions.php`) | T47/FLAG_EDITED/pai; thread-entry-view ou404 | Relação revisão/entrada visível; HTML não é contrato |
| T51 | TEA_EditAndResendThreadEntry/TEA_ResendThreadEntry (`class.thread_actions.php`) | T47, resposta R de agente, handler por permissão; commit=resend, e-mail/colaboradores/assinatura/templates/anexos/destinatários, evento resent/sinal/FLAG_RESENT; 201 JSON HTML/texto | TEA_Resend isVisible exige !parent::isEnabled e isEnabled retorna true (:416–422); não presumir a permissão de editar para reenviar; resposta não prova entrega |
| T52 | TEA_CreateTicket/TEA_CreateTask (`class.thread_actions.php`) | T47/perm create; ticket redirect formulário open&tid; tarefa prepara :form-data/:uploadedFiles e delega addTask/add | Abrir formulário não cria ticket; conclusão em O05/O26/O27, notas cruzadas/contexto |

As oito folhas `/thread/` são colaboradores: linhas 243–244 → T42, 245 → T46,
246/248 → T43, 247 → T44, 249–250 → T45 em `scp/ajax.php`.
Edição/histórico/reenvio/derivação chegam pelas famílias Tickets/Tarefas,
não por uma rota presumida de ThreadAjaxAPI::triggerThreadAction.

## Idiomas e ajuda

| ID | Entrada e guardas | Cadeia, efeitos e saída | Limite de extração |
| --- | --- | --- | --- |
| T29 | i18nAjaxAPI::getConfiguredLanguages/getSecondaryLanguages/getTranslations/getLanguageFile | STAFF no SCP/chave suportada; CustomDataTranslation/Internationalization; JSON ou asset JS/cache | JS do editor legado não é catálogo Angular independente |
| T30 | i18nAjaxAPI::updateTranslations (`ajax.i18n.php:56`) | STAFF, POST/idiomas/regras; cria/altera/remove CustomDataTranslation/save por item/status | Sem transação ou ADM adicional explícitos no método |
| T31 | HelpTipAjaxAPI::getTipsJson/getTipsJsonForLang (`ajax.tips.php`) | STAFF/template; YAML/variáveis/links → JSON ou404 | getTipsJson substitui idioma por corrente; rota não prova seleção efetiva |

## Exportação, diagnóstico e manutenção

| ID | Entrada e guardas | Cadeia, efeitos e saída | Limite de extração |
| --- | --- | --- | --- |
| T34 | ExportAjaxAPI::check (`ajax.export.php:6`) | STAFF, Exporter::load via sessão/arquivo; modal ou POST notready200/ready201 href/filename | Prontidão/lock dependem de sessão/filesystem |
| T35 | `scp/export.php` | STAFF, Exporter::load/isReady;404/416 ou download application/octet-stream; remove arquivo por padrão | Download consome recurso; repetição não garantida; não é submissão |
| T36 | `scp/autocron.php` | STAFF/intervalo sessão/sem upgrade; envia GIF201 antes de contagens/Cron::TicketMonitor/limpeza eventual/MailFetcher condicionado/sinal cron | Resposta anterior aos efeitos; coleta depende de config; não importar agendamento implicitamente |
| T37 | `scp/logs.php`, syslogs.inc, ContentAjaxAPI::syslog | ADMIN, detalhe AJAX isAdmin; SYSLOG filtros/data/ordem/paginação, Log::lookup/HTML | Query também no template, dados operacionais sensíveis |
| T38 | logs massa delete | ADMIN/CSRF/IDs; SQL no controlador, contagem parcial/total/HTML | Regra presa ao entrypoint, não copiar SQL para endpoint novo |
| T39 | `scp/audits.php`, `scp/system.php` | ADMIN; audit depende PHAR/plugin; sistema reafirma admin, versões/extensões/config | Template executa diagnóstico; plugin ausente impede descrever conteúdo real |
| T40 | `scp/upgrade.php`, UpgraderAjaxAPI::upgrade | ADMIN, estado/pré-requisitos; Upgrader::setState/getTask/doTask/upgrade; HTML/texto200/201/416, possível schema/dados | Manutenção privilegiada, não executada, exige desenho próprio |

## Persistência e evidências

Consultar [ORM](ORM_CATALOG.md), [sessão/autenticação](AUTHENTICATION.md),
[subsistemas](TRANSVERSAL_SUBSYSTEMS.md), [ações e hooks](EXTENSION_POINTS.md).
Os efeitos incluem sessão/configuração, staff, drafts/arquivos, locks, filas,
traduções, notas, threads/revisões/colaboradores, logs e recursos de exportação.
Não pressupor transação, idempotência ou isolamento por haver método de domínio.

**Herdado, não reexecutado:** login/perfil e cenários funcionais da matriz
visual; divergência CSV e alvos ausentes dos catálogos. Falhas de segurança
anteriores permanecem registradas, sem novos ensaios ou aceite de risco.
`ajax.scp/apps.scp/apps.admin` e plugins podem modificar superfície em runtime;
não há catálogo fixo das extensões não instaladas derivável desta baseline.
