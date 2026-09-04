# Visão geral dos sinais

## Contrato do barramento

`Signal::connect()` registra callbacks por nome. `Signal::send()` tenta
chamá-los na ordem de conexão, com filtros opcionais por classe e predicado
(`include/class.signal.php:56-100`). Não há veto padronizado por retorno, mas
exceção não tratada ou término do callback pode interromper os seguintes. O
payload não possui schema ou validação e pode ser recebido por referência.

Não há prioridade explícita, unsubscribe, isolamento de exceções, agregação de
retorno ou veto padronizado. Portanto, mutabilidade deve ser confirmada por
sinal; a simples emissão não define uma extensão segura.

## Cobertura estática

Excluindo exemplos em comentários, a baseline contém 154 emissões literais,
41 nomes literais distintos, 19 conexões internas e 13 nomes conectados
internamente. Há também uma emissão dinâmica em
`include/class.thread.php:260-275`, resolvida para `object.created` ou
`object.deleted`.

| Família | Nomes literais observados |
| --- | --- |
| Dispatch | `api`, `ajax.client`, `ajax.scp`, `apps.admin`, `apps.scp` |
| Autenticação | `auth.clean`, `auth.login.failed`, `auth.login.succeeded`, `auth.logout`, `auth.pwchange`, `auth.pwreset.email`, `auth.pwreset.login`, `person.login`, `person.logout` |
| Modelo/objetos | `model.created`, `model.updated`, `model.deleted`, `object.created`, `object.edited`, `object.deleted`, `object.view` |
| Domínio | `organization.created`, `task.created`, `threadentry.created`, `ticket.create.before`, `ticket.create.validated`, `ticket.created`, `user.created` |
| Auditoria/UI extensível | `agent.audit`, `agenttab.audit`, `ticket.view.more`, `user.audit`, `usertab.audit` |
| Infraestrutura | `config.ttfonts`, `cron`, `export.tables`, `mail.decoded`, `mail.received`, `session.close`, `syslog`, `system.install` |

As conexões internas cobrem apenas subconjunto: `api`, `auth.clean`, `cron`,
`model.created`, `model.deleted`, `model.updated`, `object.deleted`,
`organization.created`, `session.close`, `system.install`,
`threadentry.created`, `ticket.created` e `user.created`.

O documento histórico `setup/doc/signals.md` descreve somente oito sinais. O
teste `setup/test/tests/test.signals.php` verifica apenas se nomes literais
conectados possuem emissão literal; não valida payload, ordem, autenticação,
mutabilidade nem emissões sem subscriber interno.

## Dispatchers expostos

| Sinal | Emissor | Ordem |
| --- | --- | --- |
| `api` | `api/http.php:19-29` | após rotas nativas, antes da resolução |
| `ajax.client` | `ajax.php:28-51`; `apps/dispatcher.php:23-31` | antes da resolução |
| `ajax.scp` | `scp/ajax.php:324-333` | após rotas nativas |
| `apps.admin`/`apps.scp` | `scp/apps/dispatcher.php:29-45` | preenche dispatcher inicialmente vazio |

`Dispatcher::resolve()` usa o primeiro matcher. Rotas anexadas depois das
nativas não as substituem automaticamente (`include/class.dispatcher.php:29-65`).

## Catálogo individual

O [catálogo exaustivo](SIGNAL_CATALOG.md) relaciona os 41 nomes a emissor,
momento, objeto, payload, subscriber interno e risco estático. Nenhum sinal é
classificado como fronteira segura de customização apenas por existir.
