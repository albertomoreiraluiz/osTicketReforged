# ADR 0001 — Frontend separado com Angular e PrimeNG

- Status: **Aceito**
- Data: 2026-09-03
- Responsável pela decisão: mantenedor do projeto

## Contexto

O osTicket Reforged preservará o código upstream enquanto prepara uma
revitalização progressiva do frontend. O novo frontend precisa ter dependências,
build, testes e configuração próprios, sem misturar a toolchain JavaScript com o
runtime PHP do core.

## Decisão

O frontend será mantido no diretório `frontend/` da raiz do repositório e usará
Angular com PrimeNG. Tudo que for específico dessa aplicação — manifesto de
dependências, configuração, fontes, testes e build — pertencerá a esse diretório.

O XAMPP executará o osTicket/PHP durante a homologação local. Angular será
executado e compilado por Node.js e pnpm, usando Angular CLI fixado como
dependência do próprio projeto, sem instalação global obrigatória.

Versões de Angular, PrimeNG e demais bibliotecas, estratégia de integração com o
core, rotas, autenticação, API e destino dos artefatos de build permanecem a
definir após a engenharia reversa pertinente.

## Consequências

- o core PHP e a aplicação Angular terão fronteira explícita;
- comandos frontend serão executados a partir de `frontend/`;
- dependências JavaScript não serão instaladas na raiz nem no código upstream;
- nenhum artefato compilado será incorporado ao core sem decisão posterior;
- a seleção das versões deve considerar compatibilidade entre Angular, PrimeNG
  e Node.js.

## Riscos de atualização

- uma integração precoce pode aumentar o delta em relação ao upstream;
- versões incompatíveis de Node.js, Angular e PrimeNG podem bloquear o build;
- autenticação, sessão, CSRF, CORS e contratos de API exigirão ADRs próprios.

## Referências

- `docs/development/HOMOLOGATION_ENVIRONMENT.md`
- `docs/plans/active/0001-reverse-engineering.md`
- decisão explícita do responsável pelo projeto em 2026-09-03
