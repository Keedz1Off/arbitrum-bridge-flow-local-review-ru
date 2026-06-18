# Function Review: createOutboundTx(...)

## Что делает функция

`createOutboundTx(...)` создает outbound message из L2 в L1.

## Главные инварианты

```text
1. Outbound message must target the correct L1 gateway.
2. Outbound calldata must match the verified L2 burn.
3. Message identity must protect against replay.
```

## Важная мысль

Wrong target может привести к failed withdrawal или исполнению неправильной логики.
