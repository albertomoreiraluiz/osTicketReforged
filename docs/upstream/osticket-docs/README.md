# Snapshot da documentação oficial do osTicket

Esta árvore contém uma cópia **não modificada** dos documentos oficiais usados
como referência auxiliar na preparação do inventário de engenharia reversa.

## Proveniência

- Repositório de origem: <https://github.com/osTicket/docs>
- Branch de origem: `master`
- Commit de origem: `f2367c5f7a87e855cd200be0862dc47a3cf14708`
- Data da captura: 2026-09-03
- Licença preservada: [GPL-3.0](LICENSE)
- Publicação consultada: <https://docs.osticket.com/en/latest/>

## Conteúdo incorporado

- [guia oficial de instalação](Getting%20Started/Installation.rst);
- [índice oficial dos ERDs](Developer%20Documentation/Database%20ERDs.rst);
- quatorze diagramas PDF referenciados pelo índice, em `_static/images/`.

## Regra de utilização

Esta documentação é evidência externa e não substitui a análise do código, do
esquema nem do comportamento observado na baseline `v1.18.4`. A publicação
`latest` consultada se apresenta como documentação do osTicket `1.17.7`; toda
divergência encontrada durante o inventário deve ser registrada, e a baseline
do repositório prevalece.

Os arquivos oficiais desta árvore não devem receber adaptações do Reforged.
Notas, análises e correções próprias pertencem a `docs/reverse-engineering/`.
