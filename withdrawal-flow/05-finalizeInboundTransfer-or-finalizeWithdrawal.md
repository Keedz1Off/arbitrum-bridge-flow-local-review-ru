# Function Review: finalizeInboundTransfer(...) / finalizeWithdrawal(...)

## Что делает функция

Функция завершает withdrawal на L1 после проверки сообщения с L2.

## Главные инварианты

```text
1. Only an authentic L2 -> L1 message can release L1 tokens.
2. Released amount must equal verified burned amount on L2.
3. Released token must be the correct L1 token.
4. Recipient must match the intended L1 recipient.
```

## Security boundary

```text
proof/auth -> decode calldata -> release token
```
