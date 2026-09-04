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

Por GOV-015, a passagem atual observa o uso funcional normal da interface em
conjunto com seus efeitos no backend. Testes ofensivos ou dedicados a explorar
controles de segurança não serão ampliados nesta onda e pertencem a uma fase
posterior ao mapeamento e ao inventário completos. Os achados já confirmados
continuam registrados, mas não orientam os próximos cenários funcionais.

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
| BHV-013 | exportação PDF | administrador/agente/cliente | leitura | concluído | MIME, assinatura e acesso por papel |
| BHV-014 | buscas, filtros e ordenação | administrador/cliente | leitura | concluído | resultado positivo/negativo e controles de lista |
| BHV-015 | base de conhecimento | administrador/anônimo | mutável | concluído | categoria, artigo, publicação, configuração e busca |
| BHV-016 | respostas prontas | administrador | mutável/leitura | concluído | cadastro, listagem e carregamento no editor |
| BHV-017 | atribuição e liberação | administrador/agente | mutável | concluído | eventos, visibilidade e restauração da fixture |
| BHV-018 | abertura anônima e colaboração | anônimo/admin/cliente | mutável | concluído | validação, associação, visibilidade e resposta |
| BHV-019 | edição do perfil do cliente | cliente | mutável | concluído | formulário dinâmico, persistência e restauração |
| BHV-020 | organização e vínculo de usuário | administrador | mutável | concluído | criação, associação e agregação de ticket |
| BHV-021 | edição e nota de organização | administrador | mutável | concluído | atualização reversível e nota persistente |
| BHV-022 | edição do ticket | administrador | mutável | concluído | assunto dinâmico alterado e restaurado |
| BHV-023 | administração de usuário | administrador | mutável | concluído | edição reversível e nota persistente |

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

Em uma passagem posterior, o log PHP continuou sem crescimento, mas o log de
erros do Apache continha repetições sem timestamp de `VirtualProtect() failed
[87] Parâmetro incorreto`. As linhas não trazem rota, stack trace ou marcador do
osTicket; três leituras públicas isoladas imediatamente depois não aumentaram o
arquivo. A origem e a correlação permanecem ambientais e indeterminadas, sem
ser atribuídas a um fluxo funcional específico.

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

Pelo fluxo normal do portal, o cliente publicou depois uma nova mensagem
fictícia. O POST com o token e o nome ofuscado do campo retornou `200`; a mensagem
apareceu imediatamente tanto na tela do cliente quanto na visão staff. O backend
criou exatamente uma entrada pública `type=M` vinculada ao usuário, atualizou
`thread.lastmessage` para a mensagem mais recente e manteve o ticket aberto com
`isanswered=0`, coerente com item aguardando resposta da equipe. Não houve novo
registro no log PHP nem erro do osTicket identificado no log Apache; as mensagens
ambientais `VirtualProtect()` são tratadas separadamente em BHV-007.

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

A negação acima vale somente para o endpoint AJAX de mudança de status. O
revisor independente identificou que `postreply` e `postnote` encaminham
`task:status` a `Task::setStatus()` sem validar a permissão de transição. Um novo
POST forjado do agente, combinando `a=postreply` e `task:status=closed`, respondeu
`200`, persistiu a resposta e fechou a tarefa mesmo sem `task.reply` ou
`task.close`. O administrador reabriu a fixture por AJAX com `201`, e a leitura
do banco confirmou o retorno ao estado aberto. A escalada composta está
confirmada em runtime.

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

### BHV-011 — pré-condição do transporte de e-mail

A inspeção não destrutiva encontrou zero conta SMTP cadastrada ou ativa em
`email_account`. O fallback do PHP aponta para `localhost:25`, sem
`sendmail_path` e sem `mail.log`; uma conexão TCP local à porta 25 falhou. Esse
estado impede entrega externa pela configuração observada, mas também não
oferece um coletor capaz de provar destinatários, cabeçalhos e quantidade de
mensagens.

