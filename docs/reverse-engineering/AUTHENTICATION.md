# Autenticação e autorização

## Síntese canônica

Autenticação é fornecida por registries distintos para agentes e clientes;
sessão, CSRF, escopo do objeto e permissão da ação formam controles em camadas,
mas não um middleware uniforme. Cada operação precisa aplicar os controles
apropriados.

| Assunto | Evidência consolidada |
| --- | --- |
| login, sessão, cookie, CSRF, 2FA, reset e uploads | [modelo de segurança](SECURITY_MODEL.md) |
| backends, políticas, 2FA e permissões | [registries](REGISTRY_CATALOG.md) |
| sinais com senha/token e momento do login | [catálogo de sinais](SIGNAL_CATALOG.md) |
| API key, IP e flags | [API](API_ANALYSIS.md) |
| guardas das 229 folhas staff | [catálogo AJAX](AJAX_ROUTE_CATALOG.md) |

Riscos estáticos de maior confiança incluem payloads de sinais com credenciais,
`auth.login.succeeded` antes da conclusão potencial de 2FA e ausência de guarda
uniforme em rotas adicionadas. Testes de sessão, proxy, capability de arquivo e
upload permanecem deliberadamente dinâmicos.
