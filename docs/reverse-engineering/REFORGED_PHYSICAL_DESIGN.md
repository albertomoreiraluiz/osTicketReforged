# Reforged — Organização física e carregamento

## Estado e escopo

**Proposta para discussão**, não layout aprovado ou implementação. Data
2026-09-05, branch `codex/reforged-isolation-design`, entrada `8bd050ba` (PR #34),
baseline `v1.18.4`. [ADR 0006](../adr/0006-origem-api-isolamento-reforged.md)
aceita mesma origem, recursos/ações e isolamento; nomes novos abaixo são candidatos.

## Organização recomendada

**Proposta:** `frontend/` preservado e `reforged/` como raiz dos módulos PHP novos.
Árvore conceitual de fontes, não manifesto de deploy nem diretórios criados:

```text
repositório/
├── scp/, include/, api/, setup/, ...   # upstream preservado
├── frontend/                         # Angular/PrimeNG — ADR 0001
├── reforged/                         # candidato: código PHP próprio
│   ├── public/                       # somente entradas HTTP intencionais
│   ├── src/
│   │   ├── Integration/              # carregamento/contexto/adaptação do core
│   │   └── Modules/                  # capacidades M01–M15 conforme necessidade
│   ├── tests/                        # testes do PHP próprio
│   └── config/                       # configuração não secreta do código novo
├── docs/                             # documentação canônica, fora do produto
└── .codex/                           # automação, fora do produto
```

Subpastas não escolhem namespaces, autoloader, Composer, framework ou granularidade
de classes. Não criar quinze estruturas vazias antecipadamente. Integration é
responsabilidade transversal relacionada a M01, não um décimo sexto módulo de
negócio. Tests/config são candidatos; segredos não entram em config versionada.
O `.env` local continua exclusivo de ferramentas, sem leitura pelo produto.

| Organização | Avaliação proposta |
| --- | --- |
| `frontend/` e `reforged/` lado a lado | recomendada: preserva ADR 0001 e separa PHP novo do upstream |
| `reforged/frontend/` e `reforged/backend/` | concentra fontes, mas altera a localização já aceita no ADR 0001; não selecionada sem decisão |
| Código novo dentro de `scp/` e `include/` | mistura responsabilidades e aumenta a superfície de conflito; não atende ao isolamento aceito |

## Fontes, artefatos e endereços não são a mesma coisa

**Proposta:** o artefato publicado expõe somente os assets Angular compilados e
as entradas HTTP intencionais. Fontes PHP internas precisam estar disponíveis
ao runtime, não acessíveis diretamente pelo navegador. Não copiar a árvore
inteira do repositório para um diretório servido como método de publicação.
Arquivos de governança, testes, toolchains e segredos ficam fora do artefato público.
Anexos e exportações privados não se tornam assets estáticos pelo uso dessa pasta.

O SCP continua em `/scp`; o caminho Reforged e o prefixo da API ainda serão
escolhidos. Mapeamento por configuração do servidor, alias ou roteamento é
candidato, não regra Apache/Nginx já definida. A configuração deve preservar
rotas legadas e entregar deep links Angular sem capturar chamadas da API.
Same origin no navegador não exige mesma pasta física e não prova mesma sessão.

**Pendência:** antes de publicar, especificar allowlist de artefatos públicos,
localização interna, resolução de paths, fallback SPA, PHP handler e política
de acesso direto. Uma pasta chamada public não aplica essas garantias sozinha.
Topologia de desenvolvimento Angular (incluindo eventual proxy) requer desenho
compatível com a origem aprovada; não se presume CORS ou outra origem liberada.

## Carregamento: fatos reconfirmados e pontos ainda não resolvidos

**Fatos observados no código**, leitura estática nesta unidade:

| Fonte | Responsabilidade/acoplamento | Consequência para o desenho |
| --- | --- | --- |
| `bootstrap.php`, Bootstrap::loadConfig/loadCode/connect | configuração instalada, classes, conexão; define ROOT_DIR/INCLUDE_DIR e calcula ROOT_PATH | não duplicar credenciais; separar root físico do endereço publicado e verificar compatibilidade |
| `main.inc.php:23–47` | inicia backend/sessão e trata HTTPS; depois consome mensagens da sessão | include não equivale a contexto sem saída ou sem efeitos |
| `scp/staff.inc.php` | paths relativos, ACL, identidade/validade, atividade/offline, refresh, CSRF, perfil/upgrade e navegação | uso em nova pasta requer verificar paths e toda a cadeia; não basta carregar o arquivo |
| `scp/admin.inc.php` | exige staff/admin e pode carregar index/upgrade; monta navegação | administração não pode ser reduzida a sinalizar menu no Angular |
| `scp/ajax.php:16–25` | adapta resposta de login antes de carregar staff.inc | ponto observado de adaptação, não prova de bootstrap JSON completo |

**Proposta de responsabilidades do fluxo futuro**, não receita executável:

1. Entrada HTTP identifica intenção e aceita somente o contrato correspondente.
2. Integração estabelece contexto original uma vez por requisição, preservando
   configuração e identidade instaladas; verifica disponibilidade/compatibilidade.
3. Controles pertinentes à entrada e ao objeto condicionam a execução. Login,
   desafio e recuperação têm contexto próprio; não exigir sessão completa antes
   de iniciar autenticação, nem tratar contexto anônimo como acesso operacional.
4. Módulo coordena mecanismos originais e seus efeitos; saída traduz resultado
   para o contrato, sem encapsular páginas ou reconstruir regras de negócio.

Esta sequência é conceitual: guardas também existem no bootstrap e nos mecanismos
invocados, não somente em uma etapa isolada. Paths relativos, variáveis globais,
constantes, redirecionamentos e encerramentos antecipados exigem prova específica.
Não trocar basename, inventar flags, alterar core ou capturar HTML para declarar
a integração pronta. Se a cadeia não for reutilizável sob ADR 0004, registrar a
lacuna e solicitar decisão antes de implementar o trecho.

## Sessão: continuidade a especificar, não mecanismo novo

**Decisão aceita:** reutilizar identidade e acesso originais e permitir alternância
sem novo login (ADR 0004). **Proposta de critérios para a especificação seguinte:**

| Etapa | Evidência/critério a fechar |
| --- | --- |
| Entrada anônima e login | contexto/CSRF, políticas e desafios originais; retorno estruturado sem sessão paralela |
| Sessão completa e MFA pendente | distinguir estados; não representar desafio pendente como acesso operacional liberado |
| Requisições e alternância entre abas | compatibilidade de cookie/path, validade, renovação e expiração conforme mecanismos originais |
| Conta/ambiente restritos | preservar verificações de atividade, permissões, departamento/equipe, offline, upgrade e perfil obrigatório |
| Saída | encerramento original e efeitos associados, inclusive locks; resultado coerente nos dois painéis |
| Provedor externo | ficha por backend disponível, fluxo de retorno e continuidade; não presumir equivalência a senha local |

Fontes especializadas: [integração e contratos](INTEGRATION_CONTRACT_DESIGN.md),
[autenticação](AUTHENTICATION.md). `scp/logout.php` chama logOut, limpeza de
sessão/cookie e Lock::removeStaffLocks; esse conjunto não foi testado novamente.
Esta tabela não substitui matriz completa de guardas nem revisão IAM do contrato
concreto. As evidências visuais anteriores permanecem históricas, sem nova execução.

## Próxima unidade e critérios

Detalhamento produzido em [Entradas, sessão e publicação](REFORGED_ENTRY_SESSION_DESIGN.md).
Tipos de entrada e critérios avançaram; a árvore não foi aceita por inferência.

Discutir a árvore candidata sem reabrir mesma origem/recursos/isolamento aceitos.
Depois detalhar matriz por tipo de entrada e critérios de bootstrap compatível,
seguida de contratos concretos e revisão independente pertinente. Nome da pasta
aprovado futuramente não será prova de viabilidade nem liberação automática de código.
Nenhuma mudança em servidor, banco, sessão, frontend ou dependências nesta unidade.