A abertura pública já executada não produziu `Mailer Error` em `syslog` no
intervalo correlato nem em toda a tabela. Ausência de log não prova envio nem
supressão, portanto BHV-011 permanece pendente. O próximo ensaio de notificação
exige um coletor SMTP estritamente local, observável e sem relay; até lá nenhum
novo envio será disparado.

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

### BHV-013 — exportação PDF

As exportações do ticket na sessão administrativa, da tarefa nas sessões de
administrador e agente atribuído e do ticket no portal do cliente responderam
`200` como `application/pdf`. Os quatro corpos começaram por `%PDF`, tiveram
tamanho não nulo e foram submetidos a extração textual e inspeção visual local.

O PDF do cliente contém a mensagem inicial e a resposta pública, sem título ou
thread da tarefa, nota interna, resposta forjada ou referência ao anexo interno.
O PDF do ticket staff também não incorpora o conteúdo da tarefa vinculada. Os
PDFs da tarefa para administrador e agente atribuído têm duas páginas legíveis e
incluem o histórico da própria tarefa, como notas internas, resposta forjada,
registro do anexo e transições de fechamento e reabertura. O resultado confirma
a filtragem entre exportação de ticket e de tarefa no cenário observado; não
generaliza a conclusão para outros objetos ou permissões. Os documentos ficaram
somente na área local ignorada pelo Git e não integram as evidências versionadas.

### BHV-014 — buscas, filtros e ordenação

Na lista do cliente, a visualização padrão e a busca pelo número mostraram o
ticket ativo; um termo inexistente retirou a linha. Depois de limpar o estado de
busca mantido na sessão, o filtro `open` mostrou a fixture e `closed` não a
mostrou. O tópico correto incluiu o ticket, um tópico inexistente o excluiu, e
as ordenações ascendente e descendente por número responderam `200` preservando
a linha. Com uma única fixture visível, o teste confirma o contrato e o estado
de sessão, mas não compara a posição relativa de múltiplos itens.

No SCP, lista padrão e busca simples pelo número mostraram o ticket, enquanto o
termo inexistente não produziu sua linha. O lookup AJAX pelo número respondeu
`200` com resultados estruturados e incluiu a fixture; o termo inexistente
retornou lista vazia. O diálogo AJAX de busca avançada respondeu `200` com seu
formulário. Nenhuma dessas operações alterou registros.

### BHV-015 — base de conhecimento

O estado inicial tinha zero categorias, FAQs e vínculos com tópicos; a
configuração global `enable_kb=0` ocultava o módulo público. Pelo formulário
administrativo, foi criada uma categoria pública fictícia e uma FAQ inicialmente
interna, vinculada ao tópico geral. A persistência confirmou uma categoria, uma
FAQ e um vínculo. A rota pública não exibiu o artigo enquanto ele estava
interno.

A mesma FAQ foi editada pelo formulário normal para `ispublished=1`. O banco
registrou a mudança, mas a rota pública continuou redirecionando para a página
inicial enquanto `enable_kb` permanecia desligado. Isso confirma que publicação
do artigo e habilitação global são condições independentes e cumulativas.

O formulário administrativo de configurações foi então salvo com Base de
Conhecimento ativa, Respostas Prontas preservadas e exigência de login
desativada. Depois disso, o menu público expôs `/kb/index.php`, a página inicial
da base listou a categoria, a página da categoria listou a FAQ, o artigo mostrou
pergunta e resposta e a busca pelo marcador retornou o item, todos em sessão
anônima. As fixtures e a configuração ativa foram mantidas para os próximos
cenários; nenhuma exclusão ocorreu.

### BHV-016 — respostas prontas

Com Respostas Prontas habilitadas, o formulário administrativo criou uma
fixture ativa, global a todos os departamentos e sem anexos. Ela apareceu na
listagem e foi preservada para reutilização. A persistência confirmou o terceiro
registro da instalação, com `isenabled=1` e `dept_id=0`.

