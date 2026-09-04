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
5. [Mapa inicial de componentes](COMPONENT_MAP.md)
6. [Catálogo inicial de entrypoints](ENTRYPOINT_CATALOG.md)
7. [Superfícies HTTP — leitura estática](HTTP_SURFACES.md)
8. [APIs, AJAX e integrações](API_ANALYSIS.md)
9. [Catálogo de rotas AJAX](AJAX_ROUTE_CATALOG.md)
10. [Modelo de segurança](SECURITY_MODEL.md)
11. [Frontend PHP existente](FRONTEND_ANALYSIS.md)
12. [Ciclo inicial de requisição](REQUEST_LIFECYCLE.md)
13. [Ciclo do ticket](TICKET_LIFECYCLE.md)
14. [Falhas, atomicidade e órfãos](INTEGRITY_FAILURES.md)
15. [Subsistemas transversais](TRANSVERSAL_SUBSYSTEMS.md)
16. [Autenticação e autorização](AUTHENTICATION.md)
17. [Banco e persistência](DATABASE.md)
18. [Mapa inicial do modelo de dados](DATA_MODEL_MAP.md)
19. [Arquitetura de persistência](DATABASE_ARCHITECTURE.md)
20. [Catálogo dos metadados ORM](ORM_CATALOG.md)
21. [Instalação e evolução](INSTALLATION_UPGRADE.md)
22. [Testes da baseline](TESTING_BASELINE.md)
23. [Catálogo dos módulos CLI](CLI_CATALOG.md)
24. [Pontos iniciais de extensão](EXTENSION_POINTS.md)
25. [Arquitetura de plugins](PLUGIN_ARCHITECTURE.md)
26. [Catálogo de registries e factories](REGISTRY_CATALOG.md)
27. [Visão geral dos sinais](HOOKS.md)
28. [Catálogo individual dos sinais](SIGNAL_CATALOG.md)
29. [Matriz de customização](CUSTOMIZATION_MATRIX.md)
30. [Matriz de evidências](EVIDENCE_MATRIX.md)
31. [Revisão cruzada do Portão C](REVIEW_REPORT.md)
32. [Análise comportamental](BEHAVIORAL_ANALYSIS.md)
33. [Evidências comportamentais](evidence/README.md)

O Portão B está concluído. Estes documentos agora sustentam o aprofundamento
do Portão C e serão atualizados a cada unidade analisada.
