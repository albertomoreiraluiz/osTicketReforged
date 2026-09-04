# Ambiente de homologação

## Status

Infraestrutura **Ativa**, instalação funcional **Concluída** e contrato local
**Aceito**. O osTicket responde sob o Apache, o banco foi criado e a fase de
análise comportamental está em execução. A persistência definitiva de conexões
permanece **A definir (TBD)**.

## Decisão

O `.env` da raiz é exclusivamente uma entrada local para ferramentas de diagnóstico, testes e deploy. O osTicket não o carrega e continua usando `include/ost-config.php`, criado por seu instalador.

O ambiente primário de homologação será local, baseado em XAMPP e Apache. O
aaPanel com Nginx e Apache deixou de ser o alvo desta etapa porque dificultaria o
acesso direto ao runtime PHP e aos logs durante a engenharia reversa. Isso não
define a plataforma de produção nem impede uma futura homologação remota.

A versão-alvo do runtime é PHP 8.2, correspondente à versão mais recente
disponível no XAMPP selecionado. A baseline `v1.18.4` declara PHP 8.2 como versão
mínima em `include/class.setup.php`; portanto, a limitação conhecida do pacote é
compatível com o requisito do produto. Essa decisão não elimina a validação das
extensões carregadas nem os testes funcionais no runtime instalado.

- `.env`: arquivo local ignorado, pode receber os valores reais de homologação;
- `.env.example`: contrato versionado, sempre sem segredos;
- `include/ost-config.php`: configuração nativa do osTicket, ignorada pelo Git;
- `.local/`: artefatos locais, logs coletados e resultados sanitizados, ignorados.

As variáveis `OSTR_ADMIN_ACCOUNT` e `OSTR_ADMIN_PASSWORD` guardam exclusivamente
a conta administrativa criada pelo instalador para autenticação das ferramentas
na homologação. Seus valores não podem aparecer em documentação, saída de
comando, artefato, Issue, commit ou Pull Request. As variáveis `OSTR_TEST_*`
continuam reservadas a identidades fictícias por papel nos cenários funcionais.

## Ordem segura de preenchimento

1. preencha identificação, URL e PHP sem credenciais;
2. informe caminhos protegidos dos logs;
3. configure uma conta exclusiva do MariaDB de homologação;
4. crie contas funcionais contendo somente dados fictícios;
5. mantenha todas as barreiras de escrita como `false`;
6. valide cada acesso separadamente sem imprimir o ambiente completo.

## Fontes das informações

| Grupo | Fonte esperada |
| --- | --- |
| URL e document root | virtual host, painel da hospedagem ou administrador web |
| PHP, SAPI e `php.ini` | painel, `php -v`, `php --ini` ou diagnóstico temporário controlado |
| Logs PHP/web | `error_log`, pool PHP-FPM, virtual host ou painel |
| Banco MariaDB | administrador do banco ou painel de hospedagem |
| `TABLE_PREFIX` | `include/ost-config.php` instalado, sem copiar suas credenciais |
| Contas funcionais | painel da instalação de homologação |
| E-mail de teste | administrador de e-mail ou capturador aprovado |

## Evidência histórica do PHP no aaPanel

Em 2026-09-03 foi fornecida uma cópia textual do `php.ini` associado ao ambiente
aaPanel com Nginx e Apache. O arquivo é evidência operacional externa e não deve
ser incorporado ao repositório. A plataforma foi posteriormente descartada como
homologação primária desta etapa, mas a análise estática foi preservada como
registro da decisão:

| Item | Valor observado | Avaliação inicial |
| --- | --- | --- |
| Árvore do PHP | `/www/server/php/84/` | indica PHP 8.4; confirmar versão completa no runtime |
| `error_reporting` | `E_ALL & ~E_NOTICE` | divergente do contrato de diagnóstico `E_ALL` |
| `display_errors` | `On` | inadequado para homologação acessível pela internet |
| `display_startup_errors` | `Off` | adequado |
| `log_errors` | `On` | adequado, mas falta confirmar o destino efetivo |
| `error_log` | sem diretiva ativa encontrada | confirmar no pool PHP-FPM, virtual host ou aaPanel |
| `date.timezone` | `PRC` | revisar para o fuso deliberado do ambiente |
| limites | `128M`, `50M` para POST e upload | validar com os cenários de anexos do osTicket |
| cache | OPcache e APCu declarados | confirmar carregamento no SAPI web |
| sessões | cookies exclusivos; demais atributos sem valor explícito | revisar após conhecer HTTPS e topologia final |
| funções desabilitadas | inclui execução de processos e funções POSIX | pode limitar ferramentas; o produto deve ser testado sem reduzir a proteção por padrão |

