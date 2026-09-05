# Auditoria complementar de fechamento da Onda 9

## Objetivo e precondições

Em 2026-09-05, quatro resultados que ainda apareciam de forma incompleta ou
contraditória nos documentos foram repetidos pela interface renderizada. A
sessão administrativa foi carregada pelo formulário visual; credenciais,
cookies e tokens não foram registrados. O SMTP local sem relay estava ouvindo
somente em `127.0.0.1:25`.

Nenhum arquivo do core, schema ou registro foi excluído. A única mutação
funcional foi o reenvio autorizado de uma resposta fictícia existente.

## Resultados

| Superfície | Ação visual | Resultado observável | Estado |
| --- | --- | --- | --- |
| Fila aberta — Exportar | abrir diálogo, conferir 25 colunas e submeter duas vezes | o diálogo fechou; não houve evento de download do navegador interno nem arquivo novo nas áreas locais verificadas | divergência confirmada, sem causa presumida |
| Administração — Usuários — Modelos — Página de Login | acionar a linha pela interface | o destino renderizado foi `#ajax.php/content//manage`; abriu sobreposição vazia, que continuou sem conteúdo após espera | defeito reproduzido |
| Ticket `229189`, entrada de e-mail | menu da entrada → `View Email Headers` | modal exibiu cabeçalhos MIME, remetente, destinatário, data, assunto, tipo de conteúdo e transferência | coberto visualmente |
| Ticket `593078`, resposta do agente | menu da resposta → `Edit and Resend` → `Salvar e Reenviar` | modal exibiu editor, assinatura e três ações; a submissão substituiu a entrada `4` pela `104` e mostrou os selos `Editado` e `Reenviado` | coberto visualmente; fixture preservada |

## Correlação com o código

- `include/staff/settings-users.inc.php:160-181` indexa páginas por `type` e
  interpola o `id` no destino `content/<id>/manage`; quando o tipo esperado não
  existe, o acesso ao array fornece um identificador vazio. A ausência do
  registro persistido é uma hipótese compatível, não foi convertida em causa
  comprovada nesta auditoria.
- `TEA_ViewEmailHeaders` é registrado em
  `include/class.thread_actions.php:82`; o menu é composto por
  `include/staff/templates/thread-entry.tmpl.php:29-32`.
- `TEA_EditAndResendThreadEntry` limita a ação a respostas `R` publicadas por
  agente e trata `commit=resend` em
  `include/class.thread_actions.php:323-349`. O retorno `201` inclui o id antigo
  e o novo id da entrada.
- a fila monta o formulário em
  `include/staff/templates/queue-export.tmpl.php:24-77`; o backend reconhece o
  export, percorre a fila, finaliza, oferece envio por e-mail e remove o arquivo
  temporário em `include/ajax.tickets.php:2056-2070`. A auditoria não isolou se
  a falta de materialização decorre do navegador, do mecanismo assíncrono ou do
  backend e, por isso, não atribui causa.

## Estado final

O ticket `593078` mantém a evidência visível dos selos de edição e reenvio. A
Página de Login e a exportação CSV permanecem como divergências conhecidas da
baseline. Não há teste funcional relevante pendente apenas por falta de
repetição visual; campanhas especializadas continuam explicitamente fora do
recorte da Onda 9.
