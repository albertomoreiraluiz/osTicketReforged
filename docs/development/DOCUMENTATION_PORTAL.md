# Portal da documentação técnica

## Objetivo

Disponibilizar a engenharia reversa como um site local navegável, pesquisável e
validável, mantendo os arquivos Markdown como fonte versionada.

## Preparação

No PowerShell, a partir da raiz do repositório:

```powershell
py -m venv .local/docs-venv
.local/docs-venv/Scripts/python.exe -m pip install -r docs/requirements.txt
```

O ambiente virtual e o site gerado permanecem em `.local/`, já ignorado pelo
Git, e não alteram as dependências do osTicket.

## Uso

Validar e gerar o site:

```powershell
.local/docs-venv/Scripts/python.exe -m mkdocs build --strict
```

Abrir o portal local com atualização automática:

```powershell
.local/docs-venv/Scripts/python.exe -m mkdocs serve
```

O endereço padrão é `http://127.0.0.1:8000/`.

## Fluxo obrigatório de atualização

Toda tarefa que altere `docs/` deve:

1. atualizar o documento canônico afetado;
2. incluir páginas novas ou renomeadas na navegação de `mkdocs.yml`, quando
   forem relevantes à leitura do projeto;
3. atualizar `README.md`, `PROGRESS.md`, plano e decisões conforme o impacto;
4. executar `mkdocs build --strict` antes de concluir a tarefa;
5. repetir a validação imediatamente antes de criar uma Pull Request;
6. não versionar `.local/site-docs/` nem o ambiente virtual.

Falha de navegação, link ou build bloqueia a conclusão e o merge. O site HTML é
derivado; Markdown e configuração versionados continuam sendo as fontes.

## Ferramentas complementares

- **phpDocumentor:** candidato para gerar índices automáticos de classes,
  métodos e DocBlocks. Sua configuração será avaliada depois que o inventário
  delimitar código próprio, core e bibliotecas incorporadas.
- **OpenAPI/Swagger:** será usado quando contratos HTTP forem inventariados ou
  definidos; não substitui a documentação arquitetural.
- **Postman:** poderá materializar coleções executáveis derivadas de contratos
  confirmados; não será fonte canônica de arquitetura.

Não gere nem versione documentação automática sobre `vendor/` antes de definir
os filtros, pois isso mistura bibliotecas de terceiros com o core analisado.
