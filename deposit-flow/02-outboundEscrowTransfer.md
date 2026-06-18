# Function Review: outboundEscrowTransfer(...)

## Что делает функция

`outboundEscrowTransfer(...)` - accounting boundary deposit flow.

Именно здесь bridge должен реально получить или заблокировать L1 токены пользователя.

## Код функции

```solidity
function outboundEscrowTransfer(
    address _l1Token,
    address _from,
    uint256 _amount
) internal virtual returns (uint256 amountReceived) {
    uint256 prevBalance = IERC20(_l1Token).balanceOf(address(this));
    IERC20(_l1Token).safeTransferFrom(_from, address(this), _amount);
    uint256 postBalance = IERC20(_l1Token).balanceOf(address(this));
    return postBalance - prevBalance;
}
```

## Главная формула

```text
actualReceived = balanceAfter - balanceBefore
```

## Главные инварианты

```text
1. Amount actually received by escrow must equal amount credited on L2.
2. The escrowed token must be the intended L1 token.
```

## Почему нельзя просто доверять `_amount`

`_amount` - это то, что пользователь попросил отправить.

`actualReceived` - это сколько контракт реально получил.

Для fee-on-transfer token:

```text
User sends: 100
Bridge receives: 98
```

Если bridge credit 100 на L2, accounting ломается.
