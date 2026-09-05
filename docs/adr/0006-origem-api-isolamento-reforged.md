# ADR 0006 — Mesma origem, recursos/ações e isolamento do Reforged

- Status: **Aceito**, limitado às três decisões abaixo
- Data: 2026-09-05
- Responsável: mantenedor, por confirmação explícita seguida de autorização para formalizar
- Complementa ADRs 0001, 0003, 0004 e 0005; não encerra o Portão D

## Contexto

A proposta da PR #34 comparou mesma origem versus origens separadas e API por
recursos/ações versus CRUD genérico ou reprodução do AJAX legado. O responsável
aprovou as duas direções e permitiu separar os arquivos próprios em uma pasta
específica para não misturá-los ao principal. A autorização subsequente confirma
a formalização; não escolhe um nome de pasta PHP ou mecanismo de publicação.

## Decisão aceita

1. Painel Reforged, API destinada a ele e SCP legado serão publicados na mesma
   origem web: protocolo, host e porta, com caminhos distintos. Isso não obriga
   correspondência literal entre URL e diretório físico.
2. A API será organizada por recursos e ações explícitas de domínio. Consultar
   um ticket e responder, transferir ou mesclar tickets são intenções distintas.
   Cada operação preservará o fluxo e os controles originais conforme ADR 0004;
   não será um CRUD genérico sobre tabelas nem uma reprodução de respostas AJAX.
3. Isolar os arquivos próprios do Reforged da árvore de código original. Manter
   o frontend em `frontend/`, como já aceito no ADR 0001, e prever localização
   própria para os módulos PHP novos. Não mover nem refatorar o upstream.

O endereço `/scp/reforged` continua candidato; não é aprovado por este ADR.
A pasta PHP, seu layout interno, destinos de build e regras do servidor web
continuam propostos no [desenho físico](../reverse-engineering/REFORGED_PHYSICAL_DESIGN.md).
Nenhuma nova pasta de aplicação é criada nesta unidade.

## Alternativas consideradas

- Origens separadas: não selecionadas como direção de publicação dos painéis/API;
  uma necessidade futura exige decisão explícita, não alteração silenciosa.
- CRUD de tabelas ou reprodução do AJAX: não selecionados; não preservam por si
  só intenções, efeitos e contratos estruturados exigidos.
- Misturar arquivos novos em diretórios upstream: não atende ao isolamento.
- Colocar todo o produto novo em uma única pasta: não foi escolhido; mover
  `frontend/` exigiria revisar ADR 0001. Isolamento não significa pasta única.

## Consequências e evidências

Mesma origem não comprova sessão compartilhada. O desenho da PR #34 rastreia
osTicketSession::__construct/renewCookie e StaffSession::isValid; path, cookies,
MFA, expiração e logout continuam sujeitos a desenho e validação próprios.
`main.inc.php` inicia o backend e pode produzir respostas antes do controlador;
`scp/staff.inc.php` e `scp/admin.inc.php` combinam guardas e apresentação.
Separar arquivos não resolve automaticamente esses acoplamentos.

Uma pasta específica não é uma barreira de acesso web. Publicação deve distinguir
entradas/assets públicos de fontes internas, testes, documentação e segredos.
Não publicar a raiz inteira do repositório como artefato do Reforged. Governança
e `.env` continuam fora da aplicação, conforme AGENTS e decisões anteriores.

## Pendências e limite

Permanecem abertos: organização física exata, bootstrap, matriz de guardas,
ciclo de sessão, contratos concretos, versionamento, versões de dependências,
empacotamento, primeiro recorte e liberação de implementação. Mesma origem de
publicação não escolhe a topologia do servidor de desenvolvimento Angular.
Propostas técnicas restantes da PR #34 não são aceitas em bloco por este ADR.

## Referências

- [Desenho de integração e contratos](../reverse-engineering/INTEGRATION_CONTRACT_DESIGN.md)
- [ADR 0001 — Frontend separado](0001-frontend-angular-primeng.md)
- [ADR 0004 — Fronteira modular](0004-modulos-reforged-backend-osticket.md)
- [ADR 0005 — Paridade SCP](0005-modulos-logicos-paridade-scp.md)
