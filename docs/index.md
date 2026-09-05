<div class="docs-hero" markdown>

# osTicket Reforged

Engenharia reversa verificável da baseline osTicket `v1.18.4`, organizada para
leitura humana, rastreabilidade técnica e preparação de decisões arquiteturais.

</div>

<div class="status-strip">
  <span class="status-chip status-chip--done">Portão A · concluído</span>
  <span class="status-chip status-chip--done">Portão B · concluído</span>
  <span class="status-chip status-chip--done">Portão C · concluído</span>
  <span class="status-chip status-chip--next">Portão D · em análise</span>
</div>

## Escolha seu caminho de leitura

<div class="docs-grid" markdown>

<div class="docs-card" markdown>

### Entender o estado do projeto

Comece pelo [contexto persistente](governance/PROJECT_CONTEXT.md), valide o
[checkpoint canônico](governance/CURRENT_STATE.md), confira o
[progresso atual](governance/PROGRESS.md) e use o
[plano ativo](plans/active/0001-reverse-engineering.md) como sequência oficial.

</div>

<div class="docs-card" markdown>

### Compreender como o sistema funciona

Passe pelo [inventário da baseline](reverse-engineering/INVENTORY.md), pela
[arquitetura canônica](reverse-engineering/ARCHITECTURE.md) e pelo
[ciclo do ticket](reverse-engineering/TICKET_LIFECYCLE.md).

</div>

<div class="docs-card" markdown>

### Conferir evidências e comportamento

Use a [matriz de evidências](reverse-engineering/EVIDENCE_MATRIX.md), a
[cobertura da interface](reverse-engineering/INTERFACE_COVERAGE_MATRIX.md) e a
[análise comportamental](reverse-engineering/BEHAVIORAL_ANALYSIS.md).

</div>

<div class="docs-card" markdown>

### Preparar decisões de evolução

Comece pelo [dossiê do Portão D](reverse-engineering/ARCHITECTURE_DECISION_RECORD.md)
e pelas [premissas de coexistência](adr/0003-coexistencia-paineis-portal-separado.md).
A estratégia anterior permanece somente como histórico, não como orientação ativa.

Leia a [matriz de customização](reverse-engineering/CUSTOMIZATION_MATRIX.md), a
[arquitetura de plugins](reverse-engineering/PLUGIN_ARCHITECTURE.md), o
[modelo de segurança](reverse-engineering/SECURITY_MODEL.md) e as
[revisões independentes](reverse-engineering/REVIEW_REPORT.md).

</div>

</div>

## Estado consolidado

| Área | Situação | Evidência principal |
| --- | --- | --- |
| Governança | concluída | decisões, regras de documentação e fluxo de agentes formalizados |
| Inventário | concluído | 2.266 caminhos classificados sem lacuna ou sobreposição |
| Análise profunda | concluída | arquitetura, domínio, dados, interfaces, APIs, segurança e extensão documentados |
| Observação visual | concluída | 34 cenários reconciliados e revisão independente aprovada |
| Decisão arquitetural | reiniciada | [novas premissas](adr/0003-coexistencia-paineis-portal-separado.md); recomendação anterior descartada |

## Como interpretar as afirmações

!!! warning "Precedência das fontes"

    Código, esquema, configuração e comportamento observado na baseline
    prevalecem sobre documentação externa. Memória de conversa não substitui
    as fontes versionadas deste portal.

- **Fato observado:** sustentado por código, configuração ou execução
  reproduzível.
- **Inferência:** interpretação sustentada, ainda não confirmada integralmente.
- **Proposta:** alternativa em avaliação, sem autoridade normativa.
- **Decisão aceita:** escolha aprovada e formalizada em decisão ou ADR.

!!! tip "Encontrando uma informação"

    Use as categorias da navegação para compreender o assunto e a busca global
    para localizar símbolos, rotas, tabelas, cenários `BHV-*` ou identificadores
    de decisão `GOV-*`.
