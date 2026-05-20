# Function Review: createOutboundTx(...)

## Function Code

```solidity
function createOutboundTx(
    address _from,
    uint256, /* _tokenAmount */
    bytes memory _outboundCalldata
) internal virtual returns (uint256) {
    // We make this function virtual since outboundTransfer logic is the same for many gateways
    // but sometimes (ie weth) you construct the outgoing message differently.

    // exitNum incremented after being included in _outboundCalldata
    exitNum++;
    return
        sendTxToL1(
            // default to sending no callvalue to the L1
            0,
            _from,
            counterpartGateway,
            _outboundCalldata
        );
}
```

---

## Объяснение функции

`createOutboundTx(...)` создает L2 -> L1 message для withdrawal finalization.

Главная идея:

```text
Outbound message должен отправить verified withdrawal calldata правильному L1 gateway.
```

---

## Важные моменты логики

### exitNum increment

Функция увеличивает `exitNum`:

```solidity
exitNum++;
```

Комментарий говорит, что `exitNum` incremented после включения в calldata.

---

### sendTxToL1

Функция отправляет message:

```solidity
sendTxToL1(0, _from, counterpartGateway, _outboundCalldata)
```

`counterpartGateway` должен быть правильным L1 gateway.

---

## Инварианты

- Outbound message должен target'ить correct L1 counterpart gateway.
- `_outboundCalldata` должна соответствовать verified withdrawal.
- `exitNum` должен обеспечивать корректную message identity.
- L1 release должен происходить только через valid L2 -> L1 message.
