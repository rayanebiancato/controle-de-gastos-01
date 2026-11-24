# STACK.md

## Runtime
- C# 12 · .NET 8 (LTS)
- ASP.NET Core 8 Web API (com controllers)

## Dados
- SQLite (arquivo local `gastos.db`)
- Entity Framework Core 8 (`Microsoft.EntityFrameworkCore.Sqlite`)
- Migrations do EF Core (`dotnet ef`)

## Documentação da API
- Swagger UI (`Swashbuckle.AspNetCore`) em `/swagger`

## Qualidade
- xUnit (testes)
- `Microsoft.AspNetCore.Mvc.Testing` (testes de integração com `WebApplicationFactory`)
- SQLite em memória nos testes de integração
- `dotnet format` (formatação) · analisadores do .NET habilitados (`<Nullable>enable</Nullable>`)

## Validação
- DataAnnotations nos DTOs da API (formato dos dados)
- Regras de negócio dentro do `Domain` (nunca só no controller)

## Comandos
```bash
dotnet restore                                   # restaura os pacotes
dotnet build                                     # compila a solution
dotnet test                                      # roda todos os testes
dotnet run --project src/ControleDeGastos.Api    # sobe a API
dotnet format                                    # formata o código

# migrations (rodar na raiz da solution)
dotnet ef migrations add Initial --project src/ControleDeGastos.Infrastructure --startup-project src/ControleDeGastos.Api
dotnet ef database update --project src/ControleDeGastos.Infrastructure --startup-project src/ControleDeGastos.Api
```

## Observações
- Nullable reference types habilitado em todos os projetos.
- Não adicionar pacotes fora desta lista sem atualizar este arquivo.
- Se a versão do .NET instalada for diferente, atualizar a versão neste arquivo (`dotnet --version`).
