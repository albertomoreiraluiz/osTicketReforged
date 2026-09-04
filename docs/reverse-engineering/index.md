# Inventário de engenharia reversa

## Objetivo

Localizar os componentes arquiteturais, entrypoints e fluxos principais da
baseline antes de aprofundar comportamento, riscos ou opções de customização.

## Regras de leitura

- **Fato observado:** possui caminho, símbolo ou comando reproduzível.
- **Inferência:** interpretação ainda sujeita a confirmação.
- **Proposta:** alternativa ainda não aprovada.
- **Decisão aceita:** possui registro normativo aplicável.

## Documentos desta unidade

1. [Manifesto das ondas de inventário](WAVE_MANIFEST.md)
2. [Inventário da baseline](INVENTORY.md)
3. [Mapa inicial de componentes](COMPONENT_MAP.md)
4. [Catálogo inicial de entrypoints](ENTRYPOINT_CATALOG.md)
5. [Superfícies HTTP — leitura estática](HTTP_SURFACES.md)
6. [APIs, AJAX e integrações](API_ANALYSIS.md)
7. [Catálogo de rotas AJAX](AJAX_ROUTE_CATALOG.md)
8. [Modelo de segurança](SECURITY_MODEL.md)
9. [Frontend PHP existente](FRONTEND_ANALYSIS.md)
10. [Ciclo inicial de requisição](REQUEST_LIFECYCLE.md)
11. [Ciclo do ticket](TICKET_LIFECYCLE.md)
12. [Subsistemas transversais](TRANSVERSAL_SUBSYSTEMS.md)
13. [Mapa inicial do modelo de dados](DATA_MODEL_MAP.md)
14. [Arquitetura de persistência](DATABASE_ARCHITECTURE.md)
15. [Catálogo dos metadados ORM](ORM_CATALOG.md)
16. [Instalação e evolução](INSTALLATION_UPGRADE.md)
17. [Testes da baseline](TESTING_BASELINE.md)
18. [Catálogo dos módulos CLI](CLI_CATALOG.md)
19. [Pontos iniciais de extensão](EXTENSION_POINTS.md)
20. [Arquitetura de plugins](PLUGIN_ARCHITECTURE.md)
21. [Visão geral dos sinais](HOOKS.md)
22. [Catálogo individual dos sinais](SIGNAL_CATALOG.md)
23. [Matriz de evidências](EVIDENCE_MATRIX.md)

O Portão B está concluído. Estes documentos agora sustentam o aprofundamento
do Portão C e serão atualizados a cada unidade analisada.
