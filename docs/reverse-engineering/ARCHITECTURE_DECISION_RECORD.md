# Portão D — Dossiê de opções arquiteturais

## Estado e escopo

**Proposta**, iniciada em 2026-09-05 por autorização do responsável. Baseline
`v1.18.4`, commit `8d38b0619649a50ee7cbbf37085f5d297fdc6f36`; checkpoint de
entrada `874efbd8`. A integração documental não aceita a arquitetura nem libera
implementação. ADR 0001 continua aceito para Angular/PrimeNG em `frontend/`.

## Evidências que restringem as alternativas

| ID | Fato observado na baseline | Consequência para a proposta |
| --- | --- | --- |
| D-E01 | `api/http.php:16-29` monta criação de tickets/cron e emite `Signal::send('api', $dispatcher)` antes de resolver | Há ponto de extensão de rotas; a API nativa não cobre o atendimento completo |
| D-E02 | `Dispatcher::append/extend` em `include/class.dispatcher.php`; `UrlMatcher::dispatch` testa `access()` | É possível compor rotas; cada controlador precisa impor sua política de acesso |
| D-E03 | `Signal::send`, `include/class.signal.php:89-101`, executa callbacks sincronicamente | Sinal não é fila durável nem garantia de entrega após commit |
| D-E04 | `VerySimpleModel::save/delete`, `include/class.orm.php:613-722`, e `include/mysqli.php:21-103` | Chamadas ao domínio não garantem uma transação abrangente ou rollback de e-mail/arquivo |
| D-E05 | `Ticket::create/update/setStatus`, `include/class.ticket.php`; cadeia em `INTEGRITY_FAILURES.md` | Erro pode coexistir com efeito persistido; retry automático precisa de desenho próprio |
| D-E06 | `scp/staff.inc.php`, `scp/admin.inc.php`, `include/class.auth.php`, síntese `AUTHENTICATION.md` | Agente, administrador e cliente exigem contextos e verificações distintos |
| D-E07 | `include/class.plugin.php`, `PluginManager::bootstrap`, `Plugin` e `PluginInstance`; `PLUGIN_ARCHITECTURE.md` | Plugin executa no processo confiável e depende de símbolos internos, sem isolamento de falha |
| D-E08 | `include/client/header.inc.php`, `include/staff/header.inc.php`, `scp/js/scp.js`; `FRONTEND_ANALYSIS.md` | HTML/PJAX/AJAX não constituem contrato estável para Angular |

D-E01 a D-E07 foram reconfirmados estaticamente nesta abertura e nas revisões
registradas em `GATE_D_REVIEW.md`; D-E08 e os resultados operacionais são herdados
do inventário. Reconfirmar cada cadeia por caso de uso antes de implementação.
A cobertura estrutural não prova viabilidade operacional
de nenhuma alternativa. Achados conhecidos de segurança continuam abertos para
tratamento; não são riscos aceitos por este dossiê.

## Alternativas completas

### A — Fachada PHP carregada como extensão do osTicket

**Proposta:** Angular chama contratos JSON próprios. Um plugin registra rotas
em namespace exclusivo pelo sinal `api`; controladores próprios autenticam,
autorizam, validam e traduzem DTOs. Adaptadores internos chamam classes do
domínio existente. O osTicket continua proprietário das tabelas de negócio,
coleta de e-mail, cron e invariantes. O frontend e os DTOs não recebem modelos
ORM nem dependem de fragmentos HTML.

O pacote de extensão seria um artefato próprio, implantado pelo mecanismo de
plugins, sem editar arquivos herdados. Layout de fontes, URL definitiva,
autenticação, versão do contrato e empacotamento dependem de ADRs posteriores.
Chamadas ao domínio devem reproduzir as guardas encontradas nos controladores:
a presença de uma sessão ou de uma chave API não concede acesso ao objeto.