O arquivo isolado não confirma qual `php.ini` é carregado pelo PHP web, quais
arquivos adicionais são lidos, o SAPI efetivo nem todas as extensões disponíveis.
Também não comprova que Nginx e Apache percorrem o mesmo runtime. Esses dados só
podem preencher o `.env` depois de uma verificação controlada no servidor.

### Verificações controladas pendentes

As verificações abaixo passam a ser executadas na instalação local do XAMPP:

1. executar `php.exe -v` e `php.exe --ini` para o contexto CLI;
2. consultar temporariamente `PHP_VERSION`, `php_sapi_name()` e
   `php_ini_loaded_file()` no contexto web, sem publicar uma página completa de
   `phpinfo()`;
3. executar `php.exe --ri mysqli` e `php.exe -m`, sanitizando a saída antes de
   arquivá-la;
4. localizar o log efetivo do PHP e o `error.log` do Apache no XAMPP;
5. verificar no runtime `mysqli`, `gd`, `iconv`, `imap`, `ctype`, `xml`, `dom`,
   `json`, `mbstring`, `phar`, `intl`, `apcu` e `Zend OPcache`, conforme a tela de
   pré-requisitos desta baseline;
6. remover imediatamente qualquer endpoint temporário de diagnóstico.

Build exato do XAMPP/PHP 8.2, diretório de instalação, portas, `DocumentRoot`,
módulos e banco incluído permanecem a confirmar na instalação real. Nenhum
caminho padrão deve ser assumido pelas ferramentas.

## Inventário confirmado do XAMPP local

Inventário executado em 2026-09-03 diretamente na instalação informada:

| Item | Valor confirmado |
| --- | --- |
| Diretório do XAMPP | `C:\xampp` |
| PHP CLI | `C:\xampp\php\php.exe` — PHP 8.2.12 ZTS x64 |
| `php.ini` carregado pelo CLI | `C:\xampp\php\php.ini` |
| Log configurado do PHP | `C:\xampp\php\logs\php_error_log` |
| Apache | `C:\xampp\apache\bin\httpd.exe` — 2.4.58 Win64 |
| Configuração principal | `C:\xampp\apache\conf\httpd.conf` |
| `DocumentRoot` | `C:\xampp\htdocs` |
| Porta HTTP configurada | `80` |
| Log de erros do Apache | `C:\xampp\apache\logs\error.log` |
| Limites PHP | `memory_limit=512M`, POST e upload `40M` |

O `httpd.exe -t` retornou `Syntax OK`. No instante do inventário, não havia
processo escutando nas portas 80, 443, 3306 ou 4200; isso registra apenas que os
serviços não estavam ativos naquele momento.

O PHP carregava inicialmente `mysqli`, `pdo_mysql`, `curl`, `mbstring`, `xml`,
`dom`, `ctype`, `iconv`, `json` e `Phar`. Em 2026-09-03, após validação em uma
cópia de trabalho, foram habilitados `gd`, `imap`, `intl`, `soap`, `sockets`,
`sodium`, `xsl`, `zip` e Zend OPcache. APCu não foi localizado na distribuição e
não foi instalado.

Na mesma alteração, a homologação passou a usar `error_reporting=E_ALL`,
`display_errors=Off`, `display_startup_errors=Off`, `log_errors=On`,
`date.timezone=America/Sao_Paulo`, sessão em modo estrito, cookie de sessão
`HttpOnly` e `SameSite=Lax`. O OPcache valida timestamps em todas as requisições
(`revalidate_freq=0`) para evitar código obsoleto durante o desenvolvimento.

O arquivo anterior pode ser restaurado por
`C:\xampp\php\php.ini.backup-20260903-214627`. Após a gravação, o PHP CLI
carregou todos os módulos selecionados e `httpd.exe -t` retornou `Syntax OK`.
Como `C:\xampp\php\logs` não existia no pacote, o diretório foi criado e uma
mensagem técnica inofensiva confirmou a escrita em `php_error_log`.

