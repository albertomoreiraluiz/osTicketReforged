# Matriz de cobertura integral da interface

> A passagem reiniciada antes da leitura do Wiki foi interrompida e não vale
> como sequência integral. O novo percurso começou novamente em `/index.php`
> após a adoção de GOV-022 e do modelo operacional do Wiki; resultados úteis
> anteriores permanecem como evidência parcial, não como cobertura herdada.

## Legenda

- `Pendente`: rota ou região conhecida ainda não percorrida integralmente.
- `Em observação`: percurso visual iniciado e ainda não fechado.
- `Coberto`: todos os componentes visíveis foram classificados e exercitados
  quando seguro.
- `Bloqueado`: precondição externa ou decisão necessária foi registrada.

## Percursos

| Contexto | Entrada | Estado | Evidência atual | Próxima ação |
| --- | --- | --- | --- | --- |
| Anônimo | `/` | Em observação | cabeçalho, quatro menus, cadastro funcional, validações, criação de ticket e rodapé externo percorridos em ordem | concluir o link de confirmação recebido por e-mail |
| Cliente | `/login.php` | Coberto | autenticação, perfil, lista, filtros, ordenação, tickets próprio/colaborado, criação, KB, página inicial e saída percorridos visualmente | correlacionar divergências de filtro, contagem e impressão |
| Equipe — agente | `/scp/` | Coberto para o papel restrito | Painel, Diretório, Perfil, Tarefas, Tickets e FAQ percorridos como `OSTR Viewer`; nota funcional publicada | correlacionar divergências de ACL/contagem e modal vazio da FAQ |
| Equipe — administrador | `/scp/` | Em observação | fila de tickets revelou lacunas de massa/exportação | reiniciar pelo topo e cobrir todas as abas operacionais |
| Administração | `/scp/admin.php` | Coberto no mapa global | cinco grupos e todos os 27 submenus/abas internas percorridos em ordem pelo frontend | repetir fluxos funcionais priorizados e fechar correlações |

## Visitante anônimo — passagem sequencial reiniciada

**Entrada:** `/index.php`. **Método:** GOV-020, sem saltos entre os quatro itens
da navegação principal. Cada mudança de página reiniciou a leitura pelo topo.

| Ordem | Superfície/componente | Ação natural | Resultado observável |
| --- | --- | --- | --- |
| 1 | cabeçalho — Entrar | aberto antes da navegação principal | formulário de usuário/senha, cadastro, entrada de atendente e abertura de ticket |
| 2 | login vazio | botão `Entrar` | mensagem `Acesso negado`; link `Esqueci minha senha` passou a ser exibido |
| 3 | recuperação de senha vazia | `Enviar E-mail` | formulário foi substituído por mensagem genérica de que o envio ocorrerá se os dados forem válidos |
| 4 | logotipo | acionado após recuperação | retorno para `/index.php` |
| 5 | Página Principal | primeiro menu | atalhos de abertura/consulta, busca da KB, conteúdo institucional e rodapé |
| 6 | Base de Conhecimento | segundo menu | categoria, FAQ, seletor por tópico, busca e outros recursos |
| 7 | categoria e FAQ | links internos na ordem | categoria pública, artigo, breadcrumbs, data de atualização e associação ao tópico |
| 8 | busca positiva da KB | termo fictício existente + `Enter` | um resultado, com acesso ao FAQ |
| 9 | busca negativa da KB | termo fictício inexistente + `Enter` | estado vazio explícito |
| 10 | navegação por tópico | tópico disponível | um FAQ correspondente e categorias laterais |
| 11 | Abrir Novo Ticket | terceiro menu | contato, tópico, ações e validação do formulário |
| 12 | submissão vazia do ticket | `Criar novo Ticket` | erro geral e mensagens obrigatórias em e-mail, nome e tópico |
| 13 | quatro tópicos de ajuda | seleção na ordem | todos carregaram por AJAX o mesmo formulário integrado de resumo, editor rico e anexos |
| 14 | reinício do ticket | após selecionar o último subtópico | tópico voltou ao placeholder, mas o bloco dinâmico permaneceu renderizado |
| 15 | cancelamento do ticket | `Cancelar` | retorno à Página Principal |
| 16 | Verificar Status do Ticket | quarto menu | e-mail, número, link de acesso, login/cadastro e atalho para novo ticket |
| 17 | consulta vazia | `Link de acesso do e-mail` | URL mudou para `login.php`, conteúdo permaneceu como consulta e exibiu erro de campos válidos |
| 18 | atalhos da Página Principal | abertura e consulta acionadas na ordem | mesmos destinos dos menus principais |
| 19 | busca da Página Principal | termo positivo + `Pesquisar` | encaminhamento natural à busca da KB com um resultado |
| 20 | cadastro | formulário vazio e `Registrar` | campos obrigatórios de e-mail e nome e erro geral de informação incompleta |
| 21 | cadastro — cancelamento | `Cancelar` | retorno para `/index.php` sem mutação |
| 22 | criação funcional | todos os campos preenchidos na tela e `Criar novo Ticket` | confirmação visual de ticket criado com sucesso; fixture `[OSTR-W9] Ticket criado integralmente pelo portal` preservada |

**Fato observado:** a navegação anterior por rotas não havia registrado as
mudanças condicionais do login nem a permanência do formulário dinâmico após
`Recomeçar Formulário`. O cadastro foi exercitado até sua validação e o ticket
foi criado pelo formulário integralmente visível. O link externo do rodapé foi
acionado e abriu `https://osticket.com/` em nova aba. O cadastro foi preenchido
integralmente na tela; domínios locais e reservados de teste foram rejeitados,
um endereço fictício sintaticamente público foi aceito e o sistema confirmou o
envio da ativação. Login anterior à ativação exibiu `Confirmação de conta
necessária`. O consumo do link ainda está aberto devido à corrida com o coletor
POP3 natural, sem registrar tokens na evidência.

Uma segunda repetição controlada pausou a coleta POP3 pela própria tela,
persistiu a pausa, cadastrou outra conta fictícia e confirmou novamente a
página de envio. A mensagem não apareceu na caixa POP esperada pelo
microservidor; portanto o link não foi artificialmente marcado como validado.
A coleta foi restaurada visualmente e o banner `Atualizado com sucesso este
e-mail` confirmou o rollback da configuração.

## Cliente autenticado — passagem sequencial reiniciada

**Entrada:** `/login.php`. **Método:** credenciais locais foram lidas do `.env`
por ponte efêmera em loopback e preenchidas nos controles renderizados; nenhum
segredo foi capturado ou documentado. A leitura de cada página reiniciou pelo
topo.

