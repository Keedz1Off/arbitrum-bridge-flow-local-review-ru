# Function Review: inboundEscrowTransfer(...) / release(...)

## Function Code

```solidity
function inboundEscrowTransfer(
    address _l1Token,
    address _dest,
    uint256 _amount
) internal virtual {
    // this method is virtual since different subclasses can handle escrow differently
    IERC20(_l1Token).safeTransfer(_dest, _amount);
}
```

---

## Объяснение функции

`inboundEscrowTransfer(...)` — финальный L1 release step в withdrawal flow.

После verified finalization функция переводит L1 tokens из escrow получателю.

Главная идея:

```text
Released on L1 должен равняться burned / locked on L2.
```

---

## Важные моменты логики

### safeTransfer

Функция использует:

```solidity
IERC20(_l1Token).safeTransfer(_dest, _amount);
```

То есть L1 escrow реально отправляет токены recipient.

---

### final accounting boundary

Это последний accounting boundary withdrawal flow.

Если тут release amount неверный, bridge может выдать больше, чем было burned на L2.

---

## Инварианты

- Released amount на L1 должен равняться verified burned / locked amount на L2.
- Release должен происходить только после valid finalization.
- `_dest` должен быть intended L1 recipient.
- `_l1Token` должен быть expected L1 token.
- Escrow не должен release'ить больше, чем держит.
- Один message не должен trigger'ить release дважды.
