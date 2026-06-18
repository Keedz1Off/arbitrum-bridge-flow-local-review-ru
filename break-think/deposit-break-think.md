# Deposit Break Think

## outboundTransfer(...)

```text
ИНВАРИАНТ
Количество токенов, заблокированное на L1, должно равняться количеству токенов, заминченному или выданному на L2.

ПОСЛЕДСТВИЯ

```

## createRetryableTicket(...)

```text
ИНВАРИАНТ
Retryable ticket должен указывать на правильный L2 gateway.

ПОСЛЕДСТВИЯ

```

## finalizeInboundTransfer(...)

```text
ИНВАРИАНТ
Только подлинное bridge-сообщение L1 -> L2 может финализировать deposit.

ПОСЛЕДСТВИЯ

```
