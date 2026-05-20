# Arbitrum Address Aliasing

Address aliasing — это механизм Arbitrum, который применяется, когда L1 contract отправляет сообщение на L2.

Простая идея:

```text
Если L1 contract вызывает L2,
то L2 может видеть не raw L1 address,
а aliased address.
```

Зачем это нужно:

```text
Один и тот же address может существовать на L1 и L2,
но контролироваться разной логикой.
```

Поэтому Arbitrum смещает address L1 contract sender в специальный aliased address.

Для аудита важно:

```text
Если L2 проверяет sender, нужно понять: он должен проверять raw address или aliased address?
```

Главный риск:

```text
Wrong sender check = broken cross-chain auth
```

Инварианты:

- Если L1 contract отправляет message на L2, L2 auth должен учитывать aliasing.
- Counterpart gateway должен проверяться в правильной форме.
- Auth не должен принимать spoofed sender.
- Raw address и aliased address нельзя путать.
