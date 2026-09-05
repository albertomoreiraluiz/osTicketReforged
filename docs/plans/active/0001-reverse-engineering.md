# Plano 0001 — Engenharia reversa do osTicket 1.18.4

## Status

**Aceito** pelo responsável pelo projeto; ativo após a integração da PR #1.

## Objetivo

Produzir a documentação técnica necessária para escolher, sem antecipação, a arquitetura do osTicket Reforged.

## Escopo permitido

- análise estática do repositório;
- execução local não destrutiva para confirmar comportamento;
- inventário arquitetural;
- rastreamento de classes, fluxos, banco, plugins, hooks, autenticação, API e frontend;
- documentação e revisão independente.

## Pré-condição de homologação

A infraestrutura local está ativa com XAMPP, Apache, PHP 8.2, extensões
necessárias e logs. A instalação funcional, o banco, o ciclo HTTP público e a
autenticação administrativa foram confirmados. A Onda 7 concluiu a validação
funcional inicial; a Onda 8 complementar mapeou os menus e ações encadeadas
omitidos na primeira matriz, foi revisada e integrada pela PR #9.

## Fontes externas de apoio

O snapshot rastreável da documentação oficial usado no inventário está em
`docs/upstream/osticket-docs/`. Ele contém o guia de instalação, o índice de
ERDs e seus quatorze diagramas PDF, capturados do commit `f2367c5f` do
repositório `osTicket/docs`.

A publicação oficial consultada se apresenta como documentação `1.17.7`.
Portanto, seu conteúdo é auxiliar: código, esquema e comportamento observado na
baseline `v1.18.4` prevalecem, e divergências devem ser registradas.

O modelo operacional da Onda 9 também consulta o Wiki oficial no commit
`b641236e504bbd867b38ece275e065a9f1be4fc1`. Como suas páginas funcionais são
históricas, ele orienta conceitos e precondições; não substitui a interface, o
código nem a configuração da baseline. A síntese aplicada está em
`docs/reverse-engineering/OSTICKET_OPERATIONAL_MODEL.md`.

## Fora do escopo

- alteração funcional do core;
- Angular ou PrimeNG;
- API completa;
- plugins próprios;
- migrations e alterações de dados;
- escolha da arquitetura antes da matriz de evidências.

Mutações funcionais necessárias aos testes comportamentais não constituem
implementação do produto: GOV-014 as autoriza somente no banco descartável de
homologação, com dados fictícios. Exclusões exigem plano prévio, backup
verificável e garantia de rollback; mudança de schema permanece fora do escopo.

## Portões

### Portão A — Governança pronta

- contexto e regras versionados;
- baseline e remotos registrados;
- progresso e protocolo multiagente validados.

### Portão B — Inventário verificável

- componentes arquiteturais relevantes localizados;
- entrypoints e fluxos principais identificados;
- mapa inicial de documentos e evidências criado.

Execução atual:

- árvore e tecnologias contabilizadas na baseline;
- superfícies, bootstraps e dispatchers principais localizados;
- mapas iniciais de componentes, entrypoints, dados e extensão produzidos;
- relações centrais, frontend PHP e subsistemas transversais localizados;
- revisão independente executada e correções documentais integradas.

Catálogos exaustivos de cada rota, metadado ORM e payload de sinal pertencem ao
aprofundamento do Portão C. Eles não são pré-condição retroativa do Portão B.

### Portão C — Análise profunda concluída

- plugins, hooks, domínio, banco, autenticação, autorização, API e frontend documentados;
- limitações de extensão e possíveis alterações do core classificadas;
- dúvidas críticas resolvidas ou explicitamente registradas.

