# ADR 0004 — Módulos Reforged sobre o backend original do osTicket

- Status: **Aceito** para a estratégia e fronteira descritas abaixo
- Data: 2026-09-05
- Responsável: mantenedor do projeto, por confirmação explícita após o exemplo de autenticação
- Complementa ADR 0001 e ADR 0003; não reativa a recomendação descartada da PR #28

## Contexto

Após o reinício do Portão D, o responsável esclareceu o escopo do novo painel
e o significado de extração. O exemplo do login distinguiu apresentação,
coordenação HTTP e mecanismos existentes. A intenção não é retirar HTML dos
arquivos upstream nem copiar o backend para formar outra implementação.

## Decisão aceita

1. Manter o osTicket original intacto, sem refatoração do core para acomodar
   o Reforged. O SCP legado permanece funcional em `/scp`.
2. Escrever módulos e endpoints Reforged do zero como interfaces para as
   capacidades do backend original. Eles recebem requisições, coordenam chamadas
   aos mecanismos existentes e expõem respostas estruturadas à aplicação Angular.
3. Reutilizar regras de negócio, persistência, autenticação, políticas,
   permissões e efeitos dos fluxos existentes. Não recriar silenciosamente essas
   regras, não executar fluxos por escrita direta nas tabelas e não encapsular
   páginas HTML como se fossem contratos de API.
4. Fazer a separação da apresentação no código novo. Templates e HTML legados
   continuam intactos; não integram o contrato das respostas da API ao Angular.
5. O escopo-alvo do Angular + PrimeNG é todo o SCP: atendimento, gestão e
   configurações administrativas. Entrega incremental não reduz esse objetivo.
6. Ambos os painéis coexistem continuamente sobre o mesmo backend. Reutilizar
   identidade e mecanismos de acesso do osTicket, preservando permissões,
   departamentos e equipes. Alternar entre painéis sem novo login é requisito
   aceito; a integração técnica de sessão compartilhada precisa ser validada,
   incluindo MFA, expiração e logout.
7. O portal do usuário permanece em planejamento separado, conforme ADR 0003.

“osTicket como plugin do Reforged” descreve a relação de integração pretendida,
não uma escolha de carregador, empacotamento ou mecanismo de plugins. Não é
autorização para converter fisicamente o upstream em outro pacote agora.

## Fronteira de responsabilidades

| Parte | Responsabilidade aceita | Não deve assumir |
| --- | --- | --- |
| Backend osTicket | mecanismos e regras existentes, dados e efeitos do domínio | apresentação Angular |
| Módulos Reforged | entrada/saída da API e coordenação explícita dos fluxos reutilizados | regras duplicadas, atalhos de persistência ou HTML de páginas legadas |
| Angular + PrimeNG | interface completa do SCP e estado de interação | autoridade final de autorização ou regras de negócio do servidor |
| SCP legado | interface original preservada | obrigação de ser desativado à medida que o Reforged evolui |

## Exemplo de referência: autenticação

**Fatos observados na baseline `v1.18.4`:**

- `scp/login.php:62-74`: verifica CSRF, valida entrada e chama
  `StaffAuthenticationBackend::process()`;
- `include/staff/login.tpl.php`: renderiza campos, mensagens, MFA e JavaScript;
- `AuthenticationBackend::process`, em `include/class.auth.php:324`: coordena
  backends permitidos, autenticação, login e tratamento de falhas;
- `osTicketStaffAuthentication::authenticate`, em `include/class.auth.php:1209`:
  localiza o agente, verifica senha e aplica políticas no backend local;
- `StaffAuthenticationBackend::login`, em `include/class.auth.php:625`, e
  `StaffSession::isValid`, em `include/class.usersession.php:257`: distinguem
  preparação da autenticação, MFA pendente e sessão válida;
- `scp/staff.inc.php` e `scp/admin.inc.php`: aplicam controles adicionais nas
  páginas protegidas; chamar o autenticador não substitui essas responsabilidades.

**Aplicação da estratégia:** módulo novo recebe a entrada, preserva as guardas,
aciona os mecanismos originais e traduz estados para o Angular, sem renderizar
`login.tpl.php`. Não basta comparar a senha nem escrever uma identidade na sessão.

`AuthController`, `AuthenticationService`, `OsTicketAuthenticationAdapter`,
`AuthResponseMapper` e os JSONs mostrados na conversa foram exemplos didáticos,
não classes, DTOs, nomes ou contratos aprovados. Autenticação foi o exemplo de
extração; não foi automaticamente escolhida como primeira entrega executável.

## Alternativas discutidas e limites

- Refatorar os arquivos originais para remover HTML: rejeitado pelo responsável.
- Copiar ou reimplementar regras do backend: não atende à reutilização confirmada.
- Reutilizar a cadeia original por módulos novos com saída estruturada: aceito.

Esta decisão não é a seleção automática da alternativa A da PR #28: o mecanismo
de extensão, hospedagem e organização física daquela proposta continua descartado
como recomendação vigente. A discussão atual aprovou a fronteira, não esse pacote.

## Consequências e riscos de atualização

Cada módulo deverá rastrear entrada, validações, autorização, persistência e
efeitos até a resposta. Regras presas ao controlador legado exigem análise e
decisão explícita; não permitem modificar core ou duplicar lógica por conveniência.
O ADR 0007 complementa este limite com a adaptação aceita da coordenação e a
análise comportamental dos trechos muito acoplados antes de sua reprodução.
Reutilização não garante atomicidade nem corrige achados herdados. Atualizações
do upstream exigirão conferir símbolos usados e regressão dos dois painéis.
Os fatos acima sustentam o exemplo, não provam viabilidade de todos os módulos.

## Pendências e limite de autorização

Permanecem abertos: layout físico, bootstrap e carregamento dos módulos,
contratos HTTP/DTO/erros/versionamento, integração de sessão e CSRF, URL definitiva
(`/scp/reforged` é candidata), versões, empacotamento, implantação, primeiro
recorte, provas de compatibilidade e tratamento dos riscos pertinentes.

O Portão D permanece em andamento para esse detalhamento e revisão. A aprovação
da estratégia não inicia implementação, não altera schema/core e não constitui
aprovação de um endpoint executável de autenticação. Não reabrir os princípios
aceitos sem conflito ou evidência nova; discutir apenas as decisões ainda abertas.

## Referências

- [ADR 0001](0001-frontend-angular-primeng.md)
- [ADR 0003](0003-coexistencia-paineis-portal-separado.md)
- [Autenticação original](../reverse-engineering/AUTHENTICATION.md)
- [Plano ativo](../plans/active/0001-reverse-engineering.md)
- Confirmações explícitas do responsável sobre escopo completo do SCP,
  backend intacto, módulos novos e exemplo de autenticação, seguidas de pedido
  para formalizar no repositório.
