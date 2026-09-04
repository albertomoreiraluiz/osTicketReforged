# Controle de progresso

## Ponto de retomada

- Data de atualização: 2026-09-04.
- Baseline: `v1.18.4` (`8d38b0619649a50ee7cbbf37085f5d297fdc6f36`).
- Branch estável resultante: `main`, sincronizada após a PR #6.
- Etapa: Onda 8 integrada pela PR #9; preparação das opções do Portão D.
- Plano ativo: `docs/plans/active/0001-reverse-engineering.md`.

## Concluído

- Fork `albertomoreiraluiz/osTicketReforged` conectado como `origin`.
- Repositório oficial conectado como `upstream`; push local para ele desabilitado.
- `main` criada a partir de `v1.18.4` e definida pelo responsável como branch padrão.
- Branch documental criada e publicada.
- Contexto persistente, regras e protocolo multiagente produzidos localmente para revisão.
- Treze cargos profissionais permanentes definidos, com autoridade, limites e entregas estáveis.
- Perfis profissionais aprovados pelo responsável pelo projeto.
- Regras de uso condicional de múltiplos agentes aprovadas.
- Política obrigatória de documentação e formalização no GitHub aprovada.
- Configuração e documentação de agentes isoladas em `.codex/`, sem inclusão no código executável do osTicket.
- Leitura documental no início de toda tarefa e varredura documental anterior a todo PR aprovadas como obrigatórias.
- Governança formalizada e integrada pela PR #1.
- Infraestrutura-base de homologação preparada com XAMPP, PHP 8.2, extensões,
  logs e toolchain local inventariados.
- Snapshot rastreável do guia oficial de instalação, índice de ERDs, quatorze
  diagramas PDF e licença incorporado a partir de `osTicket/docs`.
- Instalação web concluída pelo responsável, com banco criado e painel ativo.
- Interface pública e autenticação administrativa confirmadas em runtime, sem
  registrar credenciais, cookies ou tokens.
- Mutações funcionais no banco descartável de homologação autorizadas para a
  Onda 7; exclusões condicionadas a plano, backup e rollback por GOV-014.
- Push para branches dedicadas, criação de PR e merge validado em `main`
  autorizados permanentemente por GOV-016; ações de risco material continuam
  exigindo parada e autorização específica.

## Onda 7 concluída funcionalmente

- Onda 7 comportamental executada sobre a distribuição `v1.18.4` instalada na
  raiz `C:\xampp\htdocs`.
- Varredura autenticada de 31 páginas concluída sem falha HTTP, formulário de
  login, erro fatal ou novo registro no log PHP.
- Passagem AJAX confirmou guards 400/403, respostas heterogêneas e todos os
  treze alvos ausentes retornando 500 em runtime, sem alcançar persistência.
- Fixtures fictícias de administrador, agente somente visualização e cliente
  autenticado disponíveis; segredos permanecem apenas no `.env` ignorado.
- Matriz inicial confirmou isolamento entre sessões de cliente/equipe, menu
  restrito do agente e `403` em AJAX administrativo.
- CLI `user activate` confirmou erro fatal por chamada ao método inexistente
  `UserAccount::create()`; importação de usuário funcionou.
- Cliente fictício criou ticket aberto pelo portal; administrador o atribuiu ao
  agente restrito, alterando sua visibilidade de ausente para presente.
- Agente sem permissão de resposta teve POST negado sem persistência, mas pôde
  criar nota interna; cliente viu resposta administrativa e não viu a nota.
- Transições Aberto → Resolvido → Aberto confirmadas por notas administrativas,
  locks válidos e leitura do estado persistido; ticket permanece ativo.
- Tarefa vinculada criada e atribuída; visibilidade entre administrador, agente
  e cliente confirmada. POST direto demonstrou que `task.reply` é aplicado
  somente na interface e não no controlador, permitindo resposta sem permissão.
- Nota de tarefa, negação de fechamento pelo agente e transições administrativas
  fechar/reabrir confirmadas; a fixture terminou aberta, sem exclusão.
- Upload Web aceitou TXT e extensão `.php` sob configuração permissiva; TXT foi
  associado à tarefa, entregue como attachment, protegido por login e assinatura.
- Limite temporário de 256 KiB confirmou cliente `200/500` abaixo/acima do
  limite e bypass staff também para tamanho; os campos foram restaurados a
  `NULL` e somente os dois uploads aceitos persistiram como temporários `D`.
