# Function Review: inboundEscrowTransfer(...) / mint(...)

## Что делает функция

Это финальный token credit step на L2.

После проверки bridge message функция release/mint токены получателю.

## Главные инварианты

```text
1. Credited amount on L2 must equal verified L1 escrowed amount.
2. The recipient must match the authenticated bridge message.
3. The token credited on L2 must match the intended L2 token.
```

## Важная мысль

Если здесь поменять amount/token/recipient, весь предыдущий flow может быть правильным, но финальный результат будет неправильным.
