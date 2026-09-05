# osTicket Reforged

## Situação

Projeto em engenharia reversa, baseado no osTicket `v1.18.4`. A estratégia de
módulos novos sobre o backend original está aceita no ADR 0004; implementação
própria não iniciada.

O Portão D permanece em andamento para detalhamento técnico da estratégia
aceita. A recomendação anterior da PR #28 continua descartada.
O [mapa de fluxos SCP](docs/reverse-engineering/SCP_FLOW_MAP.md) detalha a
operação, administração e dependências compartilhadas. Arquitetura dos módulos
será discutida a partir dele; UX/layout do frontend vêm em seguida.
O Plano 0002 registra a direção visual já fornecida: dashboard administrativo
intuitivo e funcional primeiro; a primeira navegação será elaborada pelo agente
principal. Essa delegação não substitui o fechamento técnico do Portão D.
ADR 0005 aceita a divisão lógica M01–M15 e cobertura integral do SCP sem depender
do frontend original, exceto setup. `INTEGRATION_CONTRACT_DESIGN.md` em
`docs/reverse-engineering/` propõe a integração e os contratos; não libera código.
ADR 0006 aceita mesma origem, API por recursos/ações e isolamento. A organização
física em `REFORGED_PHYSICAL_DESIGN.md` permanece proposta: `frontend/` preservado
e `reforged/` candidato para PHP novo; nenhuma pasta de aplicação criada.

## Objetivo

Compreender o osTicket 1.18 pelo código e definir uma estratégia sustentável
para API e painel Angular/PrimeNG adicional, preservando o painel PHP, permissões,
segurança e atualizações. O portal do usuário terá planejamento separado.

## Comece por aqui

1. [AGENTS.md](AGENTS.md) — regras obrigatórias para agentes e contribuidores;
2. [contexto persistente](docs/governance/PROJECT_CONTEXT.md) — missão e limites;
3. [checkpoint canônico](docs/governance/CURRENT_STATE.md) — estado verificável,
   decisões ausentes e protocolo contra contexto inventado;
4. [controle de progresso](docs/governance/PROGRESS.md) — ponto atual de retomada;
5. [Plano 0001](docs/plans/active/0001-reverse-engineering.md) — plano ativo da engenharia reversa;
6. [portal documental](docs/index.md) — entrada humana para inventário e evidências;
7. [instruções de automação](.codex/README.md) — configuração isolada das ferramentas de assistência.

O `README.md` original permanece quase integralmente como documentação do upstream; recebe apenas um aviso de entrada para tornar esta governança visível no GitHub.
