# Matriz de evidências do inventário

| Frente | Evidência atual | Estado | Próxima verificação |
| --- | --- | --- | --- |
| Estrutura do repositório | árvore da baseline e contagens por extensão/diretório | Inicial | separar core, bibliotecas e recursos |
| Bootstrap | `bootstrap.php`, `main.inc.php`, `osTicket::start()` | Inicial | rastrear criação de sessão, configuração e falhas |
| Portal do usuário | `client.inc.php`, entrypoints raiz | Inicial | matriz de páginas, ações e permissões |
| Painel da equipe | `scp/staff.inc.php`, `scp/login.php`, `scp/ajax.php` | Inicial | rotas, menus, ACL, CSRF e operações |
| API | `api/api.inc.php`, `api/http.php` | Inicial | catálogo de rotas, autenticação e formatos |
| CLI/cron/pipe | `manage.php`, `api/cron.php`, `api/pipe.php` | Localizado | módulos, controles e efeitos |
| Domínio | `Ticket` e classes relacionadas | Localizado | relações, serviços e ciclo do ticket |
| Banco | esquema inicial, 99 patches, constantes de tabela, ERDs externos | Inicial | extrair schema 1.18.4 e comparar ERDs |
| Plugins e sinais | `class.plugin.php`, `class.signal.php`, sinais de dispatcher | Localizado | catálogo completo e limitações |
| Autenticação/autorização | backends em `class.auth.php`; guards por superfície | Inicial | sessões, ACL, 2FA, CSRF e API keys |
| Frontend PHP | templates e assets localizados | Pendente | somente após frentes estruturais anteriores |

## Lacunas que impedem concluir o Portão B

- mapa completo de componentes internos de `include/`;
- catálogo consolidado de entrypoints e dispatchers;
- mapa inicial de classes centrais e tabelas;
- inventário dos pontos de extensão;
- revisão independente da matriz estabilizada.