**Inferência:** é a menor distância entre contratos novos e regras existentes.
O custo se concentra nos adaptadores e testes de compatibilidade por release.
Não há garantia antecipada de zero alteração do core para todos os módulos.

### B — Serviço externo com conector PHP restrito

**Proposta:** Angular chama um serviço separado que mantém os contratos públicos.
Esse serviço chama um conector PHP no osTicket; o conector usa as mesmas regras
de domínio e controla o escopo do usuário. Não há escrita externa direta nas
tabelas do osTicket. Cron/e-mail continuam no legado. Identidade propagada,
autenticação entre serviços e prevenção de uso indevido de autoridade precisam
de desenho e validação específicos.

**Inferência:** fornece isolamento de processo e maior liberdade de evolução da
camada pública, mas mantém a necessidade do adaptador PHP. Acrescenta latência,
falhas parciais de rede, correlação de logs e dois ciclos de implantação.
Tecnologia do serviço não é escolhida neste portão.

### C — Extração de módulos para serviços com dados próprios

**Proposta:** contratos e regras passam, módulo a módulo, a serviços separados.
Cada agregado migrado tem um único escritor e armazenamento sob sua autoridade.
O osTicket permanece dono do que ainda não migrou. Conectores sincronizam as
fronteiras; uma tabela de ownership e um plano de corte precedem cada migração.
Sinais síncronos não são tratados como log de eventos confiável.

**Inferência:** maximiza independência futura, mas exige reconstruir invariantes,
integrações de e-mail, referências e permissões. Dual-write sem coordenação e
retorno por restauração integral são inadequados após novas escritas. O esforço
de reconciliação e a possível necessidade de intervenção no core são maiores.

## Comparação por critérios comuns

As classificações abaixo são **inferências qualitativas**, sem estimativas de
prazo ou notas numéricas fictícias. Baixo delta de arquivo não significa baixo
acoplamento semântico.

| Critério | A — Fachada interna | B — Serviço e conector | C — Extração e dados próprios |
| --- | --- | --- | --- |
| Upstream e delta | ponto de extensão existente; adaptadores acompanham símbolos internos | mesmo conector sujeito a mudanças + contrato entre serviços | ownership e sincronização podem exigir maior intervenção no legado |
| Reuso das regras | direto via adaptadores | indireto via conector | regras reconstruídas em cada agregado extraído |
| Autorização | contexto local, guardas explícitas por caso de uso | guardas locais + propagação segura do principal | políticas coerentes entre domínios e migração de identidade |
| Integridade | herda autocommit e efeitos parciais | herda efeitos parciais e acrescenta timeouts de rede | exige consistência entre donos e corte de escrita |
| Coexistência PHP/Angular | rotas paralelas, mesmo domínio proprietário | rotas paralelas, rede adicional | frontend depende da fase e do dono de cada agregado |
| Operação | mesmo processo PHP; falha da extensão pode afetar legado | dois serviços e rastreamento distribuído | múltiplos serviços e reconciliação persistente |
| Testes | contrato, permissões e efeitos do adaptador por baseline | anteriores + timeout e identidade entre serviços | anteriores + equivalência das regras e migração dos dados |
| Rollback inicial | retirar tráfego novo preservando dados do legado | retirar tráfego/serviço preservando conector compatível | exige retorno de dados e ownership, não somente trocar a rota |
| Evolução independente | limitada pela fronteira interna do core | maior na API pública, limitada no conector | maior após cada extração concluída |
| Adequação ao primeiro módulo | melhor candidata ao aprendizado incremental | justificável se surgir necessidade concreta de isolamento | prematura para o primeiro recorte com escrita |

Rastreabilidade de cada critério da matriz (os efeitos futuros em B/C são
inferidos dessas restrições, não observados em implementações inexistentes):

