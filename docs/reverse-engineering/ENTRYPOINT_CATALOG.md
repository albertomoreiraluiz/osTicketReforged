# Catálogo inicial de entrypoints

## Portal do usuário

Foram localizados 20 arquivos PHP na raiz da baseline. Eles incluem páginas de
entrada, autenticação, recuperação de senha, abertura e consulta de tickets,
arquivos, avatar, captcha e bootstrap compartilhado.

| Categoria | Arquivos principais | Bootstrap ou guarda |
| --- | --- | --- |
| Entrada pública | `index.php`, `open.php`, `view.php`, `kb/*`, `pages/index.php` | `client.inc.php` |
| Conta | `login.php`, `logout.php`, `account.php`, `profile.php`, `pwreset.php` | `client.inc.php` ou `secure.inc.php` conforme fluxo |
| Ticket autenticado | `tickets.php` | `secure.inc.php` |
| Binários/recursos | `file.php`, `avatar.php`, `logo.php`, `captcha.php` | guardas específicas a rastrear |
| AJAX | `ajax.php` | `client.inc.php` + `Dispatcher` |

## Painel da equipe

Foram localizados 46 scripts PHP diretamente sob `scp/`, além de
`scp/apps/dispatcher.php`. A superfície inclui tickets, tarefas, usuários,
organizações, conhecimento, relatórios, equipes, departamentos, papéis,
plugins, formulários, filas, SLA e configuração.

`scp/staff.inc.php` é o bootstrap autenticado comum observado. Ele:

1. inclui `main.inc.php`;
2. aplica ACL da superfície `staff`;
3. resolve `StaffAuthenticationBackend::getUser()`;
4. verifica agente, grupo e estado do sistema;
5. exige CSRF em `POST`, `PUT`, `PATCH` e `DELETE`;
6. carrega navegação e aplica requisitos de senha e 2FA.

O login possui fluxo separado em `scp/login.php` e não depende de uma sessão de
agente já autenticada.

## API e integrações

| Entry point | Papel observado | Próxima análise |
| --- | --- | --- |
| `api/http.php` | API HTTP despachada | rotas, API keys, formatos e erros |
| `api/cron.php` | execução de cron | autenticação, exclusão mútua e efeitos |
| `api/pipe.php` | entrada por pipe de e-mail | parsing, identidade e criação de objetos |
| `api/index.php` | proteção/entrada do diretório | comportamento do servidor web |
| `apps/dispatcher.php` | aplicações registradas | seleção, autenticação e extensão |
| `scp/apps/dispatcher.php` | aplicações no contexto da equipe | controles herdados do SCP |

## CLI

`manage.php` carrega 14 módulos em `include/cli/modules/`: `agent`, `cron`,
`deploy`, `export`, `file`, `i18n`, `import`, `list`, `org`, `package`, `serve`,
`unpack`, `upgrade` e `user`.

**Fato observado:** esses módulos são superfícies executáveis administrativas,
não apenas bibliotecas. Efeitos e requisitos serão classificados antes de usar
qualquer comando mutável.

## Dispatchers observados

- API HTTP: `api/http.php` cria padrões, emite `Signal::send('api', ...)` e
  resolve o caminho.
- AJAX do usuário: `ajax.php` emite `ajax.client` antes da resolução.
- AJAX da equipe: `scp/ajax.php` agrega dezenas de grupos de rotas.
- Funções de construção: `patterns()`, `url()`, `url_post()`, `url_get()` e
  `url_delete()` em `include/class.dispatcher.php:179-204`.

O catálogo completo de rotas é trabalho posterior; esta unidade identifica os
registradores e fronteiras que deverão ser percorridos.
