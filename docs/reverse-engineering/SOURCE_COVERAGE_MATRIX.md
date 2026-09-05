# Matriz de cobertura do código-fonte

## Objetivo e critério

Esta matriz responde se a árvore completa da baseline foi localizada e
classificada. Ela não transforma localização de arquivo em alegação de que
todas as combinações de execução foram testadas.

**Fato observado:** a tag `v1.18.4`, commit
`8d38b0619649a50ee7cbbf37085f5d297fdc6f36`, contém 2.266 arquivos rastreados.
As regras abaixo são mutuamente exclusivas e cobrem os 2.266 caminhos, sem
arquivo não classificado. As contagens foram obtidas de
`git ls-tree -r --name-only v1.18.4`, portanto não incluem documentação ou
automação acrescentadas pelo Reforged.

Estados usados:

- `Contrato aprofundado`: componentes, entradas, persistência e consumidores
  possuem documentação especializada;
- `Cobertura estrutural`: todos os caminhos foram localizados e o papel do
  grupo foi delimitado, sem alegar inspeção linha a linha;
- `Dependência incorporada`: código de terceiro mantido como fronteira, não
  reinterpretado como domínio próprio do osTicket.

## Fechamento numérico

| Conjunto | Arquivos | Interpretação |
| --- | ---: | --- |
| Baseline completa | 2.266 | universo fechado desta matriz |
| Dependências incorporadas (`laminas-mail`, `mpdf`, `pear`) | 1.215 | fronteira de terceiro |
| Produto, instalação, recursos e utilitários mistos | 1.051 | superfície analisada pelo inventário |
| PHP total | 1.522 | 1.013 nas três dependências e 509 fora delas |
| JavaScript / CSS / Less | 57 / 27 / 9 | frontend legado, localização e instalação |
| SQL | 100 | esquema inicial e 99 patches do upgrader |

**Fato observado:** a soma das linhas da classificação exaustiva abaixo é
2.266. Os padrões `include/<grupo>/**` são aplicados antes de
`include/(raiz)`, e `(raiz)` significa somente caminhos sem `/`.

## Classificação exaustiva por caminho

