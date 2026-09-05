# Reforged — Entradas, sessão e publicação

## Estado, escopo e conclusão

**Desenho proposto**, não contrato executável ou decisão nova. Data 2026-09-05,
branch `codex/reforged-entry-session-design`, entrada `0f23d12e` (PR #35),
baseline `v1.18.4`. Continuação autorizada do detalhamento, sem promover a
árvore candidata da PR #35 a layout aceito nem criar aplicação/configuração web.

**Inferência sustentada pelo código:** a integração precisa distinguir contexto
de autenticação, contexto operacional e exigências administrativas. Eles podem
compartilhar infraestrutura, mas não uma autorização indistinta. Isso não exige
três scripts, três serviços ou três sessões. Ainda não foi identificado/provado
um bootstrap completo sem apresentação que satisfaça todos esses contextos.

Direção aceita: ADRs 0004–0006; complemento da
[organização física](REFORGED_PHYSICAL_DESIGN.md) e do
[desenho contratual](INTEGRATION_CONTRACT_DESIGN.md). A matriz abaixo aprofunda
T01–T14 do [mapa transversal](SCP_TRANSVERSAL_FLOWS.md), sem substituir os 153
registros SCP ou as permissões específicas de cada ação.

## Matriz por tipo de entrada

Coluna original = **Fato observado**, por leitura estática nesta unidade.
Coluna Reforged = **Proposta de requisito de integração**, não código aprovado.
Nesta matriz, nomes abreviados de páginas referem-se a `scp/`; classes citadas
no documento pertencem a `include/`, exceto Bootstrap, em `bootstrap.php` na raiz.
Os caminhos de dispatchers são explícitos
para não confundir as entradas de cliente e equipe.

| Entrada / rastreabilidade | Caminho e controles originais | Tratamento a especificar no Reforged |
| --- | --- | --- |
| Preparação de login — T05 | scp/login.php carrega main, tradução/classes e identidade existente; termina em template ou redirecionamento | contexto não autenticado limitado a iniciar/continuar autenticação, sem acesso a dados operacionais |
| Credenciais — T05 | login.php:62–74: checkCSRFToken, Validator::is_userid, StaffAuthenticationBackend::process; backend permitido e efeitos no login | preservar validação e políticas originais; traduzir resultado sem executar o template |
| Desafio — T06 | identidade com is2FAPending, backend, getInputForm/isValid/validate; ExpiredOTP encerra autenticação | representar desafio pendente separadamente de sessão apta ao SCP; contrato específico do formulário |
| Externo/SSO — T07 | getBackend/triggerAuth/processSignOn no fluxo de login | ficha por provedor disponível, início e retorno correlacionados; não converter para senha local nem presumir callback universal |
| Recuperação — T08/T09 | pwreset.php: CSRF nas submissões, identificação/backend local, envio e continuidade processSignOn; formulário/espera/redirecionamento na página | distinguir solicitação, entrega de mensagem e consumo do fluxo; preservar políticas, não prometer redefinição apenas por enviar e-mail |
| Operação autenticada — T01/O | staff.inc: ACL staff, getUser/isValid, condições de atividade/offline/upgrade, refresh, CSRF nos métodos de mutação e perfil obrigatório; depois controles da operação | conferir identidade, ambiente, objeto e ação em cada operação; menu visível não concede autoridade |
| Administração — T02/A | admin.inc carrega staff, exige isAdmin e trata upgrade; controllers administrativos podem aplicar access próprio, como EmailAjaxAPI::access | preservar exigência administrativa e controles da ação; não classificar toda sessão staff como administrativa |
| AJAX/apps — T03/T04 | scp/ajax.php adapta staffLoginPage; dispatcher/access e handlers; scp/apps/dispatcher.php acrescenta admin conforme contexto | registro de rota não substitui guardas; não herdar resposta ou autorização uniforme das rotas existentes |
| Perfil/senha/MFA obrigatório — T11–T14 | staff.inc encaminha para profile; profile usa ID próprio/updateProfile; senha e MFA têm fluxos AJAX próprios | definir conjunto restrito de operações necessárias à regularização, preservando identidade própria e validação original; não liberar ações comuns por existir sessão |
| Logout — T10 | logout.php coordena logOut, limpeza de sessão/cookie e Lock::removeStaffLocks | especificar término e efeitos, evitando logout apenas visual; proteção da intenção e comportamento de falha precisam de contrato próprio |

Limites importantes: `staff.inc.php` contém condições diferentes para admin e
não admin e exceções associadas à página original. A matriz não converte essas
condições em política nova nem afirma que um include garantirá tudo. As exceções
devem ser reconciliadas por intenção no desenho concreto; não manipular basename
ou flags para eliminar verificações. A divergência de fluxo já registrada em
T10 continua pendente de tratamento, sem reprodução ou instrução de exploração.

## Matriz de reutilização e obstáculos

**Fatos observados e inferências delimitadas:**

| Bloco | Mecanismos existentes | Limite que impede declarar integração pronta |
| --- | --- | --- |
| Configuração e paths | Bootstrap::loadConfig/loadCode/connect; ROOT_DIR/INCLUDE_DIR; osTicket::get_root_path | configuração ausente pode redirecionar ao setup; cálculo de ROOT_PATH usa contexto da requisição/arquivo; includes relativos exigem compatibilidade |
| Inicialização | main.inc.php:23–47 → osTicket::start/getSession | HTTPS, erro de inicialização, mensagens e variáveis globais podem produzir efeitos antes do módulo |
| Identidade e acesso | StaffAuthenticationBackend::getUser, StaffSession::isValid; controles staff/admin e objeto | obter identidade não executa por si só todas as guardas do SCP; regras na página não são serviço reutilizável demonstrado |
| Resultado | mecanismos devolvem valores, mas páginas e controllers também emitem HTML/redirect/exit | não há garantia geral de conversão posterior para JSON; erro anterior ao controlador precisa de tratamento definido |

**Comparação proposta, restrita às entradas:**

- Incluir staff/admin como bootstrap universal preserva várias guardas no
  caminho original, mas carrega apresentação, paths e condições por página;
  tampouco atende login anônimo. Não recomendado como solução universal.
- Usar somente main resolve parte da inicialização, não autorização de staff,
  administração, objeto ou ação. Não é uma solução suficiente.
- Coordenar mecanismos existentes em entradas novas é a direção a detalhar sob
  ADR 0004. Exige rastrear cada guarda e efeito; não autoriza copiar condicionais
  de negócio ou reconstruir políticas onde não há ponto reutilizável.

**Recomendação:** uma responsabilidade de integração compartilhada, com contextos
explícitos por entrada e coordenação do caso de uso nos módulos. Essa proposta
não escolhe middleware/framework nem prova viabilidade. Registrar a lacuna
exata quando uma guarda/regra estiver inseparável de apresentação; resolver com
o responsável antes de implementar o trecho. Não usar captura de HTML como API.

## Continuidade de sessão e CSRF

**Fatos observados:** StaffAuthenticationBackend::login (`class.auth.php:625`)
prepara identidade, possível desafio e efeitos; StaffSession::isValid
(`class.usersession.php:257`) exige ausência de desafio pendente e sessão válida.
Sinal de login não é evidência suficiente de autorização operacional concluída.

osTicketSession::__construct (`class.ostsession.php:26`) define nome/parâmetros
de cookie e seleciona armazenamento segundo configuração/mecanismo original.
renewCookie (`:252`) usa atributos efetivos e trata SameSite. Portanto a API
não deve presumir que partilhar host equivale a partilhar backend de sessão.
Nome, armazenamento, configuração, root público e atributos precisam ser coerentes
nos dois caminhos, sem criar identificador ou armazenamento paralelo.

UserSessionTrait::refreshSession (`class.usersession.php:129`) trata validade
temporal, regeneração condicionada a GET e renovação. O parâmetro padrão é
60 segundos; o comentário sobre 30 segundos não define o intervalo executado.
Na regeneração, refreshRate pode ser forçado; não transformar esses valores em
política nova do Angular. Requisições de fundo podem afetar atividade: frequência
de polling/renovação e relação com inatividade precisam ser especificadas.

CSRF::__construct mantém referência à sessão; getToken pode criar/renovar estado.
`osTicket::checkCSRFToken` (`class.osticket.php:115`) admite campo de formulário
ou cabeçalho original; rotação explícita depende do argumento e do caminho.
O comentário do login não prova rotação automática em toda tentativa. Transporte
JSON, obtenção do token e rotação entre abas precisam de contrato; não ler HTML
legado para obter token nem reutilizar o identificador de sessão como token CSRF.

**Proposta de comportamento para o frontend, ainda a especificar/testar:**

- Diferenciar anônimo, desafio pendente, sessão válida e regularização necessária.
- Preservar dados de formulário em falha, sem reenviar automaticamente mutações
  após expiração, erro CSRF, timeout ou retorno de autenticação.
- Reconferir contexto no servidor ao alternar painel; não confiar em estado
  antigo de menus/permissões mantido pelo Angular.
- Logout concluído deve invalidar a continuidade operacional nos dois painéis;
  interfaces em outras abas devem reconhecer o novo estado na próxima interação.
- Não emitir segredos de sessão, senha ou credenciais de provedor em respostas JSON,
  logs, evidências ou armazenamento do frontend. Token CSRF tem finalidade distinta.

## Publicação por intenção de rota

**Proposta:** mapeamento explícito no servidor da mesma origem, sem escrever
wrappers no SCP original. A sintaxe de Apache/Nginx, paths finais e eventual
proxy de desenvolvimento continuam pendentes; nenhum servidor foi configurado.

| Classe de destino | Artefato/intenção | Critério proposto |
| --- | --- | --- |
| SCP original | PHP e assets legados | manter rotas existentes e comportamento; não enviar navegação SCP ao fallback Angular |
| Painel Reforged | build Angular | somente navegação de página elegível recebe shell SPA; recarga/deep link permanecem coerentes |
| API Reforged | entradas PHP novas | nunca cair no shell SPA; rota desconhecida ou método não admitido retorna erro de API definido |
| Assets do painel | arquivos compilados explicitamente publicados | asset ausente não recebe HTML como sucesso; política de cache definida por tipo/versão |
| Fontes internas/testes/governança/segredos | não são destinos públicos | excluir do artefato público ou impedir exposição direta com configuração comprovada |
| Arquivos privados/exportações | fluxo autenticado do domínio | não publicar como asset do painel nem presumir URL pública permanente |

Respostas de contexto autenticado, desafios e dados privados exigem política de
cache própria; cache de assets não autoriza guardar essas respostas em cache
compartilhado. Headers e armazenamento do frontend serão definidos no contrato.

Mapeamento explícito é preferido à correspondência automática entre pasta e URL:
permite isolamento sem mover upstream. Sua viabilidade com ROOT_PATH/includes
deve ser comprovada; o nome `public/` sozinho não aplica a separação.
Rollback de publicação futuro deve restaurar roteamento/assets compatíveis sem
alterar o core nem restaurar banco como efeito automático de um rollback visual.

## Critérios de verificação futura e próxima decisão

Complemento: [Guardas e contratos](REFORGED_GUARD_CONTRACTS.md) rastreia métodos e
coordenação inline; ADR 0007 proposto delimita a decisão necessária para esse trecho.

Nenhum cenário abaixo foi executado nesta unidade; são **Propostas de aceite**:

1. Bootstrap: instalação em raiz/subcaminho, configuração ausente/incompatível,
   HTTPS e falhas controladas sem resposta HTML indevida ou exposição interna.
2. Identidade: login natural, desafio, recuperação e logout completos; alternância
   e duas abas, expiração e renovação, sem efeitos repetidos por retry automático.
3. Autorização: agente/admin, departamento/equipe/objeto, perfil obrigatório e
   modos offline/upgrade; executar apenas ações permitidas pelo contexto.
4. Publicação: deep links, recarga, API/método/asset desconhecidos, arquivos
   internos inacessíveis e regressão de navegação no SCP intacto.
5. Compatibilidade: demonstrar reutilização de cada mecanismo sem copiar regras,
   alterar core/schema ou depender de UI legada para completar a função.

Próximo passo recomendado: consolidar a matriz em contratos concretos de contexto
e erros, ligando cada guarda ao mecanismo efetivamente reutilizável. Tratar
primeiro as lacunas de bootstrap/guardas, antes de fixar URLs ou gerar código.
Isso é prioridade de especificação, não escolha do primeiro módulo implementado.
Árvore exata, mecanismo de publicação e proposta de contextos continuam sujeitos
ao aceite do responsável; detalhes de sessão exigem revisão IAM independente.
