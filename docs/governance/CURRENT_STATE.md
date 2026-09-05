# Checkpoint canônico de retomada

## Status e finalidade

Documento **Aceito** em 2026-09-05 como fotografia operacional do projeto antes
do Portão D. Ele permite que uma nova sessão retome o trabalho sem depender de
conversa, memória privada, terminal anterior ou inferências sobre arquivos
ausentes.

Este checkpoint resume; não substitui as fontes especializadas. Em conflito,
prevalecem código e configuração da baseline para comportamento, ADRs aceitos
para arquitetura, contexto e plano para escopo, e o controle de progresso para
o ponto de retomada.

## Identidade verificada

| Item | Estado canônico |
| --- | --- |
| Upstream | `https://github.com/osTicket/osTicket.git`, remoto `upstream`, push desabilitado |
| Fork | `https://github.com/albertomoreiraluiz/osTicketReforged.git`, remoto `origin` |
| Baseline | tag `v1.18.4`, commit `8d38b0619649a50ee7cbbf37085f5d297fdc6f36` |
| Linha estável | `main` |
| Etapa | engenharia reversa documental; preparação do Portão D concluída |
| Implementação própria | não iniciada |
| Arquitetura-alvo | não escolhida |

O HEAD, o estado do GitHub e o ambiente local são dados mutáveis e devem ser
reconfirmados no início de cada tarefa; nenhum hash registrado aqui autoriza
ignorar `git status`, remotos, PRs ou a branch efetivamente carregada.

## Estado dos portões

| Portão | Estado | Base de conclusão ou próximo critério |
| --- | --- | --- |
| A — Governança | concluído | regras, decisões, documentação e perfis integrados |
| B — Inventário | concluído | 2.266 caminhos da baseline classificados sem sobra ou sobreposição |
| C — Análise profunda | concluído | catálogos, análise estática, comportamento visual e revisões independentes aprovados |
| D — Decisão arquitetural | não iniciado | comparar ao menos três opções e obter aprovação explícita de ADR |

Preparar e descrever o Portão D não equivale a iniciá-lo. Somente uma nova
instrução do responsável autoriza abrir a comparação arquitetural; somente o
ADR aceito libera o planejamento da implementação.

## Fatos consolidados

- **Fato observado:** os 2.266 caminhos rastreados da baseline foram
  classificados por regras mutuamente exclusivas; 1.215 pertencem às árvores
  incorporadas Laminas Mail, mPDF e PEAR.
- **Fato observado:** a matriz visual cobre os contextos anônimo, cliente,
  agente restrito, administrador operacional e Administração; os cenários
  `BHV-001` a `BHV-034` possuem equivalência visual ou justificativa explícita
  de ausência de frontend.
- **Fato observado:** a revisão independente final da Onda 9 não manteve achado
  alto ou médio documental aberto.
- **Fato observado:** a documentação humana usa MkDocs Material e a taxonomia
  normativa de GOV-023, sem mover as fontes canônicas.
- **Fato observado neste checkpoint:** 114 arquivos Markdown rastreados ou
  adicionados foram varridos; os 55 documentos de `docs/` aparecem uma única
  vez na navegação MkDocs, sem ausências ou duplicações. Dos 200 links locais
  verificados, nenhum destino ausente pertence à documentação do Reforged; seis
  referências ausentes permanecem em fontes históricas ou de terceiros da
  baseline e não foram modificadas.
- **Fato observado na última validação documentada:** a homologação local usa
  XAMPP, PHP 8.2 e MariaDB. Serviço, sessão, fixture e credencial local podem
  mudar e devem ser testados novamente antes de uso.

As cadeias de evidência estão em `docs/reverse-engineering/`; este resumo não
autoriza ampliar uma conclusão além do recorte declarado nesses documentos.

## Decisões aceitas que limitam o próximo trabalho

- GOV-001 a GOV-024 governam memória persistente, documentação, agentes,
  homologação, Git, evidências visuais, taxonomia e retomada.
- ADR 0001 aceita apenas o isolamento futuro do frontend em `frontend/` e o uso
  de Angular com PrimeNG; versões e integração continuam indefinidas.
- ADR 0002 aceita MkDocs Material como portal documental e GOV-023 fixa sua
  taxonomia editorial.
