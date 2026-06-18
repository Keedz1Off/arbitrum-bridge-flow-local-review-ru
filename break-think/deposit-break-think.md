# Deposit Break Think

## outboundTransfer(...)

```text
ИНВАРИАНТ
Количество токенов, заблокированное на L1, должно равняться количеству токенов, заминченному или выданному на L2.

ПОСЛЕДСТВИЯ
L2 может получить больше токенов, чем реально было заблокировано на L1.
Это может привести к unbacked tokens и сломанному bridge accounting.
```

## createRetryableTicket(...)

```text
ИНВАРИАНТ
Retryable ticket должен указывать на правильный L2 gateway.

ПОСЛЕДСТВИЯ
Сообщение может исполниться в неправильном L2 gateway или полностью failed.
Это может привести к неправильной финализации deposit.
```

## finalizeInboundTransfer(...)

```text
ИНВАРИАНТ
Только подлинное bridge-сообщение L1 -> L2 может финализировать deposit.

ПОСЛЕДСТВИЯ
Attacker может создать spoofed message.
Это может привести к ghost mint или fake deposit finalization.
```
