# Function Review: finalizeInboundTransfer(...)

## Function Code

```solidity
function finalizeInboundTransfer(
    address _token,
    address _from,
    address _to,
    uint256 _amount,
    bytes calldata _data
) external payable override onlyCounterpartGateway {
    (bytes memory gatewayData, bytes memory callHookData) = GatewayMessageHandler
        .parseFromL1GatewayMsg(_data);

    if (callHookData.length != 0) {
        callHookData = bytes("");
    }

    address expectedAddress = calculateL2TokenAddress(_token);

    if (!expectedAddress.isContract()) {
        bool shouldHalt = handleNoContract(
            _token,
            expectedAddress,
            _from,
            _to,
            _amount,
            gatewayData
        );
        if (shouldHalt) return;
    }

    bool shouldWithdraw = !_isValidTokenAddress(_token, expectedAddress);
    if (shouldWithdraw) {
        triggerWithdrawal(_token, address(this), _from, _amount, "");
        return;
    }

    inboundEscrowTransfer(expectedAddress, _to, _amount);
    emit DepositFinalized(_token, _from, _to, _amount);

    return;
}
```

---

## Объяснение функции

`finalizeInboundTransfer(...)` финализирует deposit на L2.

Эта функция вызывается после того, как L1 -> L2 message дошел до L2 gateway.

Главная идея:

```text
Только authentic bridge message может mint/release токены на L2.
```

---

## Важные моменты логики

### onlyCounterpartGateway

Modifier `onlyCounterpartGateway` — главный auth boundary.

Без этой проверки attacker мог бы попытаться напрямую вызвать finalization.

---

### token validation

Функция считает expected L2 token address:

```solidity
calculateL2TokenAddress(_token)
```

Потом проверяет, соответствует ли token expected address.

---

### fallback withdrawal

Если token mapping невалидный, функция запускает withdrawal обратно:

```solidity
triggerWithdrawal(...)
```

Это защитная логика против неправильного L2 token.

---

## Инварианты

- Finalization должен вызываться только authentic counterpart gateway.
- Decoded amount должен быть backed by L1 escrow.
- `_to` должен быть intended L2 recipient.
- `_token` должен соответствовать expected L2 token mapping.
- Один message не должен финализироваться дважды.
