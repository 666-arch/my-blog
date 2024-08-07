---
author: Huang Chao
pubDatetime: 2024-01-22T10:18:53Z
modDatetime: 2023-02-06T13:15:56.066Z
title: "心动dto"
featured: false
tags:
  - nest
description: dto在程序中作用建议
---

## 基础概念

- 一种数据传输对象 `Data Transfer Object`
- 一种建立在业务逻辑层和客户端组件层之间的桥梁

## .net 中 `dto` 的传统作用

这是一个基础 `USER` 信息 dto

- 用户id
- 用户邮箱
- 密码

这些基础的字段较为都是大多数程序需要收集到的数据字段

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace BlogSystem.Dto
{
    public class UserInformation
    {
        public Guid Id { get; set; }
        public string Eamil { get; set; }
        public string Password { get; set; }
        public string NickName { get; set; }
        public string ImagePath { get; set; }
        public DateTime CreateTime { get; set; }
        public string PersonalDescription { get; set; }
        public int FansCount { get; set; }
        public int FocusCount { get; set; }
        public bool IsFreeze { get; set; }
    }
}
```