| Ordem | Superfície/componente | Ação natural | Resultado observável |
| --- | --- | --- | --- |
| 1 | autenticação | e-mail e senha preenchidos visualmente + `Entrar` | sessão do cliente aberta e contador `Tickets(3)` exibido |
| 2 | cabeçalho e navegação | leitura da esquerda para a direita | Perfil, Tickets, Sair; Página Principal, KB, Abrir Novo Ticket e tickets |
| 3 | Perfil | inspeção integral | contato, preferências, fuso horário e alteração de senha disponíveis |
| 4 | Perfil — reinício | telefone e ramal temporários preenchidos e `Recomeçar Formulário` | campos voltaram ao valor anterior sem persistência |
| 5 | Perfil — cancelamento | `Cancelar` | retorno para `/index.php`, não para a lista de tickets |
| 6 | lista aberta | busca pelo número `593078` | uma linha correspondente |
| 7 | filtro de tópico | `Questões gerais` | três linhas correspondentes |
| 8 | composição busca+tópico | termo inexistente com o filtro ativo | a URL recebeu o termo, mas a lista continuou exibindo as três linhas do tópico |
| 9 | lista fechada | alternância para `Fechado` | estado vazio; os controles ainda exibiram valores visuais anteriores até o reinício |
| 10 | ordenação | Número, Data, Status, Assunto e Departamento | Número, Status, Assunto e Departamento mudaram os parâmetros; Data não alterou a URL nesta tentativa e exige repetição |
| 11 | detalhe `593078` | abertura da linha | informações básicas, usuário, thread, impressão, edição e resposta apresentados |
| 12 | impressão | `Imprimir` | nenhum resultado visual ou navegação observado; captura de download permanece pendente |
| 13 | edição — reinício | assunto temporário preenchido e `Recomeçar Formulário` | assunto original restaurado sem persistência |
| 14 | edição — cancelamento | `Cancelar` | retorno para `/index.php`, não para o detalhe |
| 15 | resposta vazia | `Publicar Resposta` | mensagem inline `Mensagem necessária` |
| 16 | resposta funcional | texto preenchido no editor visível e `Publicar Resposta` | mensagem de sucesso e nova entrada pública na thread com marcador `[OSTR-W9]` |

**Fato observado:** esta etapa comprova somente as linhas acima. A divergência
da composição de filtros, a ordenação por Data e a impressão ainda precisam de
repetição controlada antes de qualquer classificação como defeito.

Complementos que encerraram o contexto: o ticket colaborado `807330` mostrou
na própria thread o evento de inclusão do cliente e duas mensagens publicadas
por ele; a abertura autenticada omitiu os campos de contato, carregou o
formulário pelo tópico e criou visualmente o ticket `961630`; o contador passou
de três para quatro. A KB e a Página Principal preservaram o conteúdo público,
mas mantiveram os controles autenticados. `Sair` encerrou a sessão e restaurou
o cabeçalho e a navegação anônimos.

Repetição adicional confirmou ordenação crescente e decrescente por data. A
busca `API` reduziu visualmente a tabela a um ticket, mas manteve o resumo de
quatro; ao combinar tópico com termo inexistente, a URL preservou os filtros e
a tabela voltou a exibir os quatro tickets. A impressão do ticket autenticado
não abriu visualizador nem materializou arquivo no diretório de downloads.

## Equipe — agente restrito

**Entrada:** `/scp/`, com credenciais locais preenchidas nos campos visíveis.

| Ordem | Superfície | Resultado observável |
| --- | --- | --- |
| 1 | shell | somente Painel de Controle, Tarefas, tickets e Base de Conhecimento; sem Usuários, Novo Ticket ou Administração |
| 2 | Painel/Diretório/Perfil | gráfico ainda exibiu `NaN`; dois agentes listados; Conta, Preferências e Assinatura foram lidas integralmente |
| 3 | Tarefas | uma tarefa atribuída; uma linha `Consulta retornou 0 resultados` apareceu abaixo da linha real |
| 4 | tarefa `1` | nota `[OSTR-W9]` publicada visualmente e persistida; seletor de status ofereceu somente `Aberto` |
| 5 | Tickets | filas Aberto, Atribuído a mim, Equipes e Encerrado não exibiram linhas, mas mostraram totais globais incoerentes |
| 6 | pesquisa | número `593078` retornou zero linhas e resumo `25 de cerca 500`; o link do ticket na tarefa respondeu `Acesso negado` |
| 7 | FAQ | categoria e artigo públicos foram lidos; impressão abriu o visualizador PDF; `Gerir Acesso` abriu modal vazio |
| 8 | Administração | o menu não existe; navegação direta a `admin.php` redirecionou ao painel operacional |

**Fato observado:** a ACL impediu o acesso ao ticket e à Administração. As
contagens e paginações, contudo, não refletem as linhas autorizadas e formam
uma divergência candidata de apresentação, não um ensaio de segurança.

## Equipe — shell operacional com administrador

**Entrada:** `/scp/`. **Estado:** Em observação. A autenticação foi preenchida
nos dois controles visíveis usando valores efêmeros do `.env`, sem registro dos
segredos.

| Ordem | Menu/submenu | Componentes observados | Resultado |
| --- | --- | --- | --- |
| 1 | Painel de Controle | intervalo, período, atualização, gráfico, legenda, estatísticas por departamento/tópico/agente e exportação | gráfico renderizou rótulos `NaN`; tabela departamental apresentou métricas |
| 1.1 | Diretório do Agente | busca, departamento, filtro, seis colunas ordenáveis e paginação | dois agentes visíveis |
| 1.2 | Meu Perfil — Conta | avatar, contato, senha, 2FA, férias e ações | sem submissão |
| 1.2.1 | Preferências | paginação, atualização, remetente, fila, thread, assinatura, papel, redirecionamento, anexos, editor e localidade | todos os seletores percorridos visualmente |
| 1.2.2 | Assinatura | editor rico e ações do formulário | editor vazio no estado atual |
| 2 | Usuários — Diretório | busca, inclusão, importação, Mais, ordenação, seleção e exportação | nove usuários; fixture pública da Onda 9 visível |
| 2.1 | Adicionar Usuário | busca prévia, e-mail, nome, telefone/ramal, notas e ações | modal aberto e cancelado sem mutação |
| 2.2 | Importar — Copiar e colar | uma linha por nome/e-mail | modal observado sem submissão |
| 2.2.1 | Importar — Carregar | CSV e colunas Email, Name, Phone e Notes | seletor de arquivo observado sem upload |
| 2.3 | Organizações | busca, inclusão, Mais, ordenação, seleção e exportação | duas organizações visíveis |
| 3 | Tarefas — Aberto | busca/ordem, Mais, seis colunas, seleção, exportação e duas tarefas | tarefas `1` e `2`; ciclo da `2` preservado aberto e visível |
| 3.1 | Minhas Tarefas | mesmos controles, com `Última Atualização` no lugar de criação | tarefa `2`, atribuída a Alberto, foi a única linha |
| 3.2 | Nova Tarefa | título, editor, anexo, departamento, designado, vencimento e ações | modal independente reaberto e cancelado sem mutação |
| 3.3 | Pesquisa de tarefas | campo preenchido e submetido visualmente | número de ticket `927747` não retornou linha; trecho do título `fila de impressão` retornou a tarefa `2` |
| 3.4 | Mais e seleção | Fechar, Reivindicar, Atribuir a Agente/Equipe, Transferir e Apagar; Todos/Nenhum/Alternar | menus e estados de seleção exercitados, sem ação em massa submetida |
| 4 | Tickets — Aberto | cinco submenus, busca, avançado, ações, seis colunas, seleção e exportação | 14 tickets; novas fixtures `729918` e `961630` visíveis |
| 5 | Base de Conhecimento — FAQs | busca, filtros de categoria/tópico, categoria pública, detalhe, edição, anexos e notas | busca positiva abriu a FAQ funcional; detalhe e editor integralmente percorridos |
| 5.1 | Categorias | inclusão, Mais, quatro colunas, seleção, ações Público/Interno/Apagar, edição e notas | criação e fixture existente percorridas nas duas abas; nota interna persistida foi reexibida; nada salvo |
| 5.2 | Resposta pronta | inclusão, Mais, quatro colunas, seleção, ações Ativar/Desativar/Apagar, editor, anexos, notas e variáveis | três respostas ativas; fixture funcional reabriu conteúdo e nota persistidos; manual de variáveis foi renderizado |

