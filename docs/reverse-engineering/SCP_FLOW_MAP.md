# SCP — Mapa de fluxos para módulos Reforged

## Estado e escopo

**Mapeamento estático concluído no universo declarado**, por solicitação do responsável após o ADR 0004. Entrada
`33d40fb7` (PR #30), branch `codex/scp-flow-mapping`; baseline `v1.18.4`,
`8d38b0619649a50ee7cbbf37085f5d297fdc6f36`.

Objetivo: mapear todo o SCP operacional e administrativo como fluxos de entrada,
guardas, chamadas ao backend, efeitos e apresentação a separar. Não implementar,
não escolher contratos definitivos ou layout Angular; portal separado.

## Manifesto da unidade

Três frentes independentes reduzem lacunas entre ações tradicionais e AJAX.
Subagentes somente leem código/documentação: sem escrita, descendentes, Git
mutável, rede, credenciais, banco, navegador ou testes de exploração. O integrador
é o único escritor de todos os documentos desta unidade, índices e governança.

| Instância | Cargo | Escopo de leitura |
| --- | --- | --- |
| reverse_engineering_engineer/scp-map/operacao | Engenheiro Sênior de Engenharia Reversa | tickets, tarefas, usuários, organizações, conhecimento, painel e diretório |
| reverse_engineering_engineer/scp-map/administracao | Engenheiro Sênior de Engenharia Reversa | configuração, equipe, dados de referência, e-mail, plugins, formulários e filas |
| api_architect/scp-map/transversais | Arquiteto de APIs | autenticação, perfil, sessão, dispatchers, recursos, exportação, manutenção e serviços AJAX compartilhados |
| qa_engineer/scp-map/revisao | Engenheiro de Qualidade | revisão independente posterior dos quatro mapas, catálogo e cobertura; somente leitura |

Entradas obrigatórias: AGENTS, REFORGED, contexto, checkpoint, progresso, plano
ativo, ADRs aceitos e catálogos especializados. Saída: famílias de fluxos com
IDs estáveis, caminhos/símbolos, guardas, efeitos, resposta atual e obstáculos
à extração. Distinguir fato observado, herdado e inferência. Ausência de evidência
deve permanecer lacuna; não preencher por nome de classe.

O integrador verificará a cobertura literal dos entrypoints e famílias AJAX,
reconciliará as fontes e submeterá o mapa consolidado a revisão independente.
Essa revisão não aceita decisões arquiteturais nem encerra o Portão D.

## Critério de conclusão

Cada entrypoint SCP e família AJAX da baseline tem destino no mapa; fluxos
tradicionais, modais e ações em massa são identificados; dependências de domínio,
guardas e efeitos têm fontes; pendências de contrato/viabilidade ficam explícitas.
Validar navegação MkDocs, links e diff. Não confundir cobertura estática com todas
as combinações de configuração ou runtime de plugins externos não presentes.

## Leitura humana e alcance

| Documento | Conteúdo | IDs |
| --- | --- | --- |
| [Operação](SCP_OPERATIONAL_FLOWS.md) | painel/diretório, usuários/organizações, tarefas, tickets, conhecimento | O01–O45 |
| [Administração](SCP_ADMIN_FLOWS.md) | configurações, gerenciar, e-mails, agentes/equipes/papéis/departamentos, filas | A01–A56 |
| [Transversais](SCP_TRANSVERSAL_FLOWS.md) | entrada/identidade, editor, busca, colaboradores, ações, ajuda, exportação/manutenção | T01–T52 |

São **153 registros de fluxo com variantes explícitas**, não 153 endpoints nem
153 testes executados. **Fato observado:** o universo físico é 46 PHP diretos
de `scp/` mais `apps/dispatcher.php`; dois são includes de bootstrap. As 229
folhas AJAX estão discriminadas no [catálogo literal](AJAX_ROUTE_CATALOG.md).
O mapa liga essas superfícies às cadeias tradicionais, templates e domínio.

**Decisão aceita:** aplicar o [ADR 0004](../adr/0004-modulos-reforged-backend-osticket.md).
**Inferência:** este mapa permite discutir fronteiras por capacidade e
dependências; não demonstra viabilidade universal da extração sem core change.
Não aprova nomes de módulos, DTOs, rotas, framework PHP, bootstrap ou sessão.

## Ordem da interface que o mapa representa

Fonte: `include/class.nav.php`, StaffNav e AdminNav; filas e permissões
complementam a navegação nos templates. Ordem é do legado, não layout novo.

| Contexto | Menu em sequência | Submenus/abas e localização no mapa |
| --- | --- | --- |
| Agente | 1 Painel | Dashboard/diretório O45; perfil T11–T14 |
| Agente | 2 Usuários | Usuários O34–O39; organizações O40–O42; presença depende de permissão |
| Agente | 3 Tarefas | Abertas/minhas/atrasadas/concluídas, filtros e ações O25–O33 |
| Agente | 4 Tickets | Filas hierárquicas, minhas/encerradas/buscas salvas, pesquisa/avançada, novo ticket O01–O24 e T23–T27 |
| Agente | 5 Conhecimento | FAQ/categorias/respostas prontas O43–O44; config/permissões condicionam itens |
| Agente | 6 Apps, se registrado | T04; plugins ausentes não têm menus inventados |
| Admin | 1 Painel | Logs T37/T38; auditoria condicional e sistema T39 |
| Admin | 2 Configurações | Pages/system/tickets/tasks/agents/users/kb A02–A10; subtabs e unidade de POST identificadas |
| Admin | 3 Gerenciar | Tópicos A36–A37; filtros A22–A23; SLAs A38–A39; agendas A40–A41; API A18–A19; páginas A47–A48; forms A42–A43/A56; listas A44–A46; plugins A53–A55 |
| Admin | 4 E-mails | Contas A12–A16; global A11; banlist A20–A21; templates A49–A50; diagnóstico A17 |
| Admin | 5 Agentes | Agentes A27–A30; equipes A31–A32; papéis A33–A34; departamentos A24–A26; quick-add A35 |
| Admin | 6 Apps, se registrado | T04 |

Links globais de alternância de painel, perfil e sair são parte de T01/T02/T10/T11.
Busca, ordenação, paginação, seleção individual/todos/nenhum/alternar, dropdowns,
modais, abas, editor e ajuda não desaparecem do escopo por não serem módulos.
Se apenas manipulam a seleção/exibição, não recebem comando de domínio fictício;
quando disparam operação, apontam aos IDs correspondentes. O comportamento
visual anterior permanece na [matriz de interface](INTERFACE_COVERAGE_MATRIX.md);
esta releitura não é nova passagem de navegador nem certificação de cada evento DOM.

## Reconciliação dos arquivos SCP

Cada caminho abaixo é relativo à raiz; nenhum arquivo PHP do escopo fica sem
destino. Cobertura de arquivo não implica execução de todos seus ramos.

| Arquivo | Fluxos |
| --- | --- |
| `scp/admin.inc.php` | T02 |
| `scp/admin.php` | A01 |
| `scp/ajax.php` | T03; matriz de famílias abaixo |
| `scp/apikeys.php` | A18–A19 |
| `scp/audits.php` | T39 |
| `scp/autocron.php` | T36 |
| `scp/banlist.php` | A20–A21 |
| `scp/canned.php` | O44 |
| `scp/categories.php` | O44 |
| `scp/dashboard.php` | O45 |
| `scp/departments.php` | A24–A26 |
| `scp/directory.php` | O45 |
| `scp/emails.php` | A12–A16 |
| `scp/emailsettings.php` | A11 |
| `scp/emailtest.php` | A17 |
| `scp/export.php` | T35 |
| `scp/faq.php` | O43 |
| `scp/filters.php` | A22–A23 |
| `scp/forms.php` | A42–A43 |
| `scp/helptopics.php` | A36–A37 |
| `scp/index.php` | O01 |
| `scp/kb.php` | O43 |
| `scp/lists.php` | A44–A45 |
| `scp/login.php` | T05–T07 |
| `scp/logo.php` | T41 |
| `scp/logout.php` | T10 |
| `scp/logs.php` | T37–T38 |
| `scp/orgs.php` | O40–O42 |
| `scp/pages.php` | A47 |
| `scp/plugins.php` | A53–A54 |
| `scp/profile.php` | T11 |
| `scp/pwreset.php` | T08–T09 |
| `scp/queues.php` | A51 |
| `scp/roles.php` | A33–A34 |
| `scp/schedules.php` | A40 |
| `scp/settings.php` | A02–A10 |
| `scp/slas.php` | A38–A39 |
| `scp/staff.inc.php` | T01 |
| `scp/staff.php` | A27–A30 |
| `scp/system.php` | T39 |
| `scp/tasks.php` | O25–O33 |
| `scp/teams.php` | A31–A32 |
| `scp/templates.php` | A49–A50 |
| `scp/tickets.php` | O01–O24 |
| `scp/upgrade.php` | T40 |
| `scp/users.php` | O34–O39 |
| `scp/apps/dispatcher.php` | T04 |

## Reconciliação das famílias AJAX

Linhas referem-se às declarações de `scp/ajax.php`, não linhas deste documento.
Cada folha da matriz literal cai em exatamente um intervalo abaixo; agrupadores
e comentários dentro de intervalos não são folhas adicionais. IDs compartilham
serviços, não duplicam a contagem. O catálogo conserva verbo/regex/alvo/guarda.

| Família | Linhas | Folhas | Destino no mapa |
| --- | --- | ---: | --- |
| KB | 39–41 | 3 | O43–O44 |
| Conteúdo | 44–50 | 7 | T28/T37, A48 |
| Config | 53–55 | 3 | T22 |
| Form | 58–65 | 8 | T19–T20, A43/A56 |
| Filtro | 68–68 | 1 | A22 |
| Agenda | 71–76 | 6 | A41 |
| Listas | 79–89 | 11 | A46; alvo ausente abaixo |
| Plugins | 92–95 | 4 | A55; alvo ausente abaixo |
| Relatórios | 99–102 | 4 | O45; quatro alvos ausentes |
| Usuários | 105–132 | 28 | O34–O39 |
| Organizações | 135–156 | 22 | O40–O42; alvo ausente abaixo |
| Locks | 159–161 | 3 | T21 |
| Tickets | 164–217 | 53 | O03–O04/O09–O24/O27/O44, T23–T27/T47–T52; alvos ausentes abaixo |
| Tarefas | 221–240 | 20 | O25–O33, T47–T52 |
| Threads | 243–250 | 8 | T42–T46 |
| Drafts | 253–259 | 7 | T15–T18 |
| Export | 262–262 | 1 | T34–T35 |
| Notas | 265–268 | 4 | T32 |
| Sequências | 271–273 | 3 | T33 |
| Upgrader | 275–275 | 1 | T40 |
| Ajuda | 277–278 | 2 | T31 |
| i18n | 281–285 | 5 | T29–T30 |
| Admin | 289–296 | 7 | A33/A35/A52 |
| Staff | 299–306 | 8 | A29–A30, T12–T14 |
| Filas | 309–315 | 7 | T25–T27/A52; alvo ausente abaixo |
| E-mail | 318–320 | 3 | A15 |

## Obstáculos que o desenho dos módulos precisa resolver

| Classe de dependência | Evidência | Obrigação para a próxima discussão |
| --- | --- | --- |
| Mecanismo de domínio disponível, coordenação distribuída | Ticket::postReply mais validação/colaboração/limpeza no controller (O06), release com evento/nota (O15) | Rastrear toda a cadeia; chamada isolada não garante equivalência |
| Regra/persistência no controlador ou template | Lotes admin A19/A25/A37, forms A42, logs T38, consultas O34/O45 | Identificar reutilização possível sem copiar regra, SQL novo ou alterar core; se inviável, pedir decisão, não improvisar |
| Transporte misturado à apresentação | HTML/PJAX, JSON com HTML, redirect, status embutido, binário T03/T05/T19/T35 | Definir futuramente contratos estruturados e adaptação sem encapsular HTML legado |
| Estado compartilhado | Sessão, forms, drafts, locks, filas/export, MFA T01/T06/T15–T27/T34 | Projetar coexistência/sessão/concorrência preservando identidade/permissões; mecanismo ainda não escolhido |
| Efeitos parciais/externos | O05/O19/O24, A04/A13/A14/A42/A54, T36/T51 | Erros por etapa/item, repetição, correlação e evidência de efeito; nenhuma garantia geral de atomicidade/idempotência |
| Extensões e manutenção | T04/T07/T39/T40, A53–A55 | Backend/plugin instalado/configurado determina capacidades; upgrade não é fluxo comum de atendimento |

**Fato observado:** 13 folhas têm alvo ausente: lista massManageListItems (86),
plugin actions (95), quatro relatórios (99–102), getOrg (137), changeUser (165),
deleteSearch (211), deleteQueues/disableQueues/undisableQueues (215–217),
addColumn (311). Não são 13 APIs reutilizáveis prontas. O relatório tradicional,
a troca de proprietário tradicional e o detalhe da organização têm caminhos
existentes diferentes. Não reproduzimos essas falhas nesta unidade.

**Fato observado:** claim tradicional O12 diverge da assinatura atual; mensagens
de contagem/exclusão A26 e proteção de agenda A40 não provam o efeito alegado.
Correções de precisão do catálogo AJAX distinguem JSON com HTML, texto de data,
browse de imagens e guarda de preview de fila. Não alteram código ou aceitam
riscos legados.

## Sequência proposta após o mapa

1. Discutir agrupamentos e dependências de módulos com o responsável, usando os
   IDs como rastreabilidade; não reabrir a fronteira já aceita no ADR 0004.
2. Para cada agrupamento, comparar bootstrap/chamadas/coordenação/contratos,
   retorno, erros, permissões, sessão e efeitos; explicitar bloqueios de reutilização.
3. Formalizar escolhas e revisão técnica; só então autorizar implementação e
   escolher primeiro recorte. Nenhum recorte antigo foi restaurado automaticamente.
4. Em seguida planejar frontend: navegação/UX/layout/estados/formulários/ajuda,
   vinculado às capacidades e transições; Angular/PrimeNG aceitos, versões e
   organização ainda a definir. Portal do usuário continua separado.

## Validação desta unidade

Revisão independente aprovada após quatro correções de precisão: permissão de
addcc de tarefa (O28), persistência de publicação FAQ no controller (O43), guarda
própria de reenvio (T51) e enumeração de lotes de fila (A51). Sem achado documental
aberto no escopo revisado; parecer em `GATE_D_REVIEW.md`. O integrador conferiu
essas cadeias no código, as correções do catálogo e a cobertura mecânica.

Auditoria: 47/47 arquivos, 153 IDs sem repetição, 26 famílias/229 folhas com uma
única atribuição de intervalo para cada folha; 64/64 documentos no MkDocs sem
repetição. Build estrito com `.local/docs-venv/Scripts/python.exe -m mkdocs build --strict`
e `git diff --check` aprovados. As contagens finais de links estão em PROGRESS.
Nenhum teste de produto, banco, credencial ou frontend executado.
Fechamento deste mapa não encerra o Portão D nem certifica todas as combinações
de configuração, plugins externos, acessibilidade, concorrência ou desempenho.
