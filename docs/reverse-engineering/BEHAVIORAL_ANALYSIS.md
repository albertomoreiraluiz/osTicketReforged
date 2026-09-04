# Análise comportamental da baseline

## Escopo e método

Este documento confronta a análise estática da baseline `v1.18.4` com uma
instalação descartável de homologação. Cada observação deve registrar data,
papel, superfície, ação, resultado e relação com o contrato estático, sem
copiar credenciais, cookies, tokens, dados pessoais ou conteúdo sensível.

A execução começa por navegação somente leitura. Conforme GOV-014, cenários que
criem ou editem registros estão previamente autorizados somente no banco
descartável de homologação e devem usar dados fictícios e prefixo identificável.
Exclusões permanecem condicionadas a plano prévio, backup verificável e garantia
de rollback.

## Estado da instalação

| Verificação | Resultado observado em 2026-09-04 |
| --- | --- |
| URL base | respondeu por HTTP no host local configurado |
| Portal público | `200`; página inicial renderizada em português |
| Navegação pública inicial | página principal, abertura e consulta de ticket expostas |
| Login da equipe | formulário `200` com `userid`, `passwd`, `do` e token CSRF |
| Autenticação administrativa | aceita; painel da equipe retornado sem formulário de login |
| Cookie de sessão | `HttpOnly` e `SameSite=Lax`; sem `Secure` no HTTP local |
| Rotação da sessão | adiada e confirmada na primeira requisição GET após 10 segundos |
| Logout | sessão encerrada; rota protegida voltou a apresentar login |
| Instalador pós-instalação | ainda acessível em `/setup/` e `/setup/install.php` |
| Configuração nativa | `include/ost-config.php` existe e permanece gravável |
| Segredos em evidências | nenhum valor, cookie ou token registrado |

## Matriz de execução

| ID | Superfície | Papel | Natureza | Estado | Evidência esperada |
| --- | --- | --- | --- | --- | --- |
| BHV-001 | página inicial pública | anônimo | leitura | concluído | status, título e navegação |
| BHV-002 | login `scp` | administrador | sessão | concluído | CSRF presente e painel autenticado |
| BHV-003 | painel e menus da equipe | administrador | leitura | concluído na passagem inicial | telas, links e guards observados |
| BHV-004 | portal do cliente | anônimo/cliente | leitura/sessão | concluído na passagem inicial | login, consulta e guards |
| BHV-005 | matriz de permissões | agente/admin | leitura | iniciado | diferenças de menu e resposta |
| BHV-006 | AJAX/PJAX | administrador/agente | leitura | iniciado | contrato HTTP e erros sanitizados |
| BHV-007 | logs PHP e Apache | sistema | leitura | iniciado | ausência/presença de falhas correlatas |
| BHV-008 | ticket de teste | papéis fictícios | mutável | iniciado | ciclo e persistência controlados |
| BHV-009 | tarefas vinculadas | papéis fictícios | mutável | iniciado | criação, visibilidade e ACL por ação |
| BHV-010 | anexos e arquivos | papéis fictícios | mutável | iniciado | persistência, serving e limites |
| BHV-011 | e-mail | papéis fictícios | mutável | pendente | efeitos controlados sem entrega externa acidental |
| BHV-012 | API HTTP nativa | chave fictícia local | mutável | iniciado | autenticação, flags, parsing e persistência |

## Regras de evidência

- Informar apenas caminhos de rota quando não contiverem token ou identificador
  sensível.
- Substituir IDs e dados funcionais por descrições neutras quando necessário.
- Não armazenar HTML completo de páginas autenticadas.
- Não publicar cookies, cabeçalhos de autenticação, token CSRF ou conteúdo do
  `.env`.
- Correlacionar toda divergência com o catálogo estático correspondente antes de
  classificá-la como defeito.
- Atualizar este documento e os catálogos afetados a cada conjunto de testes.

## Achados iniciais

### BHV-001 — portal público

O portal respondeu com sucesso e apresentou três caminhos coerentes com
`index.php`, `open.php` e `view.php`. A interface mostrou o estado de usuário
não registrado e conteúdo de boas-vindas em português. Isso confirma a
composição pública básica descrita estaticamente em
[`FRONTEND_ANALYSIS.md`](FRONTEND_ANALYSIS.md).

### BHV-002 — autenticação da equipe

