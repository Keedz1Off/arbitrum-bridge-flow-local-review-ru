# Function Review: createRetryableTicket(...)

## Function Code

```solidity
function createRetryableTicket(
    address to,
    uint256 l2CallValue,
    uint256 maxSubmissionCost,
    address excessFeeRefundAddress,
    address callValueRefundAddress,
    uint256 gasLimit,
    uint256 maxFeePerGas,
    bytes calldata data
) external payable whenNotPaused onlyAllowed returns (uint256) {
    return _createRetryableTicket(
        to,
        l2CallValue,
        maxSubmissionCost,
        excessFeeRefundAddress,
        callValueRefundAddress,
        gasLimit,
        maxFeePerGas,
        msg.value,
        data
    );
}
```

---

## Объяснение функции

`createRetryableTicket(...)` создает L1 -> L2 retryable ticket.

В deposit flow это шаг, где подготовленная calldata отправляется в Arbitrum message system.

Главная идея:

```text
Retryable ticket переносит deposit message с L1 на L2.
```

---

## Важные моменты логики

### msg.value как amount

Вызов передает `msg.value` в `_createRetryableTicket(...)`.

Это важно для submission/payment accounting.

---

### onlyAllowed / whenNotPaused

Функция защищена `whenNotPaused` и `onlyAllowed`, то есть вызов ограничивается системными правилами Inbox.

---

## Инварианты

- Retryable ticket должен target'ить правильный L2 contract.
- `data` должно содержать корректную bridge calldata.
- Gas/payment параметры должны быть достаточными.
- Refund addresses не должны приводить к unintended value redirection.
- Message не должен позволять double finalization.