- GOV-016 autoriza branch, push, PR e merge validados; não autoriza force-push,
  release, reset destrutivo, exclusão ou integração com risco material.

## O que continua sem decisão

Não apresentar como aprovado, iniciado ou implementado:

- arquitetura-alvo de customização e coexistência com o osTicket;
- desenho, tecnologia, autenticação e versionamento da futura API;
- versões de Angular e PrimeNG;
- mecanismo de sessão e integração entre frontend novo e backend;
- padrão definitivo de plugins próprios;
- estratégia final de persistência de conexões e segredos;
- primeiro módulo a migrar;
- política final de release e proteção obrigatória da `main`;
- qualquer alteração de core, schema ou implementação de produto.

Esses itens pertencem ao Portão D ou a decisões posteriores. Ausência de
decisão nunca deve ser preenchida por preferência técnica do agente.

## Limites e divergências conhecidas

- Cobertura estrutural não significa inspeção linha a linha nem execução de
  todas as combinações de configuração.
- Acessibilidade, compatibilidade entre navegadores, desempenho, concorrência e
  novos ensaios ofensivos de segurança são campanhas futuras.
- A exportação CSV sem materialização observável e a sobreposição vazia da
  Página de Login são divergências reproduzidas sem causa presumida.
- Achados de ACL de tarefas, capability de arquivo e upload permanecem fatos da
  baseline; correção ou aceite de risco exigem etapa própria.
- Fixtures e serviços do laboratório são evidência local, não contrato estável;
  revalide-os antes de apoiar nova conclusão.
- O `.env` é local, ignorado e exclusivo das ferramentas; nunca registrar seus
  valores em documentação, log, Issue ou PR.
- Seis referências locais ausentes pertencem a READMEs incorporados de Laminas
  ServiceManager/MyCLabs DeepCopy e ao manual histórico `setup/doc/api`; são
  limites herdados, não links criados pelo Reforged.

## Protocolo contra contexto inventado

Antes de analisar ou alterar o projeto:

1. confirme branch, status, HEAD, remotos, baseline, Issues e PRs aplicáveis;
2. leia `REFORGED.md`, este checkpoint, `PROJECT_CONTEXT.md`, `PROGRESS.md`, o
   plano ativo, decisões, ADRs e documentação especializada;
3. trate linhas históricas de `PROGRESS.md` como eventos passados, nunca como
   estado corrente;
4. aceite uma decisão somente quando estiver marcada como `Aceito` em GOV ou
   ADR e não tiver sido substituída;
5. revalide fatos mutáveis e baratos, especialmente ambiente, serviços, sessão,
   fixtures, versões externas e estado do GitHub;
6. quando faltar evidência, registre lacuna, inferência ou proposta; não complete
   a narrativa por plausibilidade;
7. se as fontes canônicas divergirem, interrompa o trecho afetado e reconcilie a
   documentação em PR antes de implementar;
8. atualize este checkpoint quando mudar baseline, portão, decisão, plano ativo,
   arquitetura aceita ou limite operacional material.

## Próxima ação autorizável

O próximo passo é iniciar, somente após ordem explícita do responsável, o
Portão D descrito no Plano 0001: consolidar restrições, formular pelo menos três
opções, compará-las por critérios comuns, produzir a estratégia de migração,
submeter revisões independentes e apresentar uma recomendação. A aprovação da
arquitetura continua reservada ao responsável pelo projeto.

## Fontes canônicas de aprofundamento

- missão e limites: `docs/governance/PROJECT_CONTEXT.md`;
- progresso e histórico: `docs/governance/PROGRESS.md`;
- trabalho do Portão D: `docs/plans/active/0001-reverse-engineering.md`;
- decisões de processo: `docs/governance/DECISIONS.md`;
- decisões arquiteturais: `docs/adr/`;
- cobertura do código: `docs/reverse-engineering/SOURCE_COVERAGE_MATRIX.md`;
- cobertura visual: `docs/reverse-engineering/INTERFACE_COVERAGE_MATRIX.md`;
- evidências consolidadas: `docs/reverse-engineering/EVIDENCE_MATRIX.md`;
- riscos e achados: `docs/reverse-engineering/SECURITY_MODEL.md` e
  `docs/reverse-engineering/REVIEW_REPORT.md`.
