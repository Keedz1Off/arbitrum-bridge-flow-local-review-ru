# Function Review: AbsInbox._createRetryableTicket(...)

## Что делает функция

`AbsInbox._createRetryableTicket(...)` - низкоуровневая часть создания retryable ticket.

Она передает сообщение в bridge/inbox infrastructure.

## Главные инварианты

```text
1. Retryable ticket must preserve the intended L2 target.
2. Retryable ticket must preserve the intended calldata.
3. Refund behavior must not redirect value in an unintended way.
```

## Address Aliasing

Для L1 -> L2 сообщений Arbitrum может использовать address aliasing.

Простая идея:

```text
L1 contract address на L2 может отображаться как aliased address.
```

Если aliasing обработан неправильно, L2 может увидеть неправильного sender.
