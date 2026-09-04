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
