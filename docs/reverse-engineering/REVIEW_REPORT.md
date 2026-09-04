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
