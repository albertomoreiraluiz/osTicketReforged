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
- revisão independente executada e correções documentais em integração.

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
varredura integral pelo frontend natural, conforme GOV-019; portanto o Portão D
aguarda essa matriz. GOV-020 exige reinício em ordem visual estrita e repetição
pelo navegador de todos os cenários antes comprovados diretamente. O trabalho
restante deste plano é dependente
da conclusão da repetição visual da Onda 9; o ciclo funcional da tarefa
derivada `2` já foi repetido pela interface e preservado aberto. Depois disso,
BHV-022 também foi repetido com edição e restauração visual do ticket
`903010`. A sequência posterior reconfirmou KB, usuários, organizações, perfil,
paginação, ações em massa, vínculo/desvínculo, fusão sem exclusão e ações
auditáveis da thread pelo frontend; a comprovação do arquivo CSV e os contextos
de cliente e agente restrito continuam na fila. Concluída a Onda 9, o plano
dependerá da comparação de opções e da decisão explícita do responsável.

### Portão D — Decisão arquitetural

- pelo menos três opções comparadas;
- riscos de atualização e segurança avaliados;
- estratégia de migração proposta;
- ADR aprovado explicitamente pelo responsável.

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