- Ensaio com dump e rollback confirmou bypass staff de allowlist, rejeição do
  cliente convertida de 415 para 500 e ausência de ACL do pai em URL assinada.
- Tentativa inválida isolada e recuperação por login correto confirmadas para
  agente e cliente; CSRF inválido em login redireciona com 302.
- API nativa validada com chave local fictícia: guards 401, JSON/XML/e-mail
  `201`, parsing inválido 400, cron sem flag 401 e cron autorizado `200` após
  backup restaurável; chave terminou desativada e sem permissão de cron.
- Revisão independente elevou ACL composta de tarefa e capability assinada a
  altas; POST forjado confirmou fechamento sem `task.close`, seguido de rollback.
- Exportações PDF de ticket/tarefa em sessões staff, agente e cliente retornaram
  documentos válidos e legíveis; o ticket do cliente omitiu conteúdo interno e
  as exportações específicas da tarefa refletiram seu histórico autorizado.
- Escopo da Onda 7 ajustado por GOV-015 para priorizar fluxos funcionais fiéis
  entre frontend e backend, sem ampliar testes dedicados a segurança.
- Buscas e listas do cliente/SCP validadas com resultados positivos e negativos,
  filtros de estado/tópico, ordenação, lookup e formulário avançado.
- Base de Conhecimento habilitada pelo fluxo administrativo após criação de
  categoria e FAQ fictícias; publicação, menu, navegação e busca anônima validados.
- Resposta Pronta fictícia criada como fixture ativa/global; carregamento genérico
  e contextual do ticket validado em JSON e texto sem envio ao cliente.
- Ciclo reversível de liberação e atribuição confirmou eventos e visibilidade
  `assigned_only`; ticket terminou novamente atribuído ao administrador.
- Abertura anônima, validação de e-mail e colaboração reproduzidas com fixtures:
  associação tornou o segundo ticket visível e permitiu resposta do colaborador.
- Perfil do cliente alterado pelo formulário dinâmico e restaurado pelo mesmo
  fluxo; persistência final não reteve o marcador fictício.
- Organização fictícia criada e associada ao proprietário do ticket anônimo;
  a tela agregou usuário e ticket por relação derivada, sem exclusão.
- Nome da organização alterado e restaurado por AJAX; nota administrativa única
  persistida como fixture polimórfica, sem exclusão.
- Assunto de ticket alterado e restaurado pelo formulário tradicional; a visão
  dinâmica terminou com o original e sem marcador temporário.
- Transporte de saída inspecionado: sem SMTP cadastrado; coletores locais
  efêmeros e sem relay classificaram os fluxos previstos e foram encerrados.
- Usuário anônimo editado e restaurado pelo diálogo staff; organização
  preservada e nota rápida única persistida como fixture.
- Fuso do perfil administrativo alterado e restaurado por dois POSTs
  tradicionais; credenciais, 2FA e permissões preservados.
- Coletor SMTP local sem relay capturou uma mensagem/um destinatário durante
  nova abertura Web; ticket persistido e porta 25 fechada ao final.
- Segunda abertura Web classificou o único envio como alerta administrativo;
  autoresposta ausente por configuração global desativada, sem armazenar endereço.
- Resposta staff com lock AJAX persistiu uma única `R`, marcou ticket respondido
  e enviou somente ao proprietário fictício pelo coletor local.
- Exportador CLI integral rejeitado como backup: runtime produziu schemas sem
  linhas por reutilização incorreta de `$res` em `DatabaseExporter::dump()`.
- Portão C concluído com catálogos exaustivos, matriz de customização, limites
  do core e revisão cruzada independente.
- Onda 5 concluída com integridade de ticket/tarefa, 22 registries/factories e
  matriz de customização, em três missões somente leitura.
- Onda 6 concluída; segunda passagem aprovada sem achado alto ou médio aberto.
- Onda 4 concluída com matriz das 229 folhas AJAX, catálogo das 72 declarações
  ORM, contratos dos 14 módulos CLI e dos 41 sinais.
- Onda 1 do inventário estático formalizada para ciclo de requisição, modelo de
  persistência e extensibilidade, sem execução da interface ou leitura de
  segredos locais.
- Superfícies HTTP centrais interpretadas estaticamente, incluindo controles do
  portal, guarda da equipe, ações de ticket e dimensão inicial do AJAX.
