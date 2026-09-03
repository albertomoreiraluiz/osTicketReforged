# Política obrigatória de documentação

## Status

**Aceito** pelo responsável pelo projeto em 2026-09-03.

## Princípio

O repositório e o GitHub constituem a memória persistente do osTicket Reforged. Uma informação que existe apenas em conversa, memória de agente, terminal ou arquivo local não versionado ainda não foi formalizada.

Documentação do produto e documentação das ferramentas de assistência são camadas distintas. O produto usa `docs/`; configurações e instruções específicas de automação usam `.codex/` e o ponto de entrada `AGENTS.md`. Nenhum conteúdo operacional de agentes deve entrar no código-fonte ou na interface do osTicket.

## Regras

1. Todo progresso é registrado em `docs/governance/PROGRESS.md` e resumido no `README.md`.
2. Toda mudança de código possui registro próprio em `docs/changes/` e atualiza a documentação afetada no mesmo Pull Request.
3. Toda decisão é registrada antes da implementação que depende dela.
4. Decisão arquitetural duradoura usa ADR numerado em `docs/adr/`.
5. Decisão de processo ou governança usa `docs/governance/DECISIONS.md`.
6. Issues organizam problema, motivação e critérios; Pull Requests registram revisão, evidências, diff e integração.
7. O README mantém um resumo operacional; detalhes permanecem nos documentos especializados para evitar duplicação extensa.
8. Implementação deve consultar e cumprir decisões aceitas. Não se pergunta novamente o que já possui resposta normativa clara.
9. Evidência nova ou conflito não autoriza ignorar decisão: exige proposta de revisão e nova aprovação.
10. Documentação divergente, link quebrado, decisão oculta ou progresso desatualizado bloqueia a conclusão da tarefa.
11. Toda nova tarefa ou retomada começa pela releitura da documentação aplicável; conhecimento anterior do agente não dispensa essa etapa.
12. Antes de criar qualquer Pull Request, execute uma varredura documental final e atualize README, progresso, plano, decisões, ADRs, documentação afetada e registro de mudança.

## Rastreabilidade mínima de uma alteração de código

```text
Issue/plano
    ↓
decisão ou ADR aplicável
    ↓
implementação + testes
    ↓
registro em docs/changes
    ↓
PROGRESS + README
    ↓
Pull Request e revisão
```

O Pull Request deve permitir responder: por que mudou, qual decisão autorizou, quais arquivos e comportamentos mudaram, como foi validado, qual risco permanece e qual impacto existe sobre o upstream.

## Portão documental anterior ao Pull Request

O autor só pode criar o Pull Request depois de verificar:

- README coerente com a etapa e os próximos passos;
- `PROGRESS.md` coerente com a unidade entregue;
- plano ativo com critérios e portões atualizados;
- decisões e ADRs formalizados;
- documentação funcional e técnica sincronizada com o diff;
- registro em `docs/changes/` para toda alteração de código;
- links, referências e estado Git validados.

Se o número do PR for necessário como evidência, abra o PR somente após o conteúdo inicial estar atualizado e acrescente o número em um commit subsequente antes do merge.

## Atualização do README

Atualize o painel do README sempre que mudar:

- baseline ou versão;
- etapa atual;
- estado de um portão;
- plano ou unidade em andamento;
- próximo passo;
- decisão que altere a direção do projeto;
- release ou integração do upstream.

## Regra de precedência para implementação

Antes de escrever código, o implementador deve registrar quais decisões e documentos governam sua missão. Se a resposta já estiver formalizada, deve aplicá-la. Perguntas são reservadas a lacuna real, contradição, mudança de requisito ou evidência nova que possa invalidar a decisão.
