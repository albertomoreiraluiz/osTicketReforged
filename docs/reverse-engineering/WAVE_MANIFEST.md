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

## Onda 3 — cobertura transversal e revisão

**Estado:** concluída e integrada documentalmente.

| Instância | Perfil permanente | Missão | Leitura autorizada | Escrita | Revisão/integrador |
| --- | --- | --- | --- | --- | --- |
| `arquiteto-frontend/onda-03/frontend-php-estatico` | Arquiteto de Frontend | mapear templates PHP, navegação, assets, JS, temas e composição sem executar UI | código versionado e documentação vigente | nenhuma | agente principal |
| `engenheiro-reversa/onda-03/subsistemas-transversais` | Engenheiro de Engenharia Reversa | mapear e-mail, arquivos, busca, cron, filtros, formulários e conhecimento | código versionado e documentação vigente | nenhuma | agente principal |
| `engenheiro-qa/onda-03/revisao-inventario` | Engenheiro de QA | revisar cobertura, evidências, contradições e lacunas das Ondas 1 e 2 | código versionado e portal atual | nenhuma | agente principal |

### Critérios de encerramento

- composição estática do frontend existente documentada sem comportamento;
- subsistemas transversais ligados a entrypoints, persistência e efeitos;
- achados da revisão independente triados e incorporados;
- matriz de evidências atualizada com pendências realmente dinâmicas;
- build estrito e diff estabilizado.

### Encerramento das missões

Frontend PHP, subsistemas transversais e parecer QA foram entregues sem
execução, banco, navegador ou leitura de `.env`. A revisão confirmou as
contagens fundamentais e identificou correções de rotas, sinais, referências e
separação entre Portões B e C.

## Onda 4 — catálogos exaustivos do Portão C

**Estado:** concluída e integrada documentalmente.

| Instância | Perfil permanente | Missão | Escrita | Integrador |
| --- | --- | --- | --- | --- |
| `arquiteto-api/onda-04/matriz-ajax` | Arquiteto de API | extração reproduzível e matriz de rotas AJAX | nenhuma | agente principal |
| `engenheiro-dados/onda-04/catalogo-orm` | Engenheiro de Dados | catálogo das 72 metas ORM e relações | nenhuma | agente principal |
| `engenheiro-reversa/onda-04/cli-sinais` | Engenheiro de Engenharia Reversa | contratos dos 14 módulos CLI e 41 sinais | nenhuma | agente principal |

As restrições das ondas anteriores permanecem: leitura apenas, sem `.env`,
runtime, banco, UI, commits ou subagentes.

### Encerramento

As três missões entregaram catálogos reproduzíveis sem alterar o workspace. A
integração registra 229 folhas AJAX, 72 metadados ORM, 14 módulos CLI e 41
sinais em `AJAX_ROUTE_CATALOG.md`, `ORM_CATALOG.md`, `CLI_CATALOG.md` e
`SIGNAL_CATALOG.md`. Os 13 alvos AJAX ausentes e três divergências ORM
permanecem achados estáticos, reservados à confirmação posterior.

## Onda 5 — integridade, registries e limites de customização

**Estado:** concluída e integrada documentalmente.

**Justificativa:** as três frentes percorrem mecanismos independentes e
extensos. A separação permite revisão especializada sem ownership concorrente;
o agente principal permanece único redator e integrador.

| Instância | Perfil permanente | Missão | Escrita | Integrador |
| --- | --- | --- | --- | --- |
| `engenheiro-dados/onda-05/falhas-orfaos` | Engenheiro de Dados | rastrear atomicidade, rollback, cascatas e limpeza de órfãos de ticket/tarefa | nenhuma | agente principal |
| `engenheiro-reversa/onda-05/registries` | Engenheiro de Engenharia Reversa | catalogar registries, callbacks e extensões externas a `Signal` | nenhuma | agente principal |
| `arquiteto-api/onda-05/matriz-customizacao` | Arquiteto de API | classificar superfícies por plugin, sinal, API/AJAX, composição ou alteração de core | nenhuma | agente principal |