SSH, SFTP e FTPS estão fora do escopo e não fazem parte do contrato `.env` da
homologação atual. `OSTR_DEPLOY_PROTOCOL` fica definido como `local`. Os campos
de banco permanecem reservados para preenchimento pelo responsável.

### Toolchain do frontend

O XAMPP não executa Angular. Ele será responsável pelo osTicket/PHP e poderá
servir artefatos estáticos quando isso for deliberado; desenvolvimento e build
do frontend usarão uma toolchain local independente. Foram encontrados Node.js
24.19.0 e pnpm 11.17.0. Não há Angular CLI global, o que é intencional: quando o
frontend Angular existir, sua versão deverá ser fixada no projeto e executada por
pnpm. Conforme o ADR 0001, a aplicação e toda a sua toolchain ficarão em
`frontend/`. A compatibilidade da versão do Node.js será decidida junto da versão
do Angular, que ainda não foi escolhida.

## Segurança

- Não usar produção, dados pessoais reais ou credenciais compartilhadas.
- Não registrar o conteúdo do `.env` em logs, Issues, PRs ou documentação.
- Não armazenar chave privada, seed TOTP ou código de recuperação no `.env`.
- Preferir caminho de chave privada e fingerprint do host.
- `display_errors` permanece desligado na homologação acessível externamente; erros devem ir para logs protegidos.
- MariaDB 10.11 é a homologação escolhida, não prova compatibilidade com todos os MySQL suportados pelo upstream.

## Barreiras de escrita

As variáveis abaixo começam obrigatoriamente seguras:

```dotenv
OSTR_DEPLOY_DRY_RUN=true
OSTR_ALLOW_DATABASE_WRITE=false
OSTR_DEPLOY_CONFIRMATION=
```

Ferramentas futuras devem recusar operações mutáveis enquanto as permissões continuarem falsas. Alterar uma variável não substitui a autorização exigida pela governança.

## Validação pendente

PHP CLI, módulos, configuração do Apache e gravação do log PHP já foram
validados.

Em 2026-09-04, a distribuição limpa da baseline `v1.18.4` foi preparada pelo
módulo nativo `manage.php deploy --setup`. O staging foi extraído diretamente
da tag Git, evitando copiar `.env`, `.codex`, documentação do Reforged, MkDocs
ou metadados Git para o webroot. Foram verificados 2.250 arquivos, presença do
instalador/schema e igualdade SHA-256 de `include/class.ticket.php` com a
baseline. `include/ost-config.php` foi criado, ainda sem credenciais, a partir
de `include/ost-sampleconfig.php` e está gravável pelo ambiente local.

O responsável moveu a distribuição para a raiz `C:\xampp\htdocs`, compatível
com a URL de homologação definida localmente no `.env`, e reiniciou o Apache
para carregar os módulos PHP habilitados. A URL sensível ao ambiente não é
duplicada na documentação versionada.

Na primeira tentativa, o instalador recusou o schema: a assinatura esperada
`5fb92bef17f3b603659e024c01cc7a59` corresponde ao conteúdo LF, enquanto a
cópia Windows havia convertido `install-mysql.sql` para CRLF, gerando
`76eb6d920994eae1ddfb8e2cae868d0b`. O schema da cópia de homologação foi
normalizado mecanicamente para LF; o MD5 passou a coincidir exatamente com
`include/upgrader/streams/core.sig`. Nenhum SQL foi executado nesta correção.

Em 2026-09-04, o responsável concluiu o instalador e confirmou a criação do
banco. A página pública respondeu com sucesso e a autenticação administrativa
foi validada com formulário protegido por CSRF, redirecionando ao Painel de
Controle da Equipe. A validação registrou somente estado HTTP e título da tela;
credenciais, cookie de sessão e token CSRF não foram exibidos nem persistidos.

Permanece como endurecimento operacional verificar a permissão de
`include/ost-config.php` e desabilitar ou remover `setup/` da cópia de
homologação. A remoção não será feita implicitamente por ser uma ação destrutiva
sobre a implantação.