**Estado:** escopo estático concluído em 2026-09-03, após seis ondas, catálogos
exaustivos e revisão cruzada sem achado alto ou médio aberto. A instalação foi
concluída em 2026-09-04; os 25 cenários da validação comportamental da Onda 7
foram concluídos, aprovados na revisão documental final e integrados pela PR #6.
Uma revisão de cobertura abriu nove cenários complementares da Onda 8: oito
foram concluídos no recorte declarado e ações em massa ficaram mapeadas sem
efeito em lote. Nenhuma exclusão ocorreu. A revisão independente foi aprovada e
integrada pela PR #9. Conforme GOV-018, as fixtures de vínculo e criação de
ticket a partir de resposta permanecem na homologação como evidência
inspecionável pelo painel. A Onda 9 reabriu a cobertura comportamental para uma
varredura integral pelo frontend natural, conforme GOV-019. GOV-020 exigiu o
reinício em ordem visual estrita e a repetição pelo navegador dos cenários antes
comprovados diretamente. Essa repetição foi concluída: o ciclo funcional da tarefa
derivada `2` já foi repetido pela interface e preservado aberto. Depois disso,
BHV-022 também foi repetido com edição e restauração visual do ticket
`903010`. A sequência posterior reconfirmou KB, usuários, organizações, perfil,
paginação, ações em massa, vínculo/desvínculo, fusão sem exclusão e ações
auditáveis da thread pelo frontend. Cliente e agente restrito já tiveram seus
percursos completos repetidos; cadastro público, SMTP visual, reenvio e
formulários administrativos priorizados também avançaram. A comprovação do
novo arquivo CSV continua como divergência de comprovação; o link de ativação
foi concluído. A matriz de visibilidade entre departamentos e equipe foi
comprovada visualmente com fixtures preservadas. Os 19 editores do conjunto
HTML padrão também foram percorridos na ordem visual, incluindo assunto, corpo,
ajuda e variáveis, sem salvar alterações. A revisão independente bloqueou a
primeira versão, as classificações históricas foram reconciliadas e a segunda
passagem foi aprovada sem achado bloqueante. Uma auditoria complementar
reproduziu a ausência de materialização do CSV e a sobreposição vazia da Página
de Login, além de concluir cabeçalhos MIME e um segundo `Salvar e Reenviar`.
A matriz literal classificou todos os 2.266 caminhos da baseline, separando
1.215 arquivos de dependências incorporadas. A revisão independente recalculou
as contagens, confirmou zero caminho sem classificação e zero sobreposição e
aprovou o conjunto sem achado alto ou médio. Com esse checkpoint integrado, o
plano passou a depender da comparação de opções e da decisão explícita do
responsável.

Antes do Portão D, o portal documental foi reorganizado por intenção de leitura.
A mudança não altera entregáveis, evidências ou decisões: mantém os caminhos
canônicos e apresenta projeto, inventário, arquitetura, domínio, interfaces,
APIs, segurança e extensibilidade em abas e grupos recolhíveis. A landing page
agora oferece trilhas para estado, funcionamento, evidências e evolução.

### Portão D — Decisão arquitetural

**Estado corrente:** estratégia confirmada pelo responsável e formalizada no
[ADR 0004](../../adr/0004-modulos-reforged-backend-osticket.md). Módulos novos
reutilizam backend intacto, cobrem todo o SCP e preservam coexistência e identidade.
O login foi exemplo didático, não primeira entrega autorizada. O portão continua
aberto para detalhamento técnico e revisão; nenhuma implementação foi iniciada.

As atividades abaixo aplicam-se às decisões ainda abertas. Não refazer escolha
entre estratégias já descartadas nem reabrir a fronteira aceita; comparações
técnicas devem ser delimitadas e discutidas com o responsável. As três opções
da primeira rodada permanecem históricas, sem demonstrar revisão da estratégia nova.

**Histórico do reinício:**

**Reiniciado em 2026-09-05**, por descarte explícito da recomendação anterior.
O [ADR 0003](../../adr/0003-coexistencia-paineis-portal-separado.md) registra
coexistência contínua dos painéis e portal separado, sem escolha de arquitetura.
Primeiro discutir critérios com o responsável; não repetir autonomamente a
comparação nem presumir o primeiro recorte. A abertura histórica ocorreu na
branch `codex/gate-d-architecture`, entrada `874efbd8`:
[dossiê comparativo](../../reverse-engineering/ARCHITECTURE_DECISION_RECORD.md),
[migração proposta](../../reverse-engineering/MIGRATION_STRATEGY.md) e
[revisões](../../reverse-engineering/GATE_D_REVIEW.md). Software, segurança e
dados revisaram a primeira proposta; API, frontend e QA concluíram a revisão
complementar. Esses pareceres e recomendações agora são históricos. Nenhuma
implementação foi autorizada por essa abertura ou pelo reinício.