As missões devem citar definição, registro, consumidor, identidade, efeito e
limite. Achados suspeitos permanecem fatos estáticos ou inferências; nenhuma
arquitetura futura será escolhida nesta onda.

### Encerramento

As missões foram concluídas sem runtime, banco, UI, rede ou `.env`. Os
resultados estão em `INTEGRITY_FAILURES.md`, `REGISTRY_CATALOG.md` e
`CUSTOMIZATION_MATRIX.md`. Possíveis órfãos e estados parciais permanecem
inferências fortes até testes controlados após a instalação.

## Onda 6 — revisão cruzada do Portão C

**Estado:** concluída e integrada documentalmente.

**Justificativa:** o volume e a criticidade exigem revisão por instâncias
distintas das produtoras de cada artefato. Cada especialista revisa domínios
produzidos por outro perfil; não há nova produção paralela nem ownership de
arquivo.

| Instância | Perfil permanente | Missão de revisão | Escrita | Integrador |
| --- | --- | --- | --- | --- |
| `arquiteto-api/onda-06/revisao-integridade` | Arquiteto de API | revisar falhas/órfãos e registries, buscando evidência insuficiente e efeito em contratos | nenhuma | agente principal |
| `engenheiro-dados/onda-06/revisao-superficies` | Engenheiro de Dados | revisar AJAX, sinais e segurança, confrontando efeitos com persistência/schema | nenhuma | agente principal |
| `engenheiro-reversa/onda-06/revisao-cobertura` | Engenheiro de Engenharia Reversa | auditar cobertura global, ORM, CLI e matriz de customização contra plano/Portão C | nenhuma | agente principal |

Achado crítico, contradição ou referência inválida deve ser entregue com
correção proposta; testes dinâmicos não serão executados nesta onda.

### Encerramento

A primeira passagem encontrou achados altos e médios, todos corrigidos e
reavaliados. A segunda passagem foi aprovada pelos três revisores sem bloqueio
alto ou médio. O relatório consolidado está em `REVIEW_REPORT.md`; o integrador
validou contagens, links, diff e build MkDocs estrito.

## Restrições da fase

- As Ondas 1 a 6 foram exclusivamente estáticas porque o osTicket ainda não
  estava instalado.
- A partir da Onda 7, o runtime instalado pode ser observado de forma
  controlada, com evidências sanitizadas e sem exposição de segredos.
- Angular e PrimeNG permanecem fora do escopo deste inventário.
- Segredos e valores de `.env` não constituem evidência documental e não devem
  ser registrados. Somente o agente principal pode consumir as variáveis
  estritamente necessárias aos testes autorizados.

## Onda 7 — validação comportamental da baseline

**Estado:** concluída e revisada independentemente.

**Objetivo:** confrontar os contratos estáticos já documentados com o
comportamento funcional observável da instalação `v1.18.4`, reproduzindo os
fluxos normais entre frontend e backend, começando por cenários somente leitura
e separando explicitamente qualquer cenário mutável. Por GOV-015, novos testes
ofensivos ou dedicados a segurança não integram esta passagem e serão tratados
em fase própria somente após o mapeamento e o inventário completos.

**Execução inicial pelo agente principal:**

- confirmar disponibilidade da interface pública;
- autenticar no painel administrativo usando o `.env` local sem expor segredo;
- inventariar navegação, respostas HTTP, sessão, guards e erros de runtime;
- registrar resultados sanitizados no portal a cada conjunto concluído;
- não alterar core, configuração funcional ou dados durante a passagem somente
  leitura.

### Critérios de encerramento

- superfícies pública, cliente, equipe e administração confrontadas com o
  catálogo estático;
- controles de autenticação, sessão, CSRF e autorização observados por papel;
- logs PHP/Apache correlacionados sem dados pessoais ou segredos;
- cenários mutáveis necessários executados apenas após classificação explícita;
- divergências e lacunas refletidas nos catálogos e na matriz de evidências;
- documentação, links e build MkDocs estrito aprovados.

Os achados de segurança já obtidos permanecem evidência histórica da baseline,
sem direcionar a sequência funcional restante.

