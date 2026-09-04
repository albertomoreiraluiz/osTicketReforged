# Instalação e evolução do schema

## Limite da análise

Este fluxo foi interpretado sem instalar o produto. Caminhos de sucesso, falha,
permissões de arquivo e rollback ainda dependem de validação em homologação.

## Instalação

`setup/install.php` conduz os estados de pré-requisito, configuração e
instalação. O arquivo alvo é `include/ost-config.php`
(`setup/install.php:21-22,39-113`).

`Installer::install()` executa, na ordem observada:

1. valida help desk, administrador, prefixo e banco (`class.installer.php:46-90`);
2. conecta, verifica versão, seleciona ou tenta criar o banco e recusa prefixo
   utilizado (`:92-116`);
3. define tabelas, carrega o core e aplica política de senha (`:118-136`);
4. abre a configuração e valida assinaturas MD5 dos schemas (`:139-169`);
5. carrega schemas/dados de i18n e emite `system.install` (`:148-178`);
6. cria administrador, permissões, acessos e e-mails padrão (`:180-250`);
7. grava configurações, assinaturas e empresa (`:252-286`);
8. reescreve `ost-config.php` por último, com credenciais e instalação marcada
   como concluída (`:289-304`);
9. recarrega a configuração e cria um ticket inicial (`:306-315`).

**Fato observado:** o instalador grava credenciais no arquivo nativo
`ost-config.php`; o `.env` de ferramentas não participa do fluxo.

**Risco a validar:** várias escritas precedem a marca final no arquivo. Não foi
localizada uma transação única envolvendo schema, defaults, administrador e
configuração.

## Upgrader

`DatabaseMigrater::getUpgradeStreams()` descobre streams e compara a assinatura
persistida com o tip (`include/class.migrater.php:76-100`). `Upgrader` coordena
streams e `StreamUpgrader` aplica uma cadeia individual
(`include/class.upgrader.php:126-159`).

O processamento é fatiado:

- tarefas pendentes são retomadas por estado em sessão e limite de tempo
  (`include/class.upgrader.php:331-359`);
- no máximo cinco patches SQL são aplicados por chamada;
- a cada patch, caches de metadados são limpos, assinatura/estado avançam e uma
  tarefa complementar pode interromper o lote (`class.upgrader.php:362-410`).

Os 99 SQL históricos formam uma cadeia ordenada por hashes. Não devem ser
aplicados isoladamente nem tratados como representação do schema final.

## Pontos pendentes

- mapear controladores e autorização da interface de upgrade;
- relacionar rollback/cleanup de cada patch e tarefa;
- confirmar atomicidade de DDL/DML no MariaDB após a instalação;
- validar recuperação de falha apenas em cópia descartável.
