# Portão D — Estratégia de migração proposta

## Estado corrente

**Descartada como estratégia ativa.** A sequência e o primeiro recorte abaixo
pertencem à PR #28, preservada como histórico. A nova análise deve planejar
evolução com coexistência contínua, sem retirada do painel PHP; o portal do
usuário será planejado separadamente. Ver
[ADR 0003](../adr/0003-coexistencia-paineis-portal-separado.md).
Nenhuma sequência substituta foi escolhida. O caminho deste documento permanece
estável para preservar referências; o título histórico não autoriza migração
de saída do painel legado.

## Histórico da estratégia anterior — sem vigência

**Status: Proposto.** Derivada das alternativas do
[dossiê comparativo](ARCHITECTURE_DECISION_RECORD.md), baseline `v1.18.4`.
Todas as etapas abaixo são futuras e condicionais à aprovação arquitetural;
não constituem autorização de implementar ou alterar o schema.

## Princípios e ownership

O osTicket mantém inicialmente domínio, banco, coleta de correio e cron. Angular
possui apresentação e estado de interação; a API possui contratos e validação de
entrada; adaptadores traduzem para as regras legadas. Nenhum componente novo
escreve diretamente em tabelas de negócio para abreviar fluxos do domínio.

Os contextos público, cliente, agente e administrador continuam distintos. A
troca de tela deve preservar sessão autorizada, escopo e histórico de navegação;
o mecanismo de autenticação e coexistência de sessões requer ADR próprio.

## Primeiro recorte candidato

**Proposta:** consulta de tickets na visão do agente, inicialmente lista paginada
e resumo de ticket com campos mínimos. Não inclui conversação, anexos, exportação,
busca avançada ou mutações. Filtros suportados devem ser explicitamente delimitados
no planejamento do contrato; totalizadores respeitam o mesmo escopo da lista.

A escolha exercita uma fronteira representativa: identidade, departamento,
equipe, atribuição, encaminhamento, papel, paginação e projeção de dados. Apoia-se na matriz da
Onda 9, `INTERFACE_COVERAGE_MATRIX.md`, e no modelo de visibilidade de tickets.
Não é considerada simples apenas por ser leitura: consultar objeto e retornar
contagens pode revelar dados fora do escopo.

Alternativas consideradas: KB pública teria menos dependências de identidade,
mas demonstraria menos a viabilidade de atendimento; criar/responder tickets
ofereceria valor mais amplo, com risco de efeitos parciais e e-mails muito maior.
O recorte inicial prioriza validar a fronteira antes de adicionar efeitos.

O encaminhamento é condição estática de `Ticket::checkStaffPerm`; sua inclusão
nos critérios não representa nova validação visual nesta etapa.

## Sequência e critérios verificáveis

| Etapa futura | Entrega esperada | Condição para avançar |
| --- | --- | --- |
| 0 — Planejamento da prova | ADR de identidade/sessão, contrato de leitura, layout e empacotamento propostos | aprovação dos contratos e das responsabilidades |
| 1 — Prova de integração | plugin/rota, DTO mínimo, teste de ciclo habilitar/desabilitar/upgrade em cópia descartável | bootstrap e contratos validados sem core patch; falha controlada |
| 2 — Consulta de agente | lista e resumo com controles por principal e objeto | equivalência com os cenários de departamento/equipe da baseline; nenhuma exposição extra |
| 3 — Coexistência visual | navegação opt-in PHP/Angular, rotas e tratamento de expiração | mesmo escopo e recuperação de erro; verificações de usabilidade/acessibilidade |
| 4 — Primeiro comando limitado | caso de uso selecionado após análise de efeitos, concorrência e idempotência | escrita, falha parcial e reconciliação demonstradas com backup |
| 5 — Expansão por módulo | matriz de paridade, dependências e ownership por módulo | revisão e aprovação por recorte; possibilidade de desativação |

Não converter esta sequência em cronograma antes de concluir a prova e estimar
o trabalho com evidências. Versões de Angular/PrimeNG serão selecionadas na etapa
frontend conforme compatibilidade, sem alteração da decisão de usá-los.

