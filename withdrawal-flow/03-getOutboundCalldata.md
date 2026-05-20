# Function Review: getOutboundCalldata(...)

## Function Code

```solidity
function getOutboundCalldata(
    address _token,
    address _from,
    address _to,
    uint256 _amount,
    bytes memory _data
) public view override returns (bytes memory outboundCalldata) {
    outboundCalldata = abi.encodeWithSelector(
        ITokenGateway.finalizeInboundTransfer.selector,
        _token,
        _from,
        _to,
        _amount,
        GatewayMessageHandler.encodeFromL2GatewayMsg(exitNum, _data)
    );

    return outboundCalldata;
}
```

---

## Объяснение функции

`getOutboundCalldata(...)` формирует calldata для L2 -> L1 withdrawal message.

Эта calldata позже decode'ится на L1 при finalization.

Главная идея:

```text
L1 finalization calldata должна представлять реальный L2 withdrawal.
```

---

## Важные моменты логики

### selector

Calldata вызывает:

```solidity
ITokenGateway.finalizeInboundTransfer.selector
```

На L1 эта функция завершит withdrawal и release токенов.

---

### exitNum

В data кодируется `exitNum`:

```solidity
GatewayMessageHandler.encodeFromL2GatewayMsg(exitNum, _data)
```

Это важно для идентификации withdrawal message.

---

## Инварианты

- Encoded amount должен равняться burned / locked amount на L2.
- Encoded recipient должен быть intended L1 recipient.
- Encoded token должен соответствовать correct L1 token.
- L2 encode format должен совпадать с L1 decode format.
- `exitNum` должен соответствовать withdrawal message identity.
