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
autenticação administrativa foram confirmados; a Onda 7 concluiu a validação
funcional controlada, revisada independentemente sem bloqueio restante.

## Fontes externas de apoio

O snapshot rastreável da documentação oficial usado no inventário está em
`docs/upstream/osticket-docs/`. Ele contém o guia de instalação, o índice de
ERDs e seus quatorze diagramas PDF, capturados do commit `f2367c5f` do
repositório `osTicket/docs`.

A publicação oficial consultada se apresenta como documentação `1.17.7`.
Portanto, seu conteúdo é auxiliar: código, esquema e comportamento observado na
baseline `v1.18.4` prevalecem, e divergências devem ser registradas.

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
foram concluídos e aprovados na revisão documental final.

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
