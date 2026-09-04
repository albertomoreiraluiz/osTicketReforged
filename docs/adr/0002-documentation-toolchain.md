# ADR 0002 — Portal documental com MkDocs Material

- Status: **Aceito**
- Data: 2026-09-03
- Responsável pela decisão: mantenedor do projeto, que autorizou a seleção da
  ferramenta antes do inventário

## Contexto

A engenharia reversa produzirá documentação narrativa, tabelas, fluxos,
diagramas, referências de código, inventário de banco e, posteriormente,
contratos de API. O responsável precisa ler esse conteúdo de forma estruturada.

Swagger e Postman são especializados em APIs HTTP e não cobrem sozinhos todo
esse conjunto. phpDocumentor gera referência estrutural a partir de PHP e
DocBlocks, mas não substitui a narrativa arquitetural baseada em evidências.

## Decisão

O portal principal usará MkDocs 1.6.1 com Material for MkDocs 9.7.7. As fontes
continuarão em Markdown dentro de `docs/`; navegação, busca, diagramas Mermaid e
validação estrita serão configurados em `mkdocs.yml`.

As dependências serão exclusivas da documentação e instaladas em ambiente local
ignorado pelo Git. O site gerado não será incorporado ao runtime PHP.

phpDocumentor, OpenAPI/Swagger e Postman são ferramentas complementares,
adotadas apenas quando o recorte correspondente possuir evidência e contrato.

## Consequências

- o Git continua sendo a fonte da documentação;
- humanos recebem navegação, busca e hierarquia consistentes;
- links inválidos bloqueiam o build estrito;
- o portal não cria dependência de produção nem altera o core;
- publicação remota e automação de deploy permanecem fora deste ADR.

## Referências

- <https://www.mkdocs.org/user-guide/configuration/>
- <https://squidfunk.github.io/mkdocs-material/setup/setting-up-navigation/>
- <https://docs.phpdoc.org/guide/>
