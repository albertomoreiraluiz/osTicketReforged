# Matriz de evidências do inventário

| Frente | Evidência atual | Estado | Próxima verificação |
| --- | --- | --- | --- |
| Estrutura do repositório | árvore, contagens e fronteira core/dependências em `PROVENANCE_MAP.md` | Mapeado estaticamente | revisar ownership por arquivo apenas quando houver alteração |
| Bootstrap e sessão | cadeias web/CLI, configuração, conexão, handlers; sessões pública, cliente e staff observadas | Confirmado estática e funcionalmente | estados de upgrade permanecem no recorte próprio |
| Portal do usuário | navegação, login, tickets, buscas, perfil, colaboração, arquivos e conhecimento | Confirmado funcionalmente | acessibilidade pertence à futura análise de frontend |
| Painel da equipe | guarda comum, 229 folhas AJAX, filas, tickets, tarefas, menus contextuais e administração | Confirmado funcionalmente no recorte BHV | novos módulos somente quando uma fase os exigir |
| API/AJAX | API JSON/XML/e-mail/cron, AJAX cliente/staff e 13 alvos não callable | Confirmado funcionalmente | correções contratuais permanecem fora do inventário |
| CLI/cron/pipe | 14 módulos catalogados; cron HTTP executado com backup; pipe/e-mail rastreados | Confirmado no recorte seguro | concorrência e falhas induzidas permanecem fases próprias |
| Domínio | criação multicanal e derivada, ticket/thread/task/forms, vínculo/fusão, estados, arquivos, busca e notificações | Confirmado funcionalmente | exclusão e falhas induzidas exigem novo plano e backup |
| Banco | 72 metas ORM, relações, cdata, schema instalado e efeitos dos cenários verificados | Confirmado estática e funcionalmente | mudanças de schema permanecem fora do escopo |
| Plugins e sinais | ciclo/persistência, 41 sinais, 22 registries/factories e matriz de customização | Mapeado e revisado estaticamente | comportamento de plugin somente quando uma fase o exigir |
| Autenticação/autorização | identidade, sessão, ACL, CSRF, API keys, arquivos e uploads confrontados por papel | Confirmado no recorte funcional | novos ensaios de segurança pertencem à fase própria |
| Frontend PHP | shells cliente/SCP, navegação, formulários, assets, widgets, AJAX e PJAX | Confirmado funcionalmente | acessibilidade e arquitetura futura permanecem no Portão D/frontend |

## Encerramento do aprofundamento estático — Portão C

Catálogos, referências e limites foram normalizados. A revisão cruzada não
mantém achado alto ou médio documental aberto. A instalação subsequente permitiu
confrontar as superfícies funcionais na Onda 7.

## Encerramento da validação funcional

A Onda 7 confrontou os 25 cenários planejados com a instalação local. Interface,
sessões, uploads, e-mail, API, cron e efeitos persistidos possuem evidência
funcional. Concorrência, falhas induzidas, acessibilidade e novos ensaios de
segurança permanecem em fases próprias; não são lacunas do inventário funcional
encerrado.

## Cobertura complementar da Onda 8

Os cenários `BHV-026` a `BHV-034` ampliaram a cobertura de menus e
submenus do ticket, ações das entradas, vínculo/desvínculo, fusão nos dois modos,
criação de ticket/tarefa a partir da thread, ações secundárias/em massa e
exportações compostas. A execução usou fixtures fictícias, dump MariaDB
restaurado previamente e nenhuma exclusão. Oito cenários foram concluídos no
recorte declarado; ações em massa ficaram mapeadas, sem efeito em lote
observado. Após um desvio de sequência apontado pelo QA, o dump pré-relação foi
restaurado na homologação com paridade de seis contagens. A segunda passagem do
parecer independente foi aprovada e integrada pela PR #9.

A passagem encontrou três divergências correlatas no vínculo: mutação visual
com resposta `404`, flag residual no antigo pai após desvínculo e erro fatal ao
renderizar o evento relacional. Elas são evidência da baseline e não foram
corrigidas no core durante o inventário.

As evidências detalhadas da repetição corretiva estão disponíveis nos dossiês
[BHV-027 — vínculo e desvínculo](evidence/BHV-027-linked-tickets.md) e
[BHV-029 — ticket a partir de resposta](evidence/BHV-029-ticket-from-response.md),
acompanhadas de registros JSON sanitizados. Essa repetição corrigiu a lacuna de
descoberta causada por saídas originalmente mantidas apenas em `.local/`.
