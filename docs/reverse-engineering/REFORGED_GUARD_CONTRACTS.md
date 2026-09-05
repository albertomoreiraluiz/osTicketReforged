# Reforged — Guardas reutilizáveis e contratos de contexto

## Estado e conclusão

**Fatos estáticos e propostas**, sem implementação. Data 2026-09-05, branch
`codex/reforged-guard-contracts`, entrada `1580ea20` (PR #36), baseline v1.18.4.
Continuação autônoma autorizada até necessidade real de intervenção.

**Fato observado:** existem métodos chamáveis para as verificações individuais.
**Inferência delimitada:** a combinação completa aplicada pelas páginas staff/admin
não foi encontrada como serviço agregado independente de apresentação nos pontos
pesquisados. Não é prova universal de impossibilidade ou ausência em plugins externos.

O ADR 0004 já aceita coordenação de chamadas. Não é preciso reabrir esse princípio.
A questão específica é a adaptação de combinações/exceções que só existem inline
nos controladores. Essa decisão está delimitada no
[ADR 0007 aceito](../adr/0007-coordenacao-guardas-controladores.md).
Aceite posterior à PR #37: análise comportamental dos trechos muito acoplados
orientará sua reprodução, com rastreio e equivalência por caso. O mapa abaixo
continua evidência estática da PR #37; não houve teste visual nesta formalização.

## Mapa de reutilização

**Fato observado:** definições e chamadores abaixo foram reconferidos pelo
integrador após rastreio independente. Métodos não são classificados como puros;
alguns consultam configuração, validam backend ou produzem efeitos.

| ID | Definição existente | Chamador e responsabilidade ainda na página |
| --- | --- | --- |
| G01 | Validator::check_acl, `include/class.validator.php:357` | `scp/staff.inc.php:25`: consulta ACL staff; negação termina a página |
| G02 | StaffAuthenticationBackend::getUser, `include/class.auth.php:702` | staff.inc:64–80 obtém identidade e combina existência/ID/isValid com mensagem e login |
| G03 | StaffSession::isValid, `include/class.usersession.php:257`; isValidSession:200 | isValid já combina validade e ausência de desafio MFA; não copiar essa expressão/cálculo para outro backend |
| G04 | Staff::isActive/isAdmin, `include/class.staff.php:643,671` | staff.inc:83–98 combina administração, atividade, disponibilidade do sistema e logout |
| G05 | osTicket::isSystemOnline/isUpgradePending, `include/class.osticket.php:71,75` | staff.inc:92 e :123–129; admin.inc:30–36 têm tratamento e exceções de upgrade |
| G06 | UserSessionTrait::refreshSession, `include/class.usersession.php:129` | staff.inc:104 chama antes da checagem CSRF, sem examinar retorno; renovação não é leitura sem efeitos |
| G07 | osTicket::checkCSRFToken, `include/class.osticket.php:115` | staff.inc:108–116 seleciona métodos, resposta e meta de HTML; preservar validação, não meta como contrato |
| G08 | Staff::forcePasswdChange/force2faConfig, `include/class.staff.php:407,411` | staff.inc:138–148 aplica prioridade senha→MFA, exceções e carregamento de perfil |
| G09 | Staff::isAdmin, `include/class.staff.php:671` | `scp/admin.inc.php:16–22` exige contexto/admin e escolhe redirect/include em falha |

Precisões importantes:

- G02 rejeita staff inativo independentemente de ser admin. A exceção em G04
  não significa que administrador inativo possa acessar o painel.
- isSystemOnline já considera ausência de upgrade. Não reconstruir seu cálculo.
- force2faConfig considera configuração global e backend selecionado/configurado;
  invocar o método, não reimplementar o comentário da função.
- A ordem de efeitos, a precedência de exigências e as exceções de página são
  parte da política de coordenação, não apenas serialização de um booleano.

**Fato observado — limite de observabilidade:** checkCSRFToken registra o token
rejeitado (`include/class.osticket.php:123–125`); refreshSession pode registrar
o identificador de sessão no ramo TTD (`include/class.usersession.php:132–135`).
Reutilizar esses métodos não garante ausência de segredos nos logs herdados.
Esta constatação é estática, sem valores ou reprodução. O tratamento desses
efeitos exige desenho e revisão próprios antes de implementação/runtime;
não autoriza alterar o core nem reproduzir falhas conhecidas.

## Alternativas verificadas e limite da busca

`scp/ajax.php:19–25` adapta staffLoginPage e depois inclui staff; `ajax.scp`
é emitido em :324, após as guardas. `scp/apps/dispatcher.php:20–41` inclui
staff/admin antes dos sinais de apps e ainda manipula navegação. Esses sinais
não foram encontrados como interceptação de toda a política antes da apresentação.

`include/class.ajax.php:29`, AjaxController::staffOnly, verifica identidade/validade;
não agrega ACL, estado operacional, regularização e administração. Controller::access
e os registries de autenticação/sinais também não representam o agregado completo.

Escopo do rastreio: definições em include/class*.php; usos nas páginas scp;
buscas de check_acl/forcePasswdChange/force2faConfig no produto; dispatchers,
autenticação, plugins e sinais. Árvores de terceiros, .local e setup ficaram fora
da busca ampliada; plugins externos instalados não foram investigados. Nenhum
runtime, rede, banco, segredo ou ensaio de exploração foi utilizado.

Não repetir indefinidamente uma busca pelo nome de um bootstrap: o próximo
passo depende do tratamento explícito de G04/G05/G08 e das respostas/efeitos,
sem afirmar que um include ou novo diretório resolve esses pontos.

## Contrato de contexto — proposta independente do transporte final

**Proposta de campos semânticos**, não endpoint, DTO aceito ou JSON implementado.
O contexto ajuda a compor a interface; não é autorização reutilizável para
pedidos posteriores. Cada operação executará seus próprios controles.

| Campo candidato | Semântica e limite |
| --- | --- |
| authentication | estado anônimo, desafio pendente ou sessão válida; não confundir autenticação válida com operação permitida |
| interaction | próxima interação permitida, como login/desafio/regularização; somente identificadores definidos no contrato, não URL arbitrária |
| actor | projeção mínima da identidade, apenas quando autorizada; nunca entidade ORM completa ou segredos de sessão |
| requirements | exigências relevantes ao ator, como senha/MFA; prioridade e operações de regularização dependem da decisão G08 |
| navigation | capacidades necessárias ao menu no contexto atual; ausência de item não é única forma de negar acesso; não enumerar objetos inacessíveis |

Não devolver detalhamento interno do sistema, identidade de conta inválida ou
permissões a quem não passou pelas guardas de leitura desse contexto. Respostas
de contexto/desafio/erro autenticado propõem cache desabilitado, sem armazenamento
compartilhado. Cabeçalhos concretos, autenticação da entrada, idioma e token CSRF
terão contrato próprio; a aquisição do contexto pode gerar atividade/renovação.
Não anunciar operação de contexto como consulta sem efeitos de sessão.

## Contrato de falha — proposta

Separar resposta de falha operacional de estado normal de autenticação/desafio.
Envelope candidato: `error.code`, `error.message`, `error.fields` quando aplicável
e `requestId` opaco gerado pelo servidor. Nomes e códigos ainda não aceitos;
não devolver campos de entrada sensíveis, stack, SQL ou mensagem interna bruta.

| Categoria | Semântica para a interface | Limite |
| --- | --- | --- |
| Entrada inválida | corrigir campos do comando | não confundir com credencial rejeitada nem emitir valor sensível recebido |
| Autenticação necessária | iniciar/retomar o fluxo apropriado | não tratar todo desafio como logout; não reenviar mutação automaticamente |
| Acesso negado | ação não disponível nesse contexto | política de existência de objeto e informação divulgada requer contrato por recurso |
| Regularização necessária | executar interação permitida de senha/MFA | conjunto permitido depende da política de coordenação formalizada |
| Estado operacional impeditivo | explicar impedimento permitido ao ator | offline/upgrade não é necessariamente falha inesperada; preservar exceções legítimas |
| Conflito/efeito incerto | reconciliar estado antes de repetir | timeout não comprova que gravação/envio não aconteceu |
| Falha inesperada | mensagem segura e correlação de suporte | cobertura de falhas anteriores ao controlador ainda depende do bootstrap |

**Fato observado adicional:** Http::response (`include/class.http.php:36`)
usa HTML por padrão e header_code_verbose (:18) possui lista limitada de status.
Não basta escolher códigos no documento e presumir que esse emissor os suporta.
Emissor, códigos HTTP, headers e versão do contrato exigem desenho posterior;
nenhuma alteração do helper original está autorizada.

## Decisão aceita e próxima entrega

O [ADR 0007](../adr/0007-coordenacao-guardas-controladores.md) aceita
adaptar no código novo somente a coordenação inline identificada, chamando os
mecanismos originais e documentando equivalência por intenção. Não pede alteração
do core, reimplementação de algoritmos, novo IAM ou dispensa de permissões.

Próximas entregas: matriz finita de intenções/exceções/efeitos, contratos e plano
de validação comportamental. Reutilizar evidências anteriores somente quando
cobrirem o cenário; completar lacunas pelo fluxo natural do frontend original.
Não considerar bootstrap resolvido ou endpoint autorizado por esse aceite.
Campos/erros acima continuam propostas; implementação sujeita ao Portão D.