Os endpoints AJAX genérico da Base de Conhecimento e contextual do ticket foram
chamados nos formatos JSON e texto. Os quatro responderam `200` e carregaram o
conteúdo fictício. JSON retornou as chaves `files`, `id`, `response` e `title`;
texto entregou o corpo que o editor pode inserir diretamente. O checkpoint não
submeteu o formulário de resposta do ticket, portanto não criou thread nem
tentou enviar e-mail.

### BHV-017 — atribuição e liberação de ticket

O administrador exerceu os endpoints normais de liberação e atribuição sobre o
ticket aberto. Na repetição com precondição isolada, o agente `assigned_only`
não abriu o ticket atribuído ao administrador; liberar a atribuição respondeu
`201` e manteve o item invisível; atribuí-lo ao agente respondeu `201` e tornou a
tela visível; reatribuí-lo ao administrador respondeu `201` e voltou a ocultá-la.

O banco terminou restaurado com o administrador como `staff_id` e sem equipe,
registrando eventos `released` e `assigned`. A primeira execução encontrou uma
visibilidade residual da história anterior da fixture antes do ciclo; completar
a reatribuição ao administrador normalizou o estado, e a repetição seguinte foi
determinística. O resíduo não foi investigado como segurança por GOV-015. Não
houve exclusão, comentário de atribuição ou e-mail.

### BHV-018 — abertura anônima e colaboração

O formulário público com tópico geral rejeitou endereços fictícios terminados em
`.invalid` e `.test`, exibiu erro de e-mail e não criou usuário ou ticket. Com um
endereço igualmente fictício sob o domínio reservado `example.com`, o mesmo
fluxo respondeu `200` e persistiu um novo usuário, um ticket Web aberto e sua
thread. Isso registra uma diferença funcional do validador, sem tratar a escolha
de TLD como teste de segurança.

Antes da colaboração, o cliente autenticado existente não recebeu a linha desse
novo ticket e não abriu sua tela direta. Uma primeira tentativa com URL de POST
incompatível retornou `400` sem associação. O POST na rota correta do diálogo
staff respondeu `200` e criou exatamente um `thread_collaborator` para o cliente,
preservando o proprietário e o estado do ticket. Em seguida, lista e tela ficaram
visíveis no portal.

O colaborador publicou uma mensagem pelo formulário normal. Ela apareceu no
portal, gerou exatamente uma entrada `type=M` ligada ao usuário colaborador e
atualizou `thread.lastmessage`. O proprietário original não mudou; ticket
aberto e `isanswered=0` também foram preservados. As fixtures permanecem para
cenários posteriores, sem exclusão.

### BHV-019 — edição e restauração do perfil do cliente

O cliente autenticado abriu `profile.php` e recebeu os campos de contato com
nomes dinâmicos, além de preferências e credenciais. O campo textual de nome foi
alterado para um marcador fictício pelo POST normal com CSRF. A operação
respondeu `200`, redirecionou para `tickets.php` e o valor reapareceu ao reabrir
o perfil, confirmando o ciclo frontend/backend.

Uma segunda submissão restaurou exatamente o valor original e repetiu o
redirecionamento. A leitura final do banco confirmou o cliente existente e zero
linha com o marcador temporário. O fluxo corresponde a `profile.php:30-36`, que
atualiza primeiro a conta e depois chama `User::updateInfo()`, e à validação dos
campos editáveis em `include/class.user.php:540-599`. Não houve alteração de
senha, e-mail, exclusão ou envio de notificação.

O log PHP não foi modificado pelo ensaio. O log do Apache continuou recebendo
somente ocorrências ambientais de `VirtualProtect() failed [87]`, sem rota,
timestamp ou stack correlacionável ao perfil.

### BHV-020 — criação de organização e associação de usuário

O diálogo administrativo `ajax.php/orgs/add` retornou o formulário dinâmico da
organização. A submissão normal com CSRF criou uma organização fictícia e
respondeu `201 application/json`. A listagem reconheceu a fixture na repetição,
evitando duplicação, e `orgs.php?id={id}` apresentou o nome cadastrado.