**Fato observado:** a passagem acima usa o administrador dentro do painel
operacional; ela não substitui a posterior repetição com o papel restrito de
agente nem a inspeção das ações encadeadas de cada ticket.

### Base de Conhecimento operacional — detalhamento funcional

**Fato observado:** a busca visível por `OSTR-W7` retornou somente a FAQ
funcional, cuja página de detalhe expôs impressão, edição, categoria, tópico,
estado de publicação e conteúdo. O editor apresentou as abas de artigo,
anexos e notas internas; a nota da fixture reapareceu ao reabrir o registro.

**Fato observado:** a inclusão de categoria apresentou tipo
Destacado/Público/Privado, categoria principal, nome, descrição e nota interna.
A edição da categoria funcional exibiu os mesmos controles com os valores e a
nota persistidos. Na listagem, seleção e ações para tornar Público, tornar
Interno ou Apagar foram classificadas, sem submissão.

**Fato observado:** a inclusão e a edição de resposta pronta expuseram estado,
departamento, título, editor rico, anexos e notas internas. O diálogo
`Variáveis Suportadas` foi aberto pelo link renderizado e listou variáveis base,
expansões e variáveis contextuais. A resposta `[OSTR-W7] Resposta pronta
funcional` conservou o conteúdo que já havia sido carregado visualmente no
formulário de resposta do ticket. O menu em massa apresentou Habilitar,
Desabilitar e Apagar; a fixture foi desabilitada, reaberta nesse estado e
reativada pela própria interface. O estado ativo inicial foi restaurado e a
ação destrutiva Apagar não foi submetida.

## Administração — reinício sequencial pós-Wiki

**Entrada lógica:** primeiro menu `Painel de Controle`, apesar de o comando de
troca de painel inicialmente abrir `settings.php`. **Estado:** Em observação.

### 1. Painel de Controle

| Ordem | Página | Componentes observados | Resultado |
| --- | --- | --- | --- |
| 1 | Eventos do Sistema | intervalo de datas, nível, filtro, exclusão, quatro colunas ordenáveis, seleção e paginação | 25 linhas na primeira página; exclusão não submetida |
| 2 | Informações | versões, extensões PHP, cache, PHP, banco, armazenamento, assinatura, fusos e idiomas | osTicket `v1.18.4-23-g9f497f4b`; PHP 8.2.12; MariaDB 10.11.19; APCu ausente e demais extensões listadas presentes |

### 2. Configurações

| Ordem | Página/aba | Componentes observados | Resultado atual |
| --- | --- | --- | --- |
| 1 | Empresa — Informações Básicas | nome, website, telefone e endereço | somente nome preenchido |
| 1.1 | Páginas do site | página inicial, offline e agradecimento | Inicial e Obrigado selecionadas; offline desligada |
| 1.2 | Logos | escolhas separadas para Cliente/Equipe e upload | logo padrão nos dois contextos |
| 1.3 | Fundo de acesso | escolha da equipe e upload | fundo padrão |
| 2 | Sistema | estado, URL, departamento, HTTPS, colisão, paginação, logs, editor, iframe, ACL, localidade, idiomas, anexos e login | sistema conectado; anexos no banco; login para anexo ativo |
| 3 | Tickets — Configurações | sequência, estado/prioridade/SLA/tópico, locks, fila, limites, CAPTCHA, colaboração, fechamento e anexos | quatro abas internas identificadas |
| 3.1 | Respostas Automáticas | novo ticket, ticket de agente, mensagem, participantes e limite | respostas de ticket/mensagem ao proprietário desativadas; ticket de agente e participantes ativos |
| 3.2 | Alertas e Avisos | novo ticket/mensagem/atividade, atribuição, transferência, atraso e sistema | destinatários e estados atuais percorridos |
| 3.3 | Filas | inclusão, Mais, nome, criador, status, data e seleção | 14 filas de sistema ativas |
| 4 | Tarefas — Configurações | formato/sequência, prioridade e anexos | sequência de tarefas e prioridade baixa |
| 4.1 | Alertas & Avisos | nova tarefa, atividade, atribuição, transferência e atraso | todos os cinco grupos desabilitados |
| 5 | Agentes — Configurações | nome, avatar, colaboradores, senha, reset, 2FA, bloqueio, sessão e IP | políticas atuais percorridas sem mutação |
| 5.1 | Modelos | boas-vindas, login, recuperação e 2FA | quatro conteúdos versionados visíveis |
| 6 | Usuários — Configurações | nome, avatar, registro, política, bloqueio, sessão, token e acesso rápido | registro público e token ativos; registro obrigatório inativo |
| 6.1 | Modelos | acesso visitante, login, recuperação e confirmação | seis entradas; `Página de Login` exibiu destino `content//manage` e data vazia |
| 7 | Base de Conhecimento | estado, login obrigatório e respostas prontas | KB e respostas prontas ativas; login não exigido |

**Fato observado:** o destino incompleto do modelo `Página de Login` foi
registrado como divergência visual/estrutural candidata. Nenhuma hipótese de
causa ou impacto foi assumida nesta etapa funcional.

### 3. Gerenciar

| Ordem | Página | Componentes observados | Resultado atual |
| --- | --- | --- | --- |
| 1 | Tópico de ajuda | inclusão, Mais, classificação, sete colunas, seleção e paginação | quatro tópicos públicos ativos, incluindo um subtópico |
| 2 | Filtros | inclusão, Mais e sete colunas ordenáveis | estado vazio explícito |
| 3 | SLA | inclusão, Mais, cinco colunas, seleção e paginação | SLA padrão ativo, 18 horas |
| 4 | Agendas | inclusão, Ações, quatro colunas, seleção e paginação | quatro agendas de negócio/férias |
| 5 | API | inclusão, Mais, chave mascarada, IP, status, datas e seleção | uma chave local desativada; valor integral não exposto |
| 6 | Páginas | inclusão, Mais, cinco colunas, seleção e paginação | Inicial, Obrigado e Offline ativas/em uso |
| 7 | Formulários | inclusão e formulários integrados/personalizados | cinco integrados; nenhum personalizado |
| 8 | Listas | inclusão, Mais, datas e seleção | lista integrada `Status do ticket` |
| 9 | Plugins | inclusão, Mais e quatro colunas | nenhum plugin instalado |

