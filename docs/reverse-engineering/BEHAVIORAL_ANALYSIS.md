# Análise comportamental da baseline

## Escopo e método

Este documento confronta a análise estática da baseline `v1.18.4` com uma
instalação descartável de homologação. Cada observação deve registrar data,
papel, superfície, ação, resultado e relação com o contrato estático, sem
copiar credenciais, cookies, tokens, dados pessoais ou conteúdo sensível.

A execução começa por navegação somente leitura. Cenários que criem, editem ou
excluam registros são classificados separadamente e devem usar dados fictícios,
prefixo identificável e as barreiras locais aprovadas.

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
| Segredos em evidências | nenhum valor, cookie ou token registrado |

## Matriz de execução

| ID | Superfície | Papel | Natureza | Estado | Evidência esperada |
| --- | --- | --- | --- | --- | --- |
| BHV-001 | página inicial pública | anônimo | leitura | concluído | status, título e navegação |
| BHV-002 | login `scp` | administrador | sessão | concluído | CSRF presente e painel autenticado |
| BHV-003 | painel e menus da equipe | administrador | leitura | concluído na passagem inicial | telas, links e guards observados |
| BHV-004 | portal do cliente | anônimo/cliente | leitura/sessão | pendente | login, consulta e guards |
| BHV-005 | matriz de permissões | agente/admin | leitura | pendente | diferenças de menu e resposta |
| BHV-006 | AJAX/PJAX | administrador/agente | leitura | pendente | contrato HTTP e erros sanitizados |
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

## Pendências imediatas

1. aprofundar as páginas administrativas sem alterar configuração;
2. validar expiração e tentativas inválidas sem acionar bloqueio destrutivo;
3. preparar identidades fictícias por papel antes da matriz de permissões;
4. confrontar rotas AJAX de leitura com o catálogo estático;
5. classificar os cenários mutáveis antes de executá-los.
