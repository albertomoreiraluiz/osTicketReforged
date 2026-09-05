# Checkpoint canônico de retomada

## Status e finalidade

Documento **Aceito** em 2026-09-05 e inicialmente entregue pela PR #27.
Atualizado na branch `codex/accept-guard-coordination`, entrada `0c259179` (PR #37),
para registrar o aceite explícito do ADR 0007 e a diretriz comportamental. Permite que uma nova sessão
retome o trabalho sem depender de conversa, memória privada, terminal anterior
ou inferências sobre arquivos ausentes.

Este checkpoint resume; não substitui as fontes especializadas. Em conflito,
prevalecem código e configuração da baseline para comportamento, ADRs aceitos
para arquitetura, contexto e plano para escopo, e o controle de progresso para
o ponto de retomada.

## Identidade verificada

| Item | Estado canônico |
| --- | --- |
| Upstream | `https://github.com/osTicket/osTicket.git`, remoto `upstream`, push desabilitado |
| Fork | `https://github.com/albertomoreiraluiz/osTicketReforged.git`, remoto `origin` |
| Baseline | tag `v1.18.4`, commit `8d38b0619649a50ee7cbbf37085f5d297fdc6f36` |
| Linha estável | `main` |
| Etapa | Portão D em andamento; estratégia aceita, detalhamento técnico pendente |
| Implementação própria | não iniciada |
| Arquitetura-alvo | fronteira modular aceita no ADR 0004; desenho executável pendente |

O HEAD, o estado do GitHub e o ambiente local são dados mutáveis e devem ser
reconfirmados no início de cada tarefa; nenhum hash registrado aqui autoriza
ignorar `git status`, remotos, PRs ou a branch efetivamente carregada.

## Estado dos portões

| Portão | Estado | Base de conclusão ou próximo critério |
| --- | --- | --- |
| A — Governança | concluído | regras, decisões, documentação e perfis integrados |
| B — Inventário | concluído | 2.266 caminhos da baseline classificados sem sobra ou sobreposição |
| C — Análise profunda | concluído | catálogos, análise estática, comportamento visual e revisões independentes aprovados |
| D — Decisão arquitetural | em andamento | ADR 0004 aceita estratégia; contratos, integração e revisão de fechamento pendentes |

O responsável descartou a recomendação anterior e reiniciou a análise. O ADR
0003 formaliza somente coexistência contínua e separação do portal; não libera
implementação nem encerra o portão. ADR 0004 complementa as premissas com a
estratégia aprovada; detalhes técnicos e revisão de fechamento ainda são necessários.

## Fatos consolidados

- **Fato observado:** os 2.266 caminhos rastreados da baseline foram
  classificados por regras mutuamente exclusivas; 1.215 pertencem às árvores
  incorporadas Laminas Mail, mPDF e PEAR.
- **Fato observado:** a matriz visual cobre os contextos anônimo, cliente,
  agente restrito, administrador operacional e Administração; os cenários
  `BHV-001` a `BHV-034` possuem equivalência visual ou justificativa explícita
  de ausência de frontend.
- **Fato observado:** a revisão independente final da Onda 9 não manteve achado
  alto ou médio documental aberto.
- **Fato observado:** a documentação humana usa MkDocs Material e a taxonomia
  normativa de GOV-023, sem mover as fontes canônicas.
- **Fato observado no checkpoint anterior (PR #27):** 114 arquivos Markdown rastreados ou
  adicionados foram varridos; os 55 documentos de `docs/` aparecem uma única
  vez na navegação MkDocs, sem ausências ou duplicações. Dos 200 links locais
  verificados, nenhum destino ausente pertence à documentação do Reforged; seis
  referências ausentes permanecem em fontes históricas ou de terceiros da
  baseline e não foram modificadas.
- **Fato observado na última validação documentada:** a homologação local usa
  XAMPP, PHP 8.2 e MariaDB. Serviço, sessão, fixture e credencial local podem
  mudar e devem ser testados novamente antes de uso.
- **Fato observado na abertura do Portão D:** build estrito aprovado, 58/58
  documentos no MkDocs sem duplicação, 117 Markdown e 217 links locais auditados;
  nenhum destino ausente do Reforged, seis referências herdadas preservadas.

As cadeias de evidência estão em `docs/reverse-engineering/`; este resumo não
autoriza ampliar uma conclusão além do recorte declarado nesses documentos.

## Decisões aceitas que limitam o próximo trabalho

- GOV-001 a GOV-026 governam memória persistente, documentação, agentes,
  homologação, Git, evidências visuais, taxonomia e retomada.
- ADR 0001 aceita apenas o isolamento futuro do frontend em `frontend/` e o uso
  de Angular com PrimeNG; versões e integração continuam indefinidas.
- ADR 0002 aceita MkDocs Material como portal documental e GOV-023 fixa sua
  taxonomia editorial.
- ADR 0003 aceita coexistência contínua: painel legado em `/scp`, painel novo
  por endereço distinto e portal do usuário com planejamento separado.
  `/scp/reforged` é candidato; esse ADR não escolheu mecanismos de sessão ou API,
  e foi complementado pela estratégia no ADR 0004.
- GOV-016 autoriza branch, push, PR e merge validados; não autoriza force-push,
  release, reset destrutivo, exclusão ou integração com risco material.
- ADR 0004 aceita módulos e endpoints novos reutilizando o backend intacto,
  sem duplicar regras ou encapsular HTML. O alvo é todo o SCP, inclusive
  administração; mesma identidade/permissões e alternância sem novo login.
  A integração técnica da sessão não foi escolhida ou provada.
- ADR 0005 aceita divisão lógica M01–M15 e cobertura integral do SCP sem
  dependência funcional do frontend legado, exceto setup. Portal separado,
  implantação, contratos e bootstrap não são escolhidos por esse aceite.
- ADR 0006 aceita publicação na mesma origem, API por recursos/ações e isolamento
  dos arquivos próprios. `frontend/` permanece conforme ADR 0001; `reforged/`
  para PHP é candidato, assim como seu layout interno e mapeamento web.

- ADR 0007 aceita adaptação limitada da coordenação inline e análise comportamental
  dos trechos muito acoplados antes de reproduzi-los nos módulos; mecanismos
  reutilizáveis e core preservados, evidências por caso e implementação ainda pendentes.

## O que continua sem decisão

Não apresentar como aprovado ou implementado; os itens arquiteturais agora estão
em comparação, não em execução:

- layout, bootstrap e empacotamento da fronteira modular aceita;
- contratos, integração da autenticação existente e versionamento da API;
- versões de Angular e PrimeNG;
- mecanismo de sessão e integração entre frontend novo e backend;
- padrão definitivo de plugins próprios;
- estratégia final de persistência de conexões e segredos;
- primeiro módulo a migrar;
- política final de release e proteção obrigatória da `main`;
- qualquer alteração de core, schema ou implementação de produto.

Esses itens pertencem ao Portão D ou a decisões posteriores. Ausência de
decisão nunca deve ser preenchida por preferência técnica do agente.

## Limites e divergências conhecidas

- Cobertura estrutural não significa inspeção linha a linha nem execução de
  todas as combinações de configuração.
- Acessibilidade, compatibilidade entre navegadores, desempenho, concorrência e
  novos ensaios ofensivos de segurança são campanhas futuras.
- A exportação CSV sem materialização observável e a sobreposição vazia da
  Página de Login são divergências reproduzidas sem causa presumida.
- Achados de ACL de tarefas, capability de arquivo e upload permanecem fatos da
  baseline; correção ou aceite de risco exigem etapa própria.
- Fixtures e serviços do laboratório são evidência local, não contrato estável;
  revalide-os antes de apoiar nova conclusão.
- O `.env` é local, ignorado e exclusivo das ferramentas; nunca registrar seus
  valores em documentação, log, Issue ou PR.
- Seis referências locais ausentes pertencem a READMEs incorporados de Laminas
  ServiceManager/MyCLabs DeepCopy e ao manual histórico `setup/doc/api`; são
  limites herdados, não links criados pelo Reforged.

## Protocolo contra contexto inventado

Antes de analisar ou alterar o projeto:

1. confirme branch, status, HEAD, remotos, baseline, Issues e PRs aplicáveis;
2. leia `REFORGED.md`, este checkpoint, `PROJECT_CONTEXT.md`, `PROGRESS.md`, o
   plano ativo, decisões, ADRs e documentação especializada;
3. trate linhas históricas de `PROGRESS.md` como eventos passados, nunca como
   estado corrente;
4. aceite uma decisão somente quando estiver marcada como `Aceito` em GOV ou
   ADR e não tiver sido substituída;
5. revalide fatos mutáveis e baratos, especialmente ambiente, serviços, sessão,
   fixtures, versões externas e estado do GitHub;
6. quando faltar evidência, registre lacuna, inferência ou proposta; não complete
   a narrativa por plausibilidade;
7. se as fontes canônicas divergirem, interrompa o trecho afetado e reconcilie a
   documentação em PR antes de implementar;
8. atualize este checkpoint quando mudar baseline, portão, decisão, plano ativo,
   arquitetura aceita ou limite operacional material.

## Trabalho atual e próxima ação

Recomendação de fachada PHP e primeiro recorte de consulta de tickets descartados
pelo responsável. As três opções, a estratégia e os seis pareceres da PR #28
são históricos, não ponto de partida preferido nem prova da nova análise.
Inventário e evidências da baseline permanecem preservados.

Após a discussão, ADR 0004 aceita a estratégia de módulos novos sobre o backend
original. ADR 0005 aceita divisão/paridade; ADR 0006 aceita mesma origem,
recursos/ações e isolamento. Não reabrir essas decisões como indefinidas.
Detalhamento corrente: [guardas e contratos](../reverse-engineering/REFORGED_GUARD_CONTRACTS.md),
com G01–G09 rastreados e contexto/erros propostos. ADR 0007 aceito explicitamente:
próxima ação é especificar intenções, exceções, efeitos e critérios de equivalência,
reutilizando evidências válidas e planejando observação visual dos casos não cobertos.
Trechos muito acoplados podem ter seu comportamento reproduzido após análise;
não reabrir esse princípio, não presumir testes nem liberação de código.
A árvore física continua candidata. Base de rastreabilidade:
[mapa SCP](../reverse-engineering/SCP_FLOW_MAP.md): 153 registros estáticos,
47 arquivos PHP do escopo (inclui dois bootstraps), 26 famílias e 229 folhas AJAX.
GOV-026 e [Plano 0002](../plans/active/0002-functional-ux-direction.md) registram
direção de dashboard administrativo intuitivo, prioridade funcional e delegação
da primeira UX ao agente principal. Navegação proposta é ajustável, não
arquitetura aceita; sua materialização depende da liberação da implementação.
Primeiro módulo, rota definitiva e funcionalidades do portal continuam
sem escolha. Não implementar, alterar core/schema ou tratar pseudocódigo como API pronta.

Os 15 módulos lógicos aceitos têm atribuição principal dos 153 registros do mapa,
não 153 operações disjuntas nem 15 unidades de implantação aprovadas. Recursos
com ações explícitas e mesma origem foram aceitos; contratos e mecanismo de
publicação ainda não. Não há integração executável comprovada. Funcionalidade que
exige completar configuração/operação no painel antigo permanece pendente,
exceto setup; não declarar paridade por oferecer link de retorno ao legado.

Fontes normativas: ADRs 0004/0005/0006/0007 em `docs/adr/`.
`ARCHITECTURE_DECISION_RECORD.md`, `MIGRATION_STRATEGY.md` e `GATE_D_REVIEW.md`,
em `docs/reverse-engineering/`, preservam o histórico; o registro de revisões
também distingue os pareceres da unidade corrente dos anteriores descartados.
A formalização não encerra o Portão D.

O mapa não significa nova validação visual nem cobertura de todas as combinações
de configuração/plugins. Conserva 13 alvos AJAX ausentes, divergência do claim
tradicional e coordenação/regras ainda presas a páginas/controllers; não garante
que todas as cadeias já sejam reutilizáveis sem decisão adicional. Nenhum core,
schema, credencial, banco ou frontend foi alterado nesta unidade.

## Fontes canônicas de aprofundamento

- missão e limites: `docs/governance/PROJECT_CONTEXT.md`;
- progresso e histórico: `docs/governance/PROGRESS.md`;
- trabalho do Portão D: `docs/plans/active/0001-reverse-engineering.md`;
- decisões de processo: `docs/governance/DECISIONS.md`;
- decisões arquiteturais: `docs/adr/`;
- cobertura do código: `docs/reverse-engineering/SOURCE_COVERAGE_MATRIX.md`;
- cobertura visual: `docs/reverse-engineering/INTERFACE_COVERAGE_MATRIX.md`;
- evidências consolidadas: `docs/reverse-engineering/EVIDENCE_MATRIX.md`;
- riscos e achados: `docs/reverse-engineering/SECURITY_MODEL.md` e
  `docs/reverse-engineering/REVIEW_REPORT.md`.
