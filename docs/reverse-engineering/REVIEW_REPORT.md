# Revisão cruzada do Portão C

## Escopo

A Onda 6 revisa artefatos por instâncias diferentes das produtoras. Nenhuma
revisão executa banco, UI, runtime, rede ou lê `.env`.

## Achados triados

| Severidade | Achado | Tratamento |
| --- | --- | --- |
| alta | 500 da API pode ocorrer após ticket persistido; retry sem idempotência pode repetir efeitos | incorporado em `INTEGRITY_FAILURES.md` |
| alta | exceção de Signal pós-SQL pode ocultar sucesso persistente | matriz de compensação ampliada |
| alta | `auth.login.succeeded` pode anteceder conclusão de 2FA | corrigido em sinais e segurança |
| alta | `Exporter::register()` não é provider extensível | separado como operação efêmera |
| alta | entregáveis canônicos de arquitetura, banco e autenticação ausentes | criados como sínteses canônicas |
| média | catálogo dizia 16 mecanismos, mas continha 22 | contagem/taxonomia corrigidas |
| média | ticket filho não apaga thread compartilhada | escopo de exclusão corrigido |
| média | evento deleted tinha inferência determinística demais | cache/nulo/Error explicitados |
| média | coleta de arquivos descrita como 1/10 | corrigida para sorteio 1/9 |
| média | 13 alvos AJAX ausentes tratados igualmente | separados nove métodos/500 e quatro falhas de loading |
| média | rota plugin omitira guarda admin | guarda e respostas condicionais corrigidas |
| média | rotas de e-mail tinham efeitos genéricos | sessão, config, credencial e account detalhados |
| média | config de campos misturada a registry de tipos | linhas separadas na matriz |
| média | navegação confundida com aplicação protegida | menu + dispatcher e ACL local separados |
| média | fronteira core/dependências ausente | criada em `PROVENANCE_MAP.md` |

## Critério de fechamento

A segunda passagem foi aprovada pelos três revisores sem achado alto ou médio
aberto. Referências, contagens e navegação foram reconciliadas; o integrador
executou `mkdocs build --strict` e `git diff --check` com sucesso. Validações
exclusivamente comportamentais continuam registradas e não bloqueiam o
fechamento da análise estática.

## Onda 7 — revisão independente de ACL e arquivos

A instância `arquiteto-seguranca/onda-07/revisao-acl-upload` revisou em modo
somente leitura o primeiro conjunto estabilizado de evidências comportamentais.

| Severidade | Achado | Resultado |
| --- | --- | --- |
| alta | `postreply` sem `task.reply` e `task:status` sem `task.close` | resposta e fechamento confirmados em runtime; fixture reaberta |
| alta | URL HMAC de arquivo não inclui principal, pai nem attachment id | cliente sem visibilidade da tarefa consumiu anexo interno com URL staff |
| média | `ajaxUpload(true)` do staff ignora tipo e tamanho do campo | allowlist `.txt` contornada em runtime; validação básica permanece |
| média contratual | 413/415 ausentes de `Http::header_code_verbose()` | rejeição 415 observada como HTTP 500 |
| média | negação AJAX de `task.close` não propaga erro ao template | estado protegido, mas 200/formulário sem mensagem inequívoca |

O revisor não acessou `.env`, banco ou runtime e não alterou arquivos. As
conclusões foram relidas no código pelo integrador e incorporadas aos documentos
canônicos; correção do core e aceite residual permanecem fora da etapa atual.

## Onda 7 — revisão QA do fechamento funcional

A instância `engenheiro-qa/onda-07/revisao-fechamento-funcional` confrontou em
modo somente leitura a matriz comportamental e o diff de consolidação.

- confirmou 25 identificadores únicos, de `BHV-001` a `BHV-025`, todos com
  estado concluído e seção de evidência;
- não encontrou lacuna funcional adicional nem afirmação sem suporte que
  exigisse novo ensaio;
- classificou corretamente concorrência, `E_ALL`, integrações reais de correio,
  acessibilidade, desempenho e novos testes de segurança como fases futuras;
- apontou duas inconsistências documentais médias e uma baixa: autorização
  cruzada já confirmada ainda listada como lacuna, fechamento anunciado antes
  do registro do parecer e um checkpoint histórico redigido no presente.

O integrador corrigiu os três itens e repetiu as validações documentais. O
revisor não leu `.env`, banco ou runtime, não executou testes ofensivos e não
alterou arquivos. Não permanece bloqueio funcional ou documental da Onda 7.

## Onda 9 — revisão QA da repetição visual profunda

A instância `qa_engineer/onda09/revisao-final-2` revisou em modo somente leitura
a matriz visual reconciliada, a análise comportamental, o plano e os resumos de
progresso. A primeira passagem bloqueou o fechamento porque BHV-011, BHV-028 e
BHV-033 ainda apresentavam estados históricos da Onda 8 como se fossem atuais.

O integrador marcou a precondição antiga de e-mail como superada pelo fluxo
POP3/SMTP visual, delimitou o checkpoint incompleto de BHV-028 e reclassificou
BHV-033 após transferência e resolução reversíveis em lote na Onda 9. A segunda
passagem aprovou essas correções e confirmou também:

- os 19 editores de e-mail individualizados, sem alegar expansão não disparada;
- a matriz de acesso por departamento primário, negação fora do escopo e
  concessão por equipe;
- a divergência de materialização do CSV preservada sem causa presumida;
- exclusão em massa limitada à observação;
- coerência entre README, progresso, plano, matriz e análise comportamental.

Não permanece achado bloqueante. O revisor não alterou arquivos, estado do
navegador, banco ou serviços externos. O fechamento foi submetido pela PR #23.