- Ciclos web e CLI corrigidos e aprofundados; configuração, sessão, dispatcher
  e fronteiras de bibliotecas agora possuem evidência estática verificável.
- Persistência aprofundada com relações ORM, associações polimórficas, cdata,
  upgrades e integridade manual sem FKs físicas.
- Onda 1 concluída: arquitetura de plugins, estados de instância, persistência,
  dispatchers e catálogo de 41 sinais literais documentados.
- Onda 2 iniciada para contratos críticos: ciclo do ticket, controles de
  segurança e superfícies de API/AJAX.
- Instalação e upgrader interpretados estaticamente, incluindo ordem de
  persistência, assinaturas e lotes de patches.
- Ciclo central do ticket rastreado nas entradas Web, agente, API e e-mail, com
  thread, tarefas, formulários, status e exclusão.
- Onda 2 concluída com API nativa, 12 rotas AJAX do cliente e modelo estático de
  segurança; a Onda 4 confirmou 229 folhas no AJAX staff.
- Onda 3 iniciada para frontend PHP estático, subsistemas transversais e revisão
  independente do inventário.
- Infraestrutura histórica de testes inventariada sem execução prematura do
  runner dependente da baseline não instalada.
- Frontend PHP mapeado estaticamente em dois shells, com navegação, templates,
  assets, widgets e contratos PJAX/AJAX.
- E-mail, arquivos, busca, cron, filtros, formulários e base de conhecimento
  ligados a entrypoints, persistência e efeitos.
- Revisão QA independente concluída; contagens fundamentais confirmadas e
  inconsistências documentais em correção.

## Onda 8 concluída e integrada

- Revisão de cobertura solicitada para menus e submenus que não receberam
  ensaio comportamental específico na Onda 7.
- Enumeração estática confirmou ações de vínculo, fusão, criação derivada de
  ticket/tarefa, ações por entrada da thread, comandos secundários e ações em
  massa ainda sem evidência dinâmica completa.
- Cenários `BHV-026` a `BHV-034` formalizados antes das mutações.
- Vínculo/desvínculo e fusão condicionados a dump MariaDB restaurado em banco
  temporário; exclusão de ticket permanece fora da execução planejada.
- Dump integral de 1.132.907 bytes restaurado com as três fixtures em banco
  temporário antes das relações.
- Vínculo visual persistiu pai/filho, mas retornou `404`; desvínculo retornou
  `201` e deixou flags residuais no antigo pai.
- A renderização do evento de vínculo registrou fatal em
  `LinkedEvent::getDescription()` por divergência na quantidade de argumentos;
  eventos de fusão e desvínculo compartilham a mesma construção estática.
- Fusão com threads separadas retornou `201`, manteve pai aberto e fechou o
  filho sem exclusão; depois, o modo combinado também foi exercitado.
- Modos combinado e separado alternados com `201`; ambos apresentaram as duas
  mensagens no pai, e a fixture terminou no modo separado.
- Ticket derivado de mensagem herdou o solicitante; ticket derivado de resposta
  exigiu seleção explícita. Ambos preservaram o corpo e registraram referências.
- Tarefa derivada criada com descrição predefinida, vínculo ao ticket e notas
  cruzadas; edição/histórico de entrada confirmados.
- Diálogos e condições de destinatários, cabeçalhos e reenvio mapeados; envio
  redundante não repetido porque o transporte já foi coberto em BHV-011.
- Ações secundárias e em massa catalogadas; respondido/não respondido foi
  alternado e restaurado. A marcação de vencido foi negada e não persistiu.
- Três PDFs e dois ZIPs válidos confirmados; o ZIP com tarefas continha dois
  documentos, contra um no arquivo sem tarefas.
- A primeira revisão independente bloqueou o fechamento por desvio no momento
  do rollback e critérios amplos demais. Um dump pós-teste preservou a evidência
  e o dump pré-relação foi restaurado; seis contagens de tabelas ficaram iguais
  à base de verificação e as três fixtures voltaram ao estado independente.
- Oito cenários complementares foram concluídos no recorte declarado;
  `BHV-033` permanece mapeado, sem alegação de efeito em lote. Segunda revisão
  documental aprovada e fechamento integrado pela PR #9.

## Próximo passo proposto

1. derivar pelo menos três opções arquiteturais das evidências consolidadas;
2. comparar riscos de atualização, segurança, integração e migração;
3. apresentar recomendação e ADR do Portão D ao responsável, sem antecipar a
   decisão de versões ou integração do frontend.

