# Function Review: getOutboundCalldata(...)

## Что делает функция

`getOutboundCalldata(...)` собирает calldata / payload для сообщения на L2.

Calldata содержит параметры, которые потом будут decoded и исполнены на другой сети.

## Код функции

```solidity
function getOutboundCalldata(
    address _token,
    address _from,
    address _to,
    uint256 _amount,
    bytes memory _data
) public view returns (bytes memory outboundCalldata) {
    return abi.encodeWithSelector(
        ITokenGateway.finalizeInboundTransfer.selector,
        _token,
        _from,
        _to,
        _amount,
        _data
    );
}
```

## Главные инварианты

```text
1. Encoded amount must match the escrowed / actual received amount.
2. Encoded token must match the intended token.
3. Encoded recipient must match the intended L2 recipient.
```

## Важная мысль

Calldata само по себе не гарантирует корректность.

Правильность должна быть обеспечена до encode и после decode.
