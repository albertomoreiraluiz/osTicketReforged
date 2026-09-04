# Mapa inicial do modelo de dados

## Fonte primária

O esquema de instalação da baseline está em
`setup/inc/streams/core/install-mysql.sql`. Foram observadas 67 instruções
`CREATE TABLE`. O bootstrap declara 72 constantes `*_TABLE`, pois também há
tabelas auxiliares/dinâmicas e nomes que não correspondem de forma simples a
uma criação estática no arquivo inicial.

Há 99 arquivos SQL em `include/upgrader/streams/core/`; eles representam a
cadeia histórica de evolução e não devem ser somados ao esquema como tabelas.

## Agrupamento inicial das 67 tabelas

| Domínio aparente | Tabelas observadas no esquema inicial |
| --- | --- |
| API e configuração | `api_key`, `config`, `sequence`, `syslog`, `session`, `translation` |
| Arquivos | `attachment`, `file`, `file_chunk` |
| Conhecimento e conteúdo | `faq`, `faq_category`, `faq_topic`, `content`, `canned_response` |
| Formulários e listas | `form`, `form_field`, `form_entry`, `form_entry_values`, `list`, `list_items` |
| E-mail e filtros | `email`, `email_account`, `email_template_group`, `email_template`, `filter`, `filter_action`, `filter_rule` |
| Estrutura da equipe | `department`, `group`, `role`, `staff`, `staff_dept_access`, `team`, `team_member` |
| Agendamento | `sla`, `schedule`, `schedule_entry` |
| Tópicos | `help_topic`, `help_topic_form` |
| Pessoas | `user`, `user_email`, `user_account`, `organization` |
| Tickets e tarefas | `ticket`, `ticket_status`, `ticket_priority`, `task`, `lock`, `event`, `note`, `draft` |
| Threads | `thread`, `thread_entry`, `thread_entry_email`, `thread_entry_merge`, `thread_event`, `thread_referral`, `thread_collaborator` |
| Plugins | `plugin`, `plugin_instance` |
| Filas | `queue`, `queue_column`, `queue_columns`, `queue_sort`, `queue_sorts`, `queue_export`, `queue_config` |

O agrupamento é uma classificação de leitura, não uma fronteira arquitetural
confirmada.

## Modelos PHP localizados

| Área | Símbolos observados | Arquivo |
| --- | --- | --- |
| Ticket | `Ticket`, `TicketCData` | `include/class.ticket.php` |
| Thread | `Thread`, `ThreadEntry`, `ThreadEvent`, `ThreadReferral` | `include/class.thread.php` |
| Tarefa | `TaskModel`, `Task`, `TaskCData` | `include/class.task.php` |
| Usuário | `UserModel`, `User`, `UserEmail`, `UserAccount` | `include/class.user.php` |
| Organização | `OrganizationModel`, `Organization`, `OrganizationCdata` | `include/class.organization.php` |
| Equipe | `Staff`, `StaffDeptAccess`, `Team`, `TeamMember` | `include/class.staff.php`, `class.team.php` |
| Formulários | `DynamicForm`, `DynamicFormField`, `DynamicFormEntry`, `DynamicFormEntryAnswer` | `include/class.dynamic_forms.php` |
| Plugins | `Plugin`, `PluginInstance` | `include/class.plugin.php` |

Esses símbolos declaram metadados `static $meta` do ORM. Relações, joins,
cardinalidades e regras de exclusão ainda precisam ser extraídos e comparados
com os ERDs oficiais.

## Relação com a documentação oficial

Os 14 ERDs incorporados são referência auxiliar por assunto. Como a publicação
se identifica como 1.17.7, nenhum relacionamento será considerado confirmado
sem confronto com o esquema e os metadados ORM da baseline 1.18.4.

## Próximas extrações

1. chaves primárias, estrangeiras e índices do esquema;
2. relações declaradas nos metadados ORM;
3. tabelas dinâmicas `__cdata` e formulários;
4. diferenças entre esquema inicial, patches e ERDs;
5. operações de escrita por fluxo de ticket.