O endpoint `ajax.php/users/{id}/org` recebeu o identificador da organização e
respondeu `201 application/json`. Depois da associação, a tela da organização
mostrou o usuário e também o ticket cujo proprietário é esse usuário. No banco
há exatamente uma organização com o marcador, um usuário associado e um ticket
herdado. A organização e a associação foram preservadas como fixtures; nenhuma
remoção, exclusão ou notificação foi executada.

O comportamento confirma que `User::setOrganization()` persiste a relação
(`include/class.user.php:143-157`), enquanto a aba de tickets deriva seu conjunto
por `ticket.user__org` (`include/staff/templates/tickets.tmpl.php:15-19`), sem
copiar `org_id` para o ticket.

### BHV-021 — edição reversível e nota de organização

O diálogo `ajax.php/orgs/{id}/edit` carregou o formulário dinâmico já
preenchido. O nome foi trocado temporariamente e restaurado em uma segunda
submissão; ambas responderam `201 application/json`, e a resposta final trouxe
o nome original. O banco permaneceu com uma única organização sob o marcador
definitivo.

O painel de notas submeteu um texto fictício a
`ajax.php/orgs/{id}/note`. A resposta `200` devolveu o fragmento HTML da nota, e
a persistência final confirmou exatamente uma linha em `note` com `ext_id=O{id}`.
A repetição consulta primeiro o painel e não duplica a fixture. A nota é mantida
como histórico funcional; não houve exclusão nem notificação.

### BHV-022 — edição reversível do ticket

O formulário tradicional `scp/tickets.php?id={id}&a=edit` foi carregado com os
campos centrais e dinâmicos do ticket. O campo cujo valor correspondia ao
assunto foi alterado para um marcador fictício e submetido com o restante do
estado selecionado. O POST respondeu `200`, redirecionou dentro de `/scp` e o
marcador reapareceu no formulário de edição.

Uma segunda submissão restaurou o assunto original. A releitura do formulário e
da visão materializada `ticket__cdata` confirmou o original e zero ocorrência
do marcador temporário. O ensaio não alterou proprietário, organização, estado
ou conteúdo da thread e não executou exclusão ou envio de e-mail.

A primeira versão local do executor resolveu a ação relativa `tickets.php` na
raiz pública e recebeu o login do cliente; o banco confirmou que essa tentativa
não persistiu nada. A correção para o contexto `/scp` demonstra que ações
relativas dependem do shell de origem e precisam ser preservadas em qualquer
reprodução futura.

### BHV-023 — edição administrativa e nota de usuário

O administrador abriu `ajax.php/users/{id}/edit`, alterou temporariamente o nome
do usuário anônimo e restaurou o valor original pelo endpoint
`ajax.php/users/{id}`. As duas respostas foram `201 application/json` e
refletiram o valor correspondente. A verificação final no banco encontrou o
nome original, zero marcador temporário e a organização anterior preservada.

O painel do usuário recebeu uma nota fictícia por
`ajax.php/users/{id}/note`. A resposta `200` devolveu o fragmento HTML e a tabela
`note` reteve exatamente um registro com `ext_id=U{id}`. A repetição evita
duplicação após consultar a tela. O ensaio não alterou conta, senha, endereço de
e-mail, tickets ou colaboração e não executou exclusão ou notificação.

Comparado a BHV-019, o backend administrativo chama o mesmo
`User::updateInfo()`, mas com o modo staff e contrato AJAX/JSON; o
autoatendimento usa POST tradicional e redirecionamento.

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

1. reproduzir os fluxos normais ainda não cobertos de ticket e administração;
2. caracterizar efeitos de notificações sem entrega externa acidental;
3. ampliar os fluxos administrativos de leitura e edição não destrutiva;
4. consolidar filas e paginação com o conjunto crescente de fixtures.