O formulário administrativo expôs os campos esperados e um token CSRF. Um POST
com a conta administrativa de homologação criou sessão válida e entregou o
Painel de Controle da Equipe, sem reapresentar o formulário. O resultado
confirma o caminho principal de autenticação documentado em
[`AUTHENTICATION.md`](AUTHENTICATION.md), mas ainda não valida logout, expiração,
2FA, bloqueio por tentativas ou permissões por papel.

O identificador não mudou imediatamente no POST de login. Isso corresponde ao
contrato estático que define uma janela de 10 segundos (`TIME_BOMB`) e regenera
a sessão apenas em uma requisição GET posterior, evitando invalidar dados em
trânsito. Após a janela e uma nova leitura do painel, a rotação foi observada.
O logout encerrou a autenticação e o acesso seguinte ao painel reapresentou o
formulário.

No transporte HTTP local, o cookie foi emitido com `HttpOnly` e
`SameSite=Lax`, mas sem `Secure`. A ausência de `Secure` é compatível com o
endpoint sem TLS usado nesta homologação e não deve ser extrapolada como política
aceitável para uma implantação HTTPS ou de produção.

### BHV-003 — painel e menus administrativos

Com a mesma sessão autenticada, `index.php`, `tickets.php`, `users.php`,
`kb.php` e `dashboard.php` sob `scp/` responderam `200`, sem formulário de
login ou marcador de erro fatal. `admin.php` também respondeu `200` e mudou o
título para o Painel de Controle do Administrador.

A navegação entregue ao administrador expôs as áreas operacionais de tickets,
tarefas, usuários, organizações, diretório, base de conhecimento, respostas
prontas e categorias. O painel administrativo acrescentou configurações,
e-mails, tópicos de ajuda, equipes, agentes, departamentos, funções, SLA,
horários, páginas, formulários, listas, plugins, chaves de API, filtros, logs e
informações do sistema. Essa é a lista observada na instalação inicial, não uma
garantia de visibilidade para outros papéis.

Sem sessão, as páginas centrais de equipe e administração reapresentaram o
formulário de login. Uma chamada direta ao dispatcher `scp/ajax.php` sem rota e
sem sessão retornou `403`, confirmando que ele não entrega uma página pública
genérica.

### BHV-007 — correlação inicial de logs

As navegações não produziram novo conteúdo no log PHP. A amostra existente
continha dois avisos anteriores de carregamento da extensão `sodium`, sem erro
fatal, banco, sessão ou filesystem. No PHP CLI atual, a extensão responde como
habilitada; portanto, os avisos históricos não são atribuídos às requisições
testadas e permanecem como observação de inicialização do Apache a revisar. A
amostra recente do log Apache não apresentou marcador de warning ou erro fatal.

### BHV-003 — varredura administrativa ampliada

Uma passagem autenticada percorreu 31 rotas operacionais e administrativas por
GET. Todas responderam `200`, preservaram a sessão, não reapresentaram o login e
não exibiram marcador de erro fatal. O log PHP não cresceu durante a passagem.
O log configurado do Apache recebeu registros das requisições, sem marcador de
nível `emerg`, `alert`, `crit`, `error`, `warn`, `notice`, `info` ou erro PHP na
amostra correlacionada.

### BHV-006 — primeira passagem AJAX

As rotas autenticadas `/config/scp` e `/config/links` responderam `200` como
`application/json`. `/config/date-format`, buscas de usuários e organizações e
`/content/context` devolveram `400` quando seus parâmetros obrigatórios foram
omitidos; com parâmetros inofensivos, responderam `200`. Os resultados vazios
de busca foram entregues com corpo mínimo e `text/html`, enquanto o contexto de
variáveis usou `application/json`. Isso confirma a heterogeneidade de tipos de
resposta descrita estaticamente.

Sem sessão, `/config/scp` retornou `403`. Um POST autenticado sem token CSRF
retornou `400`, exatamente pelo guard global de `scp/staff.inc.php`.

Os treze alvos classificados estaticamente como ausentes foram alcançados em
runtime e retornaram `500`. A primeira passagem confirmou as quatro rotas de
relatório de visão geral e `/orgs/1`. A segunda usou GET nos contratos de leitura
ou `ANY` e token CSRF válido nos contratos POST/DELETE para lista, plugin,
alteração de usuário do ticket, pesquisas/filas e coluna de fila. Foram usados
IDs e nomes fictícios; como o dispatcher falhou na resolução do método, nenhum
fluxo de persistência foi alcançado. O log PHP não cresceu durante essa passagem.

### BHV-004 — sessão do cliente

