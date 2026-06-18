# Function Review: outboundTransfer(...) / withdraw(...)

## Что делает функция

Это entry point withdrawal flow L2 -> L1.

Пользователь начинает вывод токенов с L2 обратно на L1.

## Flow

```text
user on L2
-> withdraw / outboundTransfer
-> burn on L2
-> encode L1 calldata
-> create outbound message
-> finalize on L1
-> release tokens
```

## Главные инварианты

```text
1. L2 burned amount must equal L1 released amount.
2. The L2 token must map to the correct L1 token.
3. The L1 recipient must match the intended recipient.
```
