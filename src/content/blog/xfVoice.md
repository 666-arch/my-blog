---
author: Huang Chao
pubDatetime: 2024-9-25T16:18:53Z
modDatetime: 2024-9-27T06:05:56.066Z
title: "React中如何接入科大讯飞语音流式Websocket API"
featured: true
draft: false
tags:
  - react
description: 实现浏览器语音交互（语音转文本）（文本转音频）
---

# 前言

&nbsp;&nbsp;&nbsp;&nbsp;语音听写：主要采用浏览器内置的语音系统，并基于科大讯飞流式 `API` 进行开发。它用于1分钟内的即时语音转文本技术，支持实时返回识别
结果，达到一边上传音频一边获得识别结果，它支持8k和16k采样率，16bit和单声道pcm的音频格式，单次静默时长最到可持续60s。
还支持配置多种小语种等高阶功能。

&nbsp;&nbsp;&nbsp;&nbsp;语音合成：主要是将文字消息转换为音频信息，具体可以表现为通过浏览器audio原件，将音频发送至
audio元件以此来在页面中播报对应的语音内容。

&nbsp;&nbsp;&nbsp;&nbsp;该语音能力是通过 `Websocket API` 的方式提供给开发者一个通用的接口。`Websocket API`
具备流式传输的能力，适用于需要流式数据传输
的AI服务场景，比如边说话边识别，相较于SDK，API具有轻量级，跨语言的特点。相较于 `HTTP API`，`Websocket API` 协议具有原生支持跨域的优势。

如下图：

![xfXming.png](../../assets/images/xfXmind.png)

# 语音识别

这是一个语音转文本的过程

接口调用流程步骤如下：

- 通过接口密钥基于 `hmac-sha256` 计算签名，向服务器端发送Websocket协议握手请求
- 握手成功后，客户端通过websocket连接同时上传和接收数据。数据上传完毕，客户端需要上传以此数据结束标识
- 接收到服务器端的结果全部返回标识后断开Websocket连接

## 接口鉴权

在握手阶段，请求方需要对请求进行签名，服务端通过前面来校验请求的合法性
|参数|类型|说明|是否必须|
|:---:|:---:|:---:|:---:|
|host|string|请求主机|是|
|date|string|当前时间戳，RFC123格式|是|
|authorization|string|使用base64编码签名相关的信息|是|

## 如何二次集成

> 注意：需要提前获取到官方提供的鉴权密钥（根据需要选择套餐购买）

定义一个 `DiscernClass` 公共类

```ts
import Crypto from "crypto-js";
import EventEmitter from "events";

interface IAIOptions {
  APPID: string;
  APIKEY: string;
  APISECRET: string;
}

export default class DiscernClass extends EventEmitter {
  private APP_ID: string;
  private API_KEY: string;
  private API_SECRET: string;
  private seconds: number = 0;
  private ws?: WebSocket;
  private recorder?: RecorderManager;
  private countdownInterval?: NodeJS.Timeout;
  private resultText: string = ""; //识别结果（根据需要是否暴露）
  constructor(options: IAIOptions) {
    super();
    this.APP_ID = options.APPID;
    this.API_KEY = options.APIKEY;
    this.API_SECRET = options.APISECRET;
    this.recorder = new RecorderManager("/kfspeech/discern"); //单独配置到项目下，不然可能会出现空引用情况
  }

  private getWebSocketUrl(): string {
    const url = "xxx"; //具体见文档
    const host = "xxx";
    const apikey = this.API_KEY;
    const apiSecret = this.API_SECRET;
    const date = new Date().toUTCString();
    const algorithm = "hmac-sha256";
    const headers = "host date request-line";
    const signatureOrigin = `host: ${host}\ndate: ${date}\nGET /v2/iat HTTP/1.1`;
    const signatureSha = CryptoJS.HmacSHA256(signatureOrigin, apiSecret);
    const signature = CryptoJS.enc.Base64.stringify(signatureSha);
    const authorizationOrigin = `api_key="${apiKey}", algorithm="${algorithm}", headers="${headers}", signature="${signature}"`;
    const authorization = btoa(authorizationOrigin);
    return `${url}?authorization=${authorization}&date=${date}&host=${host}`;
  }
}
```