Uma identidade fictícia foi importada, recebeu conta local confirmada e teve as
credenciais armazenadas somente no `.env`. A autenticação do cliente entregou
sessão válida para `tickets.php`, `profile.php`, `open.php` e `view.php`, todas
com resposta `200` e sem marcador fatal. A mesma sessão não autenticou
`scp/index.php`, que apresentou o login da equipe. A configuração AJAX pública
do cliente respondeu `200` como JSON.

Uma única tentativa com senha inválida em sessão nova retornou `200`, manteve o
formulário de login e apresentou mensagem genérica. Uma segunda sessão, com a
credencial correta, autenticou normalmente. O mesmo padrão foi observado para o
agente. A amostra confirma resposta não diferenciada e ausência de bloqueio após
uma falha, mas deliberadamente não mede o limiar de lockout.

Token CSRF deliberadamente inválido nos formulários de login público e staff
produziu `302` para a página inicial correspondente. Isso difere do POST sem
CSRF no dispatcher AJAX staff, que já havia retornado `400`; consumidores não
devem presumir contrato uniforme entre formulário e AJAX.

### BHV-005 — agente de visualização

Uma identidade fictícia foi criada pelo fluxo administrativo com departamento
primário Suporte, papel `Apenas visualização` e restrição a itens atribuídos. A
senha foi definida pelo endpoint administrativo nativo e mantida somente no
`.env`. O login funcionou, mas a navegação inicial expôs apenas a área de
tickets. Requisições diretas a páginas administrativas retornaram ao Painel de
Controle da Equipe, sem conteúdo de administração, enquanto a busca AJAX
`/users/staff` respondeu `403`. Isso demonstra que o status HTTP `200` isolado
não prova autorização: títulos, menus e conteúdo final também precisam ser
verificados.

### BHV-CLI-001 — ativação de cliente pelo CLI

Após importar o cliente fictício, `manage.php user --id=<id> activate` terminou
com erro fatal porque `include/cli/modules/user.php:95` chama
`UserAccount::create()`, método inexistente na baseline. O método disponível é
`ClientAccount::createForUser()` e já é usado pelo fluxo web em `account.php`.
O defeito impede ativar via CLI um usuário que ainda não possua conta.

A fixture foi concluída pelo registro web e por atualização direcionada da
própria conta para o backend nativo `client`. Nenhum schema, registro alheio ou
dado real foi alterado. Essa preparação não é evidência do fluxo normal de
registro e permanece separada dos resultados comportamentais do cliente.

### BHV-008 — criação e atribuição inicial do ticket

O cliente autenticado abriu um ticket fictício pelo formulário público com o
tópico `Questões gerais`. O formulário inicial expôs apenas o seletor de tópico;
os campos `subject` e `message` foram carregados pelo AJAX de tópico e possuem
nomes ofuscados por sessão. O POST com CSRF válido respondeu `200`, preservou a
sessão do cliente e criou no banco um ticket aberto (`status_id=1`). A referência
não secreta foi armazenada localmente em `OSTR_TEST_TICKET_NUMBER`.

Antes da atribuição, o agente configurado como `assigned_only` não via o ticket.
O administrador abriu o formulário AJAX de atribuição, selecionou o agente e
recebeu `201`. Na sessão seguinte do agente, o mesmo ticket passou a aparecer na
fila e sua visualização detalhada respondeu `200`, sem erro fatal. Esse contraste
confirma o efeito combinado de atribuição e escopo `assigned_only`.

O papel de visualização não recebeu a ação de resposta. Um POST direto com
`a=reply` foi negado pela verificação de `Ticket::PERM_REPLY` e não criou entrada
de resposta. Entretanto, o mesmo agente publicou uma nota interna: o fluxo
`postnote` exige acesso ao ticket e lock válido, mas não consulta uma permissão
de resposta equivalente. Trata-se de assimetria confirmada, não de inferência
baseada apenas no nome do papel.

O administrador adquiriu o lock pelo endpoint AJAX e publicou uma resposta com
notificação desabilitada, evitando dependência de e-mail. A thread passou a
conter uma resposta. No portal, o cliente visualizou essa resposta e não
visualizou a nota interna, confirmando a fronteira entre entradas públicas e
internas.

O administrador também publicou notas internas alterando o ticket de Aberto
para Resolvido (`status_id=2`) e depois de volta para Aberto (`status_id=1`).
Cada transição usou lock recém-adquirido e foi confirmada na persistência. O
ticket permaneceu disponível como fixture ativa; nenhuma exclusão foi executada.

