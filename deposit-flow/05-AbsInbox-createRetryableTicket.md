# Function Review: AbsInbox._createRetryableTicket(...)

## Function Code

```solidity
function _createRetryableTicket(
    address to,
    uint256 l2CallValue,
    uint256 maxSubmissionCost,
    address excessFeeRefundAddress,
    address callValueRefundAddress,
    uint256 gasLimit,
    uint256 maxFeePerGas,
    uint256 amount,
    bytes calldata data
) internal returns (uint256) {
    uint256 amountToBeMintedOnL2 = _fromNativeTo18Decimals(amount);
    if (amountToBeMintedOnL2 < (maxSubmissionCost + l2CallValue + gasLimit * maxFeePerGas)) {
        revert InsufficientValue(
            maxSubmissionCost + l2CallValue + gasLimit * maxFeePerGas, amountToBeMintedOnL2
        );
    }

    if (AddressUpgradeable.isContract(excessFeeRefundAddress)) {
        excessFeeRefundAddress = AddressAliasHelper.applyL1ToL2Alias(excessFeeRefundAddress);
    }
    if (AddressUpgradeable.isContract(callValueRefundAddress)) {
        callValueRefundAddress = AddressAliasHelper.applyL1ToL2Alias(callValueRefundAddress);
    }

    return _unsafeCreateRetryableTicket(
        to,
        l2CallValue,
        maxSubmissionCost,
        excessFeeRefundAddress,
        callValueRefundAddress,
        gasLimit,
        maxFeePerGas,
        amount,
        data
    );
}
```

---

## Объяснение функции

`AbsInbox._createRetryableTicket(...)` — lower-level Inbox step для создания retryable ticket.

Эта функция ближе к message system, чем к token accounting.

Главная идея:

```text
Inbox создает message, но bridge должен передать правильные параметры.
```

---

## Важные моменты логики

### value check

Функция проверяет, что amount покрывает:

```text
maxSubmissionCost + l2CallValue + gasLimit * maxFeePerGas
```

Если средств недостаточно, происходит revert.

---

### address aliasing

Если refund address является contract, применяется L1 -> L2 alias:

```solidity
AddressAliasHelper.applyL1ToL2Alias(...)
```

Это важно, чтобы contract refund address мог управлять средствами на L2.

---

## Инварианты

- Retryable ticket должен сохранить правильный L2 target.
- Calldata должна сохраниться без искажения.
- Payment/gas values должны быть достаточными.
- Contract refund addresses должны использовать aliasing.
- Sender/refund assumptions должны соответствовать Arbitrum rules.
