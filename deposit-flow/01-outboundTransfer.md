# Function Review: outboundTransfer(...)

## Function Code

```solidity
function outboundTransfer(
    address _l1Token,
    address _to,
    uint256 _amount,
    uint256 _maxGas,
    uint256 _gasPriceBid,
    bytes calldata _data
) public payable override returns (bytes memory res) {
    return
        outboundTransferCustomRefund(_l1Token, _to, _to, _amount, _maxGas, _gasPriceBid, _data);
}
```

---

## Объяснение функции

`outboundTransfer(...)` — это главный L1 entry point для начала deposit в Arbitrum.

Пользователь вызывает эту функцию, когда хочет перевести токены с L1 на L2.

В этой реализации функция является wrapper-ом: она передает параметры в `outboundTransferCustomRefund(...)`, где находится основная deposit-логика.

Главная идея:

```text
L2 message должен отражать то, что реально произошло на L1.
```

---

## Важные моменты логики

### Wrapper logic

Функция не делает escrow напрямую. Она вызывает:

```solidity
outboundTransferCustomRefund(...);
```

Это значит, что основной security review нужно продолжать внутри downstream-функции.

---

### Recipient и refund

Вызов передает `_to` дважды:

```solidity
outboundTransferCustomRefund(_l1Token, _to, _to, ...)
```

То есть в deprecated wrapper один и тот же address используется как destination recipient и refund-related address.

---

## Инварианты

- L1 escrowed amount должен равняться L2 minted / released amount.
- `_l1Token` должен быть ожидаемым L1 token.
- `_to` должен остаться правильным L2 recipient.
- Downstream calldata должно кодировать правильный amount/token/recipient.
- Deposit message не должен финализироваться дважды.
