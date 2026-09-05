# Portal da documentação técnica

## Objetivo

Disponibilizar a engenharia reversa como um site local navegável, pesquisável e
validável, mantendo os arquivos Markdown como fonte versionada.

## Preparação

No PowerShell, a partir da raiz do repositório:

```powershell
py -m venv .local/docs-venv
.local/docs-venv/Scripts/python.exe -m pip install -r docs/requirements.txt
```

O ambiente virtual e o site gerado permanecem em `.local/`, já ignorado pelo
Git, e não alteram as dependências do osTicket.

## Uso

Validar e gerar o site:

```powershell
.local/docs-venv/Scripts/python.exe -m mkdocs build --strict
```

Abrir o portal local com atualização automática:

```powershell
.local/docs-venv/Scripts/python.exe -m mkdocs serve
```

O endereço padrão é `http://127.0.0.1:8000/`.

## Arquitetura de informação e leitura

O portal preserva os caminhos Markdown como fontes canônicas, mas apresenta o
conteúdo em camadas orientadas à intenção do leitor:

1. `Início` oferece estado consolidado e trilhas de leitura;
2. `Projeto` reúne contexto, progresso, plano e governança;
3. `Engenharia reversa` agrupa os documentos por inventário, arquitetura,
   domínio, interfaces, APIs, segurança e extensibilidade;
4. `Decisões arquiteturais` separa propostas e decisões aceitas;
5. `Ambiente e ferramentas` reúne operação local e homologação;
6. `Referência oficial incorporada` permanece isolada da análise própria.

Dentro da engenharia reversa, os grupos são recolhíveis e somente a categoria
da página atual é expandida. Essa organização reduz o volume simultâneo da
árvore sem ocultar documentos da busca ou da navegação.

### Taxonomia normativa

GOV-023 torna esta arquitetura de informação obrigatória para atualizações
futuras do portal. Os seis contextos de primeiro nível são `Início`, `Projeto`,
`Engenharia reversa`, `Decisões arquiteturais`, `Ambiente e ferramentas` e
`Referência oficial incorporada`.

Dentro de `Engenharia reversa`, preserve as oito categorias:

1. inventário e rastreabilidade;
2. arquitetura e execução;
3. domínio e persistência;
4. interfaces e comportamento;
5. APIs e automação;
6. segurança e acesso;
7. extensibilidade e customização;
8. evidências comportamentais.

Uma página nova deve ser classificada pela principal pergunta que responde e
aparecer uma única vez na navegação principal. Links cruzados podem conectá-la
a outras trilhas sem duplicar o item. Não mova ou renomeie a fonte canônica
apenas para ajustar o menu. Uma necessidade real de novo contexto ou categoria
exige decisão de governança registrada e atualização coordenada deste documento,
de `mkdocs.yml`, do ADR 0002 e das instruções obrigatórias.

O tema oferece abas persistentes, alternância claro/escuro, busca compartilhável,
sumário acompanhando a leitura, navegação anterior/próxima e cópia de blocos de
código. O stylesheet `docs/assets/stylesheets/extra.css` melhora largura de
leitura, hierarquia de títulos, tabelas, cartões e indicadores de estado. Ele
não esconde conteúdo nem modifica o significado dos documentos.

### Regras de manutenção da navegação

- preservar os seis contextos e as oito categorias definidos pela taxonomia
  normativa;
- não renomear ou mover arquivos apenas para reorganizar o menu;
- classificar páginas novas pela pergunta que respondem, não pela ordem em que
  foram criadas;
- evitar listas técnicas extensas no primeiro nível;
- preservar uma única ocorrência de cada documento na árvore principal;
- atualizar a página inicial quando o estado dos portões mudar;
- verificar desktop, tema escuro e quebra responsiva após alterações visuais.

## Fluxo obrigatório de atualização

Toda tarefa que altere `docs/` deve:

1. atualizar o documento canônico afetado;
2. incluir páginas novas ou renomeadas na navegação de `mkdocs.yml`, quando
   forem relevantes à leitura do projeto;
3. atualizar `README.md`, `PROGRESS.md`, plano e decisões conforme o impacto;
4. executar `mkdocs build --strict` antes de concluir a tarefa;
5. repetir a validação imediatamente antes de criar uma Pull Request;
6. não versionar `.local/site-docs/` nem o ambiente virtual.
7. confirmar que todos os destinos da navegação existem e que nenhum documento
   relevante ficou sem categoria.

Falha de navegação, link ou build bloqueia a conclusão e o merge. O site HTML é
derivado; Markdown e configuração versionados continuam sendo as fontes.

## Ferramentas complementares

- **phpDocumentor:** candidato para gerar índices automáticos de classes,
  métodos e DocBlocks. Sua configuração será avaliada depois que o inventário
  delimitar código próprio, core e bibliotecas incorporadas.
- **OpenAPI/Swagger:** será usado quando contratos HTTP forem inventariados ou
  definidos; não substitui a documentação arquitetural.
- **Postman:** poderá materializar coleções executáveis derivadas de contratos
  confirmados; não será fonte canônica de arquitetura.

Não gere nem versione documentação automática sobre `vendor/` antes de definir
os filtros, pois isso mistura bibliotecas de terceiros com o core analisado.
