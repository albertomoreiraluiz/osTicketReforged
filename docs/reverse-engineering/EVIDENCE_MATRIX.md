# Matriz de evidências do inventário

| Frente | Evidência atual | Estado | Próxima verificação |
| --- | --- | --- | --- |
| Estrutura do repositório | árvore da baseline, contagens e decomposição de `include/` | Mapeado inicialmente | refinar limites entre core e bibliotecas |
| Bootstrap e sessão | cadeias web/CLI, configuração, conexão, handlers e composição de `osTicket` | Mapeado estaticamente | validar rewrite, cookies e estados de upgrade após instalação |
| Portal do usuário | controladores centrais, pré-condições e ações estáticas | Em aprofundamento | completar recursos auxiliares e matriz de permissões |
| Painel da equipe | guarda comum, ações centrais de ticket e 256 registros sintáticos de rotas AJAX | Em aprofundamento | normalizar rotas, métodos, controladores e permissões |
| API/AJAX | API nativa, 12 rotas cliente e 256 declarações AJAX staff | Em aprofundamento | reconciliar folhas; mapear permissão/efeito/resposta |
| CLI/cron/pipe | 14 módulos; cron/pipe, manutenção, e-mail e busca aprofundados | Em aprofundamento | efeitos e pré-condições dos demais comandos |
| Domínio | criação multicanal, ticket/thread/task/forms, update/status/delete | Em aprofundamento | limpeza de órfãos, atomicidade e outros agregados |
| Banco | schema sem FKs, 72 metas ORM, relações centrais, cdata, cascatas e evolução | Em aprofundamento | completar 72 metas e validar engines/índices após instalação |
| Plugins e sinais | ciclo/persistência, 41 sinais literais, dispatchers e registries | Em aprofundamento | payload e segurança de cada sinal/registry |
| Autenticação/autorização | identidade, sessão, ACL, CSRF, 2FA, reset, API keys, arquivos e uploads | Em aprofundamento | testes controlados dos riscos priorizados |
| Frontend PHP | shells cliente/SCP, navegação, templates, assets, widgets e PJAX | Mapeado estaticamente | matriz de telas e acessibilidade após instalação |

## Lacunas do aprofundamento estático — Portão C

- matriz método a método das rotas AJAX da equipe;
- tratamento estático de falhas e órfãos no ticket e tarefa;
- catálogo completo das 72 declarações ORM e invariantes polimórficas;
- contrato individual dos 41 sinais e registries;
- efeitos e pré-condições dos módulos CLI restantes;
- normalização transversal das referências de evidência.

O catálogo de nomes de sinais foi produzido; “completo” acima significa ainda
relacionar payload, momento, identidade e risco de cada emissão.

## Validações reservadas à fase comportamental

Rewrite, cookies, engines reais, concorrência, interface, acessibilidade,
uploads, e-mail e riscos priorizados serão testados somente após a instalação.
Não bloqueiam a conclusão do inventário estrutural estático.
