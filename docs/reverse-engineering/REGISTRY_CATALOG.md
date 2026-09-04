# Catálogo de registries e factories

## Escopo

Foram catalogados 22 mecanismos fora de `Signal`: 12 com intenção explícita de
extensão ou uso por plugins, cinco acessíveis porém acoplados ao core e cinco
internos. Registries vivem em memória; somente IDs, tipos e configurações
escolhidos podem persistir no domínio.

Disponibilidade no registry não implica autorização. O consumidor deve aplicar
identidade, escopo, permissão, CSRF e validação apropriados.

## Extensão suportada ou destinada a plugins

| Mecanismo | Registro e resolução | Estado/controle | Efeito e limite |
| --- | --- | --- | --- |
| autenticação staff/client | `AuthenticationBackend::register()` e `process()` (`include/class.auth.php:249-266,324-340,580-585,800-805`) | ID persiste em `staff.backend`/`user_account.backend` (`include/class.auth.php:607-622,819-830`); backend deve devolver principal válido | suporta login/sessão, mas recebe credenciais e ordem influencia avaliação |
| OAuth2 | `OAuth2Backend::register()`/`getBackend()` (`include/class.auth.php:168-213`) | memória e config de plugin; sessão seleciona backend | autenticação/autorização; acoplado a classe, sessão e ID composto |
| política de senha | `PasswordPolicy`, registry base (`include/class.auth.php:1534-1570,1640`) | seleção por ID; execução em ordem reversa | recebe senhas, valida expiração e limpa sessões; sem isolamento |
| 2FA | `TwoFactorAuthenticationBackend::register()` (`include/class.2fa.php:107-156`) | ID persiste em `staff.default_2fa` | entrega/valida OTP; depende de sessão/forms internos |
| avatar | `AvatarSource::register()`/`lookup()` (`include/class.avatar.php:45-80`) | seleção persiste em config | produz avatar; privacidade e chamadas externas são do provider |
| storage de arquivo | `FileStorageBackend::register()`/`lookup()` (`include/class.file.php:760-813`) | `attachment_file.bk` persiste um caractere | lê/grava/migra/remove; colisão silenciosa e segurança são do backend |
| busca | `SearchBackend::register()`/`getInstance()` (`include/class.search.php:28-63,202-228`) | seleção por constante; índice derivado | busca/indexa modelos; ACL depende do chamador e sinais atualizam índice |
| apps/navegação | `Application::register*App()` (`include/class.app.php:21-50`) | memória por request; menu não impõe ACL | adiciona navegação; destino precisa de guarda própria |
| tipos de campo | `FormField::addFieldTypes()` (`include/class.forms.php:605-657,1151-1170`) | tipo/config persistem em metadados | widget, validação, busca e render; contrato amplo e colisão de chave |
| matches de filtro | `Filter::addSupportedMatches()` (`include/class.filter.php:354-388`) | regra escolhida persiste | amplia catálogo; pipeline deve fornecer a chave; grupo pode colidir |
| ações de filtro | `FilterAction::register()` (`include/class.filter_action.php:80-142,225-737`) | tipo/config persistem | muta ticket por referência; privilegiado, sem transação imposta |
| permissões de papel | `RolePermission::register()` (`include/class.role.php:277-364`) | concessões persistem como JSON | declarar chave não aplica autorização no consumidor |

## Composição acessível, mas acoplada ao core

| Mecanismo | Evidência | Limite |
| --- | --- | --- |
| handlers de lista | `CustomListHandler::register()` (`include/class.list.php:100-140,1119`) | sem validação/contrato público; somente `ticket-status` nativo |
| filtros de coluna | `QueueColumnFilter::register()` (`include/class.queue.php:3013-3145`) | boa validação, mas query/HTML e sanitização ficam na implementação |
| ações de thread | `ThreadEntry::registerAction()` (`include/class.thread.php:1830-1887`; `include/class.thread_actions.php`) | implementação decide visibilidade/permissão e pode mutar domínio |
| validators de form | `AbstractForm::addValidator()` e opção de campo (`include/class.forms.php:145-150,226-229,763-768`) | composição por instância, não hook global de forms existentes |
| loader de plugin | `Plugin::getImpl()` e namespace fallback (`include/class.plugin.php:695-714`; `include/class.osticket.php:668-673`) | código confiável; namespace amplo pode colidir/sombrear classes |

## Mecanismos internos, não APIs de plugin

| Mecanismo | Evidência | Razão |
| --- | --- | --- |
| factory de sessão | `include/class.session.php:21-103`; consumidor `include/class.ostsession.php:104` | mapa fixo e TODO explícito para registro por plugins |
| exportações operacionais | `Exporter::register()`/`load()` (`include/class.export.php:545-578`) | estado efêmero em sessão, não catálogo de providers |
| import/export CLI | `include/cli/modules/export.php:47-76`; `include/cli/modules/import.php:71-80` | loader interno; diretório `importer/` referido está ausente |
| módulos CLI | `Module::register()` (`include/class.cli.php:258-266`; `manage.php:58-75`) | dispatch por arquivo core, sem descoberta em plugins |
| tradução e criptografia | `TextDomain::lookup()` (`include/class.translation.php:699-705,860-867`); `Crypto::cryptos()` (`include/class.crypto.php:100-150`) | caches/factories fixas, sem registro externo |

## Invariantes e riscos

- registrar permissão, app, rota ou ação não cria a verificação de autorização;
- IDs sem namespace podem colidir, alguns com substituição silenciosa;
- callbacks são síncronos e não recebem isolamento transacional comum;
- discriminadores persistidos podem ficar sem provider se o plugin falhar ou
  for desabilitado;
- classes de plugin recebem objetos, credenciais, arquivos ou queries internos;
- entradas fornecidas por plugins precisam ser registradas no bootstrap de toda
  requisição consumidora; entradas nativas vêm do carregamento de suas classes.

**Inferência:** autenticação/OAuth2, 2FA, avatar, storage, busca, apps, tipos de
campo, matches, ações de filtro e permissões são os pontos mais explicitamente
sustentados. Handlers de lista, filtros de fila e ações de thread são
tecnicamente acessíveis, mas devem ser tratados como acoplamentos internos.
