# BHV-027 — vínculo e desvínculo de tickets

## Resultado

O cenário foi repetido em 2026-09-04 e confirmou três comportamentos da
baseline `v1.18.4`:

1. o vínculo visual foi persistido, embora o endpoint retornasse HTTP `404`;
2. o desvínculo retornou `201`, mas deixou `flags=24` no antigo pai e `sort=1`
   no antigo filho;
3. a página do pai respondeu `200`, mas o log PHP registrou novamente
   `ArgumentCountError` em `LinkedEvent::getDescription()`.

O resultado detalhado também está disponível em
[`data/BHV-027.json`](data/BHV-027.json).

## Precondições e rollback

Foram usados dois tickets fictícios já existentes, identificados aqui apenas
como `pai` (`ticket_id=9`) e `filho` (`ticket_id=10`). Antes da mutação, ambos
estavam abertos, independentes e com `ticket_pid=0`, `sort=0`, `flags=0`; não
havia evento relacional entre eles.

Um dump binário MariaDB foi criado com `utf8mb4`, `--single-transaction` e
`--hex-blob`. O arquivo local ignorado pelo Git tinha 1.140.797 bytes e SHA-256
`98000c5b08c0dd5b5b7b3c6c1f13fdb9dfcdf98316df59b64e11667865a222ba`.
Antes do ensaio, sua restauração em banco temporário reproduziu as contagens de
sete tabelas e a amostra UTF-8 validada por `HEX()`.

## Vínculo observado

Foi submetido `POST /scp/ajax.php/tickets/9/link`, com os dois números de ticket
e `combine=2`, pela sessão administrativa e com token CSRF válido.

| Observação | Valor |
| --- | --- |
| status HTTP | `404` |
| mensagem de sucesso detectada | não |
| pai após o POST | `pid=0`, `sort=0`, `status=1`, `flags=24` |
| filho após o POST | `pid=9`, `sort=1`, `status=1`, `flags=8` |
| eventos relacionais | `2` |

Logo, o status HTTP não representa o resultado persistido. A leitura estática
explica o contrato: `Ticket::manageMerge()` retorna uma lista de tickets, mas
`Ticket::merge()` não produz um resultado verdadeiro para o modo visual; o
controlador traduz esse retorno em falha. Os caminhos relevantes são
`include/ajax.tickets.php:437-480` e `include/class.ticket.php:2498-2590`.

## Desvínculo observado

Foi submetido novo `POST` ao mesmo endpoint, agora com `dtids[]=10`.

| Observação | Valor |
| --- | --- |
| status HTTP | `201` |
| mensagem de sucesso detectada | sim |
| antigo pai | `pid=0`, `sort=0`, `status=1`, `flags=24` |
| antigo filho | `pid=0`, `sort=1`, `status=1`, `flags=0` |
| eventos relacionais acumulados | `4` |

O filho perde `ticket_pid` e a flag de vínculo, mas conserva a ordenação. O pai
mantém as flags de pai/vínculo. Isso coincide com a ordem de avaliação em
`Ticket::unlink()` e `Ticket::unlinkChild()` (`include/class.ticket.php:2464-2496`).

## Renderização e log PHP

Para isolar a apresentação, o vínculo foi repetido e a página
`GET /scp/tickets.php?id=9` foi aberta. Ela respondeu `200`, mostrou a mensagem
do pai e não chegou a mostrar a mensagem do filho. O corpo HTTP não continha o
texto do fatal; entretanto, o log `C:\xampp\php\logs\php_error_log` registrou
novamente:

```text
PHP Fatal error: Uncaught ArgumentCountError: 5 arguments are required, 4 given
in C:\xampp\htdocs\include\class.thread.php:2690
```

O ponto está em `LinkedEvent::getDescription()`
(`include/class.thread.php:2685-2693`). O `200` da resposta, isoladamente, não é
evidência de renderização completa.

## Estado final

O dump foi restaurado por streams binários. A base ativa e a base temporária
voltaram a ter contagens idênticas nas sete tabelas de controle e a mesma
amostra UTF-8. Os tickets `9` e `10` terminaram novamente independentes, com
`pid=0`, `sort=0`, `flags=0` e zero eventos relacionais. Nenhuma mutação deste
ensaio permanece ativa.

## Limitação histórica corrigida

Na execução original, as saídas detalhadas ficaram somente em `.local/`, e o
documento geral registrou apenas a conclusão agregada. Por isso elas não eram
localizáveis no GitHub. Este dossiê e seu JSON são a evidência versionada da
repetição; o dump continua local porque pode conter dados e configuração da
homologação.
