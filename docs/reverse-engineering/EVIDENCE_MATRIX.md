# Matriz de evidências do inventário

| Frente | Evidência atual | Estado | Próxima verificação |
| --- | --- | --- | --- |
| Estrutura do repositório | árvore da baseline, contagens e decomposição de `include/` | Mapeado inicialmente | refinar limites entre core e bibliotecas |
| Bootstrap | `bootstrap.php`, `main.inc.php`, `osTicket::start()` | Inicial | rastrear criação de sessão, configuração e falhas |
| Portal do usuário | catálogo dos entrypoints e `client.inc.php` | Mapeado inicialmente | matriz de páginas, ações e permissões |
| Painel da equipe | 47 scripts, guards comuns e AJAX | Mapeado inicialmente | catálogo completo de rotas, menus e operações |
| API | entrypoints e dispatcher de `api/http.php` | Mapeado inicialmente | catálogo de rotas, autenticação e formatos |
| CLI/cron/pipe | 14 módulos CLI e entrypoints de integração | Mapeado inicialmente | controles e efeitos de cada comando |
| Domínio | modelos centrais localizados por área | Mapeado inicialmente | relações, serviços e ciclo do ticket |
| Banco | 67 tabelas, 99 patches, 72 constantes e ERDs externos | Mapeado inicialmente | chaves, relações e comparação com ERDs |
| Plugins e sinais | mecanismo, ciclo e extensão de dispatchers localizados | Mapeado inicialmente | catálogo completo de sinais e limitações |
| Autenticação/autorização | backends em `class.auth.php`; guards por superfície | Inicial | sessões, ACL, 2FA, CSRF e API keys |
| Frontend PHP | templates e assets localizados | Pendente | somente após frentes estruturais anteriores |

## Lacunas que impedem concluir o Portão B

- catálogo completo de rotas e sinais;
- relações iniciais entre classes centrais e tabelas;
- confirmação das fronteiras entre core e bibliotecas;
- revisão independente da matriz estabilizada.
