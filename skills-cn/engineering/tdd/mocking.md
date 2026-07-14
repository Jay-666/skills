# 何时使用 Mock

仅在**系统边界**处使用 mock：

- 外部 API（支付、邮件等）
- 数据库（有时使用，优先选择测试数据库）
- 时间或随机性
- 文件系统（有时使用）

不要 mock：

- 自己的类或模块
- 内部协作者
- 任何你能控制的内容

## 为可 Mock 性而设计

在系统边界处，设计易于 mock 的接口：

**1. 使用依赖注入**

传入外部依赖，而非在内部创建它们：

```typescript
// 易于 mock
function processPayment(order, paymentClient) {
  return paymentClient.charge(order.total);
}

// 难以 mock
function processPayment(order) {
  const client = new StripeClient(process.env.STRIPE_KEY);
  return client.charge(order.total);
}
```

**2. 优先使用 SDK 风格接口，而非通用获取器**

为每个外部操作创建专用函数，而非使用一个包含条件逻辑的通用函数：

```typescript
// 好：每个函数可独立 mock
const api = {
  getUser: (id) => fetch(`/users/${id}`),
  getOrders: (userId) => fetch(`/users/${userId}/orders`),
  createOrder: (data) => fetch('/orders', { method: 'POST', body: data }),
};

// 差：mock 需要在 mock 内部包含条件逻辑
const api = {
  fetch: (endpoint, options) => fetch(endpoint, options),
};
```

SDK 方法意味着：
- 每个 mock 返回一种特定数据形状
- 测试设置中没有条件逻辑
- 更容易看出测试覆盖了哪些端点
- 每个端点都有类型安全保障