**Fato observado:** todas as nove páginas foram acessadas pelo submenu visível
na ordem apresentada. Os controles mutáveis foram inventariados, mas inclusão,
mudança em massa e exclusão não foram submetidas nesta passagem de cobertura.

### 4. E-mails

| Ordem | Página/aba | Componentes observados | Resultado atual |
| --- | --- | --- | --- |
| 1 | E-mails | inclusão, Mais, cinco colunas, seleção e paginação | três contas; `root@localhost.local` é a padrão |
| 2 | Configurações | modelos, remetentes, administrador, verificação, recebimento, MTA e anexos | coleta e autocron habilitados visualmente, salvos e revalidados |
| 3 | Lista Negra | busca, inclusão, Mais, quatro colunas e seleção | uma entrada ativa; data de inclusão aparece truncada como `01/12/2` |
| 4 | Modelos | inclusão, Mais, cinco colunas, seleção e paginação | conjunto HTML padrão ativo e em uso |
| 5 | Diagnóstico | origem, destino, assunto, editor e ações | conta SMTP disponível como origem |
| 1.1 | Conta padrão | endereço/nome, departamento, prioridade, tópico, autoresposta e notas | entidade principal reaberta antes das configurações dependentes |
| 1.2 | Caixa remota | host, porta, pasta, protocolo, autenticação, frequência, lote e pós-busca | POP `127.0.0.1:110`, autenticação básica, conta `ostr`, ativo, 1 minuto/10 mensagens |
| 1.2.1 | Modal de autenticação | usuário e senha persistida | usuário revalidado; senha armazenada não foi exibida nem documentada |
| 1.3 | SMTP | estado, host, porta, autenticação e cabeçalho | ativo em `127.0.0.1:25`, sem autenticação; os dois estados ativos foram conferidos no formulário renderizado |

**Fato observado:** após habilitar coleta/autocron e navegar normalmente para
o painel da equipe, o e-mail anteriormente enviado pela tela de diagnóstico foi
coletado pelo POP3 e criou o ticket `967253`, assunto `[OSTR-W9] Teste visual de
SMTP`. Não houve chamada ao receptor do osTicket por CLI. Uma segunda mensagem
externa foi entregue ao microservidor e permanece aguardando a janela natural
de coleta; sua ausência imediata não é falha.

### 5. Agentes

| Ordem | Página | Componentes observados | Resultado atual |
| --- | --- | --- | --- |
| 1 | Agentes | departamento/equipe, aplicar, inclusão, Mais, seis colunas, seleção e paginação | administrador e agente de visualização ativos |
| 2 | Equipes | inclusão, Mais, seis colunas, seleção e paginação | uma equipe ativa, sem membros/líder |
| 3 | Funções | inclusão, Mais, quatro colunas, seleção e paginação | quatro funções ativas; duas sem checkbox selecionável |
| 4 | Departamentos | inclusão, Mais, sete colunas, seleção e paginação | três departamentos públicos ativos; Suporte é padrão e não selecionável |

**Fato observado:** o mapa administrativo global reiniciado está completo nos
cinco menus principais. “Coberto no mapa global” não encerra os fluxos de
criação/edição nem os cenários funcionais repetidos; eles seguem na fila da
mesma Onda 9 e só serão encerrados com evidência de resultado.

## Repetição visual de ações encadeadas

| Ordem | Contexto/controle | Ação visual | Resultado observável |
| --- | --- | --- | --- |
| 1 | ticket `927747`, entrada `32` | seta da entrada | menu `Editar`, `Criar novo Ticket` e `Criar Tarefa` exibido |
| 2 | `Criar novo Ticket` | seleção do tópico, assunto preenchido e criação | corpo da entrada foi pré-carregado; ticket `163086` criado com sucesso |
| 3 | ticket derivado `163086` | inspeção da thread | mensagem copiada, nota de derivação e vínculo `#927747` exibidos |
| 4 | ticket de origem `927747` | retorno pela fila e inspeção da thread | duas referências visíveis: `#229189` e `#163086`, ambas apresentadas como criadas a partir da entrada `32` |
| 5 | `Criar Tarefa` | acionado no mesmo menu visível | a correlação posterior confirmou tarefa `2` e evento `Tarefa criada a partir da linha de entrada` na thread original |

**Fato observado:** `#229189` aparece na fila com o assunto
`[OSTR-W9] Entrada natural por POP3` e remetente `Ostr W9 Sender`, mas a thread
de `#927747` também o apresenta como ticket criado a partir da entrada `32`.
Essa combinação permanece em correlação e não é classificada como derivação
manual até que percurso e persistência sejam reconciliados. A ausência de
resposta de `Criar Tarefa` é somente comportamento visual reproduzido neste
ponto.

No ticket `967253`, uma resposta foi preenchida no editor renderizado e
publicada com sucesso, atualizando `Última Resposta`. O menu da nova entrada
revelou `View Email Recipients`, `Edit and Resend`, `Criar novo Ticket` e
`Criar Tarefa`; os destinatários foram lidos em modal e `Edit and Resend` foi
submetido sem alteração de conteúdo. A própria thread passou a exibir o selo
`Reenviado`.

### Relações, fusão e ações auditáveis do ticket

| Ordem | Contexto/controle | Ação visual | Resultado observável |
| --- | --- | --- | --- |
| 1 | fila aberta, tickets `903010` e `874483` | desvincular no gerenciador e salvar | aba de relacionados desapareceu do pai, confirmando persistência |
| 2 | atalho coletivo de corrente | selecionar os dois tickets, ordenar pai/filho e salvar | `903010` voltou a exibir `PRINCIPAL` e `Tickets Relacionados (1)`; estado inicial restaurado |
| 3 | fixtures `924064` e `273122` | fundir com `Combinar temas`, pai aberto, filho resolvido e sem exclusão | thread do pai passou a seis itens, registrou o evento de fusão e incorporou as entradas do filho |
| 4 | ticket dependente `273122` | abrir pelo relacionado | rótulo `DEPENDENTE`, status `Resolvido`, referência ao pai e bloqueio de resposta exibidos; ticket não foi excluído |
| 5 | entrada original do ticket `927747` | editar, salvar texto temporário e restaurar | marcador `Editado` e `View History` surgiram; conteúdo final voltou ao original e a revisão permaneceu auditável |
| 6 | menu global do ticket `927747` | marcar respondido e depois não respondido, sempre com motivo | quatro eventos de agente/sistema foram adicionados; estado final voltou a não respondido |
| 7 | `Gerenciar referências` | abrir abas Referências/Indicar | zero referências; formulário oferece agente, equipe ou departamento e motivo opcional; nenhuma indicação submetida |
| 8 | `Gerenciar Formulários` | abrir e ler o diálogo | formulário `Detalhes do chamado` listado; inclusão indisponível no contexto; nenhuma alteração submetida |
| 9 | impressão do ticket | notas + eventos, papel A4, imprimir | `Ticket-927747.pdf` gerado localmente com 76.254 bytes |
| 10 | exportação da fila | 25 colunas, opções avançadas, nome e delimitador | requisição concluída e modal fechado; o navegador não materializou arquivo no diretório de downloads, portanto o artefato CSV permanece pendente de comprovação |

