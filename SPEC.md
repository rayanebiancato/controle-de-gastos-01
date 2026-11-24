# SPEC.md

## Feature atual: registrar despesa com alerta de orçamento

**Objetivo:** permitir registrar uma despesa em uma categoria e informar, na resposta, a situação do orçamento mensal daquela categoria.

**Regras envolvidas:** RN-04, RN-05, RN-06, RN-07, RN-08, RN-11, RN-12, RN-13, RN-14, RN-15 (ver `BUSINESS_RULES.md`).

## Entidades envolvidas

| Entidade | Campos |
|---|---|
| `Category` | `Id`, `Name` |
| `Expense` | `Id`, `CategoryId`, `Amount`, `Description`, `Date`, `CreatedAt` |
| `Budget` | `Id`, `CategoryId`, `Year`, `Month`, `Limit` |
| `AuditLog` | `Id`, `Action`, `ExpenseId`, `Timestamp` |

## API

JSON em camelCase. Erros no formato `ProblemDetails` padrão do ASP.NET Core.

### Criar despesa

`POST /api/expenses`

#### Request

```json
{
  "categoryId": 1,
  "amount": 120.50,
  "description": "Almoço com a equipe",
  "date": "2026-11-20"
}
```

#### Response

- `201 Created`
- Corpo:

```json
{
  "id": 42,
  "categoryId": 1,
  "amount": 120.50,
  "description": "Almoço com a equipe",
  "date": "2026-11-20",
  "budgetStatus": {
    "limit": 500.00,
    "spent": 420.50,
    "percentage": 84.1,
    "status": "warning"
  }
}
```

Se a categoria não tem orçamento no mês, `budgetStatus` é `null`.

#### Erros

- `404`: categoria não encontrada
- `400`: valor menor ou igual a zero, descrição vazia ou longa demais, data futura

```json
{
  "title": "Dados inválidos",
  "status": 400,
  "detail": "A data da despesa não pode ser futura."
}
```

## Endpoints auxiliares (necessários para a feature funcionar)

- `POST /api/categories` cria uma categoria (`201`; `409` se o nome já existe)
- `PUT /api/budgets` cria ou atualiza o orçamento de uma categoria em um mês (`categoryId`, `year`, `month`, `limit`)

## Checklist

- [ ] criar a solution e os 4 projetos em `src/` com as referências da arquitetura
- [ ] criar entidades `Category`, `Expense`, `Budget` e `AuditLog` no Domain
- [ ] criar interfaces de repositório no Domain
- [ ] implementar `CreateExpenseUseCase` (validar regras, calcular `budgetStatus`)
- [ ] configurar EF Core, `DbContext`, migration e repositórios na Infrastructure
- [ ] criar `ExpensesController` com DTOs e DataAnnotations
- [ ] criar `CategoriesController` e `BudgetsController`
- [ ] registrar auditoria ao criar despesa
- [ ] retornar `404` para categoria inexistente
- [ ] retornar `400` para dados inválidos
- [ ] testes unitários das regras RN-04 a RN-14 (usar a tabela de exemplos)
- [ ] testes de integração do endpoint `POST /api/expenses`

## Fora do escopo desta feature

Listar, editar e excluir despesas, relatório mensal por categoria. Serão especificados em uma próxima versão deste arquivo.
