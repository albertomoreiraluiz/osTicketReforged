# Matriz de cobertura integral da interface

## Legenda

- `Pendente`: rota ou região conhecida ainda não percorrida integralmente.
- `Em observação`: percurso visual iniciado e ainda não fechado.
- `Coberto`: todos os componentes visíveis foram classificados e exercitados
  quando seguro.
- `Bloqueado`: precondição externa ou decisão necessária foi registrada.

## Percursos

| Contexto | Entrada | Estado | Evidência atual | Próxima ação |
| --- | --- | --- | --- | --- |
| Anônimo | `/` | Pendente | navegação básica anterior será revalidada | percorrer cabeçalho, conteúdo, conhecimento, abertura e rodapé |
| Cliente | `/login.php` | Pendente | fluxos anteriores não usaram matriz posicional | percorrer todas as páginas e componentes com conta fictícia |
| Equipe — agente | `/scp/` | Pendente | tickets/tarefas parcialmente exercitados | percorrer menu completo com papel operacional |
| Equipe — administrador | `/scp/` | Em observação | fila de tickets revelou lacunas de massa/exportação | reiniciar pelo topo e cobrir todas as abas operacionais |
| Administração | `/scp/admin.php` | Em observação | shell global e grupo Agentes percorridos pelo frontend natural | percorrer os demais grupos, formulários e estados configuráveis |

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