### Consolidação funcional

Os 25 cenários `BHV-001` a `BHV-025` estão concluídos. A passagem cobriu os
shells público, cliente, equipe e administração; tickets, tarefas, arquivos,
e-mail, API, cron, buscas, conhecimento, organizações, perfis, filas e
notificações. Mutações usaram apenas fixtures fictícias. Configurações
temporárias e permissões da chave de API foram restauradas; a execução
autorizada do cron contou com dump MariaDB restaurado em banco temporário antes
do ensaio e removeu somente as 12 sessões expiradas previamente inventariadas.

O estado final verificado registra oito tickets, 29 entradas de thread, dois
uploads temporários do cenário de limite no backend `D`, configurações dos dois
campos novamente `NULL`, chave de API inativa e sem permissão de cron, nenhuma
sessão expirada e nenhuma escuta SMTP na porta 25. As fixtures funcionais e os
artefatos locais ignorados foram preservados; nenhuma limpeza destrutiva foi
executada.

### Revisão independente final

**Justificativa:** o fechamento atravessa todas as superfícies funcionais e
precisa ser confrontado por profissional diferente do redator. A missão é
única, somente leitura e não reabre testes de segurança.

| Instância | Perfil permanente | Missão de revisão | Leitura autorizada | Escrita | Integrador |
| --- | --- | --- | --- | --- | --- |
| `engenheiro-qa/onda-07/revisao-fechamento-funcional` | Engenheiro de Qualidade | verificar cobertura dos 25 cenários, coerência entre matriz, catálogos, manifesto, plano e progresso, e apontar bloqueios documentais | código versionado, diff e documentação da Onda 7; sem `.env`, banco, runtime ou testes ofensivos | nenhuma | agente principal |

**Encerramento:** o parecer confirmou os 25 cenários sem lacuna funcional nova.
Duas inconsistências documentais médias e uma baixa foram corrigidas pelo
integrador. Não permanece bloqueio funcional ou documental para encerrar a
onda; concorrência, integrações reais de correio, acessibilidade, desempenho e
segurança continuam corretamente reservados às fases próprias.

### Revisão independente de segurança — checkpoint 1

**Justificativa:** a execução produziu achados estabilizados de autorização,
upload e capability que exigem especialista diferente do redator. O paralelismo
é limitado a uma missão somente leitura; o agente principal permanece único
escritor e integrador.

| Instância | Perfil permanente | Missão de revisão | Leitura autorizada | Escrita | Integrador |
| --- | --- | --- | --- | --- | --- |
| `arquiteto-seguranca/onda-07/revisao-acl-upload` | Arquiteto de Segurança e IAM | revisar evidências de `task.reply`, `task.close`, allowlist/HTTP e arquivo assinado; classificar prioridade e lacunas | código versionado, diff e documentação da Onda 7; sem `.env`, banco ou runtime | nenhuma | agente principal |

O revisor deve entregar fatos com caminhos e símbolos, separar inferências,
indicar prioridade e condição de parada e não propor correção como já aprovada.

**Encerramento:** parecer somente leitura entregue. O revisor confirmou como
altos a escrita/transição de tarefa sem ACL e a capability de arquivo interno;
classificou como médio o bypass de tipo/tamanho no upload staff e como baixo
isolado, médio no contrato, o mapeamento 413/415 para 500. A lacuna de fechamento
por `task:status` foi testada pelo integrador e confirmada em runtime, com
reabertura da fixture. Nenhum arquivo ou sistema foi alterado pelo revisor.

## Onda 8 — menus e ações secundárias do ticket

**Estado:** encerrada após segunda passagem QA aprovada.

**Motivo da reabertura:** a revisão solicitada pelo responsável identificou
que a matriz anterior validou o ciclo principal, mas não esgotou menus
contextuais e ações encadeadas. A nova passagem cobre sistematicamente menu e
submenus do ticket, ações da thread, operações em massa, vínculo, fusão e
criação derivada de ticket/tarefa.

