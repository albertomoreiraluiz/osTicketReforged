# Instruções obrigatórias do osTicket Reforged

## Escopo

Estas instruções se aplicam a todo o repositório. Um `AGENTS.md` mais próximo pode acrescentar regras para sua subárvore, mas não pode relaxar segurança, rastreabilidade, preservação do upstream ou autoridade do responsável pelo projeto.

## Leitura obrigatória

Em toda nova tarefa, retomada, sessão ou mudança material de escopo, antes de analisar, planejar, executar comandos mutáveis ou alterar qualquer arquivo, leia novamente a documentação aplicável. Esta leitura é obrigatória mesmo que o agente já conheça o projeto ou possua memória de conversas anteriores.

Sequência mínima:

1. confirme branch, `git status`, remotos e baseline;
2. leia `REFORGED.md`;
3. leia `docs/governance/PROJECT_CONTEXT.md`;
4. leia `docs/governance/PROGRESS.md`;
5. leia o plano ativo aplicável em `docs/plans/active/`;
6. leia ADRs aceitos, documentação especializada e o `AGENTS.md` mais próximo;
7. inspecione Issues e Pull Requests aplicáveis quando houver acesso ao GitHub.

Não trate memória de conversa como substituta dessas fontes versionadas.

Se algum documento obrigatório estiver ausente, contraditório ou desatualizado, resolva ou registre a divergência antes de iniciar código.

## Fontes de verdade e precedência

Em caso de conflito, use esta ordem:

1. código e configuração da baseline, para comportamento observado;
2. ADRs aceitos e não substituídos, para decisões arquiteturais;
3. contexto, planos e documentação atuais do Reforged;
4. `AGENTS.md` aplicável mais específico;
5. Issues e Pull Requests ativos;
6. conversa atual, para propostas ainda não formalizadas;
7. documentação externa.

Marque explicitamente cada conclusão relevante como **Fato observado**, **Inferência**, **Proposta** ou **Decisão aceita**. Documentação externa não prevalece sobre o código analisado.

## Baseline e Git

- O repositório oficial é `https://github.com/osTicket/osTicket.git`, remoto local `upstream`, somente leitura.
- O fork é `https://github.com/albertomoreiraluiz/osTicketReforged.git`, remoto `origin`.
- A baseline inicial é a tag `v1.18.4`, commit `8d38b0619649a50ee7cbbf37085f5d297fdc6f36`.
- `main` representa a linha estável integrada do Reforged.
- Nunca faça commit diretamente em `main`, `develop`, `master` ou `1.18.x`.
- Use branch dedicada e Pull Request para `main`.
- Mantenha commits pequenos e separe documentação, extensões e alterações inevitáveis do core.
- Não faça rebase, reset destrutivo, force-push, merge, release ou exclusão sem autorização explícita.
- Atualizações do upstream entram por branch própria, comparação da nova release, validação e Pull Request.
- Imediatamente antes de criar qualquer Pull Request, releia e atualize `README.md`, `docs/governance/PROGRESS.md`, plano ativo, decisões/ADRs, documentação técnica afetada e `docs/changes/` quando houver código. Só abra o PR depois de confirmar que esses documentos descrevem o estado que será integrado.

## Separação entre produto e automação

- O código-fonte herdado do osTicket permanece nos diretórios originais do upstream.
- Configuração e documentação específicas de agentes ficam em `.codex/`, exceto este `AGENTS.md`, necessário como ponto de entrada na raiz.
- Documentação funcional, arquitetural e de progresso do Reforged fica em `docs/` e não deve conter instruções específicas de uma ferramenta quando elas puderem ficar em `.codex/`.
- Arquivos de governança nunca devem ser importados, empacotados, servidos ou carregados pela aplicação.
- Não coloque instruções de agentes em PHP, JavaScript, CSS, templates, assets, banco ou texto visível da interface.

## Estado atual e limite da etapa

A etapa ativa é engenharia reversa documental. Até uma decisão arquitetural aceita autorizar implementação:

- não criar a aplicação Angular;
- não implementar uma API completa;
- não alterar o core PHP;
- não alterar esquema ou dados de banco;
- não adicionar dependências de produção;
- não implementar plugins ou funcionalidades futuras;
- não apresentar arquitetura proposta como decidida.

São permitidos inventário, análise estática, execução local não destrutiva, documentação, diagramas e testes exploratórios que não persistam mudanças no produto.

## Engenharia reversa baseada em evidências

Afirmações arquiteturais devem citar caminhos e símbolos e, quando relevante, demonstrar:

- onde a classe ou função é definida;
- onde é instanciada ou registrada;
- quem chama seus métodos;
- fluxo de entrada até persistência e resposta;
- interfaces, classes-base, permissões e configuração envolvidas;
- hooks, sinais, callbacks e limites de extensão;
- consultas e tabelas acessadas;
- condição ou versão em que o comportamento se aplica.

Não conclua pela aparência do nome de um arquivo. Registre dúvidas não resolvidas em `docs/governance/PROGRESS.md` e no plano ativo.

## Documentação e progresso

