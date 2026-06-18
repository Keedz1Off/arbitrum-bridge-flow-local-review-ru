# Function Review: inboundEscrowTransfer(...) / release(...)

## Что делает функция

Это финальный release step на L1.

Bridge отправляет escrowed tokens получателю после valid withdrawal.

## Главные инварианты

```text
1. Escrow must release only the verified amount.
2. Escrow must release the correct L1 token.
3. Escrow must release to the intended L1 recipient.
4. Escrow must not release more tokens than it holds.
```
