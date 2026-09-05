# Engenharia reversa

Esta área contém a leitura técnica da baseline `v1.18.4`. Os documentos estão
agrupados pela pergunta que ajudam a responder; os caminhos originais foram
preservados para manter links, histórico e contexto dos agentes.

## Inventário e rastreabilidade

Para confirmar o universo analisado, a origem dos arquivos e a qualidade das
evidências:

- [Inventário da baseline](INVENTORY.md) — dimensão e superfícies executáveis;
- [Cobertura do código-fonte](SOURCE_COVERAGE_MATRIX.md) — classificação dos
  2.266 caminhos;
- [Proveniência e ownership](PROVENANCE_MAP.md) — core e dependências
  incorporadas;
- [Ondas de inventário](WAVE_MANIFEST.md) — sequência e critérios de fechamento;
- [Matriz de evidências](EVIDENCE_MATRIX.md) — estado verificável por frente;
- [Revisões independentes](REVIEW_REPORT.md) — achados e pareceres de QA.
- [Revisões do Portão D](GATE_D_REVIEW.md) — avaliação das propostas arquiteturais.

## Arquitetura e execução

Para seguir uma requisição desde o bootstrap até os controladores e
subsistemas:

- [Arquitetura canônica](ARCHITECTURE.md);
- [Módulos por capacidade](MODULE_CAPABILITY_PROPOSAL.md) — divisão lógica aceita,
  detalhes técnicos pendentes; [integração e contratos](INTEGRATION_CONTRACT_DESIGN.md)
  distingue origem/estilo aceitos e contratos pendentes;
- [Organização física e carregamento](REFORGED_PHYSICAL_DESIGN.md) — proposta de
  isolamento, publicação e critérios de integração;
- [Entradas, sessão e publicação](REFORGED_ENTRY_SESSION_DESIGN.md) — tipos de
  entrada, guardas, continuidade e verificações futuras;
- [Portão D: reinício e dossiê histórico](ARCHITECTURE_DECISION_RECORD.md);
- [Estratégia modular aceita — ADR 0004](../adr/0004-modulos-reforged-backend-osticket.md);
- [Portão D: estratégia anterior descartada](MIGRATION_STRATEGY.md);
- [Mapa de componentes](COMPONENT_MAP.md);
- [Catálogo de entrypoints](ENTRYPOINT_CATALOG.md);
- [Ciclo de requisição](REQUEST_LIFECYCLE.md);
- [Superfícies HTTP](HTTP_SURFACES.md);
- [Subsistemas transversais](TRANSVERSAL_SUBSYSTEMS.md).

## Domínio e persistência

Para compreender tickets, relações, banco, ORM, instalação e integridade:

- [Ciclo do ticket](TICKET_LIFECYCLE.md);
- [Modelo de dados](DATA_MODEL_MAP.md);
- [Banco e persistência](DATABASE.md);
- [Arquitetura de persistência](DATABASE_ARCHITECTURE.md);
- [Catálogo ORM](ORM_CATALOG.md);
- [Instalação e upgrades](INSTALLATION_UPGRADE.md);
- [Falhas, atomicidade e órfãos](INTEGRITY_FAILURES.md).

## Interfaces e comportamento

Para confrontar a composição do frontend legado com o que foi observado no
navegador:

- [Frontend PHP existente](FRONTEND_ANALYSIS.md);
- [Análise comportamental](BEHAVIORAL_ANALYSIS.md);
- [Cobertura integral da interface](INTERFACE_COVERAGE_MATRIX.md);
- [Protocolo de observação](INTERFACE_OBSERVATION_PROTOCOL.md);
- [Modelo operacional do Wiki](OSTICKET_OPERATIONAL_MODEL.md);
- [Testes da baseline](TESTING_BASELINE.md);
- [Evidências comportamentais](evidence/README.md).

## APIs e automação

Para compreender as superfícies de integração e execução não interativa:

- [Mapa completo de fluxos SCP](SCP_FLOW_MAP.md) — operação, administração e
  serviços transversais; cadeias e obstáculos para aplicar o ADR 0004;
- [APIs, AJAX e integrações](API_ANALYSIS.md);
- [Catálogo de rotas AJAX](AJAX_ROUTE_CATALOG.md);
- [Catálogo dos módulos CLI](CLI_CATALOG.md).

## Segurança e acesso

Para localizar os controles existentes antes de qualquer evolução funcional:

- [Modelo de segurança](SECURITY_MODEL.md);
- [Autenticação e autorização](AUTHENTICATION.md).

## Extensibilidade e customização

Para avaliar limites de extensão e alternativas de evolução sustentável:

- [Pontos de extensão](EXTENSION_POINTS.md);
- [Arquitetura de plugins](PLUGIN_ARCHITECTURE.md);
- [Registries e factories](REGISTRY_CATALOG.md);
- [Visão geral dos sinais](HOOKS.md) e [catálogo de sinais](SIGNAL_CATALOG.md);
- [Matriz de customização](CUSTOMIZATION_MATRIX.md).

!!! info "Portões concluídos"

    Os Portões B e C estão concluídos. Esta organização não altera conclusões,
    decisões ou precedência das fontes; apenas cria trilhas de leitura para o
    conteúdo já versionado.
