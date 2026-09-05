# Modelo operacional do osTicket para a Onda 9

## Objetivo e proveniência

Este guia transforma o [Wiki oficial do osTicket](https://github.com/osTicket/osTicket/wiki/Getting-Started)
em conhecimento operacional para a observação visual. A leitura local foi feita
no commit `b641236e504bbd867b38ece275e065a9f1be4fc1` do repositório Git do Wiki.
As páginas funcionais foram escritas majoritariamente entre 2014 e 2017; por
isso são referência conceitual auxiliar, não descrição garantida da interface
`v1.18.4`. Código, configuração e comportamento observado continuam prevalecendo.

## Modelo funcional extraído

| Domínio | Modelo descrito pelo Wiki | Consequência para a observação |
| --- | --- | --- |
| identidades | usuário final é identificado pelo e-mail; colaborador também participa do ticket; agente opera conforme permissões | testar proprietário, colaborador, agente, equipe e administrador como papéis distintos |
| criação | tickets entram por portal, e-mail, agente ou API | não transferir evidência de um canal para outro; cada canal natural precisa de cenário próprio |
| roteamento | todo ticket pertence a um departamento; tópico é obrigatório e pode definir departamento, prioridade e atribuição; filtros podem sobrescrever o fluxo | preparar tópico, departamento, organização e filtros antes de concluir sobre o resultado da criação |
| atribuição | ticket pode ter agente e equipe; itens não atribuídos podem ser reivindicados ou autoatribuídos por resposta | observar separadamente atribuição individual, por equipe, reivindicação e transferência |
| ciclo | estados aberto/fechado são refinados por status; SLA e data de vencimento interagem; data explícita substitui SLA | registrar estado, status, SLA, vencimento e fila antes e depois de cada ação |
| filas | aberto aguarda agente; respondido aguarda usuário; mensagem do usuário retorna o ticket à fila aberta; “Meus Tickets” é atribuição direta | validar mudança de fila após resposta do agente e mensagem do proprietário/colaborador |
| acesso | departamento primário, grupos, atribuição, equipe, acesso limitado, propriedade, colaboração e organização influenciam visibilidade | não inferir ACL apenas pela existência de um menu; observar a mesma fixture em cada identidade |
| dados | ticket, usuário e organização possuem formulários próprios; formulários customizados dependem do tópico; listas possuem propriedades | abrir campos dinâmicos somente depois de selecionar o contexto que os instancia |
| correspondência | autorespostas e alertas têm chaves globais, de departamento e de tópico; destinatários são deduplicados e o agente que causou o evento pode ser omitido | registrar todas as camadas de configuração e os destinatários efetivos antes de classificar ausência de mensagem |
| usuários | modos de registro resultam da combinação “registro obrigatório” e “método”; organização pode ter contatos principais, gerente e domínio | cobrir as combinações habilitadas e os efeitos em cadastro, abertura, autenticação e colaboração |
| conhecimento e marca | categorias, artigos, respostas prontas, páginas e templates compõem superfícies diferentes | observar publicação/visibilidade, editor e página resultante, não apenas a listagem administrativa |

## Protocolo de configuração dependente

**Decisão aceita:** quando uma tela cria ou altera uma entidade principal e
oferece configuração secundária em modal, a entidade principal deve existir e
estar salva antes de o modal persistir dados dependentes. A sequência de teste é:

1. preencher visualmente os campos da entidade principal;
2. salvar o formulário principal e confirmar a mensagem/estado persistido;
3. reabrir a entidade pela listagem natural;
4. abrir o modal dependente, preencher seus controles e salvar o modal;
5. salvar novamente o formulário principal quando a tela oferecer essa ação;
6. reabrir e confirmar ambos os estados antes do teste funcional.

Um reset ocorrido antes dessa sequência é classificado como **precondição não
cumprida**, não como falha do produto. Se o estado ainda for perdido após a
sequência completa, ele passa a ser fato observado e é correlacionado com o
código e a persistência.

## Aplicação a e-mail

O canal natural de e-mail só pode ser considerado configurado depois de:

1. salvar a conta/endereço principal;
2. salvar e reabrir a caixa remota;
3. configurar e confirmar a autenticação no modal;
4. habilitar a busca global e a execução por auto-cron;
5. salvar e reabrir o SMTP e sua autenticação;
6. confirmar envio pela tela de diagnóstico;
7. confirmar coleta pelo ciclo natural do painel;
8. localizar o ticket e seus eventos pela interface.

O microservidor local é apenas uma dependência isolada. Ele não substitui
qualquer um desses passos nem constitui, sozinho, evidência do osTicket.

## Limites do Wiki

- não descreve os modais de autenticação introduzidos nas versões atuais;
- não contém páginas versionadas para “Managing Agents” e “Knowledge base”,
  apesar de o índice mencioná-las;
- contém uma página de patch de busca 1.8 não oficial, excluída do modelo de uso;
- conceitos do Wiki devem ser confrontados com a baseline `v1.18.4` durante a
  observação.
