# Banco e persistência

## Síntese canônica

A baseline possui 67 tabelas no schema de instalação, 99 scripts SQL históricos
de upgrade, 72 declarações `static $meta` e nenhuma FK SQL executável. Relações,
cascatas e integridade são majoritariamente aplicadas por ORM e lógica PHP.

| Assunto | Evidência consolidada |
| --- | --- |
| tabelas, índices e evolução | [arquitetura de persistência](DATABASE_ARCHITECTURE.md) |
| 72 modelos, tabelas e PKs | [catálogo ORM](ORM_CATALOG.md) |
| entidades centrais | [mapa de dados](DATA_MODEL_MAP.md) |
| atomicidade e órfãos | [falhas e integridade](INTEGRITY_FAILURES.md) |
| instalação e patches | [instalação e upgrades](INSTALLATION_UPGRADE.md) |

**Fato observado:** autocommit é o padrão e não há unidade transacional comum
nos agregados centrais analisados. **Inferência:** engines, concorrência e
estados órfãos previstos devem ser medidos após instalação descartável; isso
não altera o inventário estático.
