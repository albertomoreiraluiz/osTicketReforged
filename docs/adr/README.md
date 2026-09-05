# Registros de decisão arquitetural

Use um arquivo numerado por decisão duradoura, por exemplo `0001-estrategia-de-extensao.md`.

Status permitidos:

- `Proposto`;
- `Aceito`;
- `Rejeitado`;
- `Substituído`.

Cada ADR deve conter contexto, evidências do código, opções consideradas, decisão, consequências, riscos de atualização e referências. Apenas aprovação explícita do responsável permite mudar uma proposta para `Aceito`.

Decisões aceitas:

- `0001-frontend-angular-primeng.md` — frontend separado em `frontend/`, com
  Angular e PrimeNG.
- `0002-documentation-toolchain.md` — portal principal da engenharia reversa com
  MkDocs Material e ferramentas especializadas complementares.
- `0003-coexistencia-paineis-portal-separado.md` — coexistência contínua dos painéis
  e portal separado; premissas aceitas, não arquitetura técnica ou liberação do Portão D.
- `0004-modulos-reforged-backend-osticket.md` — estratégia aceita de módulos novos
  reutilizando o backend intacto, SCP completo e identidade existente; detalhes
  executáveis e fechamento do Portão D continuam pendentes.
- `0005-modulos-logicos-paridade-scp.md` — divisão lógica M01–M15 e cobertura
  integral do SCP sem dependência do frontend legado, exceto setup; não libera código.
