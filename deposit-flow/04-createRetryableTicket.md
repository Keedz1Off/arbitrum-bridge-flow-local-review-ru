# Function Review: createRetryableTicket(...)

## Что делает функция

`createRetryableTicket(...)` создает L1 -> L2 retryable ticket.

Retryable ticket - это механизм Arbitrum для доставки и исполнения сообщения на L2.

## Код функции

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
) external payable returns (uint256) {
    return _createRetryableTicket(
        to,
        l2CallValue,
        maxSubmissionCost,
        excessFeeRefundAddress,
        callValueRefundAddress,
        gasLimit,
        maxFeePerGas,
        data
    );
}
```

## Главные инварианты

```text
1. Retryable ticket must target the correct L2 gateway.
2. Retryable calldata must match the verified L1 deposit.
3. Retryable execution budget must be sufficient for finalization.
```

## Важные параметры

```text
to = L2 target
calldata = что будет исполнено на L2
gasLimit / maxFeePerGas = execution budget
refund addresses = куда вернуть unused funds
```
