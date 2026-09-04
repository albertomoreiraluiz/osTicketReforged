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
12. [Falhas, atomicidade e órfãos](INTEGRITY_FAILURES.md)
13. [Subsistemas transversais](TRANSVERSAL_SUBSYSTEMS.md)
14. [Mapa inicial do modelo de dados](DATA_MODEL_MAP.md)
15. [Arquitetura de persistência](DATABASE_ARCHITECTURE.md)
16. [Catálogo dos metadados ORM](ORM_CATALOG.md)
17. [Instalação e evolução](INSTALLATION_UPGRADE.md)
18. [Testes da baseline](TESTING_BASELINE.md)
19. [Catálogo dos módulos CLI](CLI_CATALOG.md)
20. [Pontos iniciais de extensão](EXTENSION_POINTS.md)
21. [Arquitetura de plugins](PLUGIN_ARCHITECTURE.md)
22. [Catálogo de registries e factories](REGISTRY_CATALOG.md)
23. [Visão geral dos sinais](HOOKS.md)
24. [Catálogo individual dos sinais](SIGNAL_CATALOG.md)
25. [Matriz de customização](CUSTOMIZATION_MATRIX.md)
26. [Matriz de evidências](EVIDENCE_MATRIX.md)

O Portão B está concluído. Estes documentos agora sustentam o aprofundamento
do Portão C e serão atualizados a cada unidade analisada.