## Estado dos portões

| Portão | Estado | Evidência atual | Bloqueio |
| --- | --- | --- | --- |
| A — Governança | Concluído | regras e perfis aprovados, validações e PR #1 | nenhum |
| B — Inventário | Concluído | três ondas estáticas, portal e revisão QA independente | nenhum |
| C — Análise profunda | Concluído | ciclos, dados, plugins, segurança, API, frontend, catálogos, falhas, registries, customização e revisão cruzada | nenhum bloqueio estático alto/médio |
| D — Decisão arquitetural | Não iniciado | nenhuma | Portão C e aprovação do ADR |

## Decisões futuras, fora da preparação atual

Os itens abaixo não bloqueiam o início nem a preparação do inventário. Serão
deliberados apenas na etapa a que pertencem, com base nas evidências produzidas.

- Convenção final de releases do Reforged — **A definir (TBD)**.
- Política de proteção obrigatória de `main` no GitHub — **Proposto**.
- Arquitetura de plugin, API e frontend — **A definir (TBD)** após engenharia reversa.
- Primeiro módulo a migrar — **A definir (TBD)**.
- Persistência definitiva de conexões e segredos — **A definir (TBD)**.

## Riscos conhecidos

- A cópia instalada expõe `/setup/` por HTTP e mantém
  `include/ost-config.php` gravável. O responsável aceitou essa condição somente
  para a homologação restrita à sua máquina; ela deve ser revista antes de
  qualquer exposição externa ou implantação de produção.
- O carregamento das ferramentas de assistência deve ser validado separadamente do comportamento do produto.
- Documentos de análise sem referências precisas podem virar conhecimento não verificável.
- Alterações do core antes da matriz de customização aumentariam prematuramente o delta com o upstream.

## Evidência mínima para atualizar este registro

Cada item concluído deve apontar para documento, diff, comando reproduzível, teste, Issue, Pull Request ou commit. Não registre avanço apenas por tempo gasto ou estimativa percentual.

## Histórico

