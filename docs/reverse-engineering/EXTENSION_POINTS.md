# Pontos iniciais de extensão

## Sinais

`include/class.signal.php` declara `Signal` em `:27`, `connect()` em `:56` e
`send()` em `:89`.

**Fato observado:** o mecanismo permite conectar callbacks por nome de sinal e
emitir eventos com objeto e dados associados.

O [catálogo inicial de sinais](HOOKS.md) confirma 41 nomes literais emitidos,
mas momento, payload, mutabilidade e segurança ainda precisam ser relacionados
individualmente.

## Plugins

| Símbolo | Localização | Papel inicial observado |
| --- | --- | --- |
| `PluginConfig` | `include/class.plugin.php:10` | configuração do plugin |
| `PluginManager` | `include/class.plugin.php:175` | descoberta, verificação e instalação |
| `Plugin` | `include/class.plugin.php:543` | modelo e ciclo do pacote instalado |
| `PluginInstance` | `include/class.plugin.php:1006` | instância configurável de plugin |

O ciclo, os estados, a persistência e os riscos estão detalhados na
[arquitetura de plugins](PLUGIN_ARCHITECTURE.md).

## Extensão de rotas

Antes de resolver a API HTTP, `api/http.php` emite o sinal `api` com o
dispatcher. O AJAX do cliente emite `ajax.client`. Esses pontos sugerem registro
adicional de rotas por listeners.

**Fato observado:** plugins carregados podem ampliar essas superfícies sem
editar os entrypoints. A segurança continua dependente da guarda da superfície,
do `Controller::access()` e das verificações do método registrado.

## Critério para classificar um ponto como seguro

Um ponto só será marcado como fronteira segura de customização após confirmar:

1. ciclo de carregamento e ordem;
2. contrato e estabilidade do payload;
3. autenticação, autorização e CSRF aplicáveis;
4. acesso a persistência e transações;
5. comportamento em upgrade;
6. possibilidade de teste sem alterar o core.