### BHV-009 — tarefa vinculada e autorização de resposta

O administrador criou pelo endpoint nativo uma única tarefa fictícia vinculada
ao ticket ativo, no departamento Suporte e atribuída ao agente de visualização.
A persistência confirmou o vínculo polimórfico (`object_type=T`), a atribuição e
o estado aberto. A referência não secreta pode ser mantida localmente em
`OSTR_TEST_TASK_NUMBER`; nenhuma tentativa de criação foi repetida depois que o
banco confirmou a primeira operação.

Administrador e agente atribuído visualizaram a tarefa por `scp/tasks.php`,
enquanto o cliente não recebeu seu título na tela do ticket. O agente sem
`task.create` recebeu `403` ao acessar diretamente o formulário AJAX de nova
tarefa. Isso confirma separadamente escopo de leitura, confidencialidade do
portal e permissão de criação.

**Fato observado — falha de enforcement:** o papel sem `task.reply` não recebeu
o formulário `#task_reply`, mas um POST manual para `scp/tasks.php` com
`a=postreply`, CSRF válido e manutenção do estado aberto respondeu `200` e
persistiu exatamente uma entrada `type=R` atribuída ao agente. O controlador
entra em `case postreply` sem consultar o papel
(`scp/tasks.php:47-72`), e `Task::postReply()` também não valida
`Task::PERM_REPLY` (`include/class.task.php:1004-1051`). A permissão aparece
somente na renderização do formulário
(`include/staff/templates/task-view.tmpl.php:560-567`). Portanto, ocultar a ação
na interface não impede a escrita por requisição forjada. O cenário não enviou
e-mail, não fechou a tarefa e não executou exclusão.

O formulário de nota permaneceu visível ao mesmo papel e uma nota controlada foi
persistida. Já o POST direto de fechamento sem `task.close` respondeu `200`,
reapresentou o formulário e manteve a tarefa aberta; o corpo não trouxe
`#msg_error`. O administrador fechou e reabriu a tarefa pelos endpoints AJAX,
recebendo `201` nas duas operações. A coluna `closed` e as notas de transição
confirmaram os estados fechado e aberto; ao fim, a fixture voltou a ficar
aberta. Esses resultados distinguem três contratos: nota permitida, resposta
indevidamente permitida e transição corretamente negada, mas com feedback HTTP
e visual ambíguo.

### BHV-010 — upload, associação e download inicial

Dois arquivos textuais inertes foram enviados: um `.txt` e outro nomeado com
extensão `.php`, mas sem código. Ambos receberam `200` tanto no endpoint da
equipe quanto no portal do cliente. A instalação não possui valor persistido
para `allowed_filetypes`; assim, a configuração efetiva é permissiva e o
resultado não demonstra, por si só, bypass exclusivo do caminho staff.

O TXT foi associado pelo fluxo nativo a uma nota da tarefa. A persistência
confirmou uma associação `ost_attachment.type=H` à entrada da thread. Sua URL
assinada entregou o conteúdo como `Content-Disposition: attachment` ao
administrador e ao cliente autenticado. Sem autenticação, a mesma URL respondeu
`200` com o formulário de login e sem conteúdo de download. Com sessão válida,
alterar a assinatura produziu `404`. Nenhuma URL, chave ou assinatura foi
registrada como evidência.

O arquivo de extensão `.php` não foi associado a uma entrada e permanece como
upload temporário sujeito ao coletor de arquivos órfãos. Não foi executado nem
servido inline. A confirmação de allowlist e limite de tamanho exige cenário
com configuração restritiva controlada; até lá, o parâmetro de bypass no fluxo
staff permanece achado estático condicional.

#### Plano de rollback BHV-010-A — allowlist temporária

**Estado anterior observado:** não existe linha `(namespace=core,
key=allowed_filetypes)` em `ost_config`; o default efetivo aceita qualquer
extensão. **Alvo exclusivo:** essa única chave, sem mudança de schema ou de
outros registros.

Antes do ensaio será criado dump integral local fora do Git, com tamanho não
nulo, SHA-256 e presença verificável da definição e dos dados de `ost_config`.
Depois será inserido temporariamente o valor `.txt`, confirmada exatamente uma
linha e executada a comparação do mesmo TXT e `.php` inerte nos canais cliente
e equipe. A restauração excluirá somente a linha criada, mediante predicado por
namespace, chave e valor esperado. O rollback será aceito apenas se a consulta
confirmar zero linhas e o comportamento permissivo anterior voltar a ser
observável. Se qualquer pré-condição divergir, a exclusão não será executada e o
dump será preservado para restauração manual.

