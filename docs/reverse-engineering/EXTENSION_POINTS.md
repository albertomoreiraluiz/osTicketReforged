# Pontos iniciais de extensão

## Sinais

`include/class.signal.php` declara `Signal` em `:27`, `connect()` em `:56` e
`send()` em `:89`.

**Fato observado:** o mecanismo permite conectar callbacks por nome de sinal e
emitir eventos com objeto e dados associados.

**Pendente:** catálogo de todos os nomes emitidos, momento, payload, mutabilidade
e garantias de ordem. A simples presença de `Signal::send()` não prova que um
fluxo possa ser customizado integralmente sem alteração do core.

## Plugins

| Símbolo | Localização | Papel inicial observado |
| --- | --- | --- |
| `PluginConfig` | `include/class.plugin.php:10` | configuração do plugin |
| `PluginManager` | `include/class.plugin.php:175` | descoberta, verificação e instalação |
| `Plugin` | `include/class.plugin.php:543` | modelo e ciclo do pacote instalado |
| `PluginInstance` | `include/class.plugin.php:1006` | instância configurável de plugin |

O carregamento pode registrar namespaces de biblioteca
(`include/class.plugin.php:705`). Métodos de instalação, desinstalação,
habilitação e bootstrap foram localizados, mas seus efeitos e transações ainda
não foram analisados profundamente.

## Extensão de rotas

Antes de resolver a API HTTP, `api/http.php` emite o sinal `api` com o
dispatcher. O AJAX do cliente emite `ajax.client`. Esses pontos sugerem registro
adicional de rotas por listeners.

**Inferência:** plugins podem ampliar certas superfícies sem editar seus
entrypoints. Isso será confirmado rastreando quem conecta listeners, quando os
plugins são inicializados e quais controles de autenticação permanecem ativos.

## Critério para classificar um ponto como seguro

Um ponto só será marcado como fronteira segura de customização após confirmar:

1. ciclo de carregamento e ordem;
2. contrato e estabilidade do payload;
3. autenticação, autorização e CSRF aplicáveis;
4. acesso a persistência e transações;
5. comportamento em upgrade;
6. possibilidade de teste sem alterar o core.
