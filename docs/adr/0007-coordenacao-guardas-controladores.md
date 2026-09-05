# ADR 0007 — Adaptação explícita da coordenação de guardas

- Status: **Proposto** — aguarda decisão do responsável
- Data: 2026-09-05
- Escopo: guardas de entrada staff/admin; não autorização de implementação
- Complemento proposto ao ADR 0004; não altera core, origem ou paridade SCP

## Problema que requer decisão

O ADR 0004 aceita coordenar mecanismos existentes, mas exige decisão explícita
quando regras estão presas a controladores. O rastreio atual distingue métodos
reutilizáveis de combinações que só foram encontradas inline em staff/admin.
Não reabrimos a estratégia de backend intacto ou o uso dos métodos originais.

Exemplo concreto: Staff::forcePasswdChange e force2faConfig podem ser chamados,
mas `scp/staff.inc.php:138–148` determina prioridade, exceções e carregamento do
perfil. `scp/staff.inc.php:83–98,123–129` e `scp/admin.inc.php:30–36` coordenam
estado operacional, administração e upgrade. Incluir as páginas conserva esse
acoplamento; só chamar predicados não define automaticamente o fluxo completo.

## Proposta recomendada, ainda não aceita

Permitir que o Reforged adapte explicitamente a **coordenação de guardas de
entrada** desses controladores, sob limites verificáveis:

1. Chamar os métodos originais para identidade, sessão, ACL, atividade,
   administração, estado operacional, senha obrigatória, MFA e CSRF; não copiar
   o interior dos métodos, consultas ou políticas de autenticação.
2. Especificar no módulo novo a sequência de chamadas, combinação dos resultados
   e tradução para interação/resposta, com rastreabilidade para cada trecho original.
   Isso reconhece a manutenção de coordenação equivalente em dois frontends,
   sem fingir que toda ela já existe como serviço chamável no core.
3. Reconciliar exceções associadas a páginas por intenção funcional em matriz
   finita revisada antes de código. Não substituir uma exceção ampla de AJAX por
   liberação geral da API nem manipular nomes/flags para contornar controles.
4. Preservar precedência e efeitos pertinentes. Mudanças intencionais de política,
   correção de falhas conhecidas ou ampliação de permissão exigem decisão própria;
   paridade não significa copiar uma falha como requisito.
5. Não estender este aceite a regras de tickets, SLA, e-mail, formulários,
   persistência, plugins ou outros controladores sem novo rastreio e análise.
6. Exigir revisão independente IAM, teste de equivalência por contexto e
   reconferência do delta a cada atualização do upstream. Não há teste novo nesta ADR.

## Alternativas

| Opção | Consequência |
| --- | --- |
| Adaptação limitada da coordenação, chamando métodos originais | recomendada; mantém core intacto e permite separar apresentação, mas traz custo explícito de equivalência e atualização |
| Somente chamar um agregado original completo | agregado sem apresentação não encontrado nos pontos pesquisados; mantém o trecho pendente, não prova impossibilidade universal |
| Extrair serviço comum modificando core | centralizaria a coordenação, mas contraria a preservação aceita; não recomendada sob o escopo atual |
| Executar página e capturar HTML | não atende ao contrato estruturado e ao ADR 0004; não considerada solução |

## Risco residual e decisão solicitada

O custo novo é manter equivalência da coordenação inline. A aprovação proposta
não permite escolher política por conveniência nem elimina a necessidade de
especificar bootstrap, contexto, erros, rotas, implantação e provas de sessão.

**Decisão solicitada:** autorizar essa adaptação limitada de coordenação no desenho
do Reforged, preservando os métodos originais e o core intacto. Se rejeitada,
o trecho fica pendente até outra solução compatível ser identificada. Não iniciar
código com base nesta proposta; aceite não encerra sozinho o Portão D.

## Evidências

- [Guardas e contratos de contexto](../reverse-engineering/REFORGED_GUARD_CONTRACTS.md), G01–G09 e limites de busca
- [ADR 0004](0004-modulos-reforged-backend-osticket.md), coordenação aceita e regras presas a controladores
- [Entradas e sessão](../reverse-engineering/REFORGED_ENTRY_SESSION_DESIGN.md), contexto e efeitos
