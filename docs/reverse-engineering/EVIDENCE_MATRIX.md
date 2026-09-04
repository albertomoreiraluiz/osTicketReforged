# Matriz de evidências do inventário

| Frente | Evidência atual | Estado | Próxima verificação |
| --- | --- | --- | --- |
| Estrutura do repositório | árvore da baseline, contagens e decomposição de `include/` | Mapeado inicialmente | refinar limites entre core e bibliotecas |
| Bootstrap e sessão | cadeias web/CLI, configuração, conexão, handlers e composição de `osTicket` | Mapeado estaticamente | validar rewrite, cookies e estados de upgrade após instalação |
| Portal do usuário | controladores centrais, pré-condições e ações estáticas | Em aprofundamento | completar recursos auxiliares e matriz de permissões |
| Painel da equipe | guarda comum, ações centrais de ticket e 256 registros sintáticos de rotas AJAX | Em aprofundamento | normalizar rotas, métodos, controladores e permissões |
| API | duas famílias nativas de endpoint e dispatcher extensível | Em aprofundamento | autenticação, formatos, erros e efeitos |
| CLI/cron/pipe | 14 módulos CLI e entrypoints de integração | Mapeado inicialmente | controles e efeitos de cada comando |
| Domínio | modelos centrais localizados por área | Mapeado inicialmente | relações, serviços e ciclo do ticket |
| Banco | schema sem FKs, 72 metas ORM, relações centrais, cdata, cascatas e evolução | Em aprofundamento | completar 72 metas e validar engines/índices após instalação |
| Plugins e sinais | ciclo/persistência, 41 sinais literais, dispatchers e registries | Em aprofundamento | payload e segurança de cada sinal/registry |
| Autenticação/autorização | backends em `class.auth.php`; guards por superfície | Inicial | sessões, ACL, 2FA, CSRF e API keys |
| Frontend PHP | templates e assets localizados | Pendente | somente após frentes estruturais anteriores |

## Lacunas que impedem concluir o Portão B

- catálogo completo de rotas e sinais;
- rastreamento de escrita do ciclo completo de ticket e tarefa;
- catálogo completo das 72 declarações ORM e invariantes polimórficas;
- revisão independente da matriz estabilizada.

O catálogo de nomes de sinais foi produzido; “completo” acima significa ainda
relacionar payload, momento, identidade e risco de cada emissão.
