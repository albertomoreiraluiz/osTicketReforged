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
| GOV-014 | 2026-09-04 | Aceito | Alterações funcionais necessárias à análise comportamental estão previamente autorizadas somente no banco descartável de homologação. Toda exclusão exige plano anterior à execução, backup verificável e garantia de rollback; produção, schema e core permanecem fora da autorização. | `AGENTS.md`; `docs/reverse-engineering/BEHAVIORAL_ANALYSIS.md`; decisão explícita do responsável |
| GOV-015 | 2026-09-04 | Aceito | A Onda 7 deve priorizar a reprodução fiel dos fluxos funcionais entre frontend e backend. Novos ensaios ofensivos ou dedicados a segurança ficam fora da passagem comportamental; a fase própria de segurança ocorrerá somente após o mapeamento e inventário completos. Achados já confirmados permanecem registrados como contexto. | `docs/reverse-engineering/BEHAVIORAL_ANALYSIS.md`; `docs/reverse-engineering/WAVE_MANIFEST.md`; decisão explícita do responsável |
| GOV-016 | 2026-09-04 | Aceito | Push para branch dedicada, criação de PR e merge validado em `main` estão previamente autorizados no fluxo normal. A autorização não alcança ações excessivamente arriscadas, conflitos, validações falhas, impacto não revisado, force-push, release, reset destrutivo ou exclusões. | `AGENTS.md`; decisão explícita do responsável |
| GOV-017 | 2026-09-04 | Aceito | Dumps SQL devem ser capturados e restaurados exclusivamente por streams binários, com `utf8mb4`, restauração temporária e validação de bytes por `HEX()` antes do banco ativo. Pipelines e readers/writers textuais são proibidos para backup ou restore. | incidente de encoding da Onda 8; `docs/development/HOMOLOGATION_ENVIRONMENT.md` |
| GOV-018 | 2026-09-04 | Aceito | Fixtures que constituem evidência de análise comportamental devem permanecer no osTicket de homologação para inspeção humana. Snapshot e rollback protegem contra falha ou desvio; não devem apagar automaticamente a evidência funcional aprovada. | esclarecimento explícito do responsável; dossiês BHV-027 e BHV-029 |
| GOV-019 | 2026-09-04 | Aceito | A análise comportamental integral deve usar o frontend natural e percorrer cada página de cima para baixo e da esquerda para a direita, componente por componente, em contextos separados. CLI, endpoint direto, banco e código são apenas evidência complementar. | `INTERFACE_OBSERVATION_PROTOCOL.md`; orientação explícita do responsável |

As decisões GOV-001 a GOV-006 foram integradas pela PR #1. As decisões GOV-007
a GOV-011 foram formalizadas e integradas pela PR #2. GOV-012 e GOV-013, o
inventário técnico e GOV-014 são consolidados pela PR #5. GOV-015 e o
encerramento da Onda 7 foram integrados pela PR #6.
GOV-016 formaliza a autorização operacional permanente concedida depois desse
encerramento. GOV-017 registra a correção e o procedimento binário obrigatório
após o incidente de encoding da Onda 8.
GOV-018 corrige o critério de evidência operacional após a constatação de que a
restauração integral ocultou do painel os resultados que deveriam permanecer
inspecionáveis.
GOV-019 reabre a cobertura visual e administrativa porque os cenários anteriores
não demonstraram exaustividade por página e, em alguns fluxos, substituíram a
operação natural por chamadas diretas.
