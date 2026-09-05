# Módulos Reforged — Proposta por capacidades

## Estado e limite

**Divisão lógica aceita no ADR 0005; detalhes técnicos continuam propostos.**
Origem: 2026-09-05, entrada `e68d13dc` (PR #32), branch
`codex/module-capability-proposal`, baseline `v1.18.4`, proposta integrada pela PR #33.
O responsável aprovou M01–M15 posteriormente, exigindo cobertura integral do SCP
sem dependência do frontend legado, exceto setup. O
[ADR 0005](../adr/0005-modulos-logicos-paridade-scp.md) formaliza esse aceite.
Não inicia implementação nem escolhe framework, URLs, pacotes ou primeiro recorte.

**Decisões aceitas:** ADR 0004 fixa backend intacto, módulos/endpoints novos com
reutilização das regras e respostas sem HTML legado; todo o SCP e coexistência.
GOV-026/Plano 0002 delegam primeira UX e priorizam funcionalidade, não aprovação
automática da arquitetura técnica. Portal do usuário continua separado.

**Fatos documentados de entrada:** [mapa SCP](SCP_FLOW_MAP.md),
[operação](SCP_OPERATIONAL_FLOWS.md), [administração](SCP_ADMIN_FLOWS.md) e
[transversais](SCP_TRANSVERSAL_FLOWS.md). A cobertura de 153 registros foi revisada
na PR #31; não é prova de extração executável ou nova execução comportamental.

## Por que não dividir pelo menu

**Inferência:** uma tela de ticket consome usuário, departamento, formulário,
fila, thread, anexo, lock e e-mail. Já uma configuração administrativa pode
alterar uma capacidade usada no atendimento. A organização de navegação deve
poder evoluir sem mover regras ou criar duas implementações da mesma operação.

| Alternativa de agrupamento | Benefício | Custo no código mapeado | Avaliação proposta |
| --- | --- | --- | --- |
| Um módulo por página/rota | fácil localizar tela de origem | duplica cruzamentos página/modal/AJAX; confunde transporte com domínio | manter como rastreabilidade, não fronteira principal |
| Apenas operação/administração/serviços gerais | poucos grupos iniciais | grupo geral acumula forms, threads, busca, arquivos e export; administração mistura capacidades com guardas diferentes | útil para leitura, insuficiente como divisão de responsabilidades |
| Capacidades com coordenação explícita por caso de uso | aproxima fluxos relacionados e revela serviços compartilhados | mais fronteiras documentais; exige evitar fragmentação prematura | recomendada nesta proposta, sujeita à revisão e aprovação |

Não se propõem 15 microsserviços, bancos, instalações ou pacotes obrigatórios.
Os 15 agrupamentos abaixo são **módulos lógicos aceitos**; podem inicialmente
coexistir no mesmo artefato quando o desenho físico for decidido. Serviços de
apoio não ganham endpoints CRUD universais só por aparecerem no mapa.

## Catálogo lógico aceito e responsabilidade principal

Cada ID O/A/T tem exatamente um responsável principal nesta matriz. Outros
módulos colaboram sem assumir a mesma coordenação. A atribuição não muda o
ownership das tabelas originais, nem cria proprietário exclusivo do dado legado.
Intervalos são inclusivos; IDs M servem apenas à discussão.

A unicidade é editorial, não prova de 153 operações técnicas disjuntas:
O22/O33 encaminham a T47–T52, que descrevem etapas compartilhadas. O conjunto
evita fluxos esquecidos; não deve gerar endpoints duplicados para a mesma intenção.

| ID | Capacidade lógica | Fluxos principais | Responsabilidade e fronteira |
| --- | --- | --- | --- |
| M01 | Integração e contexto de execução | T01–T03, T22, T41 | entrada, contexto de sessão/idioma/config de UI, dispatch e recurso público do login; não regras do ticket nem autenticador paralelo |
| M02 | Autenticação e perfil próprio | T05–T14 | login, MFA, recuperação, logout, senha e preferências; gestão de outros agentes fica em M10 |
| M03 | Tickets | O03–O23 | consulta do objeto, criação, atendimento, estado, encaminhamento, relações e lotes; aciona recursos compartilhados com contexto do ticket |
| M04 | Tarefas | O25–O33 | ciclo da tarefa livre ou ligada ao ticket, atribuição, nota/resposta, estados e lotes; não copiar Ticket para implementar Task |
| M05 | Usuários e organizações | O34–O42, T32 | diretório, perfil, conta do usuário gerida pelo staff, vínculo/importação, notas rápidas; não portal público nem identidade staff |
| M06 | Conhecimento | O43–O44 | artigos, categorias, publicação/acesso e respostas prontas; utilização de resposta pronta no ticket preserva contexto do chamado |
| M07 | Filas e pesquisa | O01–O02, O24, A05, A51–A52, T23–T27 | critérios, buscas salvas, filas, colunas, ordenação, contagens e solicitação de exportação de fila; não alterar ticket por query genérica |
| M08 | Formulários e dados configuráveis | A42–A46, A56, T19–T20, T33 | definição/campos/respostas, listas, metadados de apresentação, validação e numeração; não decidir estado de ticket apenas por editar lista |
| M09 | Conversas e recursos de composição | T15–T18, T21, T28, T42–T52 | rascunhos, imagens, locks, variáveis/assinaturas, colaboradores e ações de entrada; contexto/guardas do objeto continuam obrigatórios |
| M10 | Equipe e estrutura de acesso | A24–A35 | agentes, equipes, departamentos, papéis, acessos estendidos e gestão administrativa de senha/MFA; reutiliza identidade/permissões existentes |
| M11 | Políticas de atendimento | A20–A23, A36–A41 | tópicos, filtros/banlist, SLA e calendários; aplicação das políticas continua no backend original |
| M12 | E-mail e mensagens automáticas | A11–A17, A49–A50 | contas, coleta/transporte, autenticação de conta, diagnóstico e templates; resposta do ticket não vira envio SMTP isolado |
| M13 | Configuração e conteúdo do sistema | A01–A04, A06–A10, A47–A48, T29–T31 | preferências globais, páginas/textos, idiomas e ajuda; ajustes referenciam capacidades específicas, não replicam seus comandos |
| M14 | Extensões e integrações externas | A18–A19, A53–A55, T04 | API keys, plugins/instâncias e apps registrados; instalação/capacidades condicionadas ao código disponível, sem criar plataforma nova |
| M15 | Relatórios e operação técnica | O45, T34–T40 | painel/diretório, entrega de exportação, cron, logs, diagnóstico, auditoria e upgrade; manutenção privilegiada separada das consultas comuns |

**Proposta de organização interna, não separação de implantação:** M09 distingue
composição, participação e ações de entrada; M15 distingue consultas operacionais,
exportação e manutenção. Esses subgrupos impedem que um ponto genérico dê acesso
indiferenciado a recursos de riscos distintos.

## Dependências por caso de uso

As relações abaixo significam colaboração ou dados/configuração necessários,
não chamadas HTTP internas, importações PHP aprovadas ou ordem de entrega.
Não é um grafo acíclico do core: o legado tem referências cruzadas.

Arquivos têm um ciclo único com entradas distintas: M08 integra upload vinculado
a FileUploadField (`include/ajax.forms.php:378`); M09 integra imagens inline e
rascunhos via DraftAjaxAPI. A associação/publicação final pertence ao comando
de ticket/tarefa em M03/M04. Não há dois repositórios de arquivos propostos,
nem upload temporário equivale a mensagem publicada.

| Caso de uso | Coordenador proposto | Colaborações e motivo |
| --- | --- | --- |
| Abrir ticket | M03 | M01/M02 contexto; M05 solicitante; M08 campos/numeração/anexos; M10 departamento/atribuição; M11 tópico/SLA; M09 thread/draft; M12 notificações |
| Responder ticket | M03 | M09 lock, composição e participantes; M08 anexos; M06 resposta pronta; M12 envio pelos mecanismos do domínio; M10 papel/acesso |
| Criar tarefa a partir de entrada | M04 | M03 verifica contexto do ticket quando aplicável; M09 origem/anexos; M08 formulário; M10 elegibilidade; efeitos cruzados precisam de coordenação única |
| Criar ticket a partir de entrada | M03 | M09 entrega contexto da origem; reaproveita cadeia de criação do ticket; abrir formulário não é criar o registro |
| Transferir ticket | M03 | M10 departamento/acesso/atribuição; M11 políticas; M09 atividade/nota; reavaliar visibilidade após efeito |
| Gerir colaboradores | M09 | acesso ao objeto M03/M04; dados de usuário M05; inclusão pode criar usuário, edição altera seu perfil global |
| Pesquisar/exportar fila | M07 | M03 visibilidade dos tickets; M08 catálogo de campos; M01 sessão; M15 disponibilidade/download e M12 envio, quando configurado |
| Configurar e-mail | M12 | M10 departamento; M11 tópico; M13 defaults; M14 provedor externo, se presente; salvar conta/form principal antes da configuração dependente |
| Configurar agente e acessos | M10 | M02 mecanismos originais de senha/MFA; M12 convites/reset; M13 políticas; invalidação/revalidação de capacidades nas operações seguintes |
| Editar tópico e formulário | M11 | M08 definição/associação de forms; M10 roteamento; M13 defaults; alteração repercute na criação de ticket sem regras novas no Angular |

M03 e M04 não chamariam um CRUD irrestrito de M09 para contornar suas guardas;
a coordenação do caso de uso valida objeto, ação e vínculo. Esta é uma obrigação
da proposta, não um contrato de interfaces já aprovado. Assinaturas e direção
de importação devem ser resolvidas no desenho técnico para evitar dependências
circulares entre módulos novos, sem presumir que o core já seja desacoplado.

## O que pode ser reutilizado e o que ainda bloqueia o desenho

**Classificação da evidência, não selo de prontidão de um módulo inteiro:**

| Categoria | Evidência da baseline | Consequência para o contrato futuro |
| --- | --- | --- |
| Mecanismo de negócio identificável | StaffAuthenticationBackend::process; Ticket::open/setStatus/transfer; Task::create; User::fromForm; referências nos mapas O05/O14/O17/O26/O36/T05 | candidato a chamada após bootstrap, guardas e entrada corretos; a presença do método não prova execução isolada ou resposta pura |
| Coordenação também na página/controller | `scp/tickets.php:169` valida resposta/lock antes de postReply; `include/ajax.tickets.php:922` registra released e nota após release | preservar toda cadeia; não chamar só um método e declarar paridade |
| Regra/gravação presa à apresentação ou controller | forms A42, lotes A19/A25/A37, logs T38, query de diretório O45 | pendência por operação: identificar acesso reutilizável e separar coordenação de regra; se não houver caminho compatível com ADR 0004, solicitar decisão específica |
| Retorno acoplado ao legado | forms T19, ações T49/T51, login T05, export T34/T35 | formular contrato estruturado/binarizado adequado, sem capturar HTML e tratá-lo como API |
| Capacidade ausente ou condicionada | 13 alvos AJAX ausentes, claim tradicional divergente O12, apps/plugins T04/T39 | não anunciar endpoint pronto; caminho tradicional alternativo precisa ser avaliado; plugin ausente não tem comportamento comprovado |

Não existe autorização implícita para copiar regra, reescrever SQL, modificar o
core, chamar páginas por loopback HTTP ou capturar saída de templates como atalho.
Preservar comportamento também não significa aceitar vulnerabilidade conhecida:
achados e tratamento de risco seguem a revisão própria, sem novos ensaios aqui.

## Proposta de ficha obrigatória de cada operação

Antes de implementar uma operação candidata, seu contrato deverá explicar:

1. intenção e IDs do mapa; ator, objeto, permissões, precondições e parâmetros;
2. mecanismo original chamado, entradas globais/sessão/configuração e regras
   adicionais do controlador que precisam ser preservadas;
3. ordem dos efeitos: dados, eventos, anexos, e-mails, limpeza e resposta;
4. saída sem HTML legado, validação por campo e estados de falha; resultados
   parciais em lote sem inventar transação ou idempotência;
5. estado da UI: carregamento, vazio, impedimento, edição, sucesso e retomada;
6. comprovação ponta a ponta no frontend e regressão no painel antigo;
7. dependências de upstream/plugin, limites conhecidos e plano de rollback
   quando houver mutação autorizada. Não usar restore para apagar evidências.

Isso é um critério de desenho proposto, não DTO/rota/classe criada. Cabe à API
expor capacidades reais e ao Angular representar interação, sem decidir sozinho
autorização ou políticas. Escapar/sanitizar conteúdo rico continua necessário;
resposta sem template legado não significa remover conteúdo rico do usuário.

## Sequência sugerida para reduzir incerteza

1. Agrupamentos lógicos aprovados no ADR 0005; detalhar integração e contratos
   sem reabrir essa divisão ou escolher pacotes por inferência.
2. Delimitar desenho de M01/M02 para bootstrap, sessão/CSRF, login completo,
   expiração/MFA/logout e alternância entre painéis. É prioridade de análise,
   não escolha automática do primeiro módulo implementado.
3. Especificar uma cadeia transversal de criação/atendimento para testar o
   desenho documental de M03/M04/M05/M08/M09/M10/M11/M12 e localizar bloqueios.
4. Comparar opções de integração por evidência, revisar software/API/IAM/dados/
   frontend/QA conforme o risco e apresentar ADRs para aprovação.
5. Só após liberação escolher o primeiro recorte executável e construir
   backend+frontend funcional. A navegação do Plano 0002 não reduz o alvo SCP.

## Questões que esta proposta não decide

- local físico dos módulos, carregamento e implantação;
- convenções de URL, versionamento, DTOs, erros, paginação e binários;
- mecanismo de sessão compartilhada e adaptação das guardas sem renderização;
- solução para regras sem ponto reutilizável compatível com ADR 0004;
- versões de Angular/PrimeNG e contrato de metadados para formulários;
- atomicidade, concorrência, idempotência e execução assíncrona;
- primeiro módulo/recorte e aceite de riscos. Nenhuma das opções da PR #28
  volta a ser recomendada por omissão.

## Manifesto e revisão desta unidade

O integrador é único escritor deste documento, índices, progresso e planos.
Uma instância `software_architect/module-proposal/revisao`, cargo Arquiteto de
Software, realizou revisão independente após estabilização do texto: entradas são
AGENTS, checkpoint, planos, ADR 0004, mapas SCP e este documento; sem escrita,
descendentes, Git mutável, rede, banco, credenciais ou execução comportamental.
Critério: rastreabilidade dos 153 IDs, ausência de aceites indevidos, coerência
das fronteiras/dependências e limitações de reutilização. Para no parecer;
não realiza revisão final de todo o Portão D nem decide pelo responsável.

A revisão é necessária pelo risco de transformar divisões editoriais em
fronteiras técnicas sem evidência. Enquanto ocorre, o integrador sincroniza
governança e valida cobertura/MkDocs; não há escrita paralela de produto.
Parecer recebido: pronta para discussão, sem achado crítico/alto/médio no recorte.
Dois ajustes baixos incorporados: ciclo de arquivo M08/M09/M03/M04 e unicidade
editorial dos IDs versus encadeamento real de ações. Amostras de resposta/lock
e liberação/evento/nota conferidas por revisor e integrador. A revisão não aceita
arquitetura; parecer pós-ajuste e validações constam em GATE_D_REVIEW/PROGRESS.
Nenhum código ou teste de produto nesta unidade.

Validação pelo integrador: 15 grupos/153 atribuições principais sem sobra ou
duplicação; 66/66 documentos navegáveis, build MkDocs estrito e diff aprovados.
Auditoria de links em PROGRESS; nenhum novo teste visual ou de integração.
