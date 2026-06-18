# Function Review: outboundTransfer(...)

## Что делает функция

`outboundTransfer(...)` - главный entry point для deposit flow L1 -> L2.

Пользователь вызывает эту функцию на L1, чтобы начать перенос токена на L2.

## Код функции

```solidity
function outboundTransfer(
    address _l1Token,
    address _to,
    uint256 _amount,
    uint256 _maxGas,
    uint256 _gasPriceBid,
    bytes calldata _data
) external payable returns (bytes memory) {
    return outboundTransferCustomRefund(_l1Token, _to, _to, _amount, _maxGas, _gasPriceBid, _data);
}
```

## Flow внутри

```text
outboundTransfer(...)
-> outboundEscrowTransfer(...)
-> getOutboundCalldata(...)
-> createRetryableTicket(...)
-> finalizeInboundTransfer(...) on L2
```

## Главные инварианты

```text
1. L1 escrowed amount must equal L2 minted / released amount.
2. The selected L1 token must be the intended token.
3. The L2 recipient must match the intended recipient.
```

## Важные моменты

`outboundTransfer(...)` сам по себе не завершает bridge. Он только начинает flow и передает параметры дальше.

Самая важная проверка для аудитора:

```text
Does the L2 message represent what actually happened on L1?
```
