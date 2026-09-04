# Catálogo dos metadados ORM

## Resultado e notação

Foram catalogadas as 72 declarações `static $meta` em 38 arquivos:

| Tipo | Quantidade |
| --- | ---: |
| modelos concretos atuais sobre tabelas físicas | 64 |
| modelos atuais sobre CDATA dinâmico | 4 |
| metamodelo-base sem tabela | 1 |
| modelos históricos do upgrader | 3 |
| **total** | **72** |

`P` indica tabela do schema; `DYN`, CDATA materializado; `HIST`, modelo de
upgrade; `BASE`, metamodelo. Oito modelos usam PK composta:
`DynamicFormEntryAnswer`, `FaqTopic`, `AttachmentFileChunk`, `QueueConfig`,
`QueueColumnGlue`, `QueueSortGlue`, `StaffDeptAccess` e `TeamMember`.

## Catálogo integral

| Domínio | Modelo — tabela/tipo — PK — evidência |
| --- | --- |
| Base/config | `VerySimpleModel` — BASE — false — `include/class.orm.php:306-310` |
| Base/config | `ConfigItem` — config/P — id — `include/class.config.php:157-160` |
| Base/config | `DatabaseSessionRecord` — session/P — session_id — `include/class.ostsession.php:304-307` |
| Base/config | `Sequence` — sequence/P — id — `include/class.sequence.php:7-11` |
| Base/config | `CustomDataTranslation` — translation/P — id — `include/class.translation.php:873-876` |
| Pessoas | `OrganizationModel` — organization/P — id — `include/class.organization.php:22-40` |
| Pessoas | `OrganizationCdata` — organization__cdata/DYN — org_id — `include/class.organization.php:169-177` |
| Pessoas | `UserEmailModel` — user_email/P — id — `include/class.user.php:24-32` |
| Pessoas | `UserModel` — user/P — id — `include/class.user.php:49-86` |
| Pessoas | `UserCdata` — user__cdata/DYN — user_id — `include/class.user.php:199-207` |
| Pessoas | `UserAccount` — user_account/P — id — `include/class.user.php:1070-1079` |
| Equipe | `Dept` — department/P — id — `include/class.dept.php:22-53` |
| Equipe | `Staff` — staff/P — staff_id — `include/class.staff.php:28-45` |
| Equipe | `StaffDeptAccess` — staff_dept_access/P — (staff_id,dept_id) — `include/class.staff.php:1452-1467` |
| Equipe | `RoleModel` — role/P — id — `include/class.role.php:19-32` |
| Equipe | `Team` — team/P — team_id — `include/class.team.php:20-33` |
| Equipe | `TeamMember` — team_member/P — (team_id,staff_id) — `include/class.team.php:373-385` |
| Tickets | `Ticket` — ticket/P — ticket_id — `include/class.ticket.php:41-106` |
| Tickets | `TicketCData` — ticket__cdata/DYN — ticket_id — `include/class.ticket.php:4841-4854` |
| Tickets | `TaskModel` — task/P — id — `include/class.task.php:21-67` |
| Tickets | `TaskCData` — task__cdata/DYN — task_id — `include/class.task.php:1697-1705` |
| Threads | `Thread` — thread/P — id — `include/class.thread.php:24-55` |
| Threads | `ThreadEntryEmailInfo` — thread_entry_email/P — id — `include/class.thread.php:775-783` |
| Threads | `ThreadEntryMergeInfo` — thread_entry_merge/P — id — `include/class.thread.php:787-795` |
| Threads | `ThreadEntry` — thread_entry/P — id — `include/class.thread.php:800-837` |
| Threads | `ThreadReferral` — thread_referral/P — id — `include/class.thread.php:1902-1928` |
| Threads | `ThreadEvent` — thread_event/P — id — `include/class.thread.php:1974-2026` |
| Threads | `Event` — event/P — id — `include/class.thread.php:2249-2252` |
| Threads | `Collaborator` — thread_collaborator/P — id — `include/class.collaborator.php:23-35` |
| Tickets | `Lock` — lock/P — lock_id — `include/class.lock.php:23-39` |
| Tickets | `Priority` — ticket_priority/P — priority_id — `include/class.priority.php:20-24` |
| Tickets | `TicketStatus` — ticket_status/P — id — `include/class.list.php:1125-1134` |
| Tickets | `QuickNoteModel` — note/P — id — `include/class.note.php:20-24` |
| Forms | `DynamicForm` — form/P — id — `include/class.dynamic_forms.php:31-40` |
| Forms | `DynamicFormField` — form_field/P — id — `include/class.dynamic_forms.php:600-614` |
| Forms | `DynamicFormEntry` — form_entry/P — id — `include/class.dynamic_forms.php:976-990` |
| Forms | `DynamicFormEntryAnswer` — form_entry_values/P — (entry_id,field_id) — `include/class.dynamic_forms.php:1418-1432` |
| Lists | `DynamicList` — list/P — id — `include/class.list.php:149-158` |
| Lists | `DynamicListItem` — list_items/P — id — `include/class.list.php:629-638` |
| KB | `Topic` — help_topic/P — topic_id — `include/class.topic.php:23-61` |
| KB | `TopicFormModel` — help_topic_form/P — id — `include/class.topic.php:641-653` |
| KB | `FAQ` — faq/P — faq_id — `include/class.faq.php:21-46` |
| KB | `FaqTopic` — faq_topic/P — (faq_id,topic_id) — `include/class.faq.php:454-470` |
| KB | `Category` — faq_category/P — category_id — `include/class.category.php:19-35` |
| KB | `Page` — content/P — id — `include/class.page.php:18-37` |
| KB | `Canned` — canned_response/P — canned_id — `include/class.canned.php:20-38` |
| Arquivos | `Attachment` — attachment/P — id — `include/class.attachment.php:20-43` |
| Arquivos | `AttachmentFile` — file/P — id — `include/class.file.php:47-55` |
| Arquivos | `AttachmentFileChunk` — file_chunk/P — (file_id,chunk_id) — `include/class.file.php:953-961` |
| Arquivos | `Draft` — draft/P — id — `include/class.draft.php:21-35` |
| E-mail | `Email` — email/P — email_id — `include/class.email.php:26-53` |
| E-mail | `EmailAccount` — email_account/P — id — `include/class.email.php:409-417` |
| E-mail | `MailBoxAccount` — email_account/P — id — `include/class.email.php:1049-1062` |
| E-mail | `SmtpAccount` — email_account/P — id — `include/class.email.php:1261-1274` |
| Filtros | `Filter` — filter/P — id — `include/class.filter.php:21-33` |
| Filtros | `FilterRule` — filter_rule/P — id — `include/class.filter.php:716-724` |
| Filtros | `FilterAction` — filter_action/P — id — `include/class.filter_action.php:6-15` |
| Plugins | `Plugin` — plugin/P — id — `include/class.plugin.php:544-553` |
| Plugins | `PluginInstance` — plugin_instance/P — id — `include/class.plugin.php:1007-1017` |
| Agenda | `SLA` — sla/P — id — `include/class.sla.php:21-24` |
| Agenda | `Schedule` — schedule/P — id — `include/class.schedule.php:21-30` |
| Agenda | `ScheduleEntry` — schedule_entry/P — id — `include/class.schedule.php:532-541` |
| Filas | `CustomQueue` — queue/P — id — `include/class.queue.php:19-57` |
| Filas | `QueueColumn` — queue_column/P — id — `include/class.queue.php:2228-2232` |
| Filas | `QueueConfig` — queue_config/P — (queue_id,staff_id) — `include/class.queue.php:2638-2657` |
| Filas | `QueueExport` — queue_export/P — id — `include/class.queue.php:2702-2712` |
| Filas | `QueueColumnGlue` — queue_columns/P — (queue_id,staff_id,column_id) — `include/class.queue.php:2735-2755` |
| Filas | `QueueSort` — queue_sort/P — id — `include/class.queue.php:2825-2834` |
| Filas | `QueueSortGlue` — queue_sorts/P — (sort_id,queue_id) — `include/class.queue.php:2968-2981` |
| Histórico | `ThreadEntryEmailNew` — thread_entry_email_new/HIST — id — `include/upgrader/streams/core/4bd47d94-e7038ce9.task.php:11-14` |
| Histórico | `Group` — group/HIST — id — `include/upgrader/streams/core/1ee831c8-36f6b328.task.php:5-8` |
| Histórico | `OldOneSixFile` — file/HIST — id — `include/upgrader/streams/core/15b30765-dd0022fb.task.php:254-262` |