- `docs/governance/PROJECT_CONTEXT.md`: missão, escopo e restrições duradouras.
- `docs/governance/PROGRESS.md`: ponto de retomada corrente e evidências de conclusão.
- `docs/governance/MULTI_AGENT_WORKFLOW.md`: protocolo de delegação, ownership e integração.
- `docs/plans/active/`: planos em execução; mova para `completed/` apenas quando os critérios forem cumpridos.
- `docs/adr/`: decisões arquiteturais; propostas permanecem `Proposto` até aprovação explícita.
- Documentos de engenharia reversa ficarão em `docs/reverse-engineering/`.

Ao concluir uma unidade relevante, atualize no mesmo PR o progresso, o plano e documentos afetados. O registro deve indicar data, branch, baseline, evidências, concluído, em andamento, próximo passo, dúvidas e riscos. Não use porcentagens subjetivas.

### Regras obrigatórias de documentação

- Todo progresso deve ser registrado dentro do repositório; conversa, memória do agente e estado local não são fontes persistentes suficientes.
- Toda alteração de código deve atualizar, no mesmo Pull Request, o documento funcional ou técnico afetado, `docs/governance/PROGRESS.md` e um registro em `docs/changes/`.
- Toda decisão do projeto deve ser formalizada no GitHub: Issue ou discussão para contexto quando aplicável, documento versionado para o conteúdo normativo e Pull Request para revisão e rastreabilidade.
- Decisões arquiteturais duradouras usam ADR numerado. Decisões de governança usam `docs/governance/DECISIONS.md`.
- O `README.md` da raiz deve sempre indicar baseline, etapa atual, estado dos portões, trabalho em andamento e próximas etapas, com links para os detalhes.
- Antes de qualquer alteração de código, consulte README, contexto, progresso, plano, decisões, ADRs, especificações, Issues e Pull Requests aplicáveis.
- Implementações devem cumprir decisões `Aceito`; não reabra perguntas já respondidas. Só solicite nova decisão quando houver conflito, ambiguidade material, evidência nova ou escopo realmente não decidido.
- Se surgir uma decisão durante a implementação, pause o trecho afetado, registre a proposta, obtenha aprovação, formalize-a e somente então prossiga.
- Divergência entre código e documentação bloqueia conclusão e merge até reconciliação explícita.
- Não documente retrospectivamente como substituto de documentação prévia: restrições e decisões necessárias devem existir antes do código que depende delas.

## Múltiplos agentes

Antes de trabalho técnico não trivial, avalie o uso de subagentes conforme `.codex/MULTI_AGENT_WORKFLOW.md` e informe brevemente a decisão.

- Nunca inicialize múltiplos agentes por padrão. Primeiro demonstre necessidade concreta por independência das frentes, volume, risco ou revisão especializada.
- Se um único agente puder concluir com qualidade e segurança sem sobrecarregar o contexto, trabalhe com um único agente.

- O agente principal é o único orquestrador e integrador.
- Somente o agente principal atribui ou revoga missão e ownership, encerra ondas, inicia correções e integra resultados. Somente o responsável pelo projeto aceita decisões arquiteturais, exceções e riscos residuais.
- Use no máximo três subagentes simultâneos.
- Subagentes não criam descendentes, não ampliam escopo e não executam commit, push, PR, merge, release nem mutações externas.
- Exploração e revisão são somente leitura.
- Escrita paralela exige ownership exclusivo por arquivo ou diretório, declarado antes do início.
- Toda instância deve ser registrada como `cargo/onda/missao`; revisão formal exige instância diferente daquela que produziu o artefato.
- Um único agente é responsável por arquivos transversais, índices, ADRs, planos e `PROGRESS.md`.
- Não delegue decisões que dependam do responsável pelo projeto.
- Aguarde uma onda terminar e estabilize o diff antes de iniciar a próxima.
- O agente principal deve reler e validar todo resultado; saída de subagente não é evidência suficiente por si só.
- Os perfis em `.codex/agents/` representam cargos profissionais permanentes. Missões podem delimitar objetivo, entradas, ownership e critérios, mas não podem renomear o cargo, alterar suas competências, ampliar sua autoridade ou relaxar seus limites.
- Não crie perfis temporários orientados a tarefas quando um cargo permanente puder receber a missão.

## Segurança e dados

- Nunca exponha credenciais, cookies, tokens, chaves, dados pessoais ou configuração sensível.
- Não use banco de produção e não execute operações mutáveis em banco sem autorização explícita.
- Não contorne autenticação, autorização, CSRF, ACL, escopo de ticket ou validação de upload.
- Não suponha que uma API ou frontend novo possa ignorar os controles existentes.
- Mudanças futuras em autenticação, autorização, sessão, API, upload ou dados sensíveis exigem revisão independente de segurança.

## Condições de parada

Pare e solicite decisão quando houver:

- necessidade de modificar o core na fase documental;
- escolha arquitetural não aprovada;
- conflito entre ADR aceito e implementação pretendida;
- alteração destrutiva ou mutação externa não autorizada;
- ownership sobreposto entre agentes;
- ausência de evidência suficiente para uma conclusão importante;
- expansão material do escopo ou necessidade de credenciais.

## Critério de conclusão de uma tarefa

Uma tarefa só está concluída quando o escopo solicitado foi atendido, o diff foi revisado, as validações proporcionais passaram, as fontes foram citadas, progresso e documentos afetados foram atualizados e riscos remanescentes foram informados. Commit, push, Pull Request e merge são ações distintas e dependem da autorização correspondente.
