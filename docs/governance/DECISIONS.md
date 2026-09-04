# Registro de decisões de governança

Este índice registra decisões de processo. Decisões arquiteturais pertencem a `docs/adr/`.

| ID | Data | Status | Decisão | Evidência versionada |
| --- | --- | --- | --- | --- |
| GOV-001 | 2026-09-03 | Aceito | O repositório e o GitHub são a memória persistente do projeto; progresso, mudanças e decisões devem ser documentados. | `DOCUMENTATION_POLICY.md`; PR #1 |
| GOV-002 | 2026-09-03 | Aceito | O README da raiz funciona como painel da etapa atual e dos próximos passos. | `README.md`; `DOCUMENTATION_POLICY.md`; PR #1 |
| GOV-003 | 2026-09-03 | Aceito | Antes de iniciar múltiplos agentes, o agente principal deve demonstrar necessidade; tarefas simples permanecem com um agente. | `.codex/MULTI_AGENT_WORKFLOW.md`; PR #1 |
| GOV-004 | 2026-09-03 | Aceito | Os treze perfis profissionais são permanentes; missões não alteram cargo, autoridade, permissão ou limites. | `.codex/config.toml`; `.codex/agents/`; PR #1 |
| GOV-005 | 2026-09-03 | Aceito | Código deve respeitar decisões aceitas e não reabrir perguntas já formalmente respondidas. | `DOCUMENTATION_POLICY.md`; `AGENTS.md`; PR #1 |
| GOV-006 | 2026-09-03 | Aceito | Toda tarefa começa pela releitura da documentação e todo PR exige atualização e varredura documental imediatamente antes de sua criação. | `DOCUMENTATION_POLICY.md`; `AGENTS.md`; PR #1 |
| GOV-007 | 2026-09-03 | Aceito | O `.env` é entrada local e não versionada apenas para ferramentas; o osTicket mantém seu instalador e `include/ost-config.php`. A persistência definitiva das conexões permanece TBD. | `docs/development/HOMOLOGATION_ENVIRONMENT.md`; `.env.example` |
| GOV-008 | 2026-09-03 | Aceito | A homologação primária da engenharia reversa usará XAMPP local com Apache para permitir acesso direto ao PHP e aos logs; o aaPanel deixa de ser o alvo desta etapa. | `docs/development/HOMOLOGATION_ENVIRONMENT.md`; decisão explícita do responsável |
| GOV-009 | 2026-09-03 | Aceito | O runtime de homologação usará PHP 8.2 fornecido pelo XAMPP; esta versão satisfaz o requisito mínimo declarado pela baseline `v1.18.4`. | `include/class.setup.php`; `docs/development/HOMOLOGATION_ENVIRONMENT.md` |
| GOV-010 | 2026-09-03 | Aceito | A homologação atual será integralmente local; SSH, SFTP e FTPS não serão usados para PHP ou frontend, e o responsável preencherá os dados do banco. | `docs/development/HOMOLOGATION_ENVIRONMENT.md`; `.env.example` |
| GOV-011 | 2026-09-03 | Aceito | O novo frontend ficará isolado em `frontend/` e usará Angular com PrimeNG; versões e integração serão decididas posteriormente. | `docs/adr/0001-frontend-angular-primeng.md` |
| GOV-012 | 2026-09-03 | Aceito | A documentação humana da engenharia reversa será publicada localmente por MkDocs Material; ferramentas de símbolos e API serão complementares e específicas ao recorte. | `docs/adr/0002-documentation-toolchain.md`; `mkdocs.yml` |
| GOV-013 | 2026-09-03 | Aceito | O portal MkDocs integra o fluxo documental obrigatório: navegação deve acompanhar documentos relevantes e o build estrito deve passar antes da conclusão e de cada PR documental. | `AGENTS.md`; `docs/governance/DOCUMENTATION_POLICY.md`; `docs/development/DOCUMENTATION_PORTAL.md` |

As decisões GOV-001 a GOV-006 foram integradas pela PR #1. As decisões GOV-007
a GOV-011 foram formalizadas e integradas pela PR #2.
