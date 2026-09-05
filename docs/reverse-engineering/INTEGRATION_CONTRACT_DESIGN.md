# Integração e contratos — Desenho inicial

## Estado e manifesto

**Desenho inicial pronto para discussão; propostas técnicas não aceitas.** Data 2026-09-05, entrada
`2fa8e56a` (PR #33), branch `codex/scp-parity-integration-design`, baseline v1.18.4.
ADR 0005 formaliza apenas divisão lógica e cobertura integral, exceto setup.
Esta unidade compara integração/contratos sem implementar ou alterar core.

O integrador é único escritor deste documento, ADRs, planos, índices e progresso.
`api_architect/integration/contratos` analisa contratos/superfícies em leitura;
`security_architect/integration/revisao` revisa independentemente o texto
estabilizado quanto a identidade, sessão, guardas e fronteiras de confiança.
Entradas: AGENTS, checkpoint, planos, ADRs 0003–0005, mapas SCP e código aplicável.
Sem descendentes, escrita, Git mutável, rede, banco, credenciais, navegador ou
ensaios de exploração. Cada instância para no relatório; não aceita decisões.
O integrador valida fontes e MkDocs enquanto a revisão específica ocorre.

Critério da unidade: recomendações rastreadas, estados/efeitos explícitos,
alternativas comparadas e pendências reais preservadas. Não é fechamento do
Portão D nem certificação de integração executável.

## Base aceita e recomendação delimitada

Fontes: [ADR 0004](../adr/0004-modulos-reforged-backend-osticket.md),
[ADR 0005](../adr/0005-modulos-logicos-paridade-scp.md),
[capacidades](MODULE_CAPABILITY_PROPOSAL.md) e [mapa SCP](SCP_FLOW_MAP.md).
Os IDs M01–M15 estão aceitos como divisão lógica, não como interfaces de chamada.

**Proposta recomendada:** API orientada a recursos reais e ações explícitas de
domínio; integração web preferencialmente na mesma origem do SCP, mantendo os
mecanismos de identidade/sessão originais. Isso não escolhe diretórios, URLs,
plugin de hospedagem, bootstrap concreto, cookies ou framework. Mesmo processo
PHP de requisição é candidato para chamadas locais ao core; não prova ausência
de acoplamento ou efeitos de apresentação.

## Comparação de transporte e hospedagem

| Alternativa | Evidência/benefício | Limite e avaliação proposta |
| --- | --- | --- |
| Frontend e API na mesma origem do SCP | coerente com identidade compartilhada e alternância; evita acrescentar uma fronteira entre origens | recomendada para detalhar; não elimina CSRF, validação de sessão, escopo do cookie ou tratamento de proxy/HTTPS |
| Frontend/API em origem separada | poderia separar hospedagem física | requer desenho explícito de cookies, CORS, callbacks e confiança; não recomendada como ponto inicial sem necessidade concreta; não autoriza IAM paralelo |
| Entradas novas reutilizando bootstrap/mecanismos originais | permite contratos próprios sem templates como resposta | candidata, mas não basta incluir main.inc.php: faltam guardas de staff/admin; bootstrap ainda precisa de prova e decisão |
| Registro via ajax.scp/apps.scp/apps.admin | pontos reais em scp/ajax.php e scp/apps/dispatcher.php | alternativa a comparar; ajax.scp já exige staff, portanto não resolve login anônimo; apps carregam contexto/nav legado; não aprovação do pacote descartado da PR #28 |

**Fato observado:** `main.inc.php:23–47` carrega configuração/código/conexão,
inicia osTicket e sessão; pode redirecionar por HTTPS, falhar e consumir mensagens
da sessão. Não é um bootstrap JSON puro. `scp/staff.inc.php` reúne ACL, identidade,
validade, atividade/offline/upgrade condicionais, refresh, CSRF, nav/avisos e
redirecionamentos para perfil/upgrade. `AJAX_REQUEST` evita parte da navegação,
mas exceções também dependem do basename da requisição. Logo, chamar staff.inc
não comprova saída estruturada em todos os estados; chamar só main.inc não
comprova autorização. Não alterar basename ou flags para suprimir controles.

**Pendência de desenho:** identificar quais mecanismos existentes podem ser
coordenados no ponto de entrada novo preservando cada guarda e estado, sem copiar
políticas ou capturar HTML. Onde não houver ponto reutilizável, registrar bloqueio
e solicitar decisão; não declarar a escolha de bootstrap resolvida por esta comparação.

## Comparação do contrato funcional

| Opção | Consequência | Proposta |
| --- | --- | --- |
| Recursos com ações de domínio | consulta/edição de recursos reais; responder, transferir, mesclar, reenviar, autorizar provedor e manter sistema continuam intenções explícitas | recomendada |
| CRUD genérico sobre ORM/tabelas | save isolado omite guardas, regras no controller, sinais e efeitos; aceita atributos fora da intenção | não recomendado; não corresponde à reutilização do fluxo aceita |
| Replicar rotas/respostas AJAX | HTML, texto, redirects, JSON com status interno e alvos ausentes viram contrato novo | manter como rastreabilidade, não copiar como API pública |

Evidências: `scp/tickets.php:169` antes de Ticket::postReply;
`scp/emails.php` sinaliza criação após atualização;
DynamicFormsAjaxAPI::getFormsForHelpTopic devolve html/media;
EmailAjaxAPI::configureAuth alterna HTML/texto/JSON;
`scp/login.php` usa HTTP 200 com status interno no modo AJAX.

## Convenções candidatas a detalhar

Todas nesta seção são **Proposta**, não esquemas HTTP aprovados:

- Recurso consultável não implica permitir alteração de qualquer atributo.
  Entrada limitada ao caso de uso; guardas de ator/objeto/ação verificadas por
  requisição. Capacidades exibidas no Angular não substituem essa verificação.
- Erros distinguem autenticação, autorização, validação de campo, precondição,
  conflito operacional e falha inesperada. Mensagem localizada não deve ser a
  única informação interpretada pelo frontend; códigos HTTP e identificadores
  estáveis ainda serão escolhidos. Não expor stack, segredos ou contexto bruto.
- Formulários separam definição, valores de instância e comando consumidor.
  Reutilizar validação no servidor; html/media não é metamodelo JSON pronto.
  Campos de plugin exigem representação própria comprovada, não só nome/tipo/rótulo.
- Listagens preservam visibilidade, paginação/ordenação/filtros reais; não aceitam
  expressões ORM arbitrárias. Busca salva/fila é uma capacidade, não SQL genérico.
- Lotes precisam expor resultado agregado e por item quando observável, incluindo
  resultado desconhecido. Sem transação global comprovada, não prometer rollback
  integral nem repetir automaticamente todo o lote após falha.
- Repetição/idempotência será definida por comando. Timeout não comprova ausência
  de efeito; envio, criação e resposta podem duplicar efeitos. Cabeçalho sozinho
  não cria idempotência, e verbo de leitura não pode ocultar mutação nova.
- Exportação distingue solicitação, disponibilidade e transferência; bytes não
  vão dentro de envelope JSON. Exporter::load/isAvailable/isReady/download usa
  sessão/arquivo; download pode consumir o artefato. Não presumir job durável novo.
- Credenciais de configuração são entradas write-only no contrato proposto:
  indicar se configuradas, não devolvê-las ao frontend. Manter/substituir/remover
  devem ser intenções explícitas conforme mecanismo real; campo omitido não apaga
  segredo silenciosamente. Não escolhe armazenamento novo nem altera o `.env`.
- Conteúdo rico do usuário continua conteúdo rico sanitizado; proibir template
  legado na resposta não obriga converter mensagens legítimas em texto sem formato.
- Versão do contrato e baseline de compatibilidade osTicket são conceitos distintos.
  URL/header/semântica de evolução e catálogo OpenAPI serão decisões posteriores.

## Ficha conceitual: autenticação staff (M01/M02)

**Intenção:** iniciar/continuar autenticação original e representar o estado
permitido no frontend novo; sem IAM ou tokens de sessão paralelos.

**Fato observado:** `scp/login.php` valida CSRF/identificador e chama
StaffAuthenticationBackend::process; `include/class.auth.php` processa backends,
authenticate/login e efeitos. StaffAuthenticationBackend::login prepara sessão/MFA;
StaffSession::isValid (`include/class.usersession.php:257`) exige sessão válida
e ausência de MFA pendente. Validação do desafio usa o backend/form original;
ExpiredOTP encerra a sessão no fluxo de login.

**Estados conceituais a representar:** entrada necessária; credenciais rejeitadas;
desafio pendente; desafio rejeitado/expirado; sessão válida; sessão expirada;
restrição operacional; troca de senha/configuração de MFA obrigatória. Não são
nomes de enum aprovados. Login bem-sucedido não significa acesso irrestrito:
guardas por requisição/objeto permanecem necessárias.

**Proposta:** responder estado e próxima interação permitida sem template,
sem identificador de sessão no corpo e sem armazená-lo em estado JavaScript.
Usar o mecanismo de cookie original após validar compatibilidade, atributos,
escopo, rotação e expiração. Token CSRF é conceito distinto do segredo de sessão;
modo de obtenção/envio/rotação ainda será definido sobre o mecanismo original.
Mesmo domínio não comprova compartilhamento; path/host/configuração podem limitar.

**Pendências:** bootstrap, formato dos desafios dinâmicos, falhas sem vazamento,
destinos de navegação permitidos, logout/locks, rotação entre abas, recuperação e
SSO/callbacks. Backend externo exige ficha própria a detalhar; não substituí-lo por senha local.
Reconferir em testes posteriores login pelos campos, MFA, expiração, alternância
e logout em ambos painéis; nenhum desses testes foi executado nesta unidade.

**Fato observado:** osTicketSession::__construct/renewCookie
(`include/class.ostsession.php:26–37,252–265`) têm caminhos distintos de atributos;
renovação considera SameSite/configuração de iframe. UserSessionTrait::refreshSession
(`include/class.usersession.php:129`) condiciona regeneração a GET e mantém janela
de transição. Emissão, renovação, abas e retorno de provedor exigem verificação
conjunta; não foi escolhido SameSite, CORS ou cookie novo nesta unidade.

## Ficha conceitual: configurar conta de e-mail (M12)

**Ator/intenção:** administrador configura remetente, mailbox/SMTP e autenticação
do provedor; operação integral no Reforged, não link para emails.php legado.

**Cadeia original:** Email::create/update, contas mailbox/SMTP, persistência
principal e sinal de criação no controller. Configuração dependente usa
EmailAjaxAPI::configureAuth → getAuthAccount/getAuthConfigForm/saveAuth;
deleteToken usa destroyConfig. A guarda administrativa existe tanto na página
quanto no access do controller AJAX (`include/ajax.email.php:4`).

**Transições propostas:** salvar parâmetros principais e obter conta identificável;
configurar autenticação Basic ou OAuth; concluir autorização externa quando
requerida; verificar configuração efetiva e ativar coleta/transporte conforme
fluxo original. Templates e diagnóstico são operações relacionadas, não efeitos
automáticos desse comando. OAuth configurado não equivale a autorizado.

**Fato observado:** `include/class.email.php:325–335` atualiza mailbox/SMTP antes
de abortar por erros; seus update/save (`:1246`, `:1389`) persistem separadamente.
Não anunciar atualização atômica. Falha final exige reconciliar estado efetivo,
sem inventar rollback. Configuração salva não comprova coleta ou entrega de e-mail.

**Falhas conceituais:** referência inválida, endereço em uso, configuração
inválida, provedor ausente, autenticação incompleta/rejeitada, falha de conexão
ou persistência. Contrato deve preservar distinções sem devolver segredo ou
mensagem técnica bruta. O frontend precisa explicar pré-requisitos de salvamento
e continuação dos modais; testes futuros seguem o fluxo visual natural.

## Cobertura final e critérios de paridade

**Decisão aceita no ADR 0005:** capacidade só estará entregue quando puder ser
concluída no Reforged sem depender da interface antiga, exceto setup. A tela
original voluntária continua disponível; não serve como implementação de função.

Para acompanhar entrega futura, ligar cada intenção a M/O/A/T, contrato, interface,
efeitos, teste visual e regressão. Estados de acompanhamento sugeridos: não
especificada, especificada, implementada, validada ou bloqueada com motivo.
Nesta unidade nenhuma capacidade ganhou status implementada/validada por código.
Alvos ausentes, dependência de plugin e regra sem ponto reutilizável permanecem
pendências; não são exceções novas à paridade. Setup não elimina automaticamente
upgrade SCP da análise. Portal segue em plano separado.

## Próximas decisões, sem implementação automática

1. Aprovar ou ajustar direção de mesma origem e estilo recursos/ações.
2. Fechar desenho de bootstrap e matriz de guardas/estados por entrada, com
   evidência de possibilidade de reutilização sem core change.
3. Definir convenções de contrato e representação de forms/erros/binários;
   especificar operações concretas e suas garantias, não DTOs especulativos.
4. Revisar escolhas por software/API/IAM/dados/frontend/QA conforme impacto,
   formalizar ADRs e só então liberar implementação/primeiro recorte.

Pareceres desta unidade estão registrados em GATE_D_REVIEW; validações documentais
em PROGRESS. Não existem endpoints, layout físico ou integração testada nesta entrega.