**Fato observado:** a fusão foi precedida por dump binário de 1.301.412 bytes,
SHA-256 `12c9cd0108ea4ad104c5e2ba1916dd5e5be65f54db08e30a91713a74a4267886`,
restaurado em banco temporário com igualdade de sete contagens e validação de
UTF-8. A opção destrutiva `Excluir ticket` permaneceu desmarcada.

### Vínculo pela fila

| Ordem | Controle visual | Resultado observado |
| ---: | --- | --- |
| 1 | seleção das linhas `903010` e `874483` | ambas as caixas ficaram marcadas na fila |
| 2 | atalho de vínculo | modal `Tickets Link` abriu com `903010` no topo e `874483` abaixo; o próprio modal informa que o primeiro é o principal e permite reordenação |
| 3 | `Salvar Alterações` | modal fechou e a fila foi recarregada |
| 4 | detalhe de `903010` | rótulo `PRINCIPAL` e aba `Tickets Relacionados. (1)` exibidos |
| 5 | aba de relacionados | tabela apresentou `874483`, assunto, departamento e data de criação |

**Fato observado:** a composição pai/filho foi confirmada pelo fluxo natural da
fila e pela renderização posterior do ticket principal. Nenhuma remoção ou
desvinculação foi executada.

### Atribuição, transferência e status no detalhe

| Ordem | Controle visual | Resultado observado |
| ---: | --- | --- |
| 1 | menu de atribuição de `903010` | opções `Reivindicar`, `Agente` e `Equipe` exibidas |
| 2 | `Agente` | modal listou `Alberto Moreira` e `OSTR Viewer`, além da justificativa |
| 3 | seleção de `Alberto Moreira`, justificativa visível e `Atribuir` | retorno à fila; coluna `Atribuído a` passou a mostrar `Alberto Moreira` |
| 4 | reabertura de `903010` | detalhe confirmou `Atribuído a: Alberto Moreira` e uma segunda entrada na thread |
| 5 | `Transferir` | modal apresentou departamentos `Manutenção`, `Suporte` e `Vendas`, opção de manter acesso e justificativa; cancelado sem mutação |
| 6 | `Alterar status` | menu apresentou `Resolvido` e `Encerrado`; nenhuma transição foi aplicada nesta unidade |

**Fato observado:** a atribuição foi executada integralmente por controles
renderizados, inclusive justificativa. Transferência e status foram observados
até o ponto anterior à mutação para preservar a fixture principal enquanto os
cenários dedicados são preparados.

### Edição reversível do ticket

| Ordem | Ação visual | Resultado observável |
| ---: | --- | --- |
| 1 | atalho `Editar` de `903010` | formulário completo exibiu usuário, origem, tópico, SLA, vencimento, resumo, prioridade e razão interna |
| 2 | resumo alterado para `[OSTR-W9] Parent ticket actions - validação visual` e razão preenchida | ambos os valores permaneceram visíveis antes da submissão |
| 3 | `Salvar` | mensagem `O ticket foi atualizado com sucesso`, novo assunto no cabeçalho e entrada adicional na thread |
| 4 | nova abertura de `Editar` | formulário carregou o assunto temporário persistido |
| 5 | restauração para `[OSTR-W8] Parent ticket actions`, com nova razão | segunda confirmação de sucesso e cabeçalho original restaurado |

**Fato observado:** BHV-022 foi repetido exclusivamente pelo frontend natural.
As duas alterações ficaram auditáveis na thread, enquanto assunto, vínculo com
`874483`, status, departamento, SLA, prioridade e atribuição terminaram no
estado anterior ao ensaio.

### Tarefa criada a partir de entrada

| Ordem | Ação visual | Resultado observável |
| ---: | --- | --- |
| 1 | busca simples por `927747` e abertura da única linha | ticket e entrada original `32` localizados sem rota direta |
| 2 | menu da entrada → `Criar Tarefa` | diálogo `Solicitação #927747: Adicionar nova tarefa` aberto; a tentativa anterior sem resposta ocorreu porque o menu havia fechado antes do acionamento, não por ausência da função |
| 3 | leitura do formulário | título obrigatório, descrição já preenchida com o corpo da entrada, anexo, departamento, agente, vencimento, reinício, cancelamento e criação visíveis |
| 4 | título `[OSTR-W9] Verificar fila de impressão fiscal`, departamento Manutenção e Alberto Moreira | três controles preenchidos e conferidos na tela; descrição predefinida preservada |
| 5 | `Criar Tarefa` | aba passou a `Tarefas (1)` e exibiu tarefa `2`, aberta, atribuída a Alberto no departamento Manutenção |
| 6 | abertura da tarefa na aba | thread apresentou conteúdo copiado, referência ao ticket `927747`, criação e reivindicação automática |
| 7 | atualização operacional preenchida e publicada | mensagem `Atualização enviada com sucesso` e nova entrada na thread da tarefa confirmadas |
| 8 | `Ações` → `Fechar`, com motivo operacional | confirmação submetida pela interface; lista passou a exibir `Encerrado`, e a thread registrou a mudança para Completo, o motivo e o agente que fechou |
| 9 | nova atualização com seletor de status `Aberto` | mensagem publicada, evento `Reaberto por Alberto Moreira` registrado e tarefa novamente aberta |
| 10 | aba `Publicar Nota Interna` | nota de acompanhamento preenchida no editor e confirmada por `Nota publicada com sucesso`; entrada permaneceu visível na thread |
| 11 | `Ver Tarefa` | rota completa `/scp/tasks.php?id=2` preservou vínculo com o ticket, thread, status, departamento, atribuição e colaboradores |
| 12 | barra de ações da visão completa | status mostrou `Fechar`; atribuição mostrou `Agente` e `Equipe`; transferência, impressão, edição e exclusão apareceram da esquerda para a direita |
| 13 | transferência, edição, vencimento e colaboradores | formulários foram abertos e lidos: departamentos/encaminhamento/razão; título/nota; data/fuso/razão; adicionar/salvar colaboradores; todos cancelados sem mutação |

**Fato observado:** BHV-031 possui agora uma reprodução integral pelo frontend.
A criação derivada copia a entrada para a descrição, vincula a tarefa ao ticket
e, quando um agente é escolhido no formulário, registra também sua
reivindicação. O ciclo Aberto → Encerrado → Aberto foi repetido integralmente,
com justificativas e eventos persistidos na própria thread. A fixture foi
preservada aberta e nenhuma ação de remoção foi executada. A visão completa
expõe exclusão, mas ela permaneceu apenas inventariada devido ao seu caráter
destrutivo.

### Usuários, organizações e perfil — repetição funcional

