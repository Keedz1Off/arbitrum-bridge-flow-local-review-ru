# Address Aliasing

## Что это

Address aliasing - это механизм, при котором L1 contract address на L2 может отображаться как другой адрес.

Простыми словами:

```text
L1 contract sender != обычный L2 address sender
```

## Зачем нужно

Чтобы L2 мог отличать:

```text
сообщение пришло от L1 contract
или вызов сделал обычный L2 address
```

## Почему важно для аудита

Если aliasing обработан неправильно:

```text
L2 может увидеть неправильного sender
auth check может сломаться
refund может уйти не туда
message может исполниться не тем путем
```

## Главный инвариант

```text
Address aliasing must be handled correctly when relevant.
```
