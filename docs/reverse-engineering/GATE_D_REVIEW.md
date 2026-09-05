# Portão D — Registro de revisões

## Estado corrente

Unidade: `codex/scp-parity-integration-design`, entrada `2fa8e56a` (PR #33).
ADR 0005 registra aprovação do responsável para divisão lógica e paridade SCP.
O desenho `INTEGRATION_CONTRACT_DESIGN.md` permanece proposta técnica.

- `api_architect/integration/contratos`: contribuição somente leitura sobre
  alternativas, bootstrap, comandos, formulários, lotes e autenticação/e-mail;
  não é revisão independente das próprias recomendações.
- `security_architect/integration/revisao`: revisão independente IAM somente
  leitura; nenhum achado crítico, alto ou médio. Dois baixos corrigidos e
  reconferidos: ficha externa ainda a detalhar e ciclo inicial/renovação de
  cookie, SameSite e regeneração condicionada a GET.
- Parecer favorável à integração documental, sem achados documentais restantes
  nesse recorte. Guardas por entrada, ciclo completo de sessão, provedores externos,
  autorização por objeto e tratamento de segredos exigem desenho e validação
  próprios antes de implementação. Não comprova integração executável.
- Integrador único escritor, releu os pareceres e reconferiu símbolos relevantes
  de bootstrap, sessão/cookies e persistência parcial da configuração de e-mail.
  Nenhuma missão acessou banco, navegador, credenciais ou executou exploração.

### Histórico — revisão da proposta da PR #33

Unidade mais recente: proposta de capacidades em `MODULE_CAPABILITY_PROPOSAL.md`,
entrada `e68d13dc` (PR #32), branch `codex/module-capability-proposal`.
Instância independente `software_architect/module-proposal/revisao`, cargo
Arquiteto de Software, somente leitura, sem descendentes, rede, banco, credenciais,
Git mutável ou teste comportamental. O integrador é único escritor.

Parecer: **pronta para discussão**, sem achados críticos/altos/médios; dois
ajustes baixos incorporados e reconferidos — unicidade editorial dos 153 IDs
não cria 153 operações disjuntas, e upload por campo/draft/publicação tem etapas
distintas entre M08/M09/M03/M04. Sem achado aberto na reconferência.
Revisadas fronteiras, dependências e amostras de resposta/lock e release/evento/
nota; preservados ADR 0004, menus independentes de módulos e decisões pendentes.
Não é aprovação arquitetural final: API/IAM/dados/frontend/QA serão envolvidos
no desenho técnico pertinente. Auditorias mecânicas/MkDocs são do integrador.

### Contexto dos pareceres anteriores

Os seis pareceres abaixo revisaram a proposta da PR #28, posteriormente
descartada pelo responsável. Não são aprovação da nova análise, de uma nova
recomendação ou do encerramento do Portão D. O reinício é orientado pelo
[ADR 0003](../adr/0003-coexistencia-paineis-portal-separado.md).

O responsável posteriormente confirmou a estratégia do
[ADR 0004](../adr/0004-modulos-reforged-backend-osticket.md). Sua formalização
documental não constitui nova revisão especializada ou prova de viabilidade;
os pareceres históricos abaixo não foram reutilizados como aceite técnico.

A formalização da PR #30 foi centralizada e não produziu novo parecer técnico.
A unidade subsequente de mapeamento SCP possui a revisão específica abaixo,
sem reutilizar os pareceres históricos como aprovação da estratégia atual.

## Revisão do mapa SCP — 2026-09-05

Entrada `33d40fb7`, branch `codex/scp-flow-mapping`, baseline `v1.18.4`.
Instância `qa_engineer/scp-map/revisao`, cargo Engenheiro de Qualidade,
independente das três frentes que produziram insumos. Somente leitura dos
quatro mapas SCP, catálogo AJAX e amostras de código; nenhum descendente,
escrita, banco, navegador, credencial ou teste de produto.

**Parecer:** cobertura estrutural aprovada. Conferidos 47/47 arquivos, 153 IDs
sem duplicação, 26 famílias/229 folhas com uma única cobertura por intervalo,
ordem StaffNav/AdminNav e amostras de logout, filas, drafts, tarefas, thread,
FAQ, exportação e upgrade. Nenhum achado alto/médio nas amostras.

Quatro ajustes baixos incorporados e reconferidos: O28 explicita Ticket::PERM_EDIT
no addcc da tarefa; O43 registra manageFaqAccess/ispublished/save; T51 distingue
guarda própria de TEA_ResendThreadEntry; A51 enumera enable/disable/delete.
Parecer pós-correção aprovado, sem achado aberto no recorte. `git diff --check`
sem erro; avisos de normalização CRLF não foram tratados como falha.

Conferência adicional do catálogo aprovada: guardas STAFF e rotas de forms,
colaboradores, imagens, preview de fila, notas, exportação, sequências,
permissões e avatar correspondem ao código. Auditorias globais de MkDocs/links
foram executadas pelo integrador, não atribuídas ao revisor.

A revisão não certifica cada ramo/configuração/plugin nem execução visual,
atomicidade ou viabilidade de todos os módulos. Não encerra o Portão D, aceita
arquitetura, autoriza implementação ou aceita risco de segurança legado.

## Histórico de revisões da primeira rodada

## Missões e ownership

Entrada: `874efbd8`, baseline `v1.18.4`. A autorização atual inicia o Portão D;
os estados anteriores em `CURRENT_STATE.md`, progresso, plano e índices foram
atualizados pelo integrador nesta unidade. Nenhuma arquitetura foi aceita.

**Decisão operacional:** três revisões somente leitura são necessárias porque
extensão/upstream, identidade e persistência têm riscos distintos. A redação de
todos os arquivos pertence exclusivamente ao agente principal. Perfis seguem
`.codex/agents/`; nenhuma missão autoriza descendentes, edição, Git mutável,
rede, acesso a segredos, banco ou experimentos de exploração.

| Instância | Cargo | Missão delimitada |
| --- | --- | --- |
| software_architect/portao-d/revisao-opcoes | Arquiteto de Software | viabilidade das três opções, integração, acoplamento e migração |
| security_architect/portao-d/revisao-fronteiras | Arquiteto de Segurança e IAM | fronteiras de confiança e controles necessários; revisão documental defensiva |
| data_engineer/portao-d/revisao-integridade | Engenheiro de Dados | ownership, efeitos parciais, idempotência e rollback |
| api_architect/portao-d/revisao-contratos | Arquiteto de APIs | suficiência da fronteira, condições de contrato e decisões pendentes |
| frontend_architect/portao-d/revisao-coexistencia | Arquiteto Frontend | recorte visual, coexistência, navegação e condições de migração |
| qa_engineer/portao-d/revisao-criterios | Engenheiro de Qualidade | rastreabilidade, critérios verificáveis e coerência do checkpoint |

As três primeiras missões encerraram antes do início das três complementares.
A segunda rodada é somente leitura, com o mesmo ownership exclusivo do
integrador; sua necessidade decorre dos critérios expressos do Plano 0001.

Entradas: AGENTS, contexto, checkpoint, progresso, plano, ADRs e os dois novos
documentos do Portão D. Reconfirmar no código somente símbolos relevantes.
Saída exigida: fatos reconfirmados versus herdados, achados priorizados com
caminhos, limitações e parecer. Parar se faltar evidência material, sem presumir
comportamento. As seis revisões avaliam a proposta e seus critérios; contratos
executáveis futuros ainda exigirão revisão própria antes de implementação.

## Pareceres

Pareceres recebidos em 2026-09-05. Software e segurança consideram a primeira
proposta adequada após os ajustes médios; dados é favorável à abertura, com
condições adicionais para escrita futura. Nenhum achado alto ou crítico novo
foi relatado no recorte documental. Isso não elimina achados da baseline.

| ID | Origem / prioridade | Achado | Tratamento pelo integrador |
| --- | --- | --- | --- |
| D-R01 | Software / médio | B depende do mesmo conector e não resolve inviabilidade de bootstrap | recomendação distingue isolamento de processo e redesenho de fronteira |
| D-R02 | Software / médio | prova de plugin genérica omitia condições de ciclo de vida | migração exige pacote incompatível/desativado, upgrade pendente, instâncias, registro e recuperação |
| D-R03 | Software / baixo | visibilidade também depende de encaminhamento | incluído no planejamento, sem alegar teste visual novo |
| D-R04 | Segurança / médio | requisitos de identidade apareciam somente antes de escrita | controles obrigatórios desde a primeira leitura; MFA, sessão, objeto e DTO |
| D-R05 | Segurança / médio | riscos herdados não tinham condição explícita por recorte | tabela de riscos, exclusões de tarefas/anexos e tratamento antes de inclusão |
| D-R06 | Dados / médio | backup de banco não comprova recuperação integral | restauração isolada, banco/arquivos coerentes, bytes e acentuação exigidos |
| D-R07 | Dados / baixo | capacidade transacional depende das engines efetivas | verificação por tabela, charset e collation antes de comandos |
| D-R08 | Dados / baixo | proveniência de evidências estáticas desatualizada | D-E04/D-E05 distinguidos de resultados operacionais herdados |
| D-R09 | API / baixo | erros antes do controlador e Content-Type não delimitados | prova exige alcance da garantia JSON, erros de bootstrap, rota, acesso e correlação |
| D-R10 | API / baixo | conteúdo mínimo do contrato implícito | política de versão, ordenação, limites e página fora de intervalo como entregáveis futuros |
| D-R11 | Frontend / médio e baixo | coexistência e acessibilidade com critérios genéricos | URL direta, recarga, histórico, fila/filtros/ordem/página, isolamento PJAX/assets, foco e estados de tela |
| D-R12 | QA / médio | comparação sem vínculo explícito por critério às evidências | tabela complementar relaciona cada critério da matriz aos IDs D-E01–D-E08 |

## Evidências e limites das revisões

- **Software — fatos reconfirmados:** `api/api.inc.php:25-31` → `main.inc.php:34`
  → `osTicket::start`, bootstrap anterior ao sinal de API; `Dispatcher` e
  `UrlMatcher::dispatch`; `include/class.plugin.php:193-207`;
  `include/class.osticket.php:676-686`; `Ticket::checkStaffPerm:396-423`.
- **Segurança — fatos reconfirmados:** bootstrap API não executa guardas SCP;
  `scp/staff.inc.php:23-115`; `include/class.auth.php:645-674` emite sinal antes
  da conclusão de MFA; `StaffSession::isValid` exige ausência de 2FA pendente.
- **Dados — fatos reconfirmados:** autocommit em `include/mysqli.php`;
  SQL anterior a sinais em `include/class.orm.php:613-722`; ticket antes de
  thread em `include/class.ticket.php:4378-4400`; atualização e resposta antes
  de efeitos complementares em `:3763-3806` e `:3345-3459`. Schema estático
  não garante engines efetivas da instalação.
- **API — fatos reconfirmados:** `Dispatcher::resolve/UrlMatcher::dispatch`
  podem responder antes do controlador; `Http::response`, em
  `include/class.http.php:36-50`, usa HTML por padrão. Parecer favorável com os
  dois ajustes baixos incorporados.
- **Frontend — fatos reconfirmados pelo revisor:** interceptação PJAX em
  `scp/js/scp.js:1183-1192`, fila em `scp/tickets.php:111-142` e ordenação em
  `include/staff/templates/queue-tickets.tmpl.php:35-59`. Parecer favorável após
  concretizar critérios de coexistência; observação visual permanece herdada.
- **QA — verificação documental:** três opções presentes; migração, limites e
  separação proposta/aceite coerentes. Parecer favorável após rastreabilidade
  explícita por critério. Não repetiu build nem testes de produto.
  Releitura independente posterior confirmou D-R12 resolvido e parecer favorável
  à apresentação, sem achado documental remanescente dessa missão.
- **Herdado:** comportamento visual, resultados de e-mail, falhas em runtime,
  condições das fixtures e achados de tarefas/arquivos/upload. Nenhuma missão
  acessou rede, segredos, banco ou navegador; não houve teste ofensivo.

O integrador leu os pareceres, incorporou as correções e conferiu os trechos de
bootstrap, plugins, validade de sessão, visibilidade e criação de ticket. A
integração documental não prova viabilidade operacional nem fecha o portão.

## Pendências para próxima unidade

1. apresentar direção/recorte e riscos ao responsável, com seis revisões e
   correções incorporadas; aguardar aprovação, rejeição ou ajuste;
2. somente após aprovação explícita, registrar ADR final. Identidade, versões,
   contratos e detalhes de implementação não podem ser preenchidos por inferência.

## Validação da unidade

Build estrito do MkDocs e `git diff --check` executados pelo integrador.
Navegação preserva os seis contextos e oito categorias; 58/58 documentos, sem
duplicação ou ausência. Auditoria local não encontrou destino ausente do
Reforged; seis referências históricas/incorporadas seguem preservadas.
Esses testes validam documentação, não contratos executáveis ou runtime.
