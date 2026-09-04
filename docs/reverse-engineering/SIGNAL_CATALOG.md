# Catálogo de sinais

## Contrato comum

**Fato observado:** `Signal::send($signal, $object, &$data)` entrega o payload
por referência e chama subscribers na ordem de conexão. Não há schema,
prioridade, unsubscribe, agregação de retorno nem isolamento de exceções
(`include/class.signal.php:56-100`). Objetos PHP também podem ser alterados
pelos callbacks.

## Dispatch, UI e infraestrutura

| Sinal | Emissor e momento | Objeto/payload | Subscriber interno | Risco estático |
| --- | --- | --- | --- | --- |
| `agent.audit` | ficha do agente (`include/staff/staff.inc.php:476`) | `Staff`; extras | nenhum | injeta conteúdo na UI autenticada |
| `agenttab.audit` | abas do agente (`include/staff/staff.inc.php:59`) | `Staff`; extras | nenhum | contrato estrutural não formalizado |
| `ajax.client` | antes da resolução (`ajax.php:50`; `apps/dispatcher.php:30`) | `Dispatcher`; nulo | nenhum | rota deve impor seus próprios controles |
| `ajax.scp` | após rotas nativas (`scp/ajax.php:324`) | `Dispatcher`; nulo | nenhum | contexto SCP não substitui controle do controller |
| `api` | após rotas nativas (`api/http.php:27`) | `Dispatcher`; nulo | `/auth/ext` (`include/class.auth.php:787-798`) | extensão direta da API |
| `apps.admin` | dispatcher administrativo vazio (`scp/apps/dispatcher.php:36`) | `Dispatcher`; nulo | nenhum | registro privilegiado de rota |
| `apps.scp` | dispatcher da equipe vazio (`scp/apps/dispatcher.php:39`) | `Dispatcher`; nulo | nenhum | rota no contexto autenticado da equipe |
| `config.ttfonts` | antes de retornar fontes (`include/class.i18n.php:504-508`) | nulo; fontes/substituições | nenhum | altera recursos de documentos |
| `cron` | fim do cron (`include/class.cron.php:119-123`; `scp/autocron.php:63-66`) | nulo; `autocron` | cdata e indexação (`include/class.dynamic_forms.php:576`; `include/class.search.php:260-263`) | escrita estrutural e processamento síncrono |
| `export.tables` | antes do export (`include/class.export.php:807-812`) | exporter; tabelas | nenhum | altera o conteúdo do backup |
| `session.close` | antes de persistir sessão (`include/class.ostsession.php:127-131`) | nulo; `touched` | mensagens de sessão (`include/class.message.php:232-237`) | altera conteúdo e regravação da sessão |
| `syslog` | antes de filtrar/gravar (`include/class.osticket.php:309-315`) | nulo; nível/corpo | nenhum | observa ou modifica log sensível |
| `system.install` | durante instalação (`setup/inc/class.installer.php:175-180`) | installer; nulo | search backend (`include/class.search.php:654-655`) | escrita estrutural privilegiada |
| `ticket.view.more` | menu do ticket (`include/staff/ticket-view.inc.php:267-273`) | `Ticket`; extras | nenhum | ação injetada precisa de autorização própria |
| `user.audit` | ficha do usuário (`include/staff/user-view.inc.php:165-169`) | `User`; extras | nenhum | extensão da UI autenticada |
| `usertab.audit` | abas do usuário (`include/staff/user-view.inc.php:149-153`) | `User`; extras | nenhum | payload estrutural sem schema |

## Autenticação, sessão e identidade

| Sinal | Emissor e momento | Objeto/payload | Subscriber interno | Risco estático |
| --- | --- | --- | --- | --- |
| `auth.clean` | troca/reset de senha (`include/class.client.php:510`; `include/class.staff.php:302`; `include/class.user.php:1238`) | pessoa/usuário; variável | limpeza de sessões (`include/class.auth.php:1600`) | payload não uniforme |
| `auth.login.failed` | após falha (`include/class.auth.php:362-366`) | nulo; usuário e senha | nenhum | **crítico:** expõe senha em claro ao subscriber |
| `auth.login.succeeded` | após autenticação primária/chave e estado de sessão, antes de `onLogin` (`include/class.auth.php:645-674`) | `Staff`; nulo | nenhum | pode anteceder 2FA; não prova MFA ou sessão interativa concluída |
| `auth.logout` | logout do agente (`include/class.auth.php:694-699`) | `Staff`; nulo | nenhum | específico do agente |
| `auth.pwchange` | após sincronizar senha (`include/class.auth.php:490-495`; `include/class.client.php:506`) | usuário; senha nova/atual | nenhum | **crítico:** credenciais em claro e referenciadas |
| `auth.pwreset.email` | antes do e-mail (`include/class.staff.php:1123`; `include/class.user.php:1263-1268`) | pessoa; e-mail/vars/link/log | nenhum | **crítico:** expõe link/token de recuperação |
| `auth.pwreset.login` | antes do login por token (`include/class.auth.php:1266-1269,1493-1496`) | agente/cliente; nulo | nenhum | sessão privilegiada de recuperação |
| `person.login` | login ou excesso de tentativas (`include/class.auth.php:643,868,1112,1187`) | pessoa possível nula; tipo/mensagem | nenhum | sucesso e bloqueio compartilham o nome |
| `person.logout` | logout (`include/class.auth.php:697,911`) | pessoa; tipo | nenhum | superfície de origem não informada |

