# Evidências comportamentais

Esta área reúne evidências sanitizadas e versionadas dos cenários da análise
comportamental. Ela complementa as conclusões de
`BEHAVIORAL_ANALYSIS.md`: cada dossiê deve permitir distinguir o que foi
observado em runtime, o que foi confirmado no banco e o que foi inferido do
código, sem publicar credenciais, cookies ou dados pessoais.

## Contrato mínimo

Cada cenário deve registrar:

1. objetivo e precondições;
2. fixture e estado inicial sanitizados;
3. ação HTTP ou CLI exercitada;
4. resposta observada;
5. estado persistido posterior;
6. caminho de código relacionado;
7. limitações e divergências;
8. rollback e estado final da homologação.

Saídas extensas, dumps SQL, cookies e segredos permanecem em `.local/`. Os
valores necessários à auditoria humana são transcritos para o dossiê e para um
registro JSON sanitizado. Uma conclusão não deve ser tratada como evidência
completa quando existir apenas no resumo de progresso.

## Repetição corretiva de BHV-027 e BHV-029

Em 2026-09-04 foi identificada uma lacuna de rastreabilidade: esses cenários
tinham conclusões agregadas nos checkpoints da Onda 8, mas seus scripts e
resultados detalhados estavam somente na área local ignorada pelo Git.

A repetição corretiva seguirá este plano antes de qualquer mutação:

1. criar novo dump binário UTF-8 da base ativa;
2. restaurá-lo em uma base temporária e validar contagens e bytes acentuados;
3. repetir vínculo/desvínculo e criação de ticket a partir de uma entrada `R`;
4. capturar apenas resultados sanitizados;
5. restaurar a base ativa pelo mesmo fluxo binário;
6. comparar contagens, fixtures e amostra UTF-8 entre base ativa e temporária;
7. publicar os dossiês e registros JSON nesta área.

Nenhuma exclusão funcional será exercitada. A restauração integral é o rollback
planejado para tickets, entradas e eventos criados durante a repetição.

## Índice

- [BHV-027 — vínculo e desvínculo de tickets](BHV-027-linked-tickets.md)
- [BHV-029 — ticket criado a partir de resposta](BHV-029-ticket-from-response.md)

Os dados equivalentes para consumo automatizado estão em `data/`, no formato
JSON. Os dossiês são a fonte explicativa principal.
