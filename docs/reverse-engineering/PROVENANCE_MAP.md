# Fronteira entre core e dependências incorporadas

## Mapa por subárvore

| Caminho | Classificação | Evidência/observação |
| --- | --- | --- |
| PHP na raiz, `api/`, `apps/`, `scp/`, `pages/`, `kb/` | core osTicket e entrypoints | bootstraps, controllers e páginas da baseline |
| `include/class.*.php`, `include/api.*.php`, `include/ajax.*.php` | core osTicket | domínio, ORM, auth, dispatch e AJAX |
| `include/client/`, `include/staff/`, `include/config/` | templates/config do core | incluídos pelos shells e páginas |
| `include/cli/`, `include/upgrader/` | tooling/evolução do core | módulos CLI e streams de upgrade |
| `include/plugins/` | slot de pacotes instaláveis | baseline contém `.keep` e chave de updates, não plugin funcional |
| `include/fpdf/` | asset de impressão do produto; ownership a verificar | contém `print-logo.png`, usado por templates de impressão; não contém biblioteca/vendor |
| `include/mpdf/` | dependência Composer incorporada | `composer.json`, `composer.lock`, `src/` e `vendor/` próprios |
| `include/laminas-mail/` | dependência Composer incorporada | `composer.json`, `composer.lock`, `src/` e `vendor/` próprios |
| `include/pear/` | bibliotecas PEAR legadas incorporadas | pacotes `Auth`, `Crypt`, `Mail`, `Net`, `PEAR` e `PEAR.php` |
| `assets/`, `css/`, `js/`, `images/` | assets do produto e terceiros mistos | ownership deve ser avaliado por arquivo/licença antes de alterar |
| `setup/` | instalador, schema, upgrade e testes históricos do core | não é runtime normal após instalação |
| `docs/upstream/osticket-docs/` | referência documental externa incorporada pelo Reforged | snapshot auxiliar, não runtime nem fonte superior ao código |
| `.codex/`, `docs/governance/`, `docs/reverse-engineering/` | governança/documentação Reforged | não integra ou executa no produto |

## Regra de alteração

Antes de editar, classificar o arquivo como core, dependência incorporada,
asset de terceiro, setup/test ou documentação Reforged. Dependências e assets
devem preservar licença e mecanismo de atualização; alterar `vendor/` ou cópia
incorporada cria delta difícil de reconciliar. Ausência de `vendor/` raiz não
significa ausência de Composer: mPDF e Laminas Mail carregam árvores próprias.