## E-mail

| Sinal | Emissor e momento | Objeto/payload | Subscriber interno | Risco estático |
| --- | --- | --- | --- | --- |
| `mail.received` | antes do MIME decode (`include/class.mailparse.php:57-63`) | parser; mensagem bruta | nenhum | entrada externa mutável muda todo o pipeline |
| `mail.decoded` | após decode (`include/class.mailparse.php:76-82`) | decoder; info | nenhum | conteúdo externo não confiável |

## ORM, objetos e domínio

| Sinal | Emissor e momento | Objeto/payload | Subscriber interno | Risco estático |
| --- | --- | --- | --- | --- |
| `model.created` | após INSERT (`include/class.orm.php:677-681`) | modelo; nulo | cdata/FAQ (`include/class.dynamic_forms.php:553-565`; `include/class.search.php:224`) | falha ocorre depois da persistência |
| `model.deleted` | após DELETE (`include/class.orm.php:618-622`) | modelo; nulo | cdata/índice (`include/class.dynamic_forms.php:564-566`; `include/class.search.php:227`) | derivados podem ficar incompletos |
| `model.updated` | após UPDATE ou manualmente (`include/class.orm.php:681-688`; `include/class.task.php:1364,1441`; `include/class.ticket.php:3804,3895`) | modelo; normalmente `dirty` | cdata/índice (`include/class.dynamic_forms.php:556-573`; `include/class.search.php:226`) | emissões manuais omitem payload |
| `object.created` | domínio e SCP, 25 emissões; ramo dinâmico (`include/class.organization.php:591`; `include/class.thread.php:260-274`) | objeto; payload heterogêneo | nenhum | não equivale necessariamente a INSERT ORM |
| `object.deleted` | exclusões, 18 emissões; ramo dinâmico (`include/class.canned.php:207`; `include/class.thread.php:260-274`) | objeto; tipo usual | filtros (`include/class.filter.php:712`) | momento transacional não uniforme |
| `object.edited` | 56 emissões, inclusive ORM (`include/class.orm.php:685-688`) | objeto; tipo/chave/flags | nenhum | maior superfície e payload heterogêneo |
| `object.view` | views de ticket/tarefa (`include/client/view.inc.php:7`; `include/staff/ticket-view.inc.php:12`; `include/staff/templates/task-view.tmpl.php:90`) | ticket/tarefa; tipo | nenhum | observa acesso; identidade é externa ao sinal |
| `organization.created` | após dados dinâmicos (`include/class.organization.php:586-592`) | `Organization`; nulo | índice (`include/class.search.php:223`) | ator não transportado |
| `task.created` | após `onNewTask()` (`include/class.task.php:1513-1518`) | `Task`; nulo | nenhum | origem não transportada |
| `threadentry.created` | após metadados de e-mail (`include/class.thread.php:1818-1823`) | `ThreadEntry`; nulo | índice (`include/class.search.php:220`) | origem heterogênea; indexação síncrona |
| `ticket.create.before` | antes da validação (`include/class.ticket.php:4086-4092`) | nulo; vars | nenhum | **crítico:** altera entrada antes da validação |
| `ticket.create.validated` | após validar, antes de persistir (`include/class.ticket.php:4256-4262`) | nulo; vars | nenhum | **crítico:** altera dados já validados |
| `ticket.created` | fim da criação (`include/class.ticket.php:4582-4587`) | `Ticket`; nulo | índice (`include/class.search.php:221`) | efeitos pós-criação podem ficar parciais |
| `user.created` | após `object.created` (`include/class.user.php:256-260`) | `User`; nulo | índice (`include/class.search.php:222`) | ator/canal não informados |

## Emissão dinâmica e invariantes

`Thread::logCollaboratorEvents()` escolhe `object.deleted` ou
`object.created` e envia o dono da thread com payload `collab` e mapa de IDs,
nomes e origens (`include/class.thread.php:260-274`). Não é um 42º nome.

- 154 emissões literais válidas;
- 41 nomes literais distintos e uma emissão dinâmica;
- 19 conexões internas cobrindo 13 nomes;
- 28 nomes sem subscriber interno, ainda disponíveis a plugins.

**Inferência:** sinais são pontos de interceptação, não fronteiras seguras por
si. O consumidor deve conhecer momento, identidade, mutabilidade e estado de
persistência do emissor específico.
