# Function Review: inboundEscrowTransfer(...) / mint(...)

## Function Code

```solidity
function inboundEscrowTransfer(
    address _l2Address,
    address _dest,
    uint256 _amount
) internal virtual {
    // this method is virtual since different subclasses can handle escrow differently
    IArbToken(_l2Address).bridgeMint(_dest, _amount);
}
```

---

## Объяснение функции

`inboundEscrowTransfer(...)` — финальный token credit step на L2.

После успешной finalization функция mint'ит L2 representation token получателю.

Главная идея:

```text
Minted amount на L2 должен соответствовать verified L1 escrowed amount.
```

---

## Важные моменты логики

### bridgeMint

Функция вызывает:

```solidity
IArbToken(_l2Address).bridgeMint(_dest, _amount);
```

Это значит, что supply на L2 увеличивается.

---

### authorized flow

Эта функция internal, но безопасность зависит от того, что ее вызывает только verified finalization logic.

---

## Инварианты

- Mint должен происходить только после valid finalization.
- `_amount` должен равняться verified bridge amount.
- `_dest` должен быть intended recipient.
- `_l2Address` должен быть expected L2 token.
- Один deposit не должен приводить к double mint.
