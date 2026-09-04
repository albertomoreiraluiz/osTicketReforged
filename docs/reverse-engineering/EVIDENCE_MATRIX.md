# Matriz de evidências do inventário

| Frente | Evidência atual | Estado | Próxima verificação |
| --- | --- | --- | --- |
| Estrutura do repositório | árvore, contagens e fronteira core/dependências em `PROVENANCE_MAP.md` | Mapeado estaticamente | revisar ownership por arquivo apenas quando houver alteração |
| Bootstrap e sessão | cadeias web/CLI, configuração, conexão, handlers e composição de `osTicket` | Mapeado estaticamente | validar rewrite, cookies e estados de upgrade após instalação |
| Portal do usuário | controladores, pré-condições, ações e composição estática | Mapeado estaticamente | interface e respostas somente após instalação |
| Painel da equipe | guarda comum, ações centrais e 229 folhas AJAX catalogadas individualmente | Mapeado estaticamente | confirmar respostas e controles na fase comportamental |
| API/AJAX | API nativa, 12 rotas cliente e matriz integral das 229 folhas staff | Mapeado estaticamente | confirmar alcance e resposta real dos 13 alvos não callable após instalação |
| CLI/cron/pipe | contratos, efeitos e pré-condições dos 14 módulos; cron/pipe aprofundados | Mapeado estaticamente | executar somente cenários futuros controlados |
| Domínio | criação multicanal, ticket/thread/task/forms, update/status/delete, falhas e órfãos | Mapeado estaticamente | injetar falhas após instalação descartável |
| Banco | schema sem FKs, catálogo das 72 metas ORM, relações, cdata, cascatas e evolução | Mapeado estaticamente | validar engines/índices após instalação |
| Plugins e sinais | ciclo/persistência, 41 sinais, 22 registries/factories e matriz de customização | Mapeado estaticamente | revisão cruzada dos limites de extensão |
| Autenticação/autorização | identidade, sessão, ACL, CSRF, 2FA, reset, API keys, arquivos, uploads e sinais | Mapeado estaticamente | testes controlados dos riscos priorizados |
| Frontend PHP | shells cliente/SCP, navegação, templates, assets, widgets e PJAX | Mapeado estaticamente | matriz de telas e acessibilidade após instalação |

## Encerramento do aprofundamento estático — Portão C

Catálogos, referências e limites foram normalizados. A revisão cruzada não
mantém achado alto ou médio documental aberto. As verificações abaixo exigem
instalação e pertencem à fase comportamental seguinte.

## Validações reservadas à fase comportamental

Rewrite, cookies, engines reais, concorrência, interface, acessibilidade,
uploads, e-mail e riscos priorizados serão testados somente após a instalação.
Não bloqueiam a conclusão do inventário estrutural estático.
