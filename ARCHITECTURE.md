# ARCHITECTURE.md

## Visão geral

A API de Controle de Gastos segue Clean Architecture, com uma solution .NET dividida em projetos.

```text
ControleDeGastos.sln
├── src/
│   ├── ControleDeGastos.Api/             # controllers, DTOs, Program.cs
│   ├── ControleDeGastos.Application/     # casos de uso
│   ├── ControleDeGastos.Domain/          # entidades, regras e interfaces (repositórios)
│   └── ControleDeGastos.Infrastructure/  # EF Core, DbContext e repositórios
└── tests/
    ├── ControleDeGastos.UnitTests/         # Domain e Application (sem banco)
    └── ControleDeGastos.IntegrationTests/  # endpoints com banco em memória
```

## Fluxo

1. `Api` recebe a requisição, valida o formato do DTO e chama `Application`.
2. `Application` orquestra o caso de uso usando interfaces do `Domain`.
3. `Infrastructure` implementa essas interfaces com Entity Framework Core.
4. A resposta volta por `Application` até `Api`.

## Regras de dependência (referências entre projetos)

- `Api` referencia `Application` e `Infrastructure` (só para registrar a injeção de dependência em `Program.cs`).
- `Application` referencia apenas `Domain`.
- `Infrastructure` referencia apenas `Domain` (implementa suas interfaces).
- `Domain` não referencia nenhum outro projeto e não usa ASP.NET nem Entity Framework.
- `Application` não referencia `Api` nem `Infrastructure`.

## Responsabilidades

| Camada | Responsabilidade | Exemplo |
|---|---|---|
| `Api` | HTTP, DTOs, códigos de status | `ExpensesController` |
| `Application` | Casos de uso | `CreateExpenseUseCase` |
| `Domain` | Entidades e regras de negócio | `Expense`, `Budget`, `Category` |
| `Infrastructure` | Persistência | `ExpenseRepository` (EF Core) |

## Convenções

- Valores monetários usam `decimal`, nunca `double` nem `float`.
- Erros de negócio são exceções do `Domain` (ex.: `InvalidAmountException`). A `Api` as converte em status HTTP com um middleware ou filtro de exceções.
- Cada caso de uso fica em uma classe própria em `Application`, com um método `ExecuteAsync`.
- Dependências são registradas por injeção de dependência nativa do ASP.NET Core em `Program.cs`.
- Código (classes, métodos, propriedades) em inglês. Mensagens de erro ao usuário em português.
