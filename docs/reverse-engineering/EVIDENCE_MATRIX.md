# Matriz de evidências do inventário

| Frente | Evidência atual | Estado | Próxima verificação |
| --- | --- | --- | --- |
| Estrutura do repositório | árvore da baseline, contagens e decomposição de `include/` | Mapeado inicialmente | refinar limites entre core e bibliotecas |
| Bootstrap e sessão | cadeias web/CLI, configuração, conexão, handlers e composição de `osTicket` | Mapeado estaticamente | validar rewrite, cookies e estados de upgrade após instalação |
| Portal do usuário | controladores centrais, pré-condições e ações estáticas | Em aprofundamento | completar recursos auxiliares e matriz de permissões |
| Painel da equipe | guarda comum, ações centrais e 229 folhas AJAX catalogadas individualmente | Mapeado estaticamente | confirmar respostas e controles na fase comportamental |
| API/AJAX | API nativa, 12 rotas cliente e matriz integral das 229 folhas staff | Mapeado estaticamente | validar os 13 alvos ausentes após instalação |
| CLI/cron/pipe | contratos, efeitos e pré-condições dos 14 módulos; cron/pipe aprofundados | Mapeado estaticamente | executar somente cenários futuros controlados |
| Domínio | criação multicanal, ticket/thread/task/forms, update/status/delete, falhas e órfãos | Mapeado estaticamente | injetar falhas após instalação descartável |
| Banco | schema sem FKs, catálogo das 72 metas ORM, relações, cdata, cascatas e evolução | Mapeado estaticamente | validar engines/índices após instalação |
| Plugins e sinais | ciclo/persistência, 41 sinais, 16 registries/factories e matriz de customização | Mapeado estaticamente | revisão cruzada dos limites de extensão |
| Autenticação/autorização | identidade, sessão, ACL, CSRF, 2FA, reset, API keys, arquivos e uploads | Em aprofundamento | testes controlados dos riscos priorizados |
| Frontend PHP | shells cliente/SCP, navegação, templates, assets, widgets e PJAX | Mapeado estaticamente | matriz de telas e acessibilidade após instalação |

## Lacunas do aprofundamento estático — Portão C

- normalização transversal das referências de evidência.
- revisão independente final de cobertura, segurança e limites do core.

## Validações reservadas à fase comportamental

Rewrite, cookies, engines reais, concorrência, interface, acessibilidade,
uploads, e-mail e riscos priorizados serão testados somente após a instalação.
Não bloqueiam a conclusão do inventário estrutural estático.