**Resultado do protocolo A:** o dump integral foi criado e verificado por
tamanho, SHA-256, definição e dados de `ost_config`. A chave global `.txt` foi
inserida, mas os dois canais continuaram aceitando ambos os arquivos. O rollback
planejado excluiu exatamente a linha criada e confirmou zero remanescentes. O
ensaio não provou a allowlist porque os campos de thread instalados continuaram
com configuração própria vazia.

#### Plano de rollback BHV-010-B — campos de thread

**Estado anterior observado:** os campos `message` do formulário de ticket e
`description` do formulário de tarefa possuem `configuration` nula. Uma
pré-condição inicialmente escrita para string vazia falhou de forma segura e
nenhuma linha foi alterada. O mesmo
dump integral anterior às mutações cobre essas linhas. O ensaio atualizará
somente esses dois campos para JSON com anexos habilitados, extensão `.txt` e o
limite já vigente, confirmará os valores e comparará os canais. Em bloco final
obrigatório, ambas as linhas serão restauradas para `NULL` por ID e apenas
se o JSON temporário ainda for exatamente o esperado. O rollback exige duas
linhas atualizadas e verificação posterior dos dois valores nulos; divergência
interrompe o teste e preserva o dump. Nenhuma exclusão ou mudança de schema faz
parte deste protocolo.

**Resultado do protocolo B:** a primeira pré-condição para string vazia falhou
sem alterar dados e foi corrigida para `NULL`. Com `.txt` aplicado diretamente
aos dois campos, o TXT recebeu `200` nos dois canais. O `.php` inerte recebeu
`200` no canal staff e foi rejeitado pelo canal cliente com a mensagem
`File type is not allowed`, confirmando que o argumento `true` do caminho staff
contorna a allowlist. A rejeição do cliente saiu como HTTP `500`, porque
`Http::header_code_verbose()` não possui o código `415` e usa `500` no caso
default (`include/class.http.php:17-32`). O bloco `finally` restaurou exatamente
duas configurações para `NULL` e a consulta confirmou os dois valores nulos.

O download do TXT interno também confirmou a lacuna de autorização do objeto
pai: o cliente autenticado, que não visualiza a tarefa nem sua nota interna,
conseguiu consumir a URL assinada obtida na sessão staff. A assinatura continua
necessária e funcionou como capability; o resultado não demonstra descoberta
ou enumeração da URL, mas confirma que `file.php` aceita qualquer cliente
autenticado que a receba sem cruzar acesso ao pai.

### BHV-012 — API HTTP nativa

Antes do cenário, a instalação não possuía chaves de API. O painel nativo criou
uma chave fictícia restrita ao IP local e apenas à criação de tickets. Sem chave
ou com chave inválida, o endpoint JSON respondeu `401`; com a fixture válida,
respondeu `201` e o banco confirmou um ticket `source=API`. O payload usou dados
fictícios e desabilitou alertas e autoresposta.

JSON malformado retornou `400`. A tentativa de executar cron com a mesma chave,
sem `can_exec_cron`, retornou `401`, confirmando a autorização por flag. A chave
foi desativada ao final, sem exclusão, e seu valor não foi registrado. XML,
e-mail e cron autorizado permanecem fora deste checkpoint.

## Exposição local aceita na homologação

### BHV-SEC-001 — instalador acessível após a instalação

**Classificação:** risco aceito exclusivamente para a homologação local.

`/setup/` e `/setup/install.php` responderam `200` e exibiram marcadores do
instalador após a conclusão. No filesystem, o diretório `setup/` continua
presente e `include/ost-config.php` não está marcado como somente leitura.

O comportamento não é tratado como defeito novo do core: o upstream orienta a
remoção de `setup/` e a proteção do arquivo de configuração depois da
instalação. Em 2026-09-04, o responsável decidiu manter `setup/` na homologação
porque o ambiente é acessível somente em sua máquina. A permanência é aceita
apenas neste contexto, não constitui orientação para produção e deve ser
reavaliada se o serviço passar a aceitar conexões externas.

## Pendências imediatas

1. aprofundar as páginas administrativas sem alterar configuração;
2. ampliar a amostra de rotas AJAX válidas por família;
3. validar expiração longa e limiar de tentativas somente com protocolo próprio;
4. concluir limites, MIME e acesso cruzado de anexos sob configuração controlada;
5. classificar e revisar independentemente a falha de `task.reply`.
