# Frontend PHP existente — composição estática

## Dois shells distintos

| Superfície | Shell | Navegação | Atualização |
| --- | --- | --- | --- |
| Cliente | `include/client/header.inc.php` + conteúdo + `footer.inc.php` | `UserNav` | páginas completas e AJAX selecionado |
| SCP | `include/staff/header.inc.php` + conteúdo + `footer.inc.php` | `StaffNav`/`AdminNav` | página completa, PJAX, AJAX e modais |

O portal usa HTML5 e tema fixado em `assets/default`
(`client.inc.php:21-33`; `include/client/header.inc.php:17-174`). O SCP declara
HTML 4.01 e possui ciclo PJAX próprio
(`include/staff/header.inc.php:8-119`; `scp/js/scp.js:1138-1192`).

**Inferência sustentada:** cliente e SCP não são apenas temas visuais; possuem
shells, navegação, bibliotecas e ciclos de atualização diferentes.

## Composição do portal

Controladores raiz escolhem includes PHP e os envolvem no shell. Exemplos:

- `open.php:84-102`: agradecimento ou `open.inc.php`;
- `tickets.php:126-149`: editar, visualizar, listar ou abrir;
- `account.php:21-138`, `login.php:29-153`, `pwreset.php:10-98` e
  `profile.php:39-43`: controlador + include variável + shell.

`UserNav` materializa links segundo configuração e sessão: home, KB, abertura,
tickets e status (`include/class.nav.php:289-363`). Conteúdo de landing,
offline e agradecimento vem de configuração/banco
(`include/class.config.php:738-769`).

Formulários são gerados pelo domínio. Widgets coletam e emitem CSS/JS, e campos
rich text/draft/upload atravessam PHP, AJAX e JavaScript
(`include/class.forms.php:260-328,1252-1267,5194-5248`).

## Composição do SCP

`scp/staff.inc.php:64-135` autentica, atualiza sessão, injeta CSRF e cria a
navegação. `scp/admin.inc.php:16-66` exige administrador e troca para
`AdminNav`. Tabs e submenus dependem de permissões e apps registrados
(`include/class.nav.php:18-287`).

Sob `HTTP_X_PJAX`, o header omite o documento completo e devolve título/scripts.
`scp/js/scp.js` intercepta links, gerencia ciclo PJAX, abre fragmentos em modal
e adiciona CSRF às mutações same-origin
(`include/staff/header.inc.php:8-102`; `scp/js/scp.js:565-705,1138-1192`).

## Assets e bibliotecas

| Árvore | Contagem observada |
| --- | ---: |
| `include/client/` | 28 arquivos, 8 templates |
| `include/staff/` | 186 arquivos, 107 templates |
| `assets/default/css` / `less` | 3 / 9 |
| `css/` / `js/` | 33 / 14 |
| `scp/css/` / `scp/js/` | 7 / 16 |

Entre as bibliotecas carregadas estão jQuery 3.7.0, jQuery UI 1.13.2, Select2
4.0.13, Redactor, filedrop, bootstrap-typeahead, Font Awesome e PJAX. Não há
manifesto Node/build na raiz para recompilar LESS; a baseline contém fontes,
CSS compilado e instrução textual em `assets/default/about-custom-themes.md`.

O portal combina CSS global com `assets/default/css/theme.css`; o SCP usa
`scp/css/scp.css`. Logos são servidos por `logo.php` e `scp/logo.php`, com
fallbacks próprios.

## Contratos implícitos relevantes

- IDs/classes DOM como `#container`, `#content`, `#nav`, `#pjax-container`,
  `#overlay` e `.dialog#popup`;
- caminhos AJAX relativos consumidos diretamente por JS;
- fragmentos HTML, scripts e título como contrato PJAX;
- mídia de widgets e `getExtraHeaders()/getExtraPjax()`;
- configuração, sessão e permissões alterando navegação e composição.

Esses contratos deverão ser observados antes de definir qualquer frontend novo.
Esta análise não escolhe Angular/PrimeNG nem arquitetura de integração.

## Lacunas comportamentais adiadas

- telas/estados por papel, permissão e configuração;
- responsividade, teclado, foco, ARIA e contraste;
- histórico/back-forward e reexecução de scripts no PJAX;
- modais, editor, drafts, uploads e erros;
- branding/conteúdo persistido, plugins e assets injetados;
- compatibilidade entre navegadores.

Observações estáticas de markup obsoleto ou layout tabular não provam falha de
acessibilidade; a auditoria dinâmica ocorrerá após a instalação.

## Confirmação inicial de exportação — Onda 7

Ticket staff, tarefa administrativa, tarefa do agente atribuído e ticket do
cliente foram exportados pelas rotas nativas. Todos responderam `200`, MIME
`application/pdf`, prefixo `%PDF` e corpo não vazio. Extração textual e inspeção
visual confirmaram documentos legíveis: o ticket do cliente expõe somente a
conversa pública, e nem ele nem o ticket staff incorporam a thread da tarefa. As
duas exportações da tarefa exibem o histórico interno desse objeto, inclusive
notas, anexo e mudanças de estado, conforme o acesso do administrador e do
agente atribuído no cenário testado.