A prova da extensão deve cobrir pacote incompatível, upgrade pendente, pacote
desativado e múltiplas instâncias. `osTicket::start` omite bootstrap de plugins
durante upgrade pendente; `PluginManager::bootstrap` pode executar `init()` de
pacote compatível desativado. Exigir registro de rotas no estágio funcional
apropriado, ausência de duplicidade, indisponibilidade controlada e recuperação
operacional; desativação não significa ausência de efeitos de `init()`.

O contrato da etapa 0 deve delimitar política de versão/compatibilidade,
ordenação, limites de página, página fora do intervalo e semântica de erros.
A prova deve verificar Content-Type, correlação e o alcance da garantia JSON
para autorização, rota inválida, indisponibilidade e falhas de bootstrap.
`Dispatcher` pode responder antes do controlador e `Http::response` usa HTML
por padrão; um adaptador de domínio não uniformiza automaticamente esses erros.

Na coexistência, demonstrar isolamento do ciclo PJAX e dos assets do legado,
URL direta, recarga, voltar/avançar e ida/volta entre interfaces. Definir antes
do teste se fila, ordenação e página serão preservadas ou reiniciadas e verificar
o resultado, sem conteúdo transitório enganoso ou evento tratado em duplicidade.
São critérios futuros, não escolha antecipada do mecanismo de navegação.

Incluir filtros nessa política de restauração; verificar teclado, foco,
identificação de controles, anúncios de carregamento/erro/conjunto vazio e
apresentação responsiva. Diferenciar falha do serviço, sessão expirada e ausência
de resultados conforme contrato aprovado. Revalidar dados após mudança de
identidade ou escopo. O resumo novo não promete paridade integral com o detalhe
PHP e suas ações excluídas.

## Validação e observabilidade previstas

Cada caso de uso exige contrato e fixtures verificáveis. Repetir pelos frontends
os cenários relevantes da baseline, incluindo agente sem escopo, acesso por equipe,
mudança de atribuição, sessão expirada e conjunto vazio. Leitura deve conferir
ausência de comandos de negócio incidentais; manutenção normal da sessão não
equivale a mutação funcional do ticket.

Logs devem ligar solicitação, operação e resultado por identificador de correlação,
sem corpo de ticket ou credenciais. Métricas propostas: erro por operação,
latência, volume e divergências de paridade. Limiares serão medidos na homologação,
não inventados neste plano. A aprovação documental não comprova performance.

## Upstream e compatibilidade

Manter manifesto dos símbolos usados por cada adaptador, versão da baseline e
testes de contrato associados. Uma atualização entra em branch própria; comparar
bootstrap, assinatura de schema, plugins, modelos, permissões e pontos de extensão.
Executar regressão do recorte e do frontend PHP antes de liberar a nova baseline.
Não declarar compatibilidade com outra release apenas por não haver conflito Git.

## Rollback e falhas

Para o recorte de leitura: retirar o tráfego da nova interface e desativar a
extensão conforme procedimento validado, preservando tickets e evidências.
Um erro fatal no bootstrap pode impedir desativação pelo painel; a prova precisa
documentar recuperação operacional do pacote antes de disponibilizá-lo.

Após comandos: suspender novas escritas, identificar operações com resultado
incerto e reconciliar efeitos antes de repetir. Voltar ao frontend PHP não desfaz
mensagem enviada ou dado persistido. Antes de qualquer comando novo, verificar
engines por tabela e capacidades transacionais efetivas, charset e collation.
Backup só comprova recuperação após restauração isolada e comparação de bytes
e amostras com acentuação, com coerência entre banco e armazenamento de anexos.
Isso não desfaz efeitos externos. Restauração integral não é estratégia
automática após uso concorrente.
Toda exclusão continua dependente de plano e garantia de rollback.

Se C for escolhida no futuro, cada agregado exigirá plano adicional para carga
inicial, corte de escritor, referências, reconciliação e retorno. Não fazer
dual-write informal nem interpretar `Signal::send()` como outbox transacional.

## Dependências abertas

Identidade, sessão/CSRF, contrato de leitura, layout do pacote, política de releases,
versões frontend e mitigação de achados pertinentes devem estar decididos antes
do recorte correspondente. Cada ausência permanece bloqueio desse recorte,
sem impedir a comparação arquitetural documental.