| Critério da comparação | Evidências de sustentação |
| --- | --- |
| Upstream e delta | D-E01, D-E02, D-E07 |
| Reuso das regras | D-E04, D-E05, D-E06 |
| Autorização | D-E02, D-E06 |
| Integridade | D-E03, D-E04, D-E05 |
| Coexistência PHP/Angular | D-E01, D-E06, D-E08 |
| Operação | D-E03, D-E05, D-E07 |
| Testes | D-E01 a D-E08; critérios de MIGRATION_STRATEGY.md |
| Rollback inicial | D-E04, D-E05, D-E07 |
| Evolução independente | D-E01, D-E02, D-E07, D-E08 |
| Adequação ao primeiro módulo | D-E04, D-E05, D-E06, D-E08; INTERFACE_COVERAGE_MATRIX.md |

## Recomendação inicial

**Proposta:** iniciar pela alternativa A, com fronteira explícita de contratos e
adaptadores, preservando B como evolução se isolamento de processo ou requisitos
operacionais o justificarem. C permanece alternativa de longo prazo, dependente
de evidência de ownership, sincronização e benefício por módulo.

Esta recomendação atende ao objetivo de preservar o core e criar APIs por módulo.
Será revista se uma prova posterior mostrar que registro de rotas, bootstrap,
guardas ou ciclo de plugins não permitem uma fronteira adequada sem core patch.
Nesse caso, registrar o requisito e redesenhar a fronteira com nova evidência;
B depende do mesmo conector e não resolve sua inviabilidade apenas movendo a
API pública para outro processo. Não contornar controles.

## Condições antes de qualquer acesso pela nova API

**Proposta:** desde a primeira consulta, exigir identidade plenamente validada,
MFA concluído quando aplicável, conta/sessão válidas, autorização por ação e
objeto, totalizadores filtrados e projeção mínima por DTO. O bootstrap de
`api/api.inc.php` não executa automaticamente as guardas de `scp/staff.inc.php`.
`UrlMatcher::dispatch` verifica `access()` em controladores por classe, não em
todo formato de callable. Nenhuma rota pode depender de uma guarda implícita.

| Risco herdado | Impacto | Condição para avançar |
| --- | --- | --- |
| sinal de login anterior à conclusão de MFA | presença de identidade não comprova autenticação concluída | validar sessão completa; `StaffSession::isValid`, não somente sinal |
| plugin executa como código confiável | falha pode atingir o legado | revisão do pacote e prova do ciclo de vida |
| achados de tarefas, arquivos e upload | risco de reutilização dos fluxos afetados | excluídos do primeiro recorte; tratamento ou aceite específico antes de inclusão |
| coexistência mantém superfícies PHP | nova interface não elimina riscos legados | conservar registro de riscos e limites por superfície |

Fontes: `AUTHENTICATION.md`, `SECURITY_MODEL.md`, `PLUGIN_ARCHITECTURE.md` e
revisão estática em `GATE_D_REVIEW.md`. Nenhum risco é aceito por esta tabela.

## Condições adicionais antes de escrita pela nova API

**Proposta de critérios de liberação posterior:** política completa de identidade,
sessão e CSRF; autorização por principal/ação/objeto; DTO com allowlist;
tratamento explícito de falha parcial; concorrência; anexos e notificações;
correlação de operação e procedimento de reconciliação. Nenhum retry mutável
automático até existir idempotência demonstrada. Reusar domínio não corrige os
achados existentes nem garante atomicidade.

## Decisões a apresentar ao responsável

1. aceitar A como direção arquitetural inicial, sujeita às condições acima;
2. aceitar o primeiro recorte proposto na [estratégia de migração](MIGRATION_STRATEGY.md);
3. autorizar, após ADR aceito, somente o planejamento da prova de integração.

Autenticação, versões frontend, layout executável, armazenamento de segredos e
contratos concretos continuam pendentes de decisões próprias. Os seis pareceres
e seus ajustes estão registrados em `GATE_D_REVIEW.md`. Não existe ADR aceito de
arquitetura-alvo neste checkpoint.
