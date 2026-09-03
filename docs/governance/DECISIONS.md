# Registro de decisões de governança

Este índice registra decisões de processo. Decisões arquiteturais pertencem a `docs/adr/`.

| ID | Data | Status | Decisão | Evidência versionada |
| --- | --- | --- | --- | --- |
| GOV-001 | 2026-09-03 | Aceito | O repositório e o GitHub são a memória persistente do projeto; progresso, mudanças e decisões devem ser documentados. | `DOCUMENTATION_POLICY.md` |
| GOV-002 | 2026-09-03 | Aceito | O README da raiz funciona como painel da etapa atual e dos próximos passos. | `README.md` e `DOCUMENTATION_POLICY.md` |
| GOV-003 | 2026-09-03 | Aceito | Antes de iniciar múltiplos agentes, o agente principal deve demonstrar necessidade; tarefas simples permanecem com um agente. | `.codex/MULTI_AGENT_WORKFLOW.md` |
| GOV-004 | 2026-09-03 | Aceito | Os treze perfis profissionais são permanentes; missões não alteram cargo, autoridade, permissão ou limites. | `.codex/config.toml`, `.codex/agents/` e `.codex/MULTI_AGENT_WORKFLOW.md` |
| GOV-005 | 2026-09-03 | Aceito | Código deve respeitar decisões aceitas e não reabrir perguntas já formalmente respondidas. | `DOCUMENTATION_POLICY.md` e `AGENTS.md` |
| GOV-006 | 2026-09-03 | Aceito | Toda tarefa começa pela releitura da documentação e todo PR exige atualização e varredura documental imediatamente antes de sua criação. | `DOCUMENTATION_POLICY.md`, `AGENTS.md` e template de PR |

O número do Pull Request que integrar estas decisões deve ser acrescentado à evidência após sua abertura.
