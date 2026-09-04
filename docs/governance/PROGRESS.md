# Controle de progresso

## Ponto de retomada

- Data de atualização: 2026-09-03.
- Baseline: `v1.18.4` (`8d38b0619649a50ee7cbbf37085f5d297fdc6f36`).
- Branch estável resultante: `main`.
- Etapa: engenharia reversa — preparação do inventário.
- Plano ativo: `docs/plans/active/0001-reverse-engineering.md`.

## Concluído

- Fork `albertomoreiraluiz/osTicketReforged` conectado como `origin`.
- Repositório oficial conectado como `upstream`; push local para ele desabilitado.
- `main` criada a partir de `v1.18.4` e definida pelo responsável como branch padrão.
- Branch documental criada e publicada.
- Contexto persistente, regras e protocolo multiagente produzidos localmente para revisão.
- Treze cargos profissionais permanentes definidos, com autoridade, limites e entregas estáveis.
- Perfis profissionais aprovados pelo responsável pelo projeto.
- Regras de uso condicional de múltiplos agentes aprovadas.
- Política obrigatória de documentação e formalização no GitHub aprovada.
- Configuração e documentação de agentes isoladas em `.codex/`, sem inclusão no código executável do osTicket.
- Leitura documental no início de toda tarefa e varredura documental anterior a todo PR aprovadas como obrigatórias.
- Governança formalizada e integrada pela PR #1.
- Infraestrutura-base de homologação preparada com XAMPP, PHP 8.2, extensões,
  logs e toolchain local inventariados.
- Snapshot rastreável do guia oficial de instalação, índice de ERDs, quatorze
  diagramas PDF e licença incorporado a partir de `osTicket/docs`.

## Em andamento

- Preparação da composição e das fronteiras do inventário da Onda 1.

## Próximo passo proposto

1. registrar a avaliação de necessidade e a composição da Onda 1;
2. iniciar o inventário estático e somente leitura;
3. receber os dados de banco e validar a instalação web somente antes dos testes
   dinâmicos que dependam deles;
4. manter Angular, PrimeNG e decisões de integração fora da etapa atual.

## Estado dos portões

| Portão | Estado | Evidência atual | Bloqueio |
| --- | --- | --- | --- |
| A — Governança | Concluído | regras e perfis aprovados, validações e PR #1 | nenhum |
| B — Inventário | Pronto para iniciar | plano ativo e governança integrada | composição da Onda 1 |
| C — Análise profunda | Não iniciado | nenhuma | Portão B |
| D — Decisão arquitetural | Não iniciado | nenhuma | Portão C e aprovação do ADR |

## Decisões futuras, fora da preparação atual

Os itens abaixo não bloqueiam o início nem a preparação do inventário. Serão
deliberados apenas na etapa a que pertencem, com base nas evidências produzidas.

- Convenção final de releases do Reforged — **A definir (TBD)**.
- Política de proteção obrigatória de `main` no GitHub — **Proposto**.
- Arquitetura de plugin, API e frontend — **A definir (TBD)** após engenharia reversa.
- Primeiro módulo a migrar — **A definir (TBD)**.
- Persistência definitiva de conexões e segredos — **A definir (TBD)**.

## Riscos conhecidos

- O carregamento das ferramentas de assistência deve ser validado separadamente do comportamento do produto.
- Documentos de análise sem referências precisas podem virar conhecimento não verificável.
- Alterações do core antes da matriz de customização aumentariam prematuramente o delta com o upstream.

## Evidência mínima para atualizar este registro

Cada item concluído deve apontar para documento, diff, comando reproduzível, teste, Issue, Pull Request ou commit. Não registre avanço apenas por tempo gasto ou estimativa percentual.

## Histórico

| Data | Unidade | Evidência | Resultado |
| --- | --- | --- | --- |
| 2026-09-03 | Baseline Git | tag `v1.18.4`, commit `8d38b061` | Concluído |
| 2026-09-03 | Remotos | `origin`, `upstream` com push desabilitado | Concluído |
| 2026-09-03 | Governança inicial | arquivos locais; validação TOML e referências | Produzido, em revisão |
| 2026-09-03 | Perfis e políticas | aprovação explícita do responsável; `DECISIONS.md` | Aprovado, aguardando integração |
| 2026-09-03 | Portão A | PR #1 | Concluído |
| 2026-09-03 | Contrato local de homologação | `.env.example` e `docs/development/HOMOLOGATION_ENVIRONMENT.md` | Produzido, aguardando preenchimento local |
| 2026-09-03 | Configuração PHP do aaPanel | análise estática do `php.ini` fornecido; `docs/development/HOMOLOGATION_ENVIRONMENT.md` | Evidência inicial registrada; runtime ainda não validado |
| 2026-09-03 | Plataforma de homologação | GOV-008; `docs/development/HOMOLOGATION_ENVIRONMENT.md` | XAMPP local selecionado; aaPanel retirado do escopo desta etapa |
| 2026-09-03 | Runtime da homologação | GOV-009; requisito em `include/class.setup.php` | PHP 8.2 aceito para o XAMPP local |
| 2026-09-03 | Inventário do XAMPP | comandos `php`, `httpd` e leitura das configurações; guia de homologação | Caminhos e runtime registrados; serviços estavam parados |
| 2026-09-03 | Transporte da homologação | GOV-010; `.env.example` | Execução local; SSH e FTP fora do escopo |
| 2026-09-03 | Toolchain local disponível | Node.js 24.19.0 e pnpm 11.17.0; guia de homologação | Caminhos preenchidos localmente; Angular ainda não selecionado |
| 2026-09-03 | Configuração PHP | backup datado, validação de módulos, gravação no log e `httpd.exe -t` | Extensões e diagnóstico de homologação aplicados |
| 2026-09-03 | Fronteira do frontend | ADR 0001; GOV-011 | `frontend/` reservado para Angular e PrimeNG |
| 2026-09-03 | Homologação-base | `.env.example`; guia de homologação; validações PHP/Apache | Preparada; instalação funcional aguarda banco e localização web |
| 2026-09-03 | Integração da homologação-base | PR #2; merge `8786c271` | Concluída |
| 2026-09-03 | Referência oficial para o inventário | snapshot `osTicket/docs` no commit `f2367c5f`; guia de instalação, licença e 14 ERDs; hashes SHA-256 conferidos | Concluída localmente na branch documental |
