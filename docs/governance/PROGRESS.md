# Controle de progresso

## Ponto de retomada

- Data de atualização: 2026-09-04.
- Baseline: `v1.18.4` (`8d38b0619649a50ee7cbbf37085f5d297fdc6f36`).
- Branch estável resultante: `main`.
- Etapa: engenharia reversa — inventário e análise estática concluídos.
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

## Em andamento

- Distribuição limpa `v1.18.4` implantada em
  `C:\xampp\htdocs\osTicketReforged`; instalação web aguarda execução pelo
  responsável.
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

## Próximo passo proposto

1. aguardar o responsável instalar o osTicket na homologação;
2. executar testes comportamentais de interface, sessão, permissões, uploads,
   e-mail e riscos priorizados em ambiente descartável;
3. atualizar os mesmos catálogos com fatos observados em runtime;
4. somente depois preparar opções do Portão D e decisões de integração.

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
| 2026-09-04 | Preparação da instalação web | `manage.php deploy --setup`; 2.250 arquivos; hash do core; `ost-config.php` a partir do modelo | Distribuição limpa pronta em `C:\xampp\htdocs\osTicketReforged` |