| Cenário | Ação integralmente visual | Evidência observável | Estado final |
| --- | --- | --- | --- |
| BHV-020 | organização `[OSTR-W9] Organização criada pelo fluxo visual` criada; usuário fictício localizado pelo autocomplete e associado | aba Usuários mostrou o vínculo; aba Tickets agregou o ticket `729918` do usuário | organização e associação preservadas como fixture |
| BHV-021 | website alterado, reaberto e restaurado; nota criada pela aba Notas | valor temporário reapareceu no editor; valor original foi restaurado; nota `[OSTR-W9]` permaneceu visível | campos restaurados; nota preservada |
| BHV-023 | telefone/ramal do usuário `OSTR Client` alterados e limpos pela mesma interface; nota administrativa criada | atualização do perfil foi refletida no carimbo; aba Notas exibiu autor, data e conteúdo | contato restaurado; nota preservada |
| BHV-024 | fuso do agente mudou de Padrão do Sistema para America/New_York e retornou ao padrão | duas mensagens `Perfil atualizado com sucesso`; seletor reaberto mostrou primeiro o valor temporário e depois o padrão | preferência restaurada |

**Fato observado:** os quatro cenários foram executados por controles
renderizados. O autocomplete de organização somente apresentou resultados após
uma busca parcial sem os marcadores do início do nome. A associação exibiu uma
etapa intermediária de conferência com endereço, telefone, website e nota antes
de `Continuar`; o ticket do usuário só apareceu na organização depois dessa
confirmação.

**Divergência candidata:** na pesquisa simples de tarefas, o título localiza a
tarefa `2`, mas o número do ticket associado `927747`, embora seja uma coluna
visível e clicável da mesma linha, produz “Não existem tarefas com o critério
indicado”. A observação descreve o contrato visível; a correlação com a consulta
do backend permanece para uma unidade posterior.

## Administração — shell e mapa global

**Entrada observada:** `/scp/settings.php`  
**Estado:** Em observação.

| Ordem | Navegação principal | Submenus observados pelo frontend |
| --- | --- | --- |
| 1 | Painel de Controle | Eventos do Sistema; Informações |
| 2 | Configurações | Empresa; Sistema; tickets; Tarefas; Agentes; Usuários; Base de Conhecimento |
| 3 | Gerenciar | Tópico de ajuda; Filtros; SLA; Agendas; API; Páginas; Formulários; Listas; Plugins |
| 4 | E-mails | E-mails; Configurações; Lista Negra; Modelos; Diagnóstico |
| 5 | Agentes | Agentes; Equipes; Funções; Departamentos |

O shell exibe também o aviso local sobre `setup/`, troca para o Painel do
Agente, Perfil e Sair. A exceção de homologação para `setup/` permanece aceita;
o aviso é comportamento visual, não instrução para exclusão.

### Primeiras páginas percorridas

| Página | Componentes observados na primeira leitura | Estado |
| --- | --- | --- |
| Eventos do Sistema | datas, nível de log, filtro, tabela, ordenação, seleção, exclusão e paginação | aberta; exclusão não submetida |
| Sistema | status, URL, nome, departamento, HTTPS, colisão, paginação, log, avatares, rich text, iframe, ACL, data/hora, idiomas e anexos | campos e estados atuais observados; submissão pendente |
| Tópicos de ajuda | adicionar, mais, modo de classificação, tabela, seleção e paginação | aberta; ações ainda pendentes |
| Endereços de e-mail | adicionar, mais, tabela, ordenação, conta padrão e seleção | aberta; configuração de coleta pendente |
| Agentes | filtros por departamento/equipe, adicionar, mais, tabela, ordenação e seleção | aberta; ações ainda pendentes |
| Equipes | adicionar, mais, tabela, ordenação, seleção e paginação | listagem percorrida; inclusão e ações em massa não submetidas |
| Funções | adicionar, mais, tabela, seleção e paginação | listagem percorrida; quatro funções ativas observadas; edição e massa pendentes |
| Departamentos | adicionar, mais, tabela, ordenação, seleção e paginação | listagem percorrida; departamento padrão não selecionável e demais selecionáveis |

A abertura de uma página não a classifica como coberta. Cada linha acima será
expandida componente por componente antes do encerramento da Administração.

### Grupo Agentes — primeira passagem posicional

**Estado:** todas as quatro páginas de listagem foram abertas pelo submenu
visível, da esquerda para a direita; nenhuma mutação foi submetida.

| Ordem | Página | Componentes da área de conteúdo | Resultado observável |
| --- | --- | --- | --- |
| 1 | Agentes | filtros, aplicar, adicionar, mais, cabeçalhos ordenáveis, seleção e paginação | filtros e estrutura da listagem identificados |
| 2 | Equipes | adicionar, mais, nome, status, membros, liderança, criação, atualização e seleção | uma equipe ativa visível; comandos de massa não exercitados |
| 3 | Funções | adicionar, mais, nome, status, criação, atualização e seleção | quatro funções ativas visíveis; a função sem checkbox revela item não selecionável nesse estado |
| 4 | Departamentos | adicionar, mais, nome, status, tipo, agentes, e-mail, gerente, criação e seleção | três departamentos públicos ativos; o departamento padrão aparece com seleção desabilitada |

Formulários de inclusão/edição e menus `Mais` continuam pendentes. Esta
passagem comprova o mapa e os estados das listagens, não o comportamento de
submissão.

Na segunda passagem funcional, o formulário de Equipe teve as abas `Equipe` e
`Membros` percorridas; o de Função teve `Definição` e as permissões de Tickets,
Tarefas e Base de Conhecimento; o de Departamento teve `Configurações` e
`Acesso`. Todos foram cancelados sem criação. O formulário de Tópico de ajuda
teve Informações, roteamento e Formulários percorridos, o menu em massa foi
classificado como Habilitar/Desabilitar/Arquivo/Apagar e uma alteração
temporária no nome de `Questões gerais` foi revertida por `Recomeçar
Formulário`. Em Sistema, a paginação foi mudada visualmente de 25 para 30 e
restaurada para 25 pelo botão de redefinição, sem salvar.

Os editores auxiliares de Gerenciar também foram abertos pela navegação
visível, lidos e cancelados: Filtro (regras, ações e notas), SLA, Agenda,
chave de API, Página, Formulário e Lista. O formulário de Filtro expôs ações
de rejeição, departamento, prioridade, SLA, equipe, agente, tópico, estado,
endereço de resposta, autoresposta, anexo de resposta pronta e envio de
e-mail. Plugins não oferece upload pela interface e instrui a instalação em
`include/plugins`.

Em Configurações, os anexos de Tickets e Tarefas abriram o gerenciador de
sequências e a configuração do campo de anexos; as alterações de ensaio foram
descartadas. Os modelos de Agentes e Usuários foram percorridos. O editor
`E-mail de confirmação de conta` apresentou assunto, corpo e a exigência da
variável `%{link}`. **Fato observado:** a linha `Página de Login` referencia
`ajax.php/content//manage`, sem identificador, e não abre editor ou mensagem
de erro ao ser acionada. O achado permanece como divergência funcional a
correlacionar com a configuração persistida e o código.