## Listas e buscas confirmadas — Onda 7

O portal manteve filtros de tickets na sessão do cliente. Busca positiva e
negativa, estados aberto/fechado, tópico presente/ausente e ordenação por número
foram reproduzidos pela interface HTTP. Limpar os filtros foi necessário antes
de alternar estado ou tópico, confirmando que os controles se compõem sobre o
estado salvo em `client:Q`.

No SCP, a busca simples criou uma fila ad hoc: número existente retornou a linha
do ticket e termo inexistente não retornou a fixture. O typeahead AJAX entregou
JSON estruturado para o número e lista vazia para o termo ausente; a busca
avançada carregou como formulário AJAX. Esses resultados confirmam os dois
caminhos de pesquisa observáveis sem criar uma fila salva.

## Base de Conhecimento confirmada — Onda 7

Categoria pública e FAQ publicada não bastam para disponibilizar o módulo quando
`enable_kb=0`: as rotas sob `/kb/` retornam à página inicial. Depois de habilitar
o módulo pelo formulário administrativo, o shell público passou a incluir o link
da Base de Conhecimento. Em sessão anônima, índice, categoria, artigo e busca
renderizaram a fixture. A mudança demonstra que navegação e roteamento público
dependem simultaneamente da configuração global e da existência de conteúdo
publicado.

## Respostas Prontas confirmadas — Onda 7

O cadastro administrativo produziu uma resposta ativa e global. No editor de
ticket, o frontend pode obtê-la tanto pelo endpoint genérico da Base de
Conhecimento quanto pelo endpoint contextual do ticket. As variantes JSON
entregam metadados e corpo; as variantes de texto entregam HTML pronto para o
editor. O carregamento é independente da submissão final da resposta.

## Colaboração no portal confirmada — Onda 7

Um cliente que não era proprietário inicialmente não recebeu o segundo ticket
na lista nem abriu sua tela. Depois da inclusão pelo diálogo staff, o mesmo shell
do cliente passou a listar e renderizar o ticket sem novo login. O formulário de
resposta permaneceu o mesmo e a mensagem do colaborador surgiu na thread. Assim,
o frontend do portal compõe a lista com propriedade e colaboração, enquanto o
backend preserva o proprietário original.

## Perfil do cliente confirmado — Onda 7

`profile.php` renderiza os dados de contato por formulários dinâmicos: os nomes
HTTP dos campos variam entre requisições e não equivalem diretamente a `name`,
`email` ou `phone`. O fluxo normal com CSRF alterou um nome fictício,
redirecionou para a lista de tickets e apresentou o novo valor ao reabrir a
tela. A repetição restaurou o valor original, demonstrando que integrações
futuras deverão descobrir ou reproduzir o contrato dos formulários, em vez de
fixar nomes de parâmetros observados em uma única resposta.

## Organização e agregação confirmadas — Onda 7

O shell staff abre a criação de organização em um diálogo AJAX, envia CSRF e
consome a resposta `201` em JSON. A página de detalhe combina três painéis:
usuários, tickets e notas. Depois de associar um usuário existente, a mesma tela
passou a apresentar o usuário e o ticket de sua propriedade. O painel de
tickets, portanto, é uma visão derivada da organização atual do proprietário,
e não uma associação gravada diretamente no ticket.

O link de edição abre outro diálogo AJAX e espera `201` em JSON; o ciclo
temporário/restauração confirmou que o mesmo contrato serve às duas alterações.
Já o painel de notas recebe um fragmento HTML com `200` e o injeta na coleção da
tela. A área administrativa combina, portanto, respostas JSON para entidade e
HTML para conteúdo incremental na mesma página.

## Edição tradicional de ticket confirmada — Onda 7

A edição de ticket não usa o padrão de diálogo AJAX da organização. O formulário
completo envia uma ação relativa `tickets.php?...` e retorna `200` após o
redirecionamento. Essa URL só é correta quando resolvida a partir do shell
`/scp`; resolvê-la a partir da raiz conduz ao portal público. O assunto dinâmico
foi alterado e restaurado, confirmando que o frontend deve preservar tanto os
campos atuais quanto o contexto da URL relativa.

## Administração do usuário confirmada — Onda 7

O detalhe do usuário combina edição em diálogo AJAX, organização, tickets e
notas. A edição retornou JSON com `201`, enquanto a nota retornou HTML com `200`,
o mesmo padrão heterogêneo observado na organização. Já o perfil do próprio
cliente usa página e redirecionamento tradicionais. O frontend futuro precisará
distinguir esses contratos mesmo quando ambos terminam em
`User::updateInfo()`.

## Perfil do agente confirmado — Onda 7

`scp/profile.php` mantém outro contrato tradicional: formulário completo, POST
na própria página e resposta `200`, sem JSON. A preferência de fuso foi alterada
e restaurada em duas submissões. Esse comportamento contrasta com os diálogos
AJAX de administração de usuário, embora ambos pertençam ao shell staff.