| Grupo de caminhos | Arquivos | Composição dominante | Ownership | Estado | Documentação principal |
| --- | ---: | --- | --- | --- | --- |
| `(raiz)` | 28 | 20 PHP, metadados e configuração | osTicket/misto | Contrato aprofundado | [Ciclo de requisição](REQUEST_LIFECYCLE.md), [entrypoints](ENTRYPOINT_CATALOG.md) |
| `api/**` | 6 | 5 PHP e guarda de diretório | osTicket | Contrato aprofundado | [API e AJAX](API_ANALYSIS.md), [superfícies HTTP](HTTP_SURFACES.md) |
| `apps/**` | 2 | dispatcher e guarda | osTicket | Contrato aprofundado | [plugins](PLUGIN_ARCHITECTURE.md), [registries](REGISTRY_CATALOG.md) |
| `assets/**` | 52 | imagens, Less, CSS e fontes | osTicket/misto | Cobertura estrutural | [frontend PHP](FRONTEND_ANALYSIS.md), [proveniência](PROVENANCE_MAP.md) |
| `css/**` | 33 | 14 CSS e imagens | osTicket/misto | Cobertura estrutural | [frontend PHP](FRONTEND_ANALYSIS.md) |
| `images/**` | 18 | imagens | osTicket | Cobertura estrutural | [frontend PHP](FRONTEND_ANALYSIS.md) |
| `js/**` | 14 | JavaScript do shell legado | osTicket/misto | Cobertura estrutural | [frontend PHP](FRONTEND_ANALYSIS.md) |
| `kb/**` | 3 | controladores PHP públicos | osTicket | Contrato aprofundado | [superfícies HTTP](HTTP_SURFACES.md), [análise comportamental](BEHAVIORAL_ANALYSIS.md) |
| `pages/**` | 2 | controlador e guarda | osTicket | Contrato aprofundado | [superfícies HTTP](HTTP_SURFACES.md) |
| `scp/**` | 200 | 47 PHP, 16 JS e recursos do painel | osTicket/misto | Contrato aprofundado | [entrypoints](ENTRYPOINT_CATALOG.md), [matriz da interface](INTERFACE_COVERAGE_MATRIX.md) |
| `setup/**` | 73 | instalador, esquema, testes históricos e recursos | osTicket/misto | Contrato aprofundado | [instalação e upgrades](INSTALLATION_UPGRADE.md), [testes](TESTING_BASELINE.md) |
| `include/(raiz)` | 130 | 129 PHP: 94 classes, 23 AJAX, 2 API e 10 utilitários | osTicket/misto | Contrato aprofundado | [mapa de componentes](COMPONENT_MAP.md), [catálogo AJAX](AJAX_ROUTE_CATALOG.md) |
| `include/cli/**` | 15 | bootstrap e 14 módulos PHP | osTicket | Contrato aprofundado | [catálogo CLI](CLI_CATALOG.md) |
| `include/client/**` | 28 | views e templates PHP do cliente | osTicket | Contrato aprofundado | [frontend PHP](FRONTEND_ANALYSIS.md), [matriz da interface](INTERFACE_COVERAGE_MATRIX.md) |
| `include/config/**` | 1 | definição YAML | osTicket | Cobertura estrutural | [arquitetura](ARCHITECTURE.md) |
| `include/fpdf/**` | 1 | recurso incorporado usado no PDF | terceiro pontual | Cobertura estrutural | [proveniência](PROVENANCE_MAP.md), [subsistemas](TRANSVERSAL_SUBSYSTEMS.md) |
| `include/i18n/**` | 125 | 98 YAML, 25 JS, PHP e licença | osTicket/misto | Cobertura estrutural | [frontend PHP](FRONTEND_ANALYSIS.md), [proveniência](PROVENANCE_MAP.md) |
| `include/laminas-mail/**` | 444 | biblioteca incorporada | terceiro | Dependência incorporada | [proveniência](PROVENANCE_MAP.md), [subsistemas](TRANSVERSAL_SUBSYSTEMS.md) |
| `include/mpdf/**` | 667 | biblioteca e dependências do PDF | terceiro | Dependência incorporada | [proveniência](PROVENANCE_MAP.md), [subsistemas](TRANSVERSAL_SUBSYSTEMS.md) |
| `include/pear/**` | 104 | bibliotecas PEAR de e-mail e MIME | terceiro | Dependência incorporada | [proveniência](PROVENANCE_MAP.md), [subsistemas](TRANSVERSAL_SUBSYSTEMS.md) |
| `include/plugins/**` | 2 | marcador e certificado público | infraestrutura de extensão | Cobertura estrutural | [plugins](PLUGIN_ARCHITECTURE.md), [pontos de extensão](EXTENSION_POINTS.md) |
| `include/staff/**` | 186 | views e templates PHP da equipe/Administração | osTicket | Contrato aprofundado | [frontend PHP](FRONTEND_ANALYSIS.md), [matriz da interface](INTERFACE_COVERAGE_MATRIX.md) |
| `include/upgrader/**` | 132 | 99 SQL, 32 PHP e assinatura | osTicket | Contrato aprofundado | [instalação e upgrades](INSTALLATION_UPGRADE.md), [banco](DATABASE_ARCHITECTURE.md) |

## Cobertura semântica transversal

| Família | Universo fechado já catalogado | Documento |
| --- | ---: | --- |
| Folhas AJAX da equipe | 229 | [Catálogo AJAX](AJAX_ROUTE_CATALOG.md) |
| Declarações de metadados ORM | 72 | [Catálogo ORM](ORM_CATALOG.md) |
| Módulos CLI | 14 | [Catálogo CLI](CLI_CATALOG.md) |
| Sinais literais | 41 | [Catálogo de sinais](SIGNAL_CATALOG.md) |
| Registries e factories | 22 | [Catálogo de registries](REGISTRY_CATALOG.md) |

Esses catálogos aprofundam famílias para as quais nomes, registro e despacho
podem ser enumerados. Os demais arquivos ficam vinculados por componente,
entrypoint, template, recurso, instalação ou fronteira de dependência nesta
matriz, evitando a falsa equivalência entre “arquivo localizado” e “método
integralmente exercitado”.

## Garantia e limites

**Conclusão:** todo caminho rastreado da baseline está inventariado
estruturalmente e ligado a uma unidade documental. O inventário não encontrou
grupo ou arquivo fora das regras acima.

**Limites deliberados:** acessibilidade, compatibilidade entre navegadores,
segurança ofensiva, concorrência e combinações exaustivas de configuração são
campanhas especializadas futuras. Dependências incorporadas foram delimitadas
por proveniência e pontos de integração; seus internos não foram tratados como
arquitetura própria do produto. Esses limites não reabrem o Portão B, mas devem
acompanhar qualquer decisão do Portão D.
