# BUSINESS_RULES.md

Regras de negócio do Controle de Gastos. Cada regra tem um código (RN-xx) para ser referenciada em testes e commits.

## Categoria
- **RN-01**: Toda categoria tem nome único (sem diferenciar maiúsculas de minúsculas).
- **RN-02**: O nome deve ter de 2 a 50 caracteres.
- **RN-03**: Uma categoria que possui despesas não pode ser excluída (erro `409`).

## Despesa
- **RN-04**: O valor deve ser maior que zero, com no máximo 2 casas decimais.
- **RN-05**: A descrição é obrigatória e tem no máximo 100 caracteres.
- **RN-06**: A data da despesa não pode ser futura.
- **RN-07**: Toda despesa pertence a uma categoria existente (erro `404` se não existir).
- **RN-08**: Valores são em reais (BRL) e calculados com `decimal`.

## Orçamento mensal
- **RN-09**: Cada categoria pode ter no máximo um orçamento por mês (`year` + `month`).
- **RN-10**: O limite do orçamento deve ser maior que zero.
- **RN-11**: Total gasto = soma das despesas da categoria no mês do orçamento.
- **RN-12**: Status do orçamento, calculado pelo percentual gasto em relação ao limite:

| Percentual gasto | Status |
|---|---|
| Menos de 80% | `ok` |
| De 80% até 100% (inclusive) | `warning` |
| Acima de 100% | `exceeded` |

- **RN-13**: Estourar o orçamento **não bloqueia** o registro da despesa. A despesa é salva e a resposta traz o alerta.
- **RN-14**: Se a categoria não tem orçamento no mês, não há alerta (`budgetStatus = null`).

## Auditoria
- **RN-15**: Toda criação, alteração e exclusão de despesa registra a ação (`created`, `updated`, `deleted`), o `expenseId` e o horário (UTC).

## Exemplos para teste

| Limite | Já gasto | Nova despesa | Total | Status esperado |
|---|---|---|---|---|
| R$ 500,00 | R$ 0,00 | R$ 100,00 | R$ 100,00 | `ok` |
| R$ 500,00 | R$ 300,00 | R$ 100,00 | R$ 400,00 | `warning` |
| R$ 500,00 | R$ 450,00 | R$ 50,00 | R$ 500,00 | `warning` |
| R$ 500,00 | R$ 450,00 | R$ 51,00 | R$ 501,00 | `exceeded` |
