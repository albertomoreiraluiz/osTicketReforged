# Manifesto das ondas de inventário

Este registro controla as missões multiagente do inventário estático. O agente
principal é o único responsável por integrar resultados e alterar documentação
transversal. Especialistas não executam a aplicação, não leem `.env`, não
alteram arquivos e não criam agentes descendentes.

## Onda 1 — fundações arquiteturais

**Estado:** concluída e integrada documentalmente.

**Justificativa:** as três frentes percorrem conjuntos extensos e independentes
da baseline. A separação reduz omissões sem criar decisões concorrentes.

| Instância | Perfil permanente | Missão | Leitura autorizada | Escrita | Revisão/integrador |
| --- | --- | --- | --- | --- | --- |
| `engenheiro-reversa/onda-01/ciclo-requisicao` | Engenheiro de Engenharia Reversa | rastrear entrypoints, bootstrap, ciclo de requisição e fronteiras centrais | código versionado e documentação vigente | nenhuma | agente principal |
| `engenheiro-dados/onda-01/modelo-persistencia` | Engenheiro de Dados | relacionar schema, constantes, metadados ORM, chaves e entidades centrais | código versionado, SQL e ERDs incorporados | nenhuma | agente principal |
| `arquiteto-plugins/onda-01/extensibilidade` | Arquiteto de Plugins | mapear plugins, sinais, eventos, hooks, configuração e persistência | código versionado e documentação vigente | nenhuma | agente principal |

### Critérios de encerramento

- evidências entregues com caminhos, símbolos e linhas verificáveis;
- fatos separados de inferências e lacunas;
- resultados integrados ao portal documental;
- `mkdocs build --strict` e `git diff --check` aprovados;
- diff estabilizado antes da próxima onda.

### Encerramento

As três missões entregaram evidências sem alterar o workspace nem acessar
`.env`. Os resultados foram integrados em `REQUEST_LIFECYCLE.md`,
`DATABASE_ARCHITECTURE.md`, `PLUGIN_ARCHITECTURE.md` e `HOOKS.md`.

## Onda 2 — contratos críticos

**Estado:** concluída e integrada documentalmente.

| Instância | Perfil permanente | Missão | Leitura autorizada | Escrita | Revisão/integrador |
| --- | --- | --- | --- | --- | --- |
| `engenheiro-php-senior/onda-02/ciclo-ticket` | Engenheiro PHP Sênior | rastrear criação, atualização, thread, tarefa e exclusão do ticket | código versionado e documentação vigente | nenhuma | agente principal |
| `arquiteto-seguranca/onda-02/controles-acesso` | Arquiteto de Segurança | mapear autenticação, autorização, CSRF, sessões, 2FA, API keys e fronteiras de confiança | código versionado e documentação vigente | nenhuma | agente principal |
| `arquiteto-api/onda-02/contratos-integracao` | Arquiteto de API | catalogar API, AJAX, formatos, dispatch, autenticação e efeitos observáveis | código versionado e documentação vigente | nenhuma | agente principal |

### Critérios de encerramento

- ciclo central do ticket rastreado por chamadores e efeitos;
- matriz estática de controles por superfície;
- rotas de integração normalizadas até o nível suportado pelo código;
- achados suspeitos separados de defeitos confirmados;
- integração documental, build estrito e diff estabilizado.

### Encerramento

As missões foram concluídas sem runtime, banco, interface ou leitura de
`.env`. Os resultados estão em `TICKET_LIFECYCLE.md`, `API_ANALYSIS.md` e
`SECURITY_MODEL.md`.

## Restrições da fase

- A análise é exclusivamente estática enquanto o osTicket não estiver
  instalado.
- Reprodução comportamental da interface e navegação ficam para a próxima
  fase, após a instalação pelo responsável.
- Angular e PrimeNG permanecem fora do escopo deste inventário.
- Segredos e valores de `.env` não constituem evidência documental e não devem
  ser lidos ou registrados.
