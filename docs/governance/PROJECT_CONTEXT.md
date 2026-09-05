# Contexto persistente do projeto

## Estado

Documento **Aceito** pelo responsável pelo projeto como contexto persistente. Decisões arquiteturais continuam **A definir (TBD)** até aprovação em ADR.

## Missão

Realizar engenharia reversa estruturada do osTicket 1.18 e, com base no código, definir uma arquitetura sustentável para:

- preservar o core sempre que possível;
- desenvolver plugins e regras próprias;
- introduzir uma camada de API desacoplada dos detalhes internos;
- manter o painel PHP e disponibilizar um painel Angular/PrimeNG adicional por
  endereço distinto, em coexistência contínua;
- planejar separadamente a possível substituição integral do portal do usuário;
- preservar segurança, permissões, SLA, tickets, threads e anexos;
- manter compatibilidade com atualizações futuras;
- evitar um fork profundo e difícil de manter.

O objetivo não é reproduzir telas antigas nem reescrever o backend por preferência tecnológica.

## Baseline

- Upstream: `osTicket/osTicket`.
- Fork: `albertomoreiraluiz/osTicketReforged`.
- Versão inicial: `v1.18.4`.
- Commit inicial: `8d38b0619649a50ee7cbbf37085f5d297fdc6f36`.
- Branch estável do Reforged: `main`.
- Primeira branch de trabalho: `codex/docs-reverse-engineering-v1.18.4`.

## Etapa atual

Somente engenharia reversa e documentação. A implementação de Angular, API completa, plugins próprios, alterações do core e mudanças de banco está bloqueada até a análise e a decisão arquitetural.

Os Portões A, B e C estão concluídos. O Portão D foi iniciado em 2026-09-05 por
autorização do responsável e reiniciado após descarte da recomendação da PR #28.
As premissas aceitas estão no [ADR 0003](../adr/0003-coexistencia-paineis-portal-separado.md):
`/scp` preservado, endereço novo distinto (`/scp/reforged` apenas candidato),
portal planejado à parte. Não há nova recomendação ou primeiro recorte. O
[checkpoint canônico](CURRENT_STATE.md) registra o contrato de retomada e deve
ser confrontado com Git e fontes especializadas em toda nova sessão.

## Frentes obrigatórias da análise

1. inventário e bootstrap do sistema;
2. arquitetura e classes centrais;
3. plugins e ciclo de vida;
4. hooks, sinais e pontos de extensão;
5. override, composição e interceptação;
6. customizações que exigiriam core;
7. banco e persistência;
8. autenticação, sessão, autorização e ACL;
9. API existente e suas limitações;
10. opções para uma API própria;
11. frontend PHP atual;
12. futura arquitetura Angular e PrimeNG;
13. evolução incremental e coexistência contínua dos painéis;
14. padrão para plugins próprios;
15. atualizações do upstream e controle do delta;
16. segurança;
17. comparação de arquiteturas e ADR final.

## Entregáveis esperados

Em `docs/reverse-engineering/`:

- `ARCHITECTURE.md`;
- `PLUGIN_ARCHITECTURE.md`;
- `HOOKS.md`;
- `DATABASE.md`;
- `AUTHENTICATION.md`;
- `API_ANALYSIS.md`;
- `FRONTEND_ANALYSIS.md`;
- `MIGRATION_STRATEGY.md`;
- `CUSTOMIZATION_MATRIX.md`;
- `ARCHITECTURE_DECISION_RECORD.md`, como dossiê comparativo e recomendação proposta.

`ARCHITECTURE.md`, `DATABASE.md` e `AUTHENTICATION.md` são índices canônicos de
síntese e apontam aos catálogos detalhados. `MIGRATION_STRATEGY.md` e
`ARCHITECTURE_DECISION_RECORD.md` foram produzidos como propostas na abertura do
Portão D e foram descartados como orientação ativa; permanecem como histórico.
`GATE_D_REVIEW.md` registra os pareceres daquela rodada, não a aprovação da nova.

A decisão arquitetural aceita será registrada de forma canônica em um ADR numerado em `docs/adr/`. O dossiê de engenharia reversa não substitui esse ADR.

## Classificação das afirmações

- **Fato observado:** confirmado no código, esquema, configuração ou execução reproduzível.
- **Inferência:** interpretação sustentada por fatos, ainda não confirmada integralmente.
- **Proposta:** alternativa sujeita à avaliação e aprovação.
- **Decisão aceita:** escolha explicitamente aprovada e registrada em ADR.

## Regras invioláveis da etapa

- Código-fonte é a fonte primária.
- Não alterar o core.
- Não iniciar pelo frontend.
- Não implementar funcionalidades.
- Não alterar banco de produção.
- Não escolher arquitetura antes das evidências.
- Registrar divergências entre código e documentação externa.
- Não introduzir dependências desnecessárias.
- Preservar o estado e o histórico do upstream.

## Critério para encerrar a engenharia reversa

A etapa somente pode ser encerrada quando os entregáveis permitirem responder,
com evidência, como funcionam core, plugins, hooks, extensão, autenticação,
permissões, API, domínio, banco e frontend; quais customizações exigem core;
como evoluir mantendo ambos os painéis; como atualizar o upstream; quais
arquiteturas são viáveis; e qual primeiro módulo desenvolver no Reforged.
