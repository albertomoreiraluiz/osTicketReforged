# Automação e agentes

Esta pasta contém exclusivamente configuração e instruções para ferramentas de assistência ao desenvolvimento. Ela não faz parte do código executável, do frontend, do backend nem da documentação funcional do osTicket Reforged.

Conteúdo:

- `config.toml`: registro dos perfis disponíveis e limites de concorrência;
- `agents/`: cargos profissionais permanentes;
- `MULTI_AGENT_WORKFLOW.md`: avaliação de necessidade, missões, ownership, ondas e revisão.

Regras de isolamento:

- nenhum conteúdo desta pasta pode ser carregado pela aplicação;
- nenhum perfil pode ser colocado em `include/`, `api/`, `apps/`, `assets/`, `css/`, `js/`, `pages/`, `scp/`, `setup/` ou outro diretório do upstream;
- documentação funcional e arquitetural do produto permanece em `docs/`;
- `AGENTS.md` na raiz é somente o ponto de carregamento das instruções do repositório;
- toda retomada segue `AGENTS.md` e o checkpoint versionado em
  `docs/governance/CURRENT_STATE.md`; memória da ferramenta não substitui essas
  fontes;
- alterações nesta pasta são governança de automação e devem permanecer separadas de mudanças funcionais sempre que possível.
