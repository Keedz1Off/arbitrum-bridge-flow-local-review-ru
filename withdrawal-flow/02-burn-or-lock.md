# Function Review: burn(...) / lock(...)

## Function Code

```solidity
function outboundEscrowTransfer(
    address _l2Token,
    address _from,
    uint256 _amount
) internal virtual returns (uint256 amountBurnt) {
    // this method is virtual since different subclasses can handle escrow differently
    // user funds are escrowed on the gateway using this function
    // burns L2 tokens in order to release escrowed L1 tokens
    IArbToken(_l2Token).bridgeBurn(_from, _amount);
    // by default we assume that the amount we send to bridgeBurn is the amount burnt
    // this might not be the case for every token
    return _amount;
}
```

---

## Объяснение функции

Это L2 accounting step для withdrawal flow.

Функция вызывает `bridgeBurn(...)`, чтобы сжечь L2 representation tokens перед release на L1.

Главная идея:

```text
Released on L1 должен равняться burned on L2.
```

---

## Важные моменты логики

### bridgeBurn

Функция вызывает:

```solidity
IArbToken(_l2Token).bridgeBurn(_from, _amount);
```

То есть L2 balance/supply должен уменьшиться.

---

### assumed amount

Функция возвращает `_amount` и комментарий прямо говорит:

```text
this might not be the case for every token
```

Это важный audit point: bridge предполагает, что burned amount равен input amount.

---

## Инварианты

- L2 tokens должны быть burned до L1 release.
- Returned amount должен соответствовать реально burned amount.
- Failed burn должен остановить withdrawal.
- `_l2Token` должен быть expected L2 token.
- L1 release не должен происходить без успешного burn.