O Portão D transforma as evidências dos Portões B e C em uma escolha explícita
de arquitetura. Ele não é uma etapa de implementação: nenhuma aplicação
Angular, API completa, alteração de core, plugin próprio ou mudança de schema
será iniciada durante a comparação.

**Precondição de retomada:** confirmar que o
[checkpoint canônico](../../governance/CURRENT_STATE.md) permanece coerente com
Git, progresso, decisões e evidências antes de abrir a primeira missão.

Atividades obrigatórias:

1. discutir e alinhar com o responsável detalhes pendentes sob ADRs 0003 e 0004,
   depois consolidar restrições, capacidades e lacunas da baseline;
2. comparar alternativas para as decisões técnicas abertas, sem transformar
   nomes e pseudocódigo do exemplo em contrato nem reabrir a estratégia aceita;
3. rastrear cada opção até as evidências de core, plugins, hooks, API, banco,
   autenticação, autorização e frontend;
4. comparar atualização do upstream, delta de core, segurança, integridade de
   dados, coexistência contínua com o painel PHP, evolução incremental, operação,
   observabilidade, testes e rollback;
5. propor a sequência de evolução do painel adicional e justificar o primeiro
   módulo candidato, sem incluir automaticamente o portal do usuário;
6. submeter matriz, estratégia e recomendação a revisões independentes de
   arquitetura, API, frontend, dados, segurança e qualidade conforme o impacto;
7. apresentar recomendação e riscos residuais ao responsável pelo projeto;
8. registrar a escolha aprovada em ADR numerado, ou registrar a rejeição e
   reabrir somente o recorte indicado pelo responsável.

Entregáveis previstos:

- `docs/reverse-engineering/ARCHITECTURE_DECISION_RECORD.md`, como dossiê
  comparativo e recomendação proposta;
- `docs/reverse-engineering/MIGRATION_STRATEGY.md`, com coexistência, etapas,
  dependências, rollback e primeiro módulo proposto;
- matriz rastreável de opções versus critérios e evidências;
- pareceres independentes e riscos residuais;
- ADR numerado com a decisão final, somente após aprovação explícita.

Critérios de saída:

- alternativas técnicas pertinentes comparadas em critérios comuns dentro da
  estratégia aceita; histórico da primeira comparação preservado;
- riscos de atualização, integração, dados e segurança avaliados;
- estratégia de evolução reversível que preserve o painel PHP operacional;
- nenhuma pendência crítica ou alta sem correção ou aceite explícito;
- estratégia aceita no ADR 0004 e decisões técnicas necessárias formalizadas
  após aprovação explícita; ADRs 0003/0004 não comprovam integração executável
  nem encerram sozinhos o portão.

Somente o Portão D libera o planejamento da implementação. Um portão não é concluído por inferência; registre evidências em `docs/governance/PROGRESS.md`.

## Sequência proposta

1. inventário do repositório e request lifecycle;
2. classes centrais e modelo de dados;
3. plugins, hooks e possibilidades de override;
4. autenticação, autorização e API atual;
5. frontend PHP e matriz de telas;
6. matriz de customização e delta do core;
7. opções de API, Angular e migração;
8. revisão cruzada e ADR.

## Condições de parada

- impossibilidade de sustentar uma conclusão importante ou prosseguir por meios seguros;
- decisão arquitetural necessária;
- necessidade de alterar core, dependência ou banco;
- acesso a ambiente ou credencial não autorizado;
- conflito de ownership;
- descoberta que expanda materialmente o escopo.

## Critérios de conclusão

- todos os entregáveis listados no [contexto do projeto](../../governance/PROJECT_CONTEXT.md) existem e são consistentes;
- afirmações importantes têm referência verificável;
- fatos, inferências, propostas e decisões estão separados;
- revisão arquitetural e de segurança não possui achado crítico aberto;
- o [controle de progresso](../../governance/PROGRESS.md) registra evidências e pendências;
- o responsável aprovou ou rejeitou formalmente a arquitetura recomendada.
