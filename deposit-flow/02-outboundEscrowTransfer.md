# Function Review: outboundEscrowTransfer(...)

## Function Code

```solidity
function outboundEscrowTransfer(
    address _l1Token,
    address _from,
    uint256 _amount
) internal virtual returns (uint256 amountReceived) {
    // this method is virtual since different subclasses can handle escrow differently
    // user funds are escrowed on the gateway using this function
    uint256 prevBalance = IERC20(_l1Token).balanceOf(address(this));
    IERC20(_l1Token).safeTransferFrom(_from, address(this), _amount);
    uint256 postBalance = IERC20(_l1Token).balanceOf(address(this));
    return postBalance - prevBalance;
}
```

---

## Объяснение функции

`outboundEscrowTransfer(...)` — это L1 escrow step в deposit flow.

Функция переводит токены пользователя в gateway/escrow и возвращает фактически полученный amount.

Главная идея:

```text
Bridge должен credit'ить L2 только на сумму, которую реально получил на L1.
```

---

## Важные моменты логики

### balanceBefore / balanceAfter

Функция измеряет баланс до и после transfer:

```text
actualReceived = postBalance - prevBalance
```

Это защищает accounting от fee-on-transfer токенов.

---

### safeTransferFrom

Используется `safeTransferFrom`, поэтому false-return ERC20 не должен silently pass.

---

## Инварианты

- Токены должны реально перейти от пользователя в L1 escrow.
- Failed transfer должен остановить deposit.
- Returned amount должен отражать actual received amount.
- L1 escrowed amount должен равняться L2 credited amount.
- Bridge не должен использовать nominal `_amount`, если actual received меньше.
