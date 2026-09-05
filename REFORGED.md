# osTicket Reforged

## Situação

Projeto em engenharia reversa, baseado no osTicket `v1.18.4`. Ainda não existe arquitetura de customização aprovada nem implementação própria.

O Portão D está em andamento documental: comparação de três alternativas e
estratégia de migração propostas, revisões concluídas e decisão final pendente.

## Objetivo

Compreender o osTicket 1.18 diretamente pelo código e definir uma estratégia sustentável para plugins próprios, API moderna e migração progressiva do frontend PHP para Angular com PrimeNG, preservando permissões, segurança e capacidade de atualização.

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
