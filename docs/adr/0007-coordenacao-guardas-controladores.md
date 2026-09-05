# ADR 0007 — Adaptação explícita da coordenação de guardas

- Status: **Aceito** pelo responsável em 2026-09-05
- Data: 2026-09-05
- Escopo: guardas de entrada staff/admin; não autorização de implementação
- Complementa ADR 0004; não altera core, origem ou paridade SCP

## Problema analisado

O ADR 0004 aceita coordenar mecanismos existentes, mas exige decisão explícita
quando regras estão presas a controladores. O rastreio atual distingue métodos
reutilizáveis de combinações que só foram encontradas inline em staff/admin.
Não reabrimos a estratégia de backend intacto ou o uso dos métodos originais.

Exemplo concreto: Staff::forcePasswdChange e force2faConfig podem ser chamados,
mas `scp/staff.inc.php:138–148` determina prioridade, exceções e carregamento do
perfil. `scp/staff.inc.php:83–98,123–129` e `scp/admin.inc.php:30–36` coordenam
estado operacional, administração e upgrade. Incluir as páginas conserva esse
acoplamento; só chamar predicados não define automaticamente o fluxo completo.

## Decisão aceita

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

## Diretriz aceita para fluxos muito acoplados

O responsável aprovou a proposta e acrescentou: quando o comportamento estiver
muito mesclado à página, analisar seu funcionamento e reproduzi-lo no módulo
Reforged. Esse princípio vale para os fluxos acoplados identificados no SCP;
o rastreio concreto deste ADR cobre as guardas staff/admin, não todos os módulos.

Antes de implementar cada caso, registrar o trecho acoplado, os mecanismos que
continuam reutilizados, pré-condições, ator/permissões, entradas, sequência,
resultado observável e efeitos. Validar pelo fluxo natural do frontend original,
com evidências sanitizadas, e cruzar com o código; CLI isolado não comprova
equivalência visual. Depois especificar o comportamento correspondente no módulo
e os critérios de comparação entre os dois painéis. Cenário não executado fica
pendente, nunca presumido pela aparência da tela.

Reproduzir o comportamento acoplado não significa copiar HTML ou substituir
métodos reutilizáveis por algoritmos duplicados. O princípio não precisa ser
reaprovado a cada caso; rastreio e análise continuam obrigatórios. Conflitos,
mudanças de política, falhas conhecidas e riscos materiais exigem decisão própria.

## Risco residual e limite do aceite

O custo novo é manter equivalência da coordenação inline. A aprovação
não permite escolher política por conveniência nem elimina a necessidade de
especificar bootstrap, contexto, erros, rotas, implantação e provas de sessão.

**Decisão aceita:** adaptação limitada da coordenação no desenho do Reforged,
preservando os métodos originais e o core intacto. O responsável confirmou a
proposta após a PR #37 e aprovou análise comportamental dos trechos mesclados.
Nenhum teste novo ou código é entregue nesta formalização; o aceite não encerra
sozinho o Portão D nem libera implementação antes dos contratos e critérios.

## Evidências

- [Guardas e contratos de contexto](../reverse-engineering/REFORGED_GUARD_CONTRACTS.md), G01–G09 e limites de busca
- [ADR 0004](0004-modulos-reforged-backend-osticket.md), coordenação aceita e regras presas a controladores
- [Entradas e sessão](../reverse-engineering/REFORGED_ENTRY_SESSION_DESIGN.md), contexto e efeitos