**Estratégia:** a enumeração e os testes compartilham sessão, fixtures e estado
relacional; por isso a execução inicial permanece em agente único. Revisão
independente será avaliada após estabilização da matriz. As mutações usam dados
fictícios; vínculo/desvínculo e fusão obedecem ao plano de dump e rollback de
`BEHAVIORAL_ANALYSIS.md`. Nenhuma exclusão de ticket será executada nesta onda.

**Resultado da execução:** oito cenários complementares concluídos no recorte
declarado e um (`BHV-033`) mapeado sem execução em lote. Foram
confirmados menus contextuais, vínculo/desvínculo, os dois modos de fusão,
criação de ticket e tarefa a partir de entradas, edição/histórico, condições de
reenvio/destinatários, ações secundárias, menu em massa e exportações compostas.
O core não foi alterado e nenhuma exclusão foi executada.

**Primeiro parecer QA:** fechamento bloqueado por desvio do plano de rollback e
critérios que excediam a evidência. O estado pós-teste foi preservado em segundo
dump; o dump pré-relação foi restaurado na homologação com paridade nas
contagens de seis tabelas. Critérios de papel, participantes/tarefas na fusão,
anexos, reenvio, ações secundárias, massa e exportação foram estreitados ao
recorte efetivamente observado.

**Segundo parecer QA:** aprovado após confirmar o rollback reconciliado, o
estreitamento dos critérios ao que foi observado, o registro das divergências
relacionais e a coerência entre matriz, progresso, plano e portal. Nenhum
achado alto ou médio permanece aberto no fechamento documental. A Onda 8 foi
integrada em `main` pela PR #9.

## Onda 9 — observação integral pelo frontend

**Estado:** concluída, reconciliada e submetida a revisão independente.

**Motivo:** a cobertura por cenários e rotas não garantiu que todas as páginas,
regiões e ações visíveis fossem observadas. O Painel de Administração recebeu
profundidade insuficiente, ações em massa ficaram apenas estáticas e o fluxo de
entrada de e-mail foi exercitado diretamente no backend, não por uma
configuração operacional completa na interface.

**Método:** aplicar `INTERFACE_OBSERVATION_PROTOCOL.md` nos contextos anônimo,
cliente, agente, administrador operacional e Administração. O percurso é
posicional, de cima para baixo e da esquerda para a direita. Cada componente
recebe estado explícito na matriz; chamadas diretas não encerram cenários
visuais. Segurança permanece fora da onda.

**Estratégia de agentes:** execução por um único agente, porque a sessão
visual, as abas e o estado das fixtures são compartilhados e sequenciais.
Uma instância de QA diferente revisou os artefatos depois da estabilização.

**Fechamento de 2026-09-05:** os contextos anônimo, cliente, agente restrito,
administrador operacional e Administração foram percorridos em ordem. Os 34
cenários receberam equivalência visual explícita ou justificativa de ausência
de frontend. A passagem incluiu fluxo POP3/SMTP loopback configurado pela
interface, ações em massa reversíveis, matriz de departamentos/equipe, 19
editores de e-mail e ações de entrada da thread. Nenhuma exclusão foi executada.

Uma auditoria complementar repetiu a visualização de cabeçalhos MIME e o
`Salvar e Reenviar`, reproduziu o modal vazio da Página de Login e confirmou
que a nova exportação CSV não foi materializada no navegador interno. Esses
dois últimos resultados permanecem divergências conhecidas, não pendências de
execução. A cobertura literal dos 2.266 arquivos da baseline foi consolidada
em `SOURCE_COVERAGE_MATRIX.md` antes da revisão final.

**Parecer independente final:** a instância
`qa_engineer/onda09/auditoria-complementar-final` recalculou a árvore da tag e
confirmou soma 2.266, zero caminho sem classificação e zero sobreposição. A
coerência transversal, os links e a sanitização do dossiê foram aprovados sem
achado alto ou médio. A Onda 9 não possui lacuna comportamental conhecida que
exija repetição adicional antes do Portão D; as campanhas deliberadamente fora
do recorte e as divergências conhecidas continuam explicitadas.
