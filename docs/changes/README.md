# Registros de alterações

Toda alteração de código deve possuir um registro Markdown nesta pasta, incluído no mesmo Pull Request.

Use o nome:

```text
AAAA-MM-DD-identificador-curto.md
```

Cada registro deve conter:

- status;
- Issue, plano, ADR e decisão aplicáveis;
- motivação;
- escopo incluído e excluído;
- arquivos e comportamento alterados;
- impacto no core e no delta com o upstream;
- banco, segurança, API e frontend afetados;
- testes e resultados;
- riscos, rollback e pendências;
- Pull Request e commit de integração, preenchidos quando existirem.

Mudanças exclusivamente documentais podem ser registradas pelo próprio Pull Request e pelo histórico de `PROGRESS.md`; não exigem um arquivo individual nesta pasta, salvo quando alterarem uma política ou decisão duradoura.
