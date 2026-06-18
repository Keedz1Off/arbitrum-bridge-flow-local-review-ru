# Function Review: finalizeInboundTransfer(...)

## Что делает функция

`finalizeInboundTransfer(...)` завершает deposit на L2.

Она принимает bridge message и должна mint/release токены правильному recipient.

## Главные инварианты

```text
1. Only an authentic L1 -> L2 bridge message may finalize a deposit.
2. The counterpart gateway must be the expected gateway.
3. Decoded recipient must match the intended L2 recipient.
4. Decoded amount must match the verified L1 amount.
```

## Важный security boundary

```text
message authenticity -> token mapping -> amount -> recipient
```

Если auth слабая, attacker может сделать ghost mint.
