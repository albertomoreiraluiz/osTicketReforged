# Matriz de evidências do inventário

| Frente | Evidência atual | Estado | Próxima verificação |
| --- | --- | --- | --- |
| Estrutura do repositório | árvore da baseline, contagens e decomposição de `include/` | Mapeado inicialmente | refinar limites entre core e bibliotecas |
| Bootstrap e sessão | cadeias web/CLI, configuração, conexão, handlers e composição de `osTicket` | Mapeado estaticamente | validar rewrite, cookies e estados de upgrade após instalação |
| Portal do usuário | controladores centrais, pré-condições e ações estáticas | Em aprofundamento | completar recursos auxiliares e matriz de permissões |
| Painel da equipe | guarda comum, ações centrais e 229 folhas AJAX catalogadas individualmente | Mapeado estaticamente | confirmar respostas e controles na fase comportamental |
| API/AJAX | API nativa, 12 rotas cliente e matriz integral das 229 folhas staff | Mapeado estaticamente | validar os 13 alvos ausentes após instalação |
| CLI/cron/pipe | contratos, efeitos e pré-condições dos 14 módulos; cron/pipe aprofundados | Mapeado estaticamente | executar somente cenários futuros controlados |
| Domínio | criação multicanal, ticket/thread/task/forms, update/status/delete | Em aprofundamento | limpeza de órfãos, atomicidade e outros agregados |
| Banco | schema sem FKs, catálogo das 72 metas ORM, relações, cdata, cascatas e evolução | Mapeado estaticamente | validar engines/índices após instalação |
| Plugins e sinais | ciclo/persistência, catálogo individual dos 41 sinais, dispatchers e registries | Em aprofundamento | completar matriz de customização e registries |
| Autenticação/autorização | identidade, sessão, ACL, CSRF, 2FA, reset, API keys, arquivos e uploads | Em aprofundamento | testes controlados dos riscos priorizados |
| Frontend PHP | shells cliente/SCP, navegação, templates, assets, widgets e PJAX | Mapeado estaticamente | matriz de telas e acessibilidade após instalação |

## Lacunas do aprofundamento estático — Portão C

- tratamento estático de falhas e órfãos no ticket e tarefa;
- matriz de customização e limites entre extensão e alteração do core;
- catálogo individual dos registries que não usam `Signal`;
- normalização transversal das referências de evidência.

## Validações reservadas à fase comportamental

Rewrite, cookies, engines reais, concorrência, interface, acessibilidade,
uploads, e-mail e riscos priorizados serão testados somente após a instalação.
Não bloqueiam a conclusão do inventário estrutural estático.
