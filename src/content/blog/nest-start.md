---
author: Huang Chao
pubDatetime: 2024-11-16T10:16:22Z
modDatetime: 2024-11-16T11:26:56Z
title: "Nest 第一步"
featured: true
tags:
  - Nest
description: Nest 探索之路
---

## 安装

使用 `Nest命令行接口` 设置新项目，在安装 `npm` 后，你可以在操作系统终端使用一下命令去创建一个新的 `Nest` 项目，这里需要全局安装下 `Nest CLI`

```
$ npm i -g @nestjs/cli
$ nest new project-name
```

## 入口

`main.ts` 可以理解为这是一个入口文件，很多后端语言包括 `java`，`C#`都有对应的main入口文件，主要是根据其入口函数文件，针对服务进行一系列的初始化处理。

```ts
import { NestFactory } from "@nestjs/core";
import { AppModule } from "./app.module";

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  await app.listen(3000);
}
bootstrap();
```

`NestFactory` 是一个核心类，公开了一些运行创建应用实例的静态方法。

> Creates an instance of NestApplication.<br>
> 形参: module – Entry (root) application module class <br>options – List of options to initialize NestApplication <br>
> 返回值:A promise that, when resolved, contains a reference to the NestApplication instance.<br>

`create` 是一个用于创建 `NestApplication`的实例，存在两个参数分别是 `module`、`options`，其中 `options`非必传
返回值则是一个`promise`向外抛出一个示例结果供开发者调用。它内部实现了 `INestApplication` 接口。在上述代码中，
只是启动了端口号为`3000`的HTTP监听器，它让应用等待入站`HTTP`请求

```nest
create<T extends INestApplication = INestApplication>(module: any, options?: NestApplicationOptions): Promise<T>;
```

## AppModule

可以理解为NestJS框架中，`app.module.ts`文件是应用持续的根模块（Root Module）,定义了应用的入口和模块结构。
`@Module`针对装饰器中的 `imports`、`controllers`、`providers`属性加以说明

```ts
import { Module } from "@nestjs/common";
import { AppController } from "./app.controller";
import { AppService } from "./app.service";

@Module({
  imports: [],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

### imports

该属性可以在此导入其他模块的 `Module`。假如你现在存在一个`AuthModule`，处理用户的权限认证相关逻辑，你可以在此
模块中进行导入，也可导入第三方类似`ORM`插件，数据库逻辑字符串等，已便为该模块提供对应的服务。

### controllers

该属性主要用于接收客户端传入的请求体（GET、POST、Delete、Put），经过一系列处理后，返回对应的结果也就是 `response` 提供给客户端，
并且也可以注入其他模块的 controllers，共享其他模块的方法。

### providers

该模块主要是用于封装业务数据逻辑，我想可以理解为这是一层 `service`，是 `service` 服务的提供者，
并通过依赖注入的机制共享给其他组件。
