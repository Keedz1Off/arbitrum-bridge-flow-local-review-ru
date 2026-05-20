# Function Review: finalizeInboundTransfer(...) / finalizeWithdrawal(...)

## Function Code

```solidity
function finalizeInboundTransfer(
    address _token,
    address _from,
    address _to,
    uint256 _amount,
    bytes calldata _data
) public payable virtual override onlyCounterpartGateway {
    // this function is marked as virtual so superclasses can override it to add modifiers
    (uint256 exitNum, bytes memory callHookData) = GatewayMessageHandler.parseToL1GatewayMsg(
        _data
    );

    if (callHookData.length != 0) {
        // callHookData should always be 0 since inboundEscrowAndCall is disabled
        callHookData = bytes("");
    }

    // we ignore the returned data since the callHook feature is now disabled
    (_to, ) = getExternalCall(exitNum, _to, callHookData);
    inboundEscrowTransfer(_token, _to, _amount);

    emit WithdrawalFinalized(_token, _from, _to, exitNum, _amount);
}
```

---

## Объяснение функции

Эта функция финализирует withdrawal на L1.

Она вызывается после того, как L2 -> L1 message прошел bridge path.

Главная идея:

```text
Only authentic L2 withdrawal message can release funds on L1.
```

---

## Важные моменты логики

### onlyCounterpartGateway

Modifier `onlyCounterpartGateway` — главный auth boundary.

Он должен гарантировать, что finalization пришла от expected L2 gateway.

---

### parseToL1GatewayMsg

Функция достает:

```text
exitNum
callHookData
```

`exitNum` используется для withdrawal identity.

---

### inboundEscrowTransfer

После проверки функция вызывает release:

```solidity
inboundEscrowTransfer(_token, _to, _amount);
```

Именно здесь L1 escrow отдает токены recipient.

---

## Инварианты

- Finalization должен вызываться только expected counterpart gateway.
- `_amount` должен равняться burned / locked amount на L2.
- `_to` должен быть intended L1 recipient.
- `_token` должен быть correct L1 token.
- Один withdrawal message не должен release'ить токены дважды.
