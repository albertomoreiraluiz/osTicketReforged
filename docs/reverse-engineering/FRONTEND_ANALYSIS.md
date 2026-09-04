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
`application/pdf`, prefixo `%PDF` e corpo não vazio. O resultado confirma o
gerador e os guards básicos, mas a paridade e a filtragem de conteúdo interno
exigem inspeção documental controlada posterior.
