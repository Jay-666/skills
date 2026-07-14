# 好测试与坏测试

## 好测试

**集成式**：通过真实接口测试，而不是 mock 内部部件。

```typescript
// 好：测试可观察的行为
test("user can checkout with valid cart", async () => {
  const cart = createCart();
  cart.add(product);
  const result = await checkout(cart, paymentMethod);
  expect(result.status).toBe("confirmed");
});
```

特征：

- 测试用户或调用方关心的行为
- 仅使用公共 API
- 可经受内部重构
- 描述“做什么”，而非“如何做”
- 每个测试只有一个逻辑断言

## 坏测试

**实现细节测试**：与内部结构耦合。

```typescript
// 差：测试实现细节
test("checkout calls paymentService.process", async () => {
  const mockPayment = jest.mock(paymentService);
  await checkout(cart, payment);
  expect(mockPayment.process).toHaveBeenCalledWith(cart.total);
});
```

危险信号：

- mock 内部协作者
- 测试私有方法
- 断言调用次数或顺序
- 重构时行为未变但测试失败
- 测试名称描述“如何做”而非“做什么”
- 通过外部手段而非接口验证

```typescript
// 差：绕过接口进行验证
test("createUser saves to database", async () => {
  await createUser({ name: "Alice" });
  const row = await db.query("SELECT * FROM users WHERE name = ?", ["Alice"]);
  expect(row).toBeDefined();
});

// 好：通过接口验证
test("createUser makes user retrievable", async () => {
  const user = await createUser({ name: "Alice" });
  const retrieved = await getUser(user.id);
  expect(retrieved.name).toBe("Alice");
});
```

**同义反复测试**：期望值复述了实现，因此测试会按构造通过。

```typescript
// 差：期望值以与代码相同的方式重新计算
test("calculateTotal sums line items", () => {
  const items = [{ price: 10 }, { price: 5 }];
  const expected = items.reduce((sum, i) => sum + i.price, 0);
  expect(calculateTotal(items)).toBe(expected);
});

// 好：期望值是独立且已知的字面量
test("calculateTotal sums line items", () => {
  expect(calculateTotal([{ price: 10 }, { price: 5 }])).toBe(15);
});
```
