---
name: migrate-to-shoehorn
description: 将测试文件中的 `as` 类型断言迁移为 @total-typescript/shoehorn。适用于用户提到 shoehorn、希望替换测试中的 `as`，或需要部分测试数据时。
---

# 迁移至 Shoehorn

## 为什么使用 shoehorn？

`shoehorn` 允许在测试中传入部分数据，同时满足 TypeScript。它以类型安全的替代方案取代 `as` 断言。

**仅限测试代码。** 绝不可在生产代码中使用 shoehorn。

在测试中使用 `as` 的问题：

- 已被训练为不应使用它
- 必须手动指定目标类型
- 对有意传入的错误数据使用双重断言（`as unknown as Type`）

## 安装

```bash
npm i @total-typescript/shoehorn
```

## 迁移模式

### 只需要少量属性的大对象

迁移前：

```ts
type Request = {
  body: { id: string };
  headers: Record<string, string>;
  cookies: Record<string, string>;
  // ...20 more properties
};

it("gets user by id", () => {
  // Only care about body.id but must fake entire Request
  getUser({
    body: { id: "123" },
    headers: {},
    cookies: {},
    // ...fake all 20 properties
  });
});
```

迁移后：

```ts
import { fromPartial } from "@total-typescript/shoehorn";

it("gets user by id", () => {
  getUser(
    fromPartial({
      body: { id: "123" },
    }),
  );
});
```

### `as Type` → `fromPartial()`

迁移前：

```ts
getUser({ body: { id: "123" } } as Request);
```

迁移后：

```ts
import { fromPartial } from "@total-typescript/shoehorn";

getUser(fromPartial({ body: { id: "123" } }));
```

### `as unknown as Type` → `fromAny()`

迁移前：

```ts
getUser({ body: { id: 123 } } as unknown as Request); // wrong type on purpose
```

迁移后：

```ts
import { fromAny } from "@total-typescript/shoehorn";

getUser(fromAny({ body: { id: 123 } }));
```

## 各方法的适用情形

| 函数        | 使用场景                                           |
| --------------- | -------------------------------------------------- |
| `fromPartial()` | 传入仍可通过类型检查的部分数据           |
| `fromAny()`     | 传入有意错误的数据（保留自动补全） |
| `fromExact()`   | 强制使用完整对象（之后可替换为 fromPartial）    |

## 工作流

1. **收集需求**，询问用户：
   - 哪些测试文件中的 `as` 断言造成了问题？
   - 是否在处理只关心少数属性的大对象？
   - 是否需要为了错误测试而传入有意错误的数据？

2. **安装并迁移**：
   - [ ] 安装：`npm i @total-typescript/shoehorn`
   - [ ] 查找含有 `as` 断言的测试文件：`grep -r " as [A-Z]" --include="*.test.ts" --include="*.spec.ts"`
   - [ ] 用 `fromPartial()` 替换 `as Type`
   - [ ] 用 `fromAny()` 替换 `as unknown as Type`
   - [ ] 添加来自 `@total-typescript/shoehorn` 的导入
   - [ ] 运行类型检查进行验证
