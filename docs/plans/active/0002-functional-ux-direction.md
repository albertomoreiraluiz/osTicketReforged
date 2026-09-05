# Direção funcional e primeira proposta de UX do SCP Reforged

## Estado e autoridade

Data: 2026-09-05. Entrada `6ab70064` (PR #31), baseline `v1.18.4`.
Unidade documental `codex/functional-ux-direction`, subordinada ao Plano 0001.
Não inicia implementação nem encerra o Portão D.

**Decisão aceita do responsável:** experiência mais intuitiva, layout no estilo
dashboard administrativo e prioridade a tornar os fluxos funcionais. A primeira
versão da disposição dos menus e usabilidade fica a cargo do agente principal;
o responsável ajustará o layout posteriormente. Não exigir aprovação prévia de
cada detalhe visual para elaborar essa primeira versão.

**Limite:** a delegação é de elaboração da primeira UX, não de aceitação de
arquitetura pendente, dispensa de autorização no backend ou redução de escopo.
O alvo continua todo o SCP, inclusive administração, com portal separado.
Versões, contratos, bootstrap e integração de sessão continuam pendentes nos
ADRs 0001/0004. Layout visual não é layout físico dos módulos.

## Primeira organização proposta

**Proposta inicial, ajustável:** navegação lateral recolhível, área central de
trabalho e cabeçalho com contexto da página, perfil e alternância para o painel
legado. Não escolher template comercial ou dependência adicional nesta unidade.

| Área de navegação | Conteúdo inicial | Rastreabilidade no mapa SCP |
| --- | --- | --- |
| Visão geral | resumo operacional e acesso ao diretório da equipe | O45 |
| Atendimento | filas, tickets, buscas, tarefas e ações relacionadas | O01–O33; T21/T23–T27/T42–T52 |
| Pessoas | usuários e organizações | O34–O42 |
| Conhecimento | artigos, categorias e respostas prontas | O43–O44 |
| Administração | equipe e acesso; regras de atendimento; e-mails; formulários/listas; configurações; integrações; diagnóstico/manutenção | A01–A56; T37–T40 |
| Perfil, no cabeçalho | preferências, senha, MFA e saída | T10–T14 |

As áreas são agrupamentos de navegação, não módulos PHP, permissões novas ou
URLs aprovadas. Apps instalados entram conforme capacidade real (T04), sem
inventar telas de plugins ausentes. Busca, editor, anexos, rascunhos, ajuda,
traduções e exportação permanecem componentes transversais, não menus artificiais.
Fontes: [mapa SCP](../../reverse-engineering/SCP_FLOW_MAP.md) e seus detalhes.

## O que significa funcional primeiro

**Proposta de critério de entrega:** cada recorte deverá ter início, execução,
resultado e recuperação de erro verificáveis pelo frontend, usando os mecanismos
originais. Dados fictícios poderão apoiar homologação; não substituir integração
real por botões sem ação ou sucesso meramente visual.

- Listagens com filtros, ordenação, paginação, seleção e ações de lote quando
  a capacidade existir; erros e resultados parciais identificáveis.
- Formulários com rótulos claros, ajuda, validação, estado de gravação e
  preservação de dados em erro; indicar quando salvar o registro principal
  é pré-requisito para configurar um modal.
- Estados vazio, carregando, sem permissão, sessão expirada e falha explícitos.
- Menus/ações respeitam capacidades do usuário; autorização efetiva permanece
  no backend, inclusive acesso por departamento/equipe e ao objeto.
- Uso básico por teclado, foco e contraste considerados desde o início;
  refinamento estético posterior não justifica interação inutilizável.
- Aperfeiçoamentos de cores, espaçamento, composição e densidade poderão ser
  feitos depois, sem alterar silenciosamente regras e contratos.

## Próxima unidade do Portão D

A primeira proposta de agrupamentos foi produzida em
[Módulos por capacidades](../../reverse-engineering/MODULE_CAPABILITY_PROPOSAL.md).
Divisão lógica aceita pelo ADR 0005; detalhes técnicos continuam em discussão.
O frontend deve permitir concluir todas as funções SCP sem retorno obrigatório
ao legado, exceto setup. Essa paridade não altera a liberdade de ajustar menus.

1. Concluído: agrupamentos lógicos aceitos no ADR 0005, rastreados aos IDs O/A/T,
   sem assumir correspondência de um menu para um módulo.
2. Discutir coordenação, contratos e integração com o backend, especialmente
   regras presas a controladores e estado compartilhado entre os painéis.
3. Comparar opções pertinentes, revisar e formalizar as escolhas necessárias;
   selecionar primeiro recorte somente com justificativa e aprovação.
4. Após liberação da implementação, materializar a primeira versão funcional
   com esta direção de UX e submetê-la a ajustes do responsável.

## Registro da unidade

Um único agente é suficiente: consolidação de direção fornecida pelo responsável
e proposta editorial, sem frentes técnicas independentes ou decisão arquitetural
nova. Documentação, checkpoint e MkDocs atualizados; sem código de produto,
dependências, banco, navegador ou teste comportamental. Este plano permanece
ativo para orientar a elaboração, não representa frontend entregue.
