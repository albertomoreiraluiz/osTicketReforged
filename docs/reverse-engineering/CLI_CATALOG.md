# Catálogo dos módulos CLI

## Bootstrap comum

`manage.php:21-27,58-75` inclui `bootstrap.php`, exige CLI, carrega
`include/cli/cli.inc.php` e seleciona dinamicamente o módulo. A CLI define
`ROOT_PATH=/`, desabilita sessões e prepara i18n. `Module::bootstrap()` carrega
configuração, tabelas e código, mas cada módulo decide conexão e
`osTicket::start()` (`include/class.cli.php:235-250`).

| Módulo | Comandos/opções | Inicialização | Efeitos principais |
| --- | --- | --- | --- |
| `agent` | import, export, list, login, backends | conecta + start | importa/exporta agentes, autentica e pode enviar boas-vindas |
| `cron` | fetch, search | conecta + start | coleta e-mail e reconstrói índice |
| `deploy` | destino, dry-run, setup, clean, git, force | bootstrap vazio | copia/regrava e, com clean, remove arquivos do destino; grava manifesto |
| `export` | dump integral/módulo | conecta + start | exporta banco, opcionalmente comprimido |
| `file` | backends, list, dump, load, migrate, export, import, zip, expunge | conecta + start | lê, substitui, migra, importa/exporta e remove blobs/metadados |
| `i18n` | list, build, similar, make-pot, sign | sem banco/start | Crowdin/rede, PHAR, ZIP, POT, assinatura e DNS opcional |
| `import` | stream, prime-time, drop | conecta | emite ou executa SQL; cria/trunca/insere conforme modo |
| `list` | import, export, show | conecta | itens de listas dinâmicas e CSV |
| `org` | import, export | conecta | cria organizações e exporta CSV |
| `package` | formato, DNS, skip-test | bootstrap vazio | staging, testes, deploy, ZIP e limpeza temporária |
| `serve` | host, port | sem banco/start explícito | cria router temporário e inicia `php -S` |
| `unpack` | destino, include, verbose | bootstrap vazio | copia distribuição e pode reescrever `INCLUDE_DIR` |
| `upgrade` | summary/execução | conecta + start | aplica patches/tarefas e avança assinatura |
| `user` | import, export, activate, lock, list, set-password | conecta; start condicional | cria/exporta, ativa/bloqueia conta e define senha |

Evidências completas por módulo estão em `include/cli/modules/*.php`. Conectam:
`agent`, `cron`, `export`, `file`, `import`, `list`, `org`, `upgrade` e
`user`. Iniciam a aplicação: `agent`, `cron`, `export`, `file`, `upgrade`
e `user` apenas quando envia ativação.

## Riscos e inconsistências estáticas

- `manage.php:70` compõe o include com argumento local antes de uma whitelist
  explícita; a fronteira pressupõe shell confiável.
- `deploy --clean`, import prime-time/drop, file load/migrate/expunge e upgrade
  são mutáveis/destrutivos e exigem alvo/backup/ambiente controlado.
- `include/cli/modules/file.php:288-294` usa `unserialize()` em backup; o stream
  deve ser confiável.
- `include/cli/modules/package.php:14-18` anuncia `targz`, mas
  `include/cli/modules/package.php:86-90` implementa apenas ZIP.
- `include/cli/modules/export.php:51,76` procura
  `include/cli/modules/importer/`, diretório ausente na baseline.
- `include/cli/modules/org.php:48` contém item `email` sem valor explícito na
  chamada a `Organization::fromVars()`; efeito pendente.
- `include/cli/modules/user.php:95` chama `UserAccount::create()` ao ativar um
  usuário sem conta, mas esse método não existe na baseline; a Onda 7 confirmou
  término fatal no comando `user activate`. O fluxo web usa
  `ClientAccount::createForUser()` em `account.php`.

## Regras operacionais para a engenharia reversa

Na Onda 7, `user import` criou uma fixture fictícia com sucesso; `user activate`
falhou antes da criação da conta pelo defeito acima. As demais operações
mutáveis ou destrutivas continuam condicionadas a alvo, evidência e, para
exclusão, plano/backup/rollback. Ferramentas de distribuição e desenvolvimento
não devem ser confundidas com comandos administrativos do domínio.
