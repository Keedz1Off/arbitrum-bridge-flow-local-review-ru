# Function Review: getOutboundCalldata(...)

## Function Code

```solidity
function getOutboundCalldata(
    address _l1Token,
    address _from,
    address _to,
    uint256 _amount,
    bytes memory _data
) public view virtual override returns (bytes memory outboundCalldata) {
    // this function is public so users can query how much calldata will be sent to the L2
    // before execution
    // it is virtual since different gateway subclasses can build this calldata differently
    // ( ie the standard ERC20 gateway queries for a tokens name/symbol/decimals )
    bytes memory emptyBytes = "";

    outboundCalldata = abi.encodeWithSelector(
        ITokenGateway.finalizeInboundTransfer.selector,
        _l1Token,
        _from,
        _to,
        _amount,
        GatewayMessageHandler.encodeToL2GatewayMsg(emptyBytes, _data)
    );

    return outboundCalldata;
}
```

---

## Объяснение функции

`getOutboundCalldata(...)` формирует calldata для L2 finalization.

Именно здесь token, sender, recipient и amount превращаются в encoded message data.

Главная идея:

```text
Calldata должно представлять реальный L1 deposit.
```

---

## Важные моменты логики

### selector

Используется:

```solidity
ITokenGateway.finalizeInboundTransfer.selector
```

Это значит, что L2 message будет вызывать `finalizeInboundTransfer(...)`.

---

### encoded amount

`_amount` попадает прямо в calldata. Поэтому важно, чтобы до вызова этой функции `_amount` уже был actual received / escrowed amount.

---

## Инварианты

- Encoded amount должен равняться actual L1 escrowed amount.
- Encoded recipient должен быть intended L2 recipient.
- Encoded token должен соответствовать token mapping.
- L1 encode format должен совпадать с L2 decode format.
- `_data` не должен unsafe-образом менять critical values.
