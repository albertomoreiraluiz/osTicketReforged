# Fluxo obrigatório de múltiplos agentes

## Status

Processo **Aceito** pelo responsável pelo projeto em 2026-09-03. A avaliação é obrigatória e a delegação é sempre condicional à necessidade demonstrada.

## Princípios

- O agente principal mantém contexto, decisões, integração e comunicação com o responsável.
- Paralelismo existe para reduzir contexto e melhorar revisão, não para multiplicar editores.
- Até três subagentes podem trabalhar simultaneamente; o agente principal permanece o quarto participante.
- Investigação, escrita e revisão ocorrem em ondas separadas.
- Nenhuma saída de agente substitui confirmação direta no código.
- `docs/governance/CURRENT_STATE.md` é entrada obrigatória de toda missão; cada
  agente deve separar o que reconfirmou do que apenas herdou do checkpoint.
- Perfis representam cargos profissionais permanentes; tarefas são missões temporárias atribuídas a esses cargos.
- Cargo, competências, autoridade, permissões e limites não são redefinidos por missão; somente uma mudança de governança aprovada pode alterá-los.

## Avaliação antes de delegar

Use subagentes quando houver frentes independentes, investigação volumosa, fronteira de segurança, revisão especializada ou conjuntos de arquivos sem sobreposição. Trabalhe sozinho quando a tarefa for pequena, sequencial, altamente acoplada ou disputar os mesmos arquivos.

É proibido inicializar múltiplos agentes antes dessa avaliação. Disponibilidade de slots, conveniência ou desejo de acelerar, isoladamente, não justificam delegação. A avaliação deve responder:

1. existem pelo menos duas frentes concretas e independentes;
2. cada frente possui entrega e condição de parada verificáveis;
3. o paralelismo reduz tempo, risco ou volume de contexto de forma material;
4. o ownership pode ser separado sem sobreposição;
5. o custo de coordenação é menor que o benefício;
6. alguma revisão independente é obrigatória pelo risco.

Se as respostas não demonstrarem benefício claro, o agente principal executa sozinho e registra uma justificativa breve. Revisores podem ser convocados em uma onda posterior sem transformar a implementação em escrita paralela.

Antes de cada onda, o agente principal registra:

- HEAD, baseline e coerência do checkpoint canônico com o estado real;
- objetivo e escopo;
- perfis escolhidos e motivo;
- entradas obrigatórias;
- ownership exato de arquivos ou diretórios;
- ações proibidas;
- critérios de aceite e validações;
- condição de parada.

## Perfis

| Perfil profissional permanente | Responsabilidade | Escrita |
| --- | --- | --- |
| `reverse_engineering_engineer` — Engenheiro Sênior de Engenharia Reversa | Comportamento real do sistema legado | Não |
| `software_architect` — Arquiteto de Software | Limites, opções, ADRs e compatibilidade | Não |
| `plugin_architect` — Arquiteto de Extensibilidade e Plugins | Plugins, hooks e estratégias de customização | Não |
| `data_engineer` — Engenheiro de Dados | Modelo relacional, consultas e persistência | Não |
| `security_architect` — Arquiteto de Segurança e IAM | Auth, sessão, ACL, API e dados sensíveis | Não |
| `api_architect` — Arquiteto de APIs | Contratos, recursos, versionamento e isolamento do core | Não |
| `frontend_architect` — Arquiteto Frontend | UI atual, Angular, PrimeNG e migração | Não |
| `senior_php_engineer` — Engenheiro PHP Sênior | Implementação aprovada de PHP, API e extensões | Ownership atribuído |
| `senior_angular_engineer` — Engenheiro Angular Sênior | Implementação aprovada de Angular e PrimeNG | Ownership atribuído |
| `qa_engineer` — Engenheiro de Qualidade | Estratégia de testes, automação atribuída e verificação independente | Ownership de testes |
| `technical_writer` — Especialista em Documentação Técnica | Documentação explicitamente atribuída | Ownership documental |
| `database_engineer` — Engenheiro de Banco de Dados | Migrations e mudanças executáveis aprovadas | Ownership de banco |
| `devops_release_engineer` — Engenheiro DevOps e Release | CI/CD, upstream, builds e preparação de releases | Ownership operacional |

Os perfis herdam o modelo e esforço escolhidos na sessão, evitando fixar uma versão que possa ficar obsoleta.

`max_depth = 1` limita descendentes quando suportado pelo runtime. A proibição de subagentes criarem descendentes permanece normativa mesmo se uma versão do runtime ignorar esse campo.

## Imutabilidade dos perfis

Cada arquivo em `.codex/agents/` define de forma duradoura:

- cargo e especialidade;
- responsabilidades permanentes;
- autoridade;
- limites e proibições;
- formato mínimo de entrega.

Uma missão deve indicar apenas o problema, o recorte, as entradas, o ownership, os critérios de aceite e a condição de parada. Se o trabalho exigir autoridade incompatível com o cargo, o agente principal deve escolher outro perfil; não deve modificar o perfil existente. Mudança futura em um cargo é alteração de governança, exige justificativa, revisão e aprovação explícita em Pull Request próprio.

Cada instância recebe um identificador auditável no formato `cargo/onda/missao`. Antes do spawn, o agente principal registra esse identificador, perfil, missão, arquivos de leitura, ownership de escrita e revisores previstos. Duas instâncias do mesmo cargo continuam distintas e não podem comprovar independência sem identificadores diferentes.

