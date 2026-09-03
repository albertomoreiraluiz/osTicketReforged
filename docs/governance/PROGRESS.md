# Controle de progresso

## Ponto de retomada

- Data de atualização: 2026-09-03.
- Baseline: `v1.18.4` (`8d38b0619649a50ee7cbbf37085f5d297fdc6f36`).
- Branch estável resultante: `main`.
- Etapa: engenharia reversa — preparação do inventário.
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

## Em andamento

- Preparação da Onda 1 de inventário somente leitura.

## Próximo passo proposto

1. validar o carregamento da governança em um novo chat iniciado na raiz;
2. registrar a avaliação de necessidade e a composição da Onda 1;
3. iniciar o inventário somente leitura;
4. criar documentos de engenharia reversa apenas com evidência suficiente;
5. atualizar este arquivo ao final de cada unidade verificável.

## Estado dos portões

| Portão | Estado | Evidência atual | Bloqueio |
| --- | --- | --- | --- |
| A — Governança | Concluído | regras e perfis aprovados, validações e PR #1 | nenhum |
| B — Inventário | Pronto para iniciar | plano ativo e governança integrada | composição da Onda 1 |
| C — Análise profunda | Não iniciado | nenhuma | Portão B |
| D — Decisão arquitetural | Não iniciado | nenhuma | Portão C e aprovação do ADR |

## Decisões pendentes

- Convenção final de releases do Reforged — **A definir (TBD)**.
- Política de proteção obrigatória de `main` no GitHub — **Proposto**.
- Arquitetura de plugin, API e frontend — **A definir (TBD)** após engenharia reversa.
- Primeiro módulo a migrar — **A definir (TBD)**.

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
