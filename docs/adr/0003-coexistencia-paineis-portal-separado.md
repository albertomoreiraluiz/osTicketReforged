# ADR 0003 — Coexistência contínua dos painéis e portal separado

- Status: **Aceito**, exclusivamente para a intenção de produto abaixo
- Data: 2026-09-05
- Responsável: mantenedor do projeto, por confirmação explícita na conversa

## Contexto

A PR #28 apresentou uma recomendação antes da discussão dos objetivos com o
responsável. O contexto ainda previa substituir progressivamente o frontend PHP.
O responsável corrigiu essa premissa e determinou descartar a recomendação e
reiniciar as análises. Este registro não escolhe uma arquitetura técnica.

## Decisão aceita

- Preservar o painel legado em `/scp`, acessível e funcional.
- Disponibilizar Angular + PrimeNG por endereço distinto, em coexistência
  contínua com o painel antigo, sem plano implícito de desativá-lo.
- Tratar `/scp/reforged` como endereço candidato, não como rota aprovada.
- Evoluir capacidades no novo painel sem retirar as existentes no legado.
- Separar o planejamento do portal do usuário, que poderá substituir totalmente
  o portal antigo e melhorar acompanhamento de tickets e visibilidade de SLA.
- Descartar a recomendação da PR #28 e seu primeiro recorte sugerido. Preservar
  documentos e pareceres como histórico, sem herdá-los como recomendação atual.

## O que esta decisão não autoriza

Complemento posterior: [ADR 0004](0004-modulos-reforged-backend-osticket.md)
aceita a fronteira modular, escopo integral do SCP e requisito de alternância
sem novo login. Os limites abaixo descrevem o alcance deste ADR 0003, não
revogam o complemento; integração técnica e implementação continuam pendentes.

Não escolhe plugin, fachada, serviço, contrato, autenticação, sessão compartilhada,
URL definitiva, primeiro módulo, versões, infraestrutura ou armazenamento.
Não autoriza implementação, mudanças de core/schema ou substituição imediata do
portal. ADR 0001 permanece válido para `frontend/` e Angular/PrimeNG.
Este ADR de premissas não atende sozinho ao critério de saída do Portão D.

## Alternativas de intenção consideradas

Substituir gradualmente o painel PHP foi descartado como objetivo. Coexistência
apenas temporária também não atende à intenção confirmada. A coexistência contínua
foi escolhida pelo responsável; a arquitetura que a sustentará será discutida.

## Consequências e riscos a analisar

As análises deverão considerar uso simultâneo, consistência dos mesmos tickets,
permissões, ações concorrentes, isolamento de navegação/assets e atualizações
sem regressão em nenhum painel. Esses são temas de avaliação, não resultados
comprovados ou novas políticas técnicas aceitas. Não há nova prova de runtime.

Antes de comparar novamente opções, discutir critérios e requisitos com o
responsável. A ordem dos módulos não deve partir automaticamente da antiga
sugestão de consulta de tickets.

## Referências

- [Contexto](../governance/PROJECT_CONTEXT.md)
- [Plano ativo](../plans/active/0001-reverse-engineering.md)
- [Dossiê reiniciado e histórico](../reverse-engineering/ARCHITECTURE_DECISION_RECORD.md)
- [PR #28 — proposta anterior](https://github.com/albertomoreiraluiz/osTicketReforged/pull/28)
