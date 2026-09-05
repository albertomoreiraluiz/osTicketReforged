# BHV-029 — ticket criado a partir de resposta

## Resultado

O cenário foi repetido em 2026-09-04 especificamente a partir de uma entrada de
resposta administrativa, `type=R`. O formulário recuperou o corpo da resposta,
mas não preencheu um solicitante. Após a seleção explícita do usuário, o fluxo
criou um novo ticket aberto, de origem `Phone`, atribuído ao agente criador e
com uma mensagem inicial byte a byte igual ao corpo da resposta de origem.

O resultado detalhado também está disponível em
[`data/BHV-029.json`](data/BHV-029.json).

## Precondições e proteção

Foi usada uma resposta fictícia existente, identificada apenas por
`entry_id=25`, no ticket `5`. A entrada tinha `type=R`, `staff_id=1` e corpo de
58 bytes; o ticket pertencia ao usuário interno `3`.

O mesmo snapshot binário validado descrito em BHV-027 protegeu o ensaio. O novo
ticket e suas entradas eram temporários e seriam removidos somente pela
restauração integral da base — não por exclusão funcional.

## Formulário observado

A interface usa navegação direta para
`GET /scp/tickets.php?a=open&tid=25`. O formulário principal respondeu `200` e
o formulário dinâmico do tópico respondeu `200`. O campo dinâmico que recebeu o
corpo estava preenchido com 58 bytes.

O formulário inicial não continha `uid`. Esse é o contraste relevante entre
uma mensagem de cliente e uma resposta: a entrada `R` tem `staff_id`, mas não
um `user_id` que possa ser herdado como solicitante. O ensaio selecionou
explicitamente o usuário `3`, proprietário do ticket de origem.

O caminho de interface que inicia a ação está em
`TEA_CreateTicket::getCreateTicketUrl()`
(`include/class.thread_actions.php:445-480`). Ele gera a rota
`tickets.php?a=open&tid={entry_id}`; não é um diálogo AJAX.

## Submissão e persistência

A submissão tradicional respondeu `200` após o redirecionamento e a URL final
continha o identificador do ticket criado. A página exibiu o assunto fictício.

| Observação | Valor |
| --- | --- |
| ticket derivado | `ticket_id=12` durante o ensaio |
| solicitante selecionado | correspondeu ao usuário `3` |
| agente | `staff_id=1` |
| origem | `Phone` |
| estado | aberto (`status_id=1`) |
| corpo inicial | igualdade binária com a resposta `25` |
| tipos de entrada iniciais | `M`, `N`, `R`, `N` |
| notas no ticket de origem após a ação | `1` |
| notas no ticket derivado após a ação | `2` |

A entrada `M` do ticket derivado pertence ao solicitante selecionado; a entrada
`R` pertence ao agente. A conclusão sobre preservação do corpo não decorre de
inspeção visual: foi verificada no MariaDB com comparação `BINARY` entre a
entrada de origem e a mensagem inicial derivada, resultando em exatamente uma
igualdade.

## Rollback da repetição corretiva anterior

Após a captura, o snapshot foi restaurado por streams binários. As contagens de
sete tabelas e a amostra UTF-8 coincidiram com o banco temporário. O ticket
temporário `12` deixou de existir, a resposta `25` reapareceu no ticket `5` e as
fixtures relacionais voltaram ao estado inicial. Nenhuma mutação deste ensaio
permanece ativa.

Esse estado deixou de satisfazer o critério de inspeção humana definido
posteriormente em GOV-018.

## Evidência operacional mantida no osTicket

Uma passagem adicional criou e manteve o ticket:

| Campo | Valor visível ou persistido |
| --- | --- |
| número | `166522` |
| ID interno | `12` |
| assunto | `[OSTR-EVIDENCE] Ticket criado a partir de resposta` |
| resposta de origem | entrada `25` do ticket `912803` |
| origem/estado | `Phone`, aberto |

O número `166522` aparece na fila administrativa. O corpo inicial mantém
igualdade binária com a resposta `R`; o ticket conserva as entradas `M`, `N`,
`R`, `N` e as referências produzidas pelo fluxo. O snapshot de contingência não
foi restaurado, pois a fixture deve permanecer inspecionável no painel.

## Limitação histórica corrigida

A execução anterior documentava corretamente a diferença entre mensagem e
resposta, mas não expunha as asserções numa página própria e versionada. Este
dossiê registra exatamente a repetição sobre `type=R`; não generaliza o
comportamento para e-mail importado, nota interna ou outros tipos de entrada.
