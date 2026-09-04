# Inventário inicial da baseline

## Identificação

| Item | Fato observado |
| --- | --- |
| Baseline | tag `v1.18.4`, commit `8d38b0619649a50ee7cbbf37085f5d297fdc6f36` |
| Arquivos rastreados | 2.266, obtidos com `git ls-tree -r --name-only <baseline>` |
| PHP | 1.522 arquivos |
| SQL | 100 arquivos: um esquema de instalação e 99 scripts do fluxo de upgrade |
| JavaScript/CSS/Less | 57 / 27 / 9 arquivos |
| Maior árvore | `include/`, com 1.835 arquivos, incluindo core e bibliotecas incorporadas |

As contagens descrevem a árvore Git da baseline, não o diretório de trabalho do
Reforged.

Para reproduzir o commit de uma tag anotada, use `git rev-parse
v1.18.4^{commit}`. `git rev-parse v1.18.4` retorna o objeto da tag, não o
commit referenciado.

## Superfícies executáveis localizadas

| Superfície | Entry point ou bootstrap | Evidência inicial |
| --- | --- | --- |
| Portal do usuário | `index.php`, `open.php`, `tickets.php`, `view.php` | os entrypoints carregam `client.inc.php`; `open.php:51` chama `Ticket::create()` |
| Painel da equipe | `scp/index.php`, `scp/login.php`, demais scripts em `scp/` | `scp/index.php:17` encaminha a `tickets.php`; `scp/staff.inc.php:20` carrega `main.inc.php` e aplica autenticação/CSRF |
| API HTTP | `api/http.php` | registra rotas de tickets e cron e emite o sinal `api` antes da resolução (`api/http.php:16-29`) |
| AJAX do usuário | `ajax.php` | monta dispatcher próprio e emite `ajax.client` (`ajax.php:22-51`) |
| AJAX da equipe | `scp/ajax.php` | concentra grupos de rotas para KB, formulários, usuários, tickets e outras áreas |
| CLI | `manage.php` | exige CLI, carrega módulos em `include/cli/modules/` e instancia `Manager` (`manage.php:21-80`) |
| Instalação e upgrade | `setup/`, `setup/install.php`, `setup/upgrade.php` | esquema inicial em `setup/inc/streams/core/install-mysql.sql` |
| Cron e entrada por e-mail | `api/cron.php`, `api/pipe.php` | entrypoints distintos dentro da superfície `api/` |
| Aplicações/plugins | `apps/dispatcher.php` e `include/class.plugin.php` | `PluginManager` em `include/class.plugin.php:175`; `Plugin` em `include/class.plugin.php:543` |

## Estrutura principal

| Diretório | Papel inicialmente observado | Estado da análise |
| --- | --- | --- |
| `include/` | classes do domínio, ORM, autenticação, dispatchers, templates e dependências incorporadas | localizado; classificação interna pendente |
| `scp/` | controladores e páginas do Staff Control Panel | entrypoints localizados |
| `api/` | HTTP API, cron e pipe de e-mail | entrypoints localizados |
| `setup/` | instalador, upgrader, esquema inicial e testes históricos | fontes localizadas |
| `apps/` | despacho de aplicações registradas | aprofundamento pendente |
| `pages/` e `kb/` | páginas públicas e base de conhecimento | aprofundamento pendente |
| `assets/`, `css/`, `js/`, `images/` | recursos do frontend PHP existente | inventário detalhado futuro |

## Núcleos técnicos localizados

- **Bootstrap global:** classe `Bootstrap` em `bootstrap.php:3`; inicialização em
  `bootstrap.php:393`.
- **Inicialização da aplicação:** `main.inc.php:23-34` executa bootstrap,
  configuração, tabelas, i18n, carregamento de código, conexão e
  `osTicket::start()`.
- **Aplicação:** classe `osTicket` em `include/class.osticket.php:30` e método
  `start()` em `include/class.osticket.php:676`.
- **Sessões:** orquestração em `include/class.ostsession.php` e handlers em
  `include/class.session.php`.
- **Domínio de tickets:** classe `Ticket` em `include/class.ticket.php:39`,
  baseada em `VerySimpleModel` e associada a `RestrictedAccess`, `Threadable` e
  `Searchable`.
- **Autenticação:** famílias `StaffAuthenticationBackend` e
  `UserAuthenticationBackend` em `include/class.auth.php:580` e
  `include/class.auth.php:800`.
- **Extensão:** sinais em `include/class.signal.php`; plugins em
  `include/class.plugin.php`; dispatch genérico em `include/class.dispatcher.php`.
- **Banco:** constantes de tabelas derivadas do prefixo em
  `Bootstrap::defineTables()` (`bootstrap.php:72`); esquema inicial e cadeia de
  patches SQL localizados.

## Limites deste documento

Esta unidade confirma localização e encadeamento inicial. Ainda não confirma:

- contratos completos de classes ou tabelas;
- todas as rotas e controles de autorização;
- cobertura e estabilidade dos hooks;
- compatibilidade dos ERDs oficiais com `v1.18.4`;
- fronteiras seguras para customização.

O CLI constitui uma exceção ao bootstrap web: `manage.php` carrega
`bootstrap.php` e `cli.inc.php`, e cada módulo decide se conecta ao banco ou
executa `osTicket::start()`.
