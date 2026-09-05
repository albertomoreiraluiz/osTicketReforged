# Protocolo de observação integral da interface

## Objetivo

Observar o comportamento funcional do osTicket `v1.18.4` pelo frontend real,
sem inferir cobertura a partir de endpoints, CLI ou leitura estática. A
varredura percorre todas as superfícies habilitadas na instalação e registra
cada componente visível antes de avançar ao seguinte.

## Regra de percurso

Cada página será lida **de cima para baixo e da esquerda para a direita**, nesta
ordem:

1. cabeçalho global, identidade e troca de contexto;
2. navegação primária;
3. navegação secundária, menus e submenus;
4. título, breadcrumbs, ajuda e ações de página;
5. filtros, busca, ordenação e paginação;
6. conteúdo principal, colunas, linhas, estados vazios e mensagens;
7. ações por item e menus contextuais;
8. seleção e ações em massa;
9. rodapé, exportações e links auxiliares;
10. diálogos, abas e formulários abertos por cada controle.

O percurso reinicia no topo após navegação, submissão, modal ou mudança de
estado que altere a composição visual.

Não é permitido usar a matriz como lista aleatória de destinos. Em cada
contexto, o percurso começa no primeiro item visual e avança somente ao item
seguinte: menu principal da esquerda para a direita, cada submenu da esquerda
para a direita e, dentro da página, componentes de cima para baixo. Um salto ou
uso acidental de índice obsoleto invalida a passagem daquele grupo e exige
reinício pelo primeiro item.

## Superfícies e contextos

Serão mantidos percursos separados para:

- visitante anônimo;
- cliente autenticado;
- agente operacional;
- administrador no painel da equipe;
- administrador no Painel de Administração.

A Administração não é considerada coberta por apenas abrir `admin.php`. Cada
aba, item secundário e formulário administrativo precisa de linha própria na
matriz.

## Unidade mínima de evidência

Cada componente recebe:

- rota e contexto;
- posição/região;
- rótulo, ícone e condição de visibilidade;
- ação natural realizada no frontend;
- estados inicial, intermediário, sucesso, validação e vazio aplicáveis;
- efeito visual e, quando houver mutação, persistência observada;
- dependências de configuração e dados;
- evidência sanitizada e fixture persistente quando útil;
- resultado: `não observado`, `somente estático`, `observado`, `exercitado` ou
  `bloqueado por precondição`.

Uma página só pode ser marcada como coberta quando todos os seus componentes
visíveis possuem resultado explícito. Ausência de ação também é registrada.

## Hierarquia das evidências

1. uso pelo frontend natural no navegador;
2. estado visual posterior e fixture inspecionável;
3. persistência e logs correlacionados;
4. requisição HTTP capturada como apoio;
5. código e documentação como explicação;
6. CLI apenas como diagnóstico auxiliar.

Chamadas diretas a método, CLI, endpoint ou banco não comprovam o fluxo visual.
Quando forem usadas, a conclusão deve dizer expressamente qual camada foi
comprovada e manter o frontend como pendência.

Todos os cenários históricos executados por CLI, endpoint ou método direto
devem constar em uma fila de repetição. Se houver operação equivalente no
frontend, ela será novamente executada pelo navegador e somente essa passagem
poderá encerrar o comportamento visual. Se não houver equivalente visível, a
ausência será documentada como limite do produto.

## Formulários e eventos

- Preencher cada campo no controle renderizado pelo navegador quando o objetivo
  for submissão; atribuições indiretas, chamadas de endpoint, CLI ou banco não
  contam como preenchimento visual.
- Antes de submeter, conferir na própria interface os valores não secretos, as
  seleções, os estados condicionado/expandido e a presença do valor mascarado
  nos controles secretos.
- Credenciais locais podem ser reutilizadas a partir do `.env` não versionado
  ou da sessão do navegador, mas seus valores não entram em capturas,
  documentação, logs, comandos registrados ou fixtures.
- Usar movimento real do cursor somente para hover, drag, tooltip ou outro
  evento dependente de ponteiro.
- Abrir todos os selects, dropdowns, abas, accordions e diálogos.
- Exercitar validações normais e estados condicionais sem iniciar testes de
  segurança.
- Não usar requisições forjadas nesta passagem.
- Configurações funcionais devem ser realizadas pelo Painel de Administração.
- Quando um modal configurar um objeto dependente, aplicar o ciclo documentado
  em `OSTICKET_OPERATIONAL_MODEL.md`: salvar primeiro a entidade principal,
  reabri-la, configurar e salvar o modal, salvar novamente a principal quando
  aplicável e reabrir para confirmar a persistência. Reset anterior a esse ciclo
  é precondição não cumprida, não defeito presumido.
- Dependências locais necessárias podem ser parametrizadas pela interface. É
  permitido manter um microserviço de e-mail exclusivo da homologação para SMTP
  e IMAP/POP, desde que não funcione como relay externo, use apenas dados
  fictícios, não registre segredos e tenha execução e desligamento documentados.

## Mutações e permanência

Fixtures comportamentais permanecem na homologação conforme GOV-018. Snapshot
binário é contingência contra falha ou desvio. Exclusões continuam exigindo
plano específico, backup validado e rollback; até lá, controles destrutivos são
abertos somente até a confirmação, sem submissão final.

## Correção da evidência de e-mail

A criação anterior por `tickets.email`/CLI comprova parsing e persistência do
backend, não o recebimento natural configurado pela Administração. O fluxo de
e-mail permanece pendente até que conta, protocolo, coleta e comportamento
sejam configurados pela interface e observados pelo caminho operacional do
produto. A saída capturada por coletor local também permanece evidência apenas
do transporte isolado, não de uma instalação administrativa completa.

## Fora desta passagem

- exploração ofensiva;
- tentativa de contornar ACL, CSRF ou autenticação;
- correção do core;
- decisões sobre Angular, PrimeNG ou API futura;
- automação que substitua a primeira observação humana da interface.

## Critério de encerramento

A bateria termina somente quando a matriz não tiver página conhecida sem
visita, componente visível sem classificação ou fluxo natural substituído por
CLI/endpoint. Bloqueios por infraestrutura externa permanecem explícitos e não
são convertidos em conclusão.