Em Empresa, as quatro abas foram relidas: Informações Básicas; seleção das
páginas Inicial, Offline e Agradecimento; Logos distintos para cliente/equipe;
e Fundo de acesso. Uploads de logo e fundo existem como controles visíveis,
mas não foram submetidos por ausência de artefato de homologação aprovado.

#### Agentes — formulário de inclusão

O formulário `Adicionar Novo Agente` foi aberto naturalmente e percorrido sem
submissão. Ele possui quatro abas de primeiro nível e, dentro de permissões,
quatro abas adicionais:

| Ordem | Aba | Componentes observados |
| --- | --- | --- |
| 1 | Conta | avatar, nome, e-mail, telefones, usuário, definição de senha, backend, bloqueio, administrador, acesso limitado, férias e notas internas |
| 2 | Acesso | departamento e função principais, fallback de função e acessos estendidos por departamento |
| 3.1 | Permissões — Usuários | criar, apagar, editar, gerenciar conta e diretório |
| 3.2 | Permissões — Organizações | criar, apagar e editar |
| 3.3 | Permissões — Base de Conhecimento | administrar categorias e perguntas frequentes |
| 3.4 | Permissões — Diversos | visualizar agentes/departamentos, lista negra, pesquisa ampla e estatísticas |
| 4 | Equipes | associação a equipes e indicação de alertas por vínculo |

**Fato observado:** a aba `Permissões` é um segundo nível de navegação e seu
conteúdo muda sem alterar o fragmento final exibido na URL para cada categoria.
Uma captura isolada dessa aba cobriria apenas `Usuários`. Os comandos Criar,
Recomeçar e Cancelar foram classificados; nenhum dado foi preenchido ou salvo.

### Grupo Configurações — primeira passagem posicional

**Estado:** as sete entradas do submenu e todas as abas internas imediatamente
visíveis foram percorridas pelo frontend natural; os valores foram somente
lidos e nenhum botão `Salvar Alterações` foi submetido.

| Ordem | Página | Abas/regiões internas percorridas | Comportamentos e controles observados |
| --- | --- | --- | --- |
| 1 | Empresa | informações básicas; páginas do site; logos; fundo de acesso | dados institucionais, seleção das páginas públicas, logos separados de cliente/equipe, upload e seleção de fundo |
| 2 | Sistema | formulário único | estado do helpdesk, URL, departamento, HTTPS, colisão, paginação, logs, conteúdo rico, incorporação, ACL, localidade, agenda, idiomas e anexos |
| 3 | tickets | configurações; respostas automáticas; alertas e avisos; filas | numeração, padrões, SLA, fila padrão, colaboração, anexos, destinatários dos avisos e 14 filas de sistema configuráveis |
| 4 | Tarefas | configurações; alertas e avisos | numeração, prioridade, anexos e grupos de alertas; alertas aparecem desabilitados no estado observado |
| 5 | Agentes | configurações; modelos | nome/avatar, colaboração, autenticação, sessão e quatro modelos de autenticação/comunicação |
| 6 | Usuários | configurações; modelos | nome/avatar, registro, autenticação, sessão, acesso rápido e seis modelos/páginas de conta |
| 7 | Base de Conhecimento | formulário único | habilitação, exigência de login e respostas prontas |

**Fato observado:** cinco páginas deste grupo possuem navegação interna por
fragmentos (`#...`) que altera o painel de conteúdo. Portanto, inventariar
somente a rota de topo não cobre a página. Os botões de configuração anexos,
gerência de sequências e editores de modelos ainda precisam de abertura e
classificação individual.

### Grupo Gerenciar — primeira passagem posicional

**Estado:** as nove listagens foram abertas pelo submenu, da esquerda para a
direita. Inclusões, edições e comandos de massa não foram submetidos.

| Ordem | Página | Estrutura e estado observáveis |
| --- | --- | --- |
| 1 | Tópico de ajuda | adicionar, mais, classificação alfabética/manual, quatro tópicos, status/tipo/prioridade/departamento, seleção e paginação |
| 2 | Filtros | adicionar, mais, sete colunas ordenáveis; estado vazio |
| 3 | SLA | adicionar, mais, cinco colunas ordenáveis, plano padrão ativo, seleção e paginação |
| 4 | Agendas | adicionar, ações, quatro agendas de expediente/feriados, seleção e paginação |
| 5 | API | adicionar, mais, cinco colunas ordenáveis, uma chave mascarada desativada, seleção e paginação |
| 6 | Páginas | adicionar, mais, cinco colunas ordenáveis, páginas inicial/offline/agradecimento ativas e em uso, seleção e paginação |
| 7 | Formulários | adicionar, mais, cinco formulários integrados e ausência de formulários extras |
| 8 | Listas | adicionar, mais, lista integrada de status, seleção e paginação |
| 9 | Plugins | adicionar, mais e tabela; nenhum plugin instalado |

**Fato observado:** a listagem de API mascara a chave, mas ainda representa
material operacional; a documentação registra somente o estado e jamais o
identificador exibido. Editores de cada tipo e menus de massa seguem pendentes.

### Grupo E-mails — primeira passagem e precondição natural

| Ordem | Página/painel | Resultado observável |
| --- | --- | --- |
| 1 | Endereços de e-mail | adicionar, mais, cinco colunas ordenáveis, três endereços locais, conta padrão não selecionável e seleção dos demais |
| 2 | Configurações | modelos/remetentes padrão, endereço administrativo, validação, coleta, tratamento de resposta, remetentes desconhecidos/colaboradores, MTA e anexos |
| 3 | Lista Negra | busca, banir, mais, quatro colunas ordenáveis, uma fixture ativa, seleção e paginação |
| 4 | Modelos | adicionar, mais, cinco colunas ordenáveis e conjunto HTML padrão ativo/em uso, não selecionável |
| 5 | Diagnóstico | origem, destino, assunto, editor rico, enviar, reiniciar e cancelar |
| 6 | Conta padrão — Conta | identidade, roteamento de novo ticket, prioridade, tópico, resposta automática e notas internas |
| 7 | Conta padrão — Caixa de Correio Remota | host, porta, pasta, IMAP/POP, autenticação, coleta, frequência, quantidade e ação pós-coleta |
| 8 | Conta padrão — SMTP | estado, host, porta, autenticação e permissão de cabeçalho |

**Fato observado:** a busca global de e-mail está desabilitada; a conta padrão
não possui host, porta, pasta nem protocolo de caixa remota e sua coleta está
desabilitada. O SMTP da conta também está desabilitado e sem host/porta; o MTA
global observado é a função `mail` do PHP. Portanto, os tickets anteriormente
injetados por coletor/CLI não comprovam o fluxo natural de recebimento.

**Bloqueio externo:** validar recebimento natural exige uma caixa IMAP ou POP
de homologação e respectivas credenciais fornecidas pelo responsável. Nenhuma
credencial será inventada, extraída ou persistida na documentação. Até essa
precondição existir, BHV-011 permanece evidência apenas de backend; o formulário
de diagnóstico permite testar somente a saída pelo frontend.

### Grupo Painel de Controle — primeira passagem posicional