## Autoridade e RACI

| Assunto | Responsável técnico | Aprovação técnica | Decisão final | Implementação |
| --- | --- | --- | --- | --- |
| Arquitetura global e delta upstream | Arquiteto de Software | revisores especializados aplicáveis | responsável pelo projeto | engenheiro designado |
| Contrato de API | Arquiteto de APIs | Segurança e Arquiteto de Software | responsável pelo projeto | Engenheiro PHP Sênior |
| Controles de autenticação e autorização | Arquiteto de Segurança e IAM | Arquiteto de Software | responsável pelo projeto ou aceite explícito de risco | engenheiro designado |
| Arquitetura frontend | Arquiteto Frontend | API, Segurança e Arquiteto de Software, conforme impacto | responsável pelo projeto | Engenheiro Angular Sênior |
| Modelo de dados | Engenheiro de Dados | Segurança e Arquiteto de Software, conforme impacto | responsável pelo projeto | Engenheiro de Banco de Dados |
| Estratégia de testes | Engenheiro de Qualidade | especialista do domínio afetado | agente principal para a execução autorizada | Engenheiro de Qualidade nos testes atribuídos |
| CI/CD, upstream e release | Engenheiro DevOps e Release | Arquiteto de Software e QA | responsável pelo projeto | Engenheiro DevOps e Release |

Somente o responsável pelo projeto aceita decisões arquiteturais, exceções e risco residual. Somente o agente principal atribui ou revoga missões e ownership, encerra ondas, abre ondas de correção e integra resultados.

## Achados, bloqueios e exceções

- **Crítico:** risco de segurança, perda de dados, quebra de autorização, alteração destrutiva ou violação de decisão aceita; bloqueia integração.
- **Alto:** quebra provável de contrato, compatibilidade ou critério obrigatório; bloqueia até correção ou aceite explícito do responsável.
- **Médio:** risco relevante com alternativa segura; exige registro e decisão do agente principal sobre correção ou encaminhamento.
- **Baixo:** melhoria sem impacto no critério de aceite; não bloqueia, mas permanece registrada.

O agente principal reconcilia divergências factuais pelas evidências. Divergências arquiteturais ou aceite de risco são apresentados ao responsável pelo projeto; não podem ser resolvidos informalmente por votação entre agentes.

## Ondas da engenharia reversa

### Onda 1 — Inventário

Até três profissionais somente leitura mapeiam estrutura, bootstrap, plugins, domínio, autenticação e frontend. Antes de concluir a onda, o agente principal mantém uma matriz `frente → cargo → evidência → lacuna`; frentes não cobertas permanecem explicitamente pendentes.

### Onda 2 — Rastreamento profundo

Analistas recebem perguntas delimitadas e devem retornar cadeias de evidência: definição, registro ou instanciação, chamadores, persistência, controles e limitações.

### Onda 3 — Documentação

Um único escritor, normalmente o agente principal, atualiza documentos transversais. Escritores paralelos só podem atuar em arquivos diferentes e previamente atribuídos.

### Onda 4 — Revisão independente

Revisores leem o diff estabilizado. Achados devem conter prioridade, evidência, impacto e recomendação. Correções formam nova onda; o revisor não altera silenciosamente o texto revisado.

## Regras de ownership

- Nenhum arquivo pode ter dois escritores simultâneos.
- `AGENTS.md`, `REFORGED.md`, `.codex/config.toml`, ADRs, índices, planos e `PROGRESS.md` têm um único proprietário por onda.
- O agente que descobre impacto fora de seu escopo relata; não edita o arquivo externo.
- Mudanças em contrato compartilhado aguardam decisão e coordenação central.
- Antes de editar, releia o arquivo e `git status`; outros agentes compartilham a mesma árvore.
- Antes de criar um escritor, registre um manifesto de ownership com caminhos exatos. O handoff deve confirmar os caminhos efetivamente alterados. Qualquer colisão interrompe os escritores envolvidos até nova atribuição do agente principal.

## Restrições dos subagentes

Subagentes não podem:

- criar outros agentes;
- redefinir objetivo, escopo ou critérios;
- tomar decisão pendente em nome do responsável;
- editar fora do ownership;
- descartar mudanças existentes;
- executar commit, push, PR, merge ou release;
- modificar Issues ou outros sistemas externos;
- usar credenciais ou dados de produção.

## Handoff obrigatório

Cada retorno deve conter:

1. resumo do resultado;
2. fatos reconfirmados e fatos apenas herdados do checkpoint;
3. caminhos, símbolos e evidências;
4. inferências separadas;
5. arquivos lidos ou alterados;
6. validações executadas;
7. dúvidas, riscos e condição de parada;
8. recomendação do próximo passo, sem executá-lo fora do escopo.

## Integração

O agente principal deve aguardar o fim da onda, reler os arquivos afetados, verificar `git diff`, reconciliar contradições, confirmar referências e executar validação integrada. Revisão formal deve ser feita por uma instância que não produziu o artefato revisado. A conclusão informa identificadores das instâncias, perfis usados, resultados incorporados, testes, riscos e pendências.

## Carregamento

Inicie o Codex na raiz Git e marque o projeto como confiável. Alterações em `AGENTS.md` ou `.codex/` podem exigir um novo chat para carregamento completo. Ao retomar, use `docs/governance/PROGRESS.md` como ponte, mas reconfirme o estado real do Git.
