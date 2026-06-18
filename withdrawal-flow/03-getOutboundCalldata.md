# Function Review: getOutboundCalldata(...)

## Что делает функция

Функция кодирует withdrawal calldata для L1 finalization.

## Главные инварианты

```text
1. Encoded amount must match the burned amount.
2. Encoded token must match the intended L1 token.
3. Encoded recipient must match the intended L1 recipient.
```

## Риск

Если calldata содержит неправильный amount/token/recipient, L1 может release неправильные токены или неправильное количество.