| Data | Unidade | Evidência | Resultado |
| --- | --- | --- | --- |
| 2026-09-03 | Baseline Git | tag `v1.18.4`, commit `8d38b061` | Concluído |
| 2026-09-03 | Remotos | `origin`, `upstream` com push desabilitado | Concluído |
| 2026-09-03 | Governança inicial | arquivos locais; validação TOML e referências | Produzido, em revisão |
| 2026-09-03 | Perfis e políticas | aprovação explícita do responsável; `DECISIONS.md` | Aprovado, aguardando integração |
| 2026-09-03 | Portão A | PR #1 | Concluído |
| 2026-09-03 | Contrato local de homologação | `.env.example` e `docs/development/HOMOLOGATION_ENVIRONMENT.md` | Produzido, aguardando preenchimento local |
| 2026-09-03 | Configuração PHP do aaPanel | análise estática do `php.ini` fornecido; `docs/development/HOMOLOGATION_ENVIRONMENT.md` | Evidência inicial registrada; runtime ainda não validado |
| 2026-09-03 | Plataforma de homologação | GOV-008; `docs/development/HOMOLOGATION_ENVIRONMENT.md` | XAMPP local selecionado; aaPanel retirado do escopo desta etapa |
| 2026-09-03 | Runtime da homologação | GOV-009; requisito em `include/class.setup.php` | PHP 8.2 aceito para o XAMPP local |
| 2026-09-03 | Inventário do XAMPP | comandos `php`, `httpd` e leitura das configurações; guia de homologação | Caminhos e runtime registrados; serviços estavam parados |
| 2026-09-03 | Transporte da homologação | GOV-010; `.env.example` | Execução local; SSH e FTP fora do escopo |
| 2026-09-03 | Toolchain local disponível | Node.js 24.19.0 e pnpm 11.17.0; guia de homologação | Caminhos preenchidos localmente; Angular ainda não selecionado |
| 2026-09-03 | Configuração PHP | backup datado, validação de módulos, gravação no log e `httpd.exe -t` | Extensões e diagnóstico de homologação aplicados |
| 2026-09-03 | Fronteira do frontend | ADR 0001; GOV-011 | `frontend/` reservado para Angular e PrimeNG |
| 2026-09-03 | Homologação-base | `.env.example`; guia de homologação; validações PHP/Apache | Preparada; instalação funcional aguarda banco e localização web |
| 2026-09-03 | Integração da homologação-base | PR #2; merge `8786c271` | Concluída |
| 2026-09-03 | Referência oficial para o inventário | PR #4; merge `d90486ad`; snapshot `osTicket/docs` no commit `f2367c5f`; guia de instalação, licença e 14 ERDs | Concluída e integrada |
| 2026-09-03 | Portal documental | ADR 0002; `mkdocs.yml`; build estrito e inspeção local | Validado com MkDocs 1.6.1 e Material 9.7.7 |
| 2026-09-03 | Inventário inicial | árvore da baseline; entrypoints; bootstrap; matriz de evidências | Portão B iniciado; conclusão ainda bloqueada pelas lacunas registradas |
| 2026-09-03 | Mapas do Portão B | componentes; 46 scripts diretos e dispatcher de apps no SCP; 14 módulos CLI; 67 tabelas; 99 patches SQL; plugins e sinais | Mapeamento inicial ampliado; rastreamento profundo ainda não iniciado |
| 2026-09-03 | Onda 1 do inventário | `docs/reverse-engineering/WAVE_MANIFEST.md` | Três missões somente leitura registradas; integração documental centralizada |
| 2026-09-03 | Superfícies HTTP estáticas | `docs/reverse-engineering/HTTP_SURFACES.md`; controladores e dispatchers da baseline | Portal, equipe, API e aplicações delimitados sem reprodução comportamental |
| 2026-09-03 | Ciclos e persistência — Onda 1 | `REQUEST_LIFECYCLE.md`; `DATABASE_ARCHITECTURE.md` | Cadeias web/CLI e modelo lógico/físico documentados; validação dinâmica adiada |
| 2026-09-03 | Extensibilidade — Onda 1 | `PLUGIN_ARCHITECTURE.md`; `HOOKS.md` | Ciclo de plugins, registries e sinais mapeados; contratos individuais seguem pendentes |
| 2026-09-03 | Onda 2 do inventário | `docs/reverse-engineering/WAVE_MANIFEST.md` | Três missões críticas somente leitura registradas |
| 2026-09-03 | Instalação e evolução | `INSTALLATION_UPGRADE.md`; instalador e upgrader da baseline | Fluxos estáticos registrados; execução reservada para homologação instalada |
| 2026-09-03 | Ciclo do ticket — Onda 2 | `TICKET_LIFECYCLE.md`; classes e adaptadores centrais | Pipeline multicanal e riscos de atomicidade documentados estaticamente |
| 2026-09-03 | API e segurança — Onda 2 | `API_ANALYSIS.md`; `SECURITY_MODEL.md` | Contratos e riscos estáticos integrados; testes dinâmicos permanecem adiados |
| 2026-09-03 | Onda 3 do inventário | `docs/reverse-engineering/WAVE_MANIFEST.md` | Cobertura transversal e revisão independente registradas |
| 2026-09-03 | Testes da baseline | `TESTING_BASELINE.md`; 20 arquivos em `setup/test/` | Runner próprio e limites de confiança documentados; execução adiada |
| 2026-09-03 | Cobertura transversal — Onda 3 | `FRONTEND_ANALYSIS.md`; `TRANSVERSAL_SUBSYSTEMS.md` | Frontend e subsistemas documentados sem execução |
| 2026-09-03 | Revisão independente — Onda 3 | parecer do Engenheiro de QA; matriz e documentos corrigidos | Contagens-base validadas; aprofundamentos separados do Portão B |
| 2026-09-03 | Fechamento do Portão B | plano 0001; matriz de evidências; três ondas e revisão QA | Inventário estrutural concluído; Portão C iniciado |
| 2026-09-03 | Onda 4 do aprofundamento | `docs/reverse-engineering/WAVE_MANIFEST.md` | Três catálogos exaustivos somente leitura registrados |
| 2026-09-03 | Catálogos exaustivos — Onda 4 | `AJAX_ROUTE_CATALOG.md`; `ORM_CATALOG.md`; `CLI_CATALOG.md`; `SIGNAL_CATALOG.md` | 229 rotas, 72 metas ORM, 14 módulos e 41 sinais integrados; Onda 4 concluída |
| 2026-09-03 | Integridade e customização — Onda 5 | `INTEGRITY_FAILURES.md`; `REGISTRY_CATALOG.md`; `CUSTOMIZATION_MATRIX.md` | Estados parciais, 22 registries/factories e limites do core integrados; Onda 5 concluída |
| 2026-09-03 | Revisão cruzada — Onda 6 | `REVIEW_REPORT.md`; três pareceres independentes; build estrito | Achados reconciliados; segunda passagem aprovada; Portão C concluído |
| 2026-09-04 | Preparação da instalação web | `manage.php deploy --setup`; 2.250 arquivos; hash do core; `ost-config.php` a partir do modelo | Distribuição limpa movida pelo responsável para `C:\xampp\htdocs` |
| 2026-09-04 | Assinatura do schema | comparação binária e MD5 do `install-mysql.sql`; `core.sig` | CRLF identificado como causa; schema de homologação normalizado para LF e assinatura validada |
| 2026-09-04 | Instalação funcional | confirmação do responsável; ciclo HTTP público | Banco criado e instalação concluída |
| 2026-09-04 | Início da análise comportamental | interface pública e login administrativo por HTTP com CSRF | Página inicial em português e painel da equipe autenticado; nenhum segredo registrado |
| 2026-09-04 | Endurecimento pós-instalação | GET sanitizado de `/setup/`; atributos locais do arquivo de configuração | Instalador acessível e configuração gravável; condição submetida ao responsável |
| 2026-09-04 | Exposição local do instalador | decisão explícita do responsável | `setup/` será mantido na homologação acessível somente pela máquina local; produção permanece fora dessa exceção |
| 2026-09-04 | Autorização de dados da Onda 7 | GOV-014; decisão explícita do responsável | Criação e edição funcionais autorizadas na homologação; exclusões exigem plano, backup verificável e rollback |
| 2026-09-04 | Consolidação da engenharia reversa | PR #5 | Inventário estático, homologação e início da Onda 7 submetidos para integração em `main` |
| 2026-09-04 | Integração documental | PR #5; merge `9feda032` | Engenharia reversa estática e início comportamental integrados em `main` |
| 2026-09-04 | Onda 7 — páginas e AJAX | 31 páginas autenticadas; rotas AJAX válidas, inválidas e treze alvos ausentes | Páginas estáveis; guards registrados; todos os alvos ausentes confirmados com 500 sem persistência |
| 2026-09-04 | Onda 7 — identidades e permissões | criação administrativa de agente; importação/registro de cliente; três sessões | Agente de visualização e cliente autenticados; fronteiras iniciais confirmadas sem expor credenciais |
| 2026-09-04 | CLI de cliente | `manage.php user import`; `manage.php user activate` | Importação concluída; ativação fatal por `UserAccount::create()` inexistente |
| 2026-09-04 | Onda 7 — ticket inicial | formulário público, AJAX de tópico, persistência e atribuição AJAX | Ticket fictício aberto e atribuído; escopo `assigned_only` confirmado antes/depois |
| 2026-09-04 | Onda 7 — thread e ACL | resposta negada do agente; nota interna; resposta administrativa; visão do cliente | `ticket.reply` aplicado; nota interna permitida ao papel; conteúdo interno oculto do cliente |
| 2026-09-04 | Onda 7 — transições | notas administrativas com lock; estados 1 e 2 | Fechamento e reabertura confirmados; fixture preservada, sem exclusão |
| 2026-09-04 | Onda 7 — tarefa vinculada e ACL | criação AJAX; três identidades; POST direto; persistência da thread | Tarefa única confirmada; `task.create` negado ao agente, mas resposta persistiu sem `task.reply` |
| 2026-09-04 | Onda 7 — tarefa, nota e estado | nota do agente; fechamento negado; fechamento/reabertura administrativos; banco | Nota persistida; ACL de status preservada com feedback ambíguo; tarefa novamente aberta |
| 2026-09-04 | Onda 7 — arquivos inicial | uploads staff/cliente; associação em thread; URL assinada; três contextos de acesso | Configuração sem allowlist aceita extensões; TXT associado e serving básico confirmado |
| 2026-09-04 | Onda 7 — allowlist e rollback | dump SHA-256; protocolos A/B; campos `.txt`; restauração a `NULL` | Bypass staff confirmado; cliente rejeita com 500; acesso ao pai não é verificado |
| 2026-09-04 | Onda 7 — autenticação negativa | sessões isoladas; senha e CSRF inválidos; login correto subsequente | Mensagem genérica e 200 para senha; 302 para CSRF; sem bloqueio após uma falha |
| 2026-09-04 | Onda 7 — API HTTP | chave local; JSON válido/inválido; flag de cron; banco | 401/201/400 confirmados; ticket `source=API`; chave desativada ao final |
| 2026-09-04 | Onda 7 — revisão de segurança 1 | parecer independente; releitura do código; fechamento forjado e reabertura | ACL composta e capability altas; upload médio; contratos de erro classificados |
| 2026-09-04 | Onda 8 — menus e ações encadeadas | PR #9; `BHV-026`–`BHV-034`; três passagens QA; rollback verificado | oito cenários concluídos no recorte, massa mapeada, divergências relacionais registradas |
| 2026-09-04 | Onda 7 — exportações PDF | ticket/tarefa; administrador/agente/cliente; MIME, extração textual e inspeção visual | Quatro PDFs válidos e legíveis; cliente recebeu somente conversa pública, enquanto tarefa manteve histórico próprio; artefatos apenas na área local ignorada |
| 2026-09-04 | Onda 7 — foco funcional | GOV-015; verificação de persistência após interrupção do ensaio seguinte | Novos testes de segurança suspensos; upload deduplicado não criou resposta nem associação e a execução prossegue pelos fluxos normais de frontend/backend |
| 2026-09-04 | Onda 7 — resposta funcional do cliente | formulário real do portal; visão staff; thread, indicadores e logs | Uma mensagem pública foi exibida nos dois shells, persistida como `M`, atualizou `lastmessage` e manteve ticket aberto/não respondido; sem novo erro de runtime |
| 2026-09-04 | Onda 7 — buscas e filtros | cliente e SCP; número/termo ausente; estado, tópico, ordem, lookup e diálogo avançado | Resultados positivos e negativos coerentes; filtros do cliente persistem em sessão e precisam ser limpos entre composições; nenhuma mutação |
| 2026-09-04 | Onda 7 — Base de Conhecimento | estado vazio; categoria pública; FAQ interna→pública; configuração administrativa; sessão anônima | Artigo interno oculto; artigo publicado ainda bloqueado por `enable_kb=0`; após habilitação, menu, índice, categoria, artigo e busca públicos funcionaram; fixtures preservadas |
| 2026-09-04 | Onda 7 — Respostas Prontas | cadastro administrativo; listagem; endpoints KB/ticket em JSON e texto | Fixture ativa/global persistida; quatro contratos carregaram conteúdo e metadados no contexto do editor, sem criar thread ou e-mail |
| 2026-09-04 | Onda 7 — atribuição e liberação | administrador→sem atribuição→agente→administrador; duas sessões; banco/eventos | Três mutações `201`; visibilidade acompanhou `assigned_only`; repetição isolada determinística e estado final restaurado sem exclusão |
| 2026-09-04 | Onda 7 — abertura anônima e colaboração | TLD rejeitado/aceito; novo usuário/ticket; diálogo staff; portal do cliente; thread | `.invalid`/`.test` rejeitados sem persistência; ticket Web criado com `example.com`; colaborador ganhou lista/tela e publicou `M`, preservando proprietário e estado |
| 2026-09-04 | Onda 7 — perfil do cliente | formulário dinâmico; POST com CSRF; releitura Web; banco; restauração | Nome fictício persistiu e apareceu no perfil; valor original restaurado exatamente; nenhum marcador residual, e-mail ou exclusão |
| 2026-09-04 | Onda 7 — organização | diálogo AJAX; criação JSON; associação de usuário; detalhe; banco | Uma organização persistida; usuário ligado por `org_id`; ticket existente apareceu pela relação do proprietário; fixtures preservadas |
| 2026-09-04 | Onda 7 — edição e nota de organização | formulário AJAX; alteração/restauração; nota rápida; banco | Dois `201` restauraram o nome; nota retornou HTML `200` e persistiu uma vez com `ext_id=O{id}` |
| 2026-09-04 | Onda 7 — edição de ticket | formulário completo; ação relativa; dois POSTs; `ticket__cdata` | Assunto temporário apareceu e foi restaurado; resolução correta depende do contexto `/scp`; nenhuma notificação ou exclusão |
| 2026-09-04 | Onda 7 — pré-condição de notificações | `email_account`; PHP mail; porta local; `syslog` | Sem SMTP ativo ou listener/coleção; ausência de erro não prova envio; ensaio aguarda coletor local sem relay |
| 2026-09-04 | Onda 7 — administração de usuário | diálogo AJAX; dois POSTs; detalhe; banco; nota rápida | Nome restaurado por dois `201`; `org_id` preservado; uma nota `U{id}` retornada em HTML `200` |
| 2026-09-04 | Onda 7 — perfil do agente | formulário completo; fuso alternativo; duas releituras | Dois `200`; preferência alternativa confirmada e valor inicial restaurado; nenhum campo sensível alterado |
| 2026-09-04 | Onda 7 — notificação de abertura Web | coletor `127.0.0.1:25` sem relay; formulário público; banco; `syslog` | Uma mensagem/um destinatário aceito; ticket Web aberto persistido; sem erro de mailer; coletor encerrado e tipo da notificação ainda não classificado |
| 2026-09-04 | Onda 7 — classificação de notificação | coletor efêmero com classificação de domínio; nova abertura Web; configuração | Única mensagem destinada ao lado interno; alerta administrativo ativo, autoresponder global desligado e departamento sem gerente; porta fechada ao final |
| 2026-09-04 | Onda 7 — resposta staff notificada | plano prévio; snapshot SHA-256; lock AJAX; POST de resposta; banco; coletor local | Sem lock, validação sem efeito; com lock, uma `R`, ticket aberto/respondido, `lastresponse` atualizado, draft vazio e uma mensagem ao proprietário |
| 2026-09-04 | Onda 7 — mensagem de colaborador notificada | plano prévio; snapshot SHA-256; portal; banco; configuração; coletor local | Uma `M`, ticket aberto/não respondido, `lastmessage` atualizado, draft vazio e uma mensagem ao proprietário; sem autoresposta ou destinatário interno |
| 2026-09-04 | Onda 7 — filas e paginação | preferência temporária 5; fila com 6 tickets; páginas 1, 2 e 99; restauração | Divisão 5+1 confirmada; página fora do intervalo fica vazia apesar de indicar página 1; preferência original restaurada |
| 2026-09-04 | Onda 7 — atribuição e nota notificadas | plano prévio; snapshot SHA-256; atribuição; lock; nota; liberação; coletor | Uma `N`, relógios de mensagem/resposta preservados, draft vazio e atribuição restaurada; zero e-mail pelas políticas efetivas |
| 2026-09-04 | Onda 7 — API XML e e-mail | chave temporariamente ativa; XML; RFC 822; banco; coletor; restauração | Dois `201`; tickets `API` e `Email` com entrada `M`; zero saída no recorte; flags originais da chave restauradas |
| 2026-09-04 | Onda 7 — cron HTTP autorizado | inventário; dump SHA-256; restauração em banco temporário; cron; pós-check | `200 Completed`; 12 sessões expiradas removidas, demais candidatos zero, dados funcionais preservados e chave restaurada |
| 2026-09-04 | Onda 7 — limite de upload | configuração temporária 256 KiB; cliente abaixo/acima; staff acima; `finally`; banco | Cliente `200/500`, staff `200`; bypass inclui tamanho; dois arquivos temporários `D`; configurações novamente `NULL` |
| 2026-09-04 | Onda 7 — exportador de backup | export CLI comprimido; inspeção sem expor conteúdo; `class.export.php` | 47 schemas e zero linhas; artefato rejeitado para rollback; causa é `SELECT *` sem atribuição a `$res` |
| 2026-09-04 | Onda 7 — consolidação funcional | matriz BHV; estado final do banco e serviços; auditoria transversal | BHV-001 a BHV-025 concluídos; restaurações confirmadas; fixtures preservadas sem limpeza destrutiva; revisão QA final preparada |
| 2026-09-04 | Onda 7 — revisão QA final | `REVIEW_REPORT.md`; matriz BHV; diff documental | 25 cenários confirmados; duas inconsistências médias e uma baixa corrigidas; nenhum bloqueio funcional ou documental restante |
| 2026-09-04 | Integração da Onda 7 | PR #6; merge `845241ed`; `main` sincronizada | 45 commits e 19 arquivos documentais integrados; engenharia reversa funcional encerrada; Portão D permanece não iniciado até decisão explícita |
| 2026-09-04 | Autorização operacional de PR | GOV-016; decisão explícita do responsável | Push em branch dedicada, criação de PR e merge validado passam a ser autônomos; ações excessivamente arriscadas permanecem fora da autorização |
