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
| BHV-008 | ticket de teste | papéis fictícios | mutável | pendente | ciclo e persistência controlados |
| BHV-009 | anexos, e-mail e tarefas | papéis fictícios | mutável | pendente | efeitos controlados por subsistema |

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
3. validar expiração e tentativas inválidas sem acionar bloqueio destrutivo;
4. aprofundar a matriz de permissões com tickets atribuídos e não atribuídos;
5. iniciar o ciclo funcional de ticket com rastreabilidade.
