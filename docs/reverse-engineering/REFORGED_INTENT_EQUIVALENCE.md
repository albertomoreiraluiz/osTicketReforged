# Reforged — Intenções de entrada e equivalência comportamental

## Estado e alcance

Data 2026-09-05; branch `codex/guard-intent-matrix`, entrada `3eb3d7cf` (PR #38),
baseline `v1.18.4`, commit `8d38b0619649a50ee7cbbf37085f5d297fdc6f36`.
**Decisão aceita:** ADR 0007 permite adaptar a coordenação acoplada após análise.
**Entrega documental:** matriz de intenções, efeitos e critérios; não API,
contrato aprovado ou prova de equivalência com aplicação Reforged inexistente.

O recorte é entrada/contexto/regularização do SCP, não todas as ações de negócio.
Complementa [G01–G09](REFORGED_GUARD_CONTRACTS.md) e
[tipos de entrada](REFORGED_ENTRY_SESSION_DESIGN.md). Identificadores I01–I12
são de rastreabilidade documental, não nomes de endpoints ou permissões novas.

## Matriz de intenções

Original = **Fato observado estático**; tradução = **Proposta a validar**.
Paths de páginas abaixo pertencem a `scp/`; métodos/classes a `include/`.
Nenhuma linha autoriza contornar guardas ou simular privilégios por flags.

| ID / intenção | Fonte e coordenação original | Efeito e requisito de tradução |
| --- | --- | --- |
| I01 — iniciar acesso | login.php:16–33 prepara contexto, mensagem e destino; staff.inc.php:52–80 encaminha identidade ausente/inválida | apresentar login sem atribuir acesso operacional; não retornar destino arbitrário ao cliente |
| I02 — autenticar | login.php:62–86 chama checkCSRFToken, Validator::is_userid e StaffAuthenticationBackend::process | distinguir rejeição, desafio e sessão válida; sucesso da etapa de credenciais não comprova acesso ao SCP |
| I03 — concluir desafio | login.php:89–115 usa formulário e validação do backend MFA existente | representar continuação pendente; backend conserva política e efeitos; não criar verificador paralelo |
| I04 — autenticação externa | login.php:116–136 aciona backend externo/SSO | preservar início e retorno próprios do provedor; não presumir formulário de senha universal |
| I05 — recuperar acesso | pwreset.php e T08/T09 do mapa transversal | separar solicitação, entrega e conclusão; mensagem enviada não significa senha alterada |
| I06 — consultar/operar como equipe | staff.inc.php:25–148, depois controlador do recurso | ACL, identidade, disponibilidade, atividade, CSRF e regularização precedem controles do objeto/ação; menus não substituem autorização |
| I07 — administrar | admin.inc.php:14–36 inclui contexto staff e exige administração | contexto administrativo é adicional, não sessão paralela; falha não deve executar a ação administrativa |
| I08 — consultar/atualizar próprio perfil | profile.php:16–55; Staff::updateProfile em class.staff.php:818 | separar preferência comum de regularização; confirmação e releitura do estado após salvar |
| I09 — cumprir troca de senha | staff.inc.php:138–148 → profile.php:35–44 → StaffAjaxAPI::changePassword em ajax.staff.php:74 | a página direciona ao modal; formulário, setPassword e persistência originais permanecem; modal aberto/cancelado não conclui a exigência |
| I10 — configurar MFA obrigatório | profile.php:44–52 → StaffAjaxAPI::configure2FA em ajax.staff.php:236 | configuração possui etapas e confirmação; salvar dados não é necessariamente concluir verificação; reconsultar exigência após o fluxo |
| I11 — encerrar acesso | logout.php:16–37 consulta validateLinkToken e coordena logOut, limpeza de sessão/cookie e remoção de locks | presença da checagem não comprova bloqueio efetivo; redirecionamento não comprova atomicidade ou liberação de locks; verificar pós-condições por cenário, sem reproduzir falhas como requisito |
| I12 — lidar com indisponibilidade | staff.inc.php:83–129 e admin.inc.php:30–36 | distinguir offline de upgrade e papel do ator; não executar upgrade como teste de navegação nem converter aviso em sucesso de operação |

## Precedência, exceções e efeitos

**Fatos estáticos:** no caminho protegido, ACL precede obtenção de identidade;
identidade válida precede verificações operacionais. Renovação de atividade
ocorre antes da validação CSRF. Upgrade é tratado antes da regularização de
perfil; quando ambas as exigências de perfil se aplicam, senha precede MFA.
Administração inclui previamente o contexto staff. getUser rejeita agente inativo
inclusive administrador; não interpretar exceção de modo offline como exceção
de atividade da conta. Ver fontes G01–G09 para métodos originais.

**Proposta de tradução por intenção:** classificar cada operação explicitamente
como início de autenticação, continuação, regularização própria, operação comum,
administração, saída ou diagnóstico autorizado. Exceções associadas a páginas
no legado não se tornam liberação de uma família inteira da API. O catálogo
de operações permitido durante cada impedimento ainda deve ser conferido por
fluxo e revisão IAM, sem experimentar formas de contornar a restrição.

**Limite:** uma resposta de contexto não concede acesso futuro. Revalidar no
servidor antes de executar a operação; não confiar em estado Angular antigo.
Renovação de sessão, mensagens, envio de desafio/recuperação, atualização de
configuração e locks são efeitos relevantes, não inferidos de um HTTP 200.
Os efeitos de observabilidade herdados descritos em G06/G07 continuam pendentes
de desenho próprio; não os reproduzir como requisito de paridade.

## Evidências reaproveitáveis e lacunas

**Fato observado documental:** auditoria de QA e reconferência do integrador.
Fonte principal: [matriz visual](INTERFACE_COVERAGE_MATRIX.md). Não se transporta
um rótulo global de cobertura para combinações que não foram exercitadas.

| Caso | Evidência histórica localizada | Limite / trabalho restante |
| --- | --- | --- |
| Login staff positivo | BHV-002; seção Equipe — shell operacional com administrador | comprova preenchimento e entrada, não MFA/expiração ou alternância Reforged |
| Agente restrito | Equipe — agente restrito, entrada e ordem 8 | sessão de agente e retorno ao painel ao tentar Administração; não todas as combinações de permissão |
| Administração positiva | Administração — shell e mapa global, BHV-003 | navegação autorizada; não estado offline/upgrade |
| Preferência de perfil | BHV-024; Usuários, organizações e perfil — repetição funcional | fuso alterado/reaberto/restaurado; não troca de senha nem configuração MFA |
| Logout staff | histórico HTTP complementado por V06 abaixo | saída e nova entrada protegida exigindo login comprovadas visualmente; locks, outras abas e Reforged continuam não validados |
| Recuperação staff completa | não localizada no recorte auditado | recuperação pública vazia não comprova solicitação staff válida, entrega e conclusão |
| Senha obrigatória | controles observados sem submissão | falta sequência funcional própria |
| MFA obrigatório e combinação com senha | configuração administrativa lida, sem mutação | falta setup, desafio e prioridade no fluxo natural |
| Offline por papel | configuração e página Offline inventariadas com sistema conectado | falta transição e retorno por papel; página ativa não significa sistema offline |
| Expiração/upgrade | nenhuma sequência funcional dedicada localizada | não presumir resultado por incidente de login; upgrade não será provocado nesta instalação |

Busca delimitada à matriz visual, análise comportamental, manifesto e revisão.
Ausência de evidência localizada não prova impossibilidade do comportamento.
Os resultados anteriores permanecem válidos no seu alcance; estas lacunas são
critérios adicionais da integração, não alegação de repetição já concluída.

## Plano de verificação por caso

**Proposta operacional sob ADR 0007:**

1. Registrar baseline instalada, configuração relevante não secreta, papel e
   fixture fictícia; reaproveitar evidência somente se essas condições forem compatíveis.
2. Percorrer a tela e seus controles na ordem visual de GOV-019–022; ler ajuda,
   preencher controles renderizados e salvar/reabrir a entidade principal antes
   de configurar dependências em modal.
3. Registrar estado inicial, ação, estados intermediários, confirmação ou
   cancelamento, resultado persistido e nova interação após a ação.
4. Para I09/I10, usar identidade de teste e plano de recuperação; mudanças de
   credenciais exigem etapa assistida pelo responsável conforme limites da
   ferramenta de navegador. Não alterar a credencial administrativa principal.
5. Para offline, preservar configuração inicial e acesso administrativo de
   recuperação; não executar mudança antes de planejar retorno. Não modificar
   schema ou simular upgrade em banco funcional para preencher a matriz.
6. Redigir contrato e comparação original/Reforged somente após as evidências;
   a futura implementação deverá repetir os cenários e verificar regressão do legado.

Critério de aceite por intenção: mesmas pré-condições, decisões, efeitos
pertinentes e resultado funcional, com apresentação e transporte novos. Não
exigir HTML idêntico, não copiar falhas e não prometer atomicidade sem prova.

## Passagem visual após PR #39 — checkpoint concluído, regularização pendente

2026-09-05, branch `codex/visual-profile-validation`, entrada `0ea1ab7a`.
Sessão administrativa ativada pelo responsável e reconfirmada no navegador.
Único agente: navegação sequencial e sessão compartilhada, sem benefício de
escrita/navegação paralela. Escopo: perfil, validações comuns e precondições de
regularização; não campanha de segurança nem cobertura de todo o SCP nesta passagem.

- V01: Painel de Controle → Diretório do Agente → Meu Perfil. Um índice obsoleto
  no ícone de ajuda exigiu reinício no primeiro submenu. Gráfico exibiu datas e
  valores; não comprova correção do NaN histórico. Clique no ícone de ajuda não
  expôs conteúdo adicional; ajuda não declarada lida. Diretório exibiu três agentes.
- V02: Conta → Alterar Senha → Atualizar com campos vazios. Modal exibiu
  obrigatoriedade, senha atual incorreta e comprimento mínimo. Cancelado, sem
  inserir senha, sem sucesso de alteração e sem concluir I09.
- V03: Conta → Opções de configuração → Email. Texto explicou envio de códigos;
  tela seguinte pediu endereço e Próximo para verificar. Cancelado sem envio,
  sem ativar MFA e sem concluir I10; credencial administrativa preservada.
- V04 exercitado: plano prévio de 25 → 20 → 25 executado por controles visíveis.
  Ambas gravações exibiram Perfil atualizado com sucesso. Reabertura pelo link
  Meu Perfil e aba Preferências confirmou primeiro 20 e depois 25. Preferência
  original restaurada, sem exclusão ou restauração de banco. Evidência de I08,
  não de regularização obrigatória.
- V05 observado: Assinatura explicou uso opcional em e-mails/respostas e exibiu
  editor rico; não houve edição nem envio de mensagem.
- V06 exercitado: Sair pelo link natural → Login do Agente, Autenticação Necessária.
  Logo do formulário apontando para scp/index.php clicado → voltou a login.php
  com Autenticação Necessária. Pós-condição visual de nova entrada protegida
  comprovada. Não houve fixture de lock, teste de outra aba, atomicidade ou
  Reforged; essas conclusões não decorrem do redirecionamento. Sessão encerrada
  intencionalmente por este teste, não expirada nem perdida por falha presumida.
- V07 exercitado: login vazio → ID de usuário inválido e link Esqueci minha senha.
  Nenhuma credencial real submetida; não conta como falha de autenticação com
  valores válidos ou como teste de CSRF.
- V08 exercitado parcialmente: Esqueci minha senha → formulário staff de nome/
  e-mail → Enviar E-mail vazio → título Foi enviado um e-mail de confirmação e
  explicação condicional Se as informações fornecidas forem válidas. Botão Login
  retornou ao formulário inicial. Não comprova envio/entrega, consumo de link ou
  mudança de senha; I05 completo continua pendente.

**Corroboração estática:** hashes SHA-256 iguais entre checkout e homologação
para scp/profile.php, login.php, logout.php, pwreset.php, include/ajax.staff.php
e class.staff.php. Isso compara seis arquivos, não toda a instalação/plugins.
pwreset.php, ramo sendmail, explica a mensagem genérica; a observação visual
continua sendo a evidência do resultado apresentado, sem envio SMTP comprovado.

**Retomada operacional:** a sessão inicial foi usada e sua saída foi testada.
O navegador está no login. Próximo passo é autenticar uma identidade fictícia
reservada aos cenários de regularização, preservando a conta administrativa;
não criar/modificar credenciais por automação nem pedir senhas pela conversa.
Senha obrigatória/MFA, recuperação válida, offline e expiração continuam pendentes.

## Histórico — verificação da PR #39

**Observado no navegador em 2026-09-05:** abertura de `http://localhost/scp/`
resultou em `scp/login.php`, título Login do Agente, mensagem Autenticação
Necessária, dois campos e botão Iniciar Sessão. Sem sessão autenticada ativa.
Nenhuma credencial foi submetida: não é tentativa falha de autenticação nem
evidência de CSRF, expiração ou defeito do backend.

A retomada visual depende de autenticação pelos controles renderizados. Não
usar bridge, chamada HTTP direta, alteração de sessão ou banco para suprir essa
precondição. O acesso de automação atual não oferece transferência documentada
do segredo local ao campo sem expô-lo em saída; não imprimir o `.env` para fazê-la.
Ativação manual da sessão permite retomar os cenários normais; quando alcançar
mudança de credenciais, a conclusão dessa etapa também será assistida.

Documentação e desenho estático podem ser integrados antes dessa intervenção.
Matriz não encerra Portão D e não autoriza implementação de M01/M02, rotas,
bootstrap ou contratos ainda propostos.
