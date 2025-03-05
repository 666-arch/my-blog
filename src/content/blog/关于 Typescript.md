---
author: Huang Chao
pubDatetime: 2023-03-23T11:18:53Z
modDatetime: 2023-03-24T01:22:56.066Z
title: "关于 Typescript"
featured: true
draft: false
tags:
  - Typescript
description: "typescript碎片化"
---

### 为什么选择 TypeScript？

- 代码的健壮性更强
- 在和服务端进行通信时，能校准类型参数
- 符合现代前端开发趋势，弱类型转强类型语言
- 能有更好的开发体验

### any、unknown、never的区别？

- any

  > 没有任何限制的一个类型，只要语法正确，编译也会通过，如果你需要做一些js到ts的代码迁移，可以通过 any
  > 快速的推荐，避免庞大的类型错误情况

- unknown

  > 处理不确定的一些类型，但是它会保留原类型的学习，所以它是一个安全的类型，需要进行类型断言去通过类型检查

- never
  > 表示不可能出现的值，比如抛出错误的函数返回值

### 如何避免过度的使用 any？

- 适当的使用 `unknown` 守卫来代替 `any`
- 开启 `严格模式`，禁用隐式的 `any`
- 使用 `泛型` 替代

### `interface` 和 `type` 区别？

> interface 可通过 `extend` 关键字实现继承操作，对象类型优先使用哦 `interface` 便于更好的扩展

> type 可以定义联合类型（A | B）和 交叉类型（A & B）用来合并属性

```ts
type Admin = User & { permissions: string[] }; //合并属性
```

### 泛型是什么？作用？

我的理解就是泛指很多个类型 就是泛型。最大的作用避免了过度使用 `any`，可以在你不确定类型的时候去使用它，你可以在 类、函数、接口的参数类型中
去定义一个泛型。这一也可以提高类型的安全性

```ts
function identity<T>(arg: T): T {
  return arg;
}
//使用
const num = identity<number>(66); //显式的指定类型
const str = identity("str"); //自动类型推断为 string
```

### 你这么处理第三方TS类型缺少定义的情况？

#### 第一种方式：

在 `src` 目录下的 `.d.ts` 中通过 `declare` 关键字 声明该模块，例如“

```ts
declare module "type-lib";
```

#### 第二种方式：

在 `tsconfig.json` 关键配置项中配置
