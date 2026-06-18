# Function Review: burn(...)

## Что делает функция

`burn(...)` удаляет L2 токены перед созданием withdrawal message.

## Код

```solidity
function burn(address _from, uint256 _amount) external {
    _burn(_from, _amount);
}
```

## Главные инварианты

```text
1. Tokens must be burned on L2 before L1 release.
2. Burned amount must equal amount encoded for withdrawal.
3. User must have enough balance to burn the requested amount.
```

## Важная мысль

Без burn на L2 нельзя выпускать токены на L1.

Главная формула:

```text
L2 burned = L1 released
```
