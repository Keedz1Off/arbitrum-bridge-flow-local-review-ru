# Function Review: outboundTransfer(...) / withdraw(...)

## Function Code

```solidity
function outboundTransfer(
    address _l1Token,
    address _to,
    uint256 _amount,
    uint256, /* _maxGas */
    uint256, /* _gasPriceBid */
    bytes calldata _data
) public payable virtual override returns (bytes memory res) {
    require(msg.value == 0, "NO_VALUE");

    address _from;
    bytes memory _extraData;
    {
        if (isRouter(msg.sender)) {
            (_from, _extraData) = GatewayMessageHandler.parseFromRouterToGateway(_data);
        } else {
            _from = msg.sender;
            _extraData = _data;
        }
    }
    require(_extraData.length == 0, "EXTRA_DATA_DISABLED");

    uint256 id;
    {
        address l2Token = calculateL2TokenAddress(_l1Token);
        require(l2Token.isContract(), "TOKEN_NOT_DEPLOYED");
        require(_isValidTokenAddress(_l1Token, l2Token), "NOT_EXPECTED_L1_TOKEN");

        _amount = outboundEscrowTransfer(l2Token, _from, _amount);
        id = triggerWithdrawal(_l1Token, _from, _to, _amount, _extraData);
    }
    return abi.encode(id);
}
```

---

## Объяснение функции

`outboundTransfer(...)` на L2 начинает withdrawal flow обратно на L1.

Пользователь вызывает эту функцию, когда хочет вывести токены с L2 на L1.

Главная идея:

```text
L1 release должен быть backed by real L2 burn / lock.
```

---

## Важные моменты логики

### NO_VALUE

Функция требует:

```solidity
require(msg.value == 0, "NO_VALUE");
```

Этот withdrawal path не должен принимать ETH/value.

---

### router parsing

Если вызов идет через router, `_from` достается из encoded data.

Если нет — `_from = msg.sender`.

---

### token validation

Функция проверяет, что L2 token существует и соответствует L1 token:

```solidity
require(_isValidTokenAddress(_l1Token, l2Token), "NOT_EXPECTED_L1_TOKEN");
```

---

### burn / escrow before message

Перед созданием outbound message вызывается:

```solidity
outboundEscrowTransfer(l2Token, _from, _amount);
```

Это accounting boundary withdrawal flow.

---

## Инварианты

- L2 burned / locked amount должен равняться L1 released amount.
- `_l1Token` должен соответствовать expected L2 token.
- `_to` должен быть intended L1 recipient.
- Burn/lock должен произойти до L1 release.
- Encoded amount должен равняться real burned / locked amount.
- Withdrawal message не должен финализироваться дважды.
