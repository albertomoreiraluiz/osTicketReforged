# ADR 0005 — Módulos lógicos e cobertura integral do SCP

- Status: **Aceito** para a divisão lógica e o requisito funcional abaixo
- Data: 2026-09-05
- Responsável: mantenedor, por aprovação explícita dos agrupamentos e confirmação do escopo
- Complementa ADRs 0003/0004 e GOV-026; não encerra o Portão D

## Contexto e evidências

A PR #33 apresentou 15 capacidades com atribuição principal dos 153 registros
do [mapa SCP](../reverse-engineering/SCP_FLOW_MAP.md). O responsável aprovou
a divisão e reforçou que não deseja acessar o frontend original para configurar
ou operar o osTicket, exceto o setup. A confirmação subsequente manteve o
portal do usuário no planejamento separado e autorizou prosseguir.

Os registros distinguem páginas, AJAX, modais, lotes e mecanismos originais.
Exemplos: O06 rastreia resposta/lock até Ticket::postReply; A12–A15 rastreiam
conta de e-mail e configuração de autenticação; A24–A35 cobrem administração
de acesso; T40 mantém upgrade SCP no mapa. Isso sustenta um escopo maior que
atendimento, sem provar implementação ou reutilização universal.

## Decisão aceita

1. Adotar os agrupamentos lógicos M01–M15 e sua atribuição principal O/A/T,
   conforme o [catálogo preservado](../reverse-engineering/MODULE_CAPABILITY_PROPOSAL.md):
   integração/contexto; autenticação/perfil; tickets; tarefas; usuários/organizações;
   conhecimento; filas/pesquisa; formulários/dados configuráveis; conversas/composição;
   equipe/acesso; políticas; e-mail; configuração/conteúdo; extensões/integrações;
   relatórios/operação técnica.
2. A divisão é lógica. Não significa 15 microsserviços, bancos, pacotes de
   implantação ou menus. A unicidade dos IDs é editorial, não 153 operações
   técnicas disjuntas. Casos encadeados conservam coordenação explícita.
3. O Reforged deverá cobrir integralmente as capacidades funcionais do SCP,
   inclusive administração e configurações. Nenhuma funcionalidade será
   considerada entregue se exigir retorno ao painel legado para completá-la.
4. O setup permanece como exceção indicada pelo responsável. Essa exceção
   não exclui automaticamente manutenção/upgrade existente no SCP (T40).
   Detalhes do ciclo de instalação/atualização serão definidos no desenho próprio.
5. O painel legado permanece disponível por coexistência, não como dependência
   funcional da solução concluída. Link voluntário para alternância não serve
   como implementação substituta de uma função faltante.
6. Entregas incrementais são permitidas, mas não diminuem o escopo final.
   Enquanto faltar uma capacidade, registrar como pendente — não como concluída
   por haver link para a página original.
7. Portal do usuário mantém planejamento separado. O requisito não autoriza
   refatorar core, copiar regras, contornar controles ou executar fluxo por SQL novo.

## Alternativas consideradas

- Cobertura parcial do atendimento com configuração no legado: não atende à
  condição explícita de aprovação do responsável.
- Um módulo por menu/página: comparação da PR #33 não recomendou essa divisão;
  módulos por capacidade foram aprovados, mantendo menus ajustáveis.
- Cobertura integral e coexistência voluntária: aceita, com setup como exceção.

## Critério de paridade e limites da evidência

Cada capacidade deverá ter correspondência backend/API/frontend e evidência
do fluxo completo, incluindo permissões, dependências, validações, estados de
erro e efeitos. O mapa da baseline é ponto de partida, não certificado de
funcionalidade do Reforged ou lista imutável de tudo que um plugin pode adicionar.

Treze alvos AJAX ausentes e divergências conhecidas não devem ser copiados como
falhas intencionais nem omitidos silenciosamente. Deve-se identificar a intenção
funcional e decidir o tratamento dentro do ADR 0004. Sem solução validada,
registrar bloqueio/pendência; não declarar paridade ou recorrer ao legado como entrega.

Configuração/uso de plugins e apps fazem parte do escopo quando disponíveis.
Código externo não inventariado exige levantamento adicional antes de prometer
compatibilidade; ausência de evidência não é exclusão automática do requisito.

## Consequências e pendências

Aprovação lógica não aceita convenções HTTP, DTOs, bootstrap, sessão, URLs,
versionamento, layout físico, empacotamento, versões, primeiro recorte ou provas
de atomicidade/concorrência. Esses itens continuam no Portão D. Nenhum código
de produto é autorizado apenas pela formalização deste ADR.

Atualizações do upstream exigirão reconciliar capacidades e regressões dos dois
painéis. Revisões de arquitetura/API/IAM/dados/frontend/QA permanecem proporcionais
ao desenho. O nome histórico MODULE_CAPABILITY_PROPOSAL.md foi preservado para
não romper links; seu cabeçalho distingue a divisão aceita dos detalhes propostos.