| Ordem | Página | Resultado observável |
| --- | --- | --- |
| 1 | Eventos do Sistema | período, nível, aplicar, quatro colunas ordenáveis, seleção, exclusão e paginação; exclusão não submetida |
| 2 | Informações | versões do produto/servidor/PHP/banco, extensões, configuração PHP, uso do banco e pacotes de idioma |

**Fato observado:** o carregamento PJAX de `Informações` atualizou primeiro a
URL e manteve momentaneamente o conteúdo de logs; após a conclusão da navegação,
a página correta foi renderizada. As extensões funcionais listadas aparecem
disponíveis, com aviso para APCu e para `cgi.fix_pathinfo`. Valores de conexão,
assinaturas e outros detalhes locais não são reproduzidos nesta documentação.

## Equipe — administrador — fila de tickets abertos

**Rota observada:** `/scp/tickets.php?queue=1`  
**Estado:** Em observação — regiões visíveis cobertas; mutações em massa ainda
precisam de fixtures próprias.

| Ordem | Região/componente | Ação natural observada | Resultado |
| --- | --- | --- | --- |
| 1 | cabeçalho | identidade, Administração, Perfil e Sair renderizados | observado |
| 2 | navegação principal | Painel, Usuários, Tarefas, tickets e Base de Conhecimento | observado |
| 3 | submenu Aberto | aberto e percorrido | opções Aberto, Respondidos, Atrasado e adicionar fila pessoal |
| 4 | submenu Meus Tickets | aberto e percorrido | Atribuído a mim, Equipes Atribuídos e adicionar fila pessoal |
| 5 | submenu Encerrado | aberto e percorrido | Hoje, Ontem, semana, mês, trimestre, ano e adicionar fila pessoal |
| 6 | Pesquisar | diálogo aberto pelo frontend | pesquisa pai, critérios, colunas, salvar busca e campos adicionais observados |
| 7 | busca simples | controle e lookup visíveis | submissões positiva/negativa serão repetidas neste protocolo |
| 8 | Ordenar | controle visível | opções ainda serão abertas e exercitadas nesta onda |
| 9 | cabeçalhos da tabela | Ticket, atualização, assunto, origem, prioridade e atribuição | links de ordenação observados |
| 10 | checkboxes | `Todos`, `Nenhum` e `Alternar` exercitados | 12/12, 0/12 e complemento 1→11 confirmados visualmente |
| 11 | Alterar status | dropdown aberto | Aberto, Resolvido e Encerrado |
| 12 | Atribuir | dropdown aberto | Reivindicar, Agente e Equipe |
| 13 | Fundir | diálogo aberto com duas fixtures | ordem, participantes, status, modos, excluir filho e mover tarefas; não submetido |
| 14 | Vincular | diálogo aberto com duas fixtures | ordem, adicionar ticket e salvar; não submetido nesta passagem |
| 15 | Transferir | diálogo aberto | departamento, manter encaminhamento e razão; não submetido |
| 16 | Excluir | confirmação aberta | aviso de irreversibilidade e razão observados; exclusão não confirmada |
| 17 | Exportar | diálogo e opções avançadas abertos; botão submetido | download CSV natural observado, 25 campos padrão, nome e delimitador `;` |
| 18 | paginação/contagem | rodapé lido | 12 itens, página única no estado atual |

As ações individuais anteriores não são usadas para encerrar as mutações em
massa. Cada submissão não destrutiva receberá fixture e evidência própria. A
exclusão permanece bloqueada até plano específico.

## Regras de preenchimento

Cada página ganhará uma subseção própria durante a execução, com componentes
na ordem visual. A matriz não herdará o estado “concluído” dos cenários antigos:
eles serão usados como evidência auxiliar e vinculados somente após
reconfirmação pelo frontend natural.

## Ajuda contextual e leitura integral

**Fato observado:** os ícones `?` usam tópicos de ajuda vinculados pelo
atributo `href`. Na fila de tickets, o tópico `advanced` foi aberto pelo próprio
ícone e exibiu o manual **Avançado**: os critérios reduzem os parâmetros da
pesquisa e o resultado da busca avançada pode ser exportado no rodapé. No
formulário de resposta, o tópico `reply_types` foi aberto visualmente e explicou:

- **Responder a Todos** envia ao usuário e aos colaboradores selecionados;
- **Responder ao Usuário** envia apenas ao proprietário;
- **Não Enviar E-mail de Resposta** não dispara alerta, mas mantém a resposta
  visível a todos os usuários que possam consultar o ticket.

Esses textos foram efetivamente renderizados na interface. A partir deste
checkpoint, cada página deve inventariar também os tópicos de ajuda disponíveis;
Wiki e código servem apenas para correlação, não substituem a abertura visual.

## Cenário realista 01 — interrupção de impressão no fechamento

| Ordem | Papel e ação visual | Resultado observável |
| ---: | --- | --- |
| 1 | solicitante anônima `Marina Costa - Financeiro` selecionou `Relate um problema` | formulário dinâmico exibiu resumo, editor e instrução para descrever o problema |
| 2 | contato, telefone, ramal, resumo e relato operacional foram preenchidos na tela | após a troca de tópico, e-mail e telefone inicialmente preenchidos foram limpos pelo recarregamento dinâmico; todos os campos foram novamente preenchidos e conferidos antes do envio |
| 3 | `Criar novo Ticket` | confirmação pública exibida; ticket `990221` criado no departamento Manutenção, origem Web, prioridade Normal e SLA Padrão |
| 4 | agente pesquisou o assunto pela busca simples | uma linha correta foi retornada, mas o rodapé indicou `Mostrando 1 - 25` e páginas `2` a `15` mesmo havendo somente um resultado renderizado; divergência candidata |
| 5 | agente abriu `Reivindicar`, preencheu a razão e confirmou | primeira tentativa permaneceu em `Carregando` enquanto SMTP/POP local não escutava; nenhuma atribuição persistiu após recarga |
| 6 | microservidor isolado foi restabelecido e o mesmo formulário foi repetido | mensagem `Ticket atribuído a você Com sucesso`, campo `Atribuído a: Alberto Moreira` e evento com a justificativa foram exibidos |
| 7 | agente abriu Nota Interna, preencheu título e diagnóstico | confirmação `Nota interna foi postado com sucesso` e entrada `Diagnóstico inicial da impressora fiscal` visíveis na thread |
| 8 | agente abriu Resposta, carregou a resposta pronta de teste e substituiu o conteúdo por solução contextual | carregamento da resposta pronta foi comprovado no editor antes da substituição |
| 9 | destinatário `Proprietário do Ticket`, status `Resolvido` e resposta final foram selecionados na tela | confirmação `Resposta postada com sucesso`; ticket permaneceu localizável na busca ad hoc com quatro itens na thread |

**Fato observado:** a dependência do envio de alerta pode prolongar a operação
de reivindicação quando o transporte configurado está indisponível. A primeira
tentativa não persistiu a atribuição; a segunda, com o serviço local ativo,
concluiu e deixou evidência visual. O cenário permanece como fixture humana e
não contém dados reais.