## Relações e cardinalidade

O mapa completo de relações centrais e polimórficas está em
[Arquitetura de persistência](DATABASE_ARCHITECTURE.md). Regras de leitura:

- join com `constraint` é direto; `reverse`, reverso;
- `list=false` expressa zero-ou-um no ORM, não unicidade física;
- reversos assumem coleção por padrão;
- discriminadores literais condicionam relações polimórficas;
- nenhum join cria FK SQL.

## Divergências verificadas

1. `OrganizationCdata.org` usa `ord_id → OrganizationModel.id`, mas a PK e a
   materialização usam `org_id`
   (`include/class.organization.php:169-177,661-665`).
2. `TaskCData.task` aponta a `TaskModel.task_id`, enquanto o modelo/tabela usam
   PK `id` (`include/class.task.php:21-23,1697-1705`;
   `setup/inc/streams/core/install-mysql.sql:857-872`).
3. `UserModel.account` é zero-ou-um lógico, porém
   `user_account.user_id` não é UNIQUE
   (`include/class.user.php:61-65`;
   `setup/inc/streams/core/install-mysql.sql:1053-1066`).

Os dois primeiros são prováveis erros de metadado, mas não serão alterados sem
comparação upstream e confirmação futura.

## Método reproduzível

A extração localiza `static $meta = array`, balanceia o array, identifica classe
envolvente, extrai tabela/PK/ordering/fields/joins, classifica reverse/constraint,
preserva discriminadores, confronta constantes com `bootstrap.php:74-168` e
schema, e separa arquivos do upgrader. A busca inicial é:

    rg -n --glob '*.php' 'static\s+\$meta\s*=\s*array' .
