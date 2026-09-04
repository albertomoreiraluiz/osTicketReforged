# Infraestrutura de testes da baseline

## Inventário estático

Foram localizados 20 arquivos sob `setup/test/`: runner, framework próprio,
stubs/mock de banco e testes. Não há configuração PHPUnit do core na raiz; o
`phpunit.xml` localizado pertence à cópia incorporada do mPDF.

| Categoria | Arquivos representativos | Propósito observado |
| --- | --- | --- |
| Runner/framework | `run-tests.php`, `class.test.php` | descoberta, execução e assertions próprias |
| Análise de fonte | `test.syntax.php`, `test.staticmethods.php`, `test.undefinedmethods.php`, `test.unitialized.php` | heurísticas sobre PHP e símbolos |
| Higiene | `test.git-conflicts.php`, `test.extra-whitespace.php`, `test.shortopentags.php`, `test.var-dump.php` | padrões indesejados no código |
| Integração estática | `test.signals.php`, `test.jslint.php` | correspondência de sinais e lint legado |
| Funções | `test.validation.php`, `test.crypto.php`, `test.mail-parse.php`, `test.email-bounces.php`, `test.header_functions.php` | contratos selecionados |
| Suporte | `mockdb.php`, `stubs.php`, `class.php_analyze.php` | doubles e análise |

## Limites de confiança

- `test.signals.php` confirma apenas que conexão literal possui emissão literal;
  não valida payload, ordem, autenticação ou mutabilidade.
- testes de análise de fonte usam heurísticas e não substituem execução PHP,
  análise estática moderna ou cobertura funcional.
- arquivos incorporados de terceiros devem ser excluídos das métricas do core.
- o runner e seus pré-requisitos ainda precisam ser classificados antes da
  execução em PHP 8.2.

## Estado nesta fase

Nenhum teste legado foi executado. Como o osTicket ainda não está instalado,
executar indiscriminadamente o runner poderia misturar falhas de ambiente com
defeitos da baseline. Na fase dinâmica será criada uma matriz que indique:

1. testes puramente estáticos seguros antes da instalação;
2. testes que exigem bootstrap/configuração;
3. testes que exigem banco ou dados;
4. compatibilidade do runner com PHP 8.2;
5. lacunas que pedem testes novos no Reforged.
