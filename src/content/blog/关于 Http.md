---
author: Huang Chao
pubDatetime: 2023-03-23T11:18:53Z
modDatetime: 2023-03-24T01:22:56.066Z
title: "关于 Http 那些事"
featured: true
draft: true
tags:
  - http
description: "http碎片化"
---

### http 常见的状态码有哪些？

一共可以分为 5 中状态码

- 1xx 表示消息
- 2xx 表示成功
- 3xx 表示重定向
- 4xx 表示客户端错误
- 5xx 表示服务端错误

常见的状态码

- 200

  > http 请求成功

- 301

  > 永久重定向

- 304

  > 自上次请求，未修改的文件

- 400

  > 错误的请求

- 403

  > 请求被拒绝

- 404

  > 资源确实，接口不存在，或请求的资源不存在

- 500

  > 服务端位置错误

- 502
  > 网关错误

### 了解过强缓存和协商缓存吗？

首先 http 缓存大致可以分为两大类

- 强缓存：浏览器直接使用本地缓存
- 协商缓存：与服务器通信向服务器请求资源是否变更，未变更 `304 Not Modified` 使用本地缓存

#### `强缓存`

需要通过服务器设置 `Cache-Control` 并且设置为公开，设置 `max-age` 过期时间

#### NestJS

```ts
@Get('cached')
@Header('Cache-Control', 'public, max-age=10') // 缓存 10s
getCachedData() {
  return {
    data: 'This is cached data (强制缓存生效)',
    timestamp: new Date().toISOString(),
  };
}
```

浏览器在进行 `http` 请求时 会在 `Headers` 中携带 `Cache-Control`

`Cache-Control: public, max-age=10`

> 上述代码中，如果浏览器在 `10s` 内再次发送 http 请求，会从浏览器中直接读取第一期请求的缓存，不会发送到服务器

`协商缓存（ETag）`

#### NestJS

```ts
@Get('etag')
getEtag(@Req() req: Request, @Res() res: Response){
  const clientEtag = req.headers['if-none-match'];
  // 对比 ETag
  if (clientEtag === this.etag) {
    return res.status(304).end(); // 资源未修改
  }

  // 返回新内容并设置 ETag
  res.setHeader('ETag', this.etag);
  res.send(this.data);
}
```

### Http方法 Get 和 Post？

`Get`

- 一般是向服务器请求资源和数据
- 通过 URL 传输 查询字符串，数量大小有显示
- 传输的URL明文显示，数据不安全

`Post`

- 向服务器提交表单数据
- 通过请求主体传递数据，不易被篡改和缓存
- 提交的数据不暴露，通过报文传递更加安全
