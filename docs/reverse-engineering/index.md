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
2. [Arquitetura canônica](ARCHITECTURE.md)
3. [Proveniência e ownership](PROVENANCE_MAP.md)
4. [Inventário da baseline](INVENTORY.md)
5. [Matriz de cobertura do código-fonte](SOURCE_COVERAGE_MATRIX.md)
6. [Mapa inicial de componentes](COMPONENT_MAP.md)
7. [Catálogo inicial de entrypoints](ENTRYPOINT_CATALOG.md)
8. [Superfícies HTTP — leitura estática](HTTP_SURFACES.md)
9. [APIs, AJAX e integrações](API_ANALYSIS.md)
10. [Catálogo de rotas AJAX](AJAX_ROUTE_CATALOG.md)
11. [Modelo de segurança](SECURITY_MODEL.md)
12. [Frontend PHP existente](FRONTEND_ANALYSIS.md)
13. [Ciclo inicial de requisição](REQUEST_LIFECYCLE.md)
14. [Ciclo do ticket](TICKET_LIFECYCLE.md)
15. [Falhas, atomicidade e órfãos](INTEGRITY_FAILURES.md)
16. [Subsistemas transversais](TRANSVERSAL_SUBSYSTEMS.md)
17. [Autenticação e autorização](AUTHENTICATION.md)
18. [Banco e persistência](DATABASE.md)
19. [Mapa inicial do modelo de dados](DATA_MODEL_MAP.md)
20. [Arquitetura de persistência](DATABASE_ARCHITECTURE.md)
21. [Catálogo dos metadados ORM](ORM_CATALOG.md)
22. [Instalação e evolução](INSTALLATION_UPGRADE.md)
23. [Testes da baseline](TESTING_BASELINE.md)
24. [Catálogo dos módulos CLI](CLI_CATALOG.md)
25. [Pontos iniciais de extensão](EXTENSION_POINTS.md)
26. [Arquitetura de plugins](PLUGIN_ARCHITECTURE.md)
27. [Catálogo de registries e factories](REGISTRY_CATALOG.md)
28. [Visão geral dos sinais](HOOKS.md)
29. [Catálogo individual dos sinais](SIGNAL_CATALOG.md)
30. [Matriz de customização](CUSTOMIZATION_MATRIX.md)
31. [Matriz de evidências](EVIDENCE_MATRIX.md)
32. [Revisão cruzada do Portão C](REVIEW_REPORT.md)
33. [Análise comportamental](BEHAVIORAL_ANALYSIS.md)
34. [Evidências comportamentais](evidence/README.md)

O Portão B está concluído. Estes documentos agora sustentam o aprofundamento
do Portão C e serão atualizados a cada unidade analisada.
