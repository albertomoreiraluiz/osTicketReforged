# Matriz de customização e delta do core

## Critério

Esta matriz classifica mecanismos existentes; não escolhe arquitetura futura.
Todas as linhas são **fatos observados**, exceto inferência explicitamente
marcada. “Extensível” não significa API pública estável: plugins executam como
código PHP plenamente confiável e herdam a autoridade do processo.

Registro ou disponibilidade não implica autorização: o consumidor ainda deve
aplicar identidade, escopo, permissão, CSRF e validação.

| Capacidade | Mecanismo | Evidência | Controle e acoplamento | Impacto de atualização/lacuna |
| --- | --- | --- | --- | --- |
| conteúdo de landing, offline e agradecimento | configuração | `include/class.config.php:738-769`; `include/client/header.inc.php:17-174` | escrita administrativa; preservar escaping; baixo acoplamento | baixo; formatos/idiomas aguardam runtime |
| logos e branding | configuração | `logo.php`; `scp/logo.php`; `include/client/header.inc.php:17-174` | upload/config e fallbacks próprios | baixo-médio; cache/dimensões aguardam runtime |
| instâncias de forms, campos e listas existentes | metadados/configuração | `include/class.dynamic_forms.php`; `include/class.forms.php:1252-1267,5194-5248`; `include/ajax.forms.php:17-442` | staff, CSRF e validação; widgets/JS internos | médio; usa tipos já registrados |
| novo tipo de campo | registry PHP/plugin | `include/class.forms.php:605-657,1151-1170` | classe concreta controla validação/renderização | alto; chave pode colidir e contrato é interno |
| regras com ações de filtro existentes | configuração/registry | `include/class.filter_action.php:115,225-737`; `include/ajax.filter.php:7-25` | execução herda pipeline; IDs registrados | médio; ordem e falhas aguardam teste |
| pacote e instâncias | plugin | `include/class.plugin.php:175-524,543-999,1006-1183`; `include/ajax.plugins.php:4-60` | código confiável; gestão admin; ciclo explícito | médio-alto; símbolos PHP não versionados |
| inicialização e listeners | plugin/Signal | `include/class.plugin.php:193-221,1159-1183`; `include/class.signal.php:27-101` | dados e autoridade da requisição; ordem síncrona | alto; payload/momento podem mudar |
| navegação e rota no portal/SCP | registro de menu + Signal | `include/class.app.php:21-50`; `apps/dispatcher.php:23-33`; `scp/apps/dispatcher.php:20-46` | menu não instala controller nem ACL; rota impõe controle | médio-alto; URLs/controllers/HTML internos |
| adicionar rota à API | Signal `api` | `api/http.php:16-29`; `include/class.dispatcher.php:87-175` | rota deve autenticar; `Controller::access()` é permissivo por padrão | alto; sem versão, DTO ou envelope uniforme |
| adicionar AJAX cliente | Signal `ajax.client` | `ajax.php:22-51`; `apps/dispatcher.php:23-33` | CSRF mutável; sem login global | alto; DOM/resposta internos |
| adicionar AJAX SCP | Signal `ajax.scp` | `scp/ajax.php:25-333`; `AJAX_ROUTE_CATALOG.md` | staff/sessão/CSRF; guarda do objeto é local | alto; 13 alvos não callable e falha prevista são fatos estáticos; alcance/resposta aguardam runtime |
| observar criação/edição/exclusão | Signal genérico | `include/class.orm.php:621,680-688`; `SIGNAL_CATALOG.md` | recebe objetos internos; payload/momento variáveis | alto; atomicidade não uniforme |
| interceptar criação de ticket | Signal específico | `include/class.ticket.php:4089,4259,4585` | dados pessoais e `$vars` mutável | alto; rollback do listener não garantido |
| eventos de autenticação/sessão | Signal sensível | `include/class.auth.php:358-366,486-494,643-698`; `include/class.ostsession.php:130` | alguns payloads expõem senha/token | alto e sensível; exige governança própria |
| autenticação de agente/cliente | registry/backend | `include/class.auth.php:173-180,254-261,1127-1526` | credencial, identidade, sessão e reset | alto; precedência/configuração internas |
| 2FA e avatar | registry/backend | `include/class.2fa.php:107,262`; `include/class.avatar.php:62,104,217` | 2FA crítico; avatar envolve conteúdo externo | médio-alto; 2FA cliente tem TODO |
| armazenamento de arquivos | registry/backend | `include/class.file.php:771,1031,1082` | download, HMAC, MIME e objeto | alto; tipo-char persistido liga dados ao backend |
| busca | registry/backend | `include/class.search.php:49-69,657` | permissão registrada; visibilidade ainda obrigatória | alto; equivalência de ACL não demonstrada |
| ações de filtro | registry PHP | `include/class.filter_action.php:115-737` | opera sobre ticket; permissão é do chamador | médio-alto; tipo pode colidir e não há transação comum |
| lista e coluna de fila | registries internos | `include/class.list.php:100-140,1119`; `include/class.queue.php:3019-3145` | controles são do chamador | médio-alto; acoplamento a modelos, query e HTML |
| operação de export | armazenamento efêmero | `include/class.export.php:500-509,545-578`; `include/ajax.tickets.php:2055` | sessão guarda export já instanciado | não é registry de providers; novo formato exige outro ponto |
| permissão customizada | registry declarativo | `include/class.role.php:277-364` | consumidor deve chamar `hasPerm()` equivalente | declarar chave não protege operação automaticamente |
| criar ticket/rodar cron externamente | API nativa | `api/http.php:16-29`; `include/api.tickets.php:11-174`; `include/api.cron.php:5-20` | API key, IP e flag operacional | médio-alto; contrato estreito e sem versão |
| autenticação externa HTTP | API/Signal | `include/class.auth.php:787-798` | sessão e backend externo | alto; threat model depende do backend |
| conteúdo adicional em views | Signal/template | `include/staff/ticket-view.inc.php:12,270`; `include/staff/user-view.inc.php:152,168`; `include/staff/staff.inc.php:59,476` | escaping e autorização equivalentes à página | médio-alto; markup/ordem implícitos |
| CSS, JS, head e PJAX adicionais | composição global | `include/class.osticket.php:162-165`; `include/client/header.inc.php:39,66`; `include/staff/header.inc.php:58,92-97` | conteúdo executável confiável | alto; contrato DOM/PJAX implícito |
| substituir templates PHP | arquivo/composição | `include/class.forms.php:243-245,4347,5790,6077,6185`; headers cliente/staff | PHP confiável herda sessão/ACL | **Inferência:** sem resolver geral de override, cria delta e conflito alto |
| novo recurso “nativo” de API | core ou rota via Signal | `api/http.php:16-29`; sinal `api` | reproduzir key/IP/flags ou guarda equivalente | core conflita diretamente; Signal permanece interno |
| mudar invariantes de Ticket/Task/Thread sem hook adequado | core, caso comprovado | `include/class.ticket.php`; `include/class.task.php`; `include/class.thread.php` | risco de ACL, SLA, e-mail e integridade | muito alto; somente após excluir alternativas por requisito |

## Leitura consolidada

Configuração é a menor superfície de delta quando a capacidade já existe.
Plugins/apps, sinais e registries são mecanismos reais, mas não contratos
públicos versionados. A API pública é estreita; AJAX e templates são internos e
fortemente acoplados. Uma alteração de core só pode ser declarada inevitável
para requisito concreto após excluir, com evidência, configuração, plugin,
sinal, registry e composição.
