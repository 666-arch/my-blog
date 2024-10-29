---
author: Huang Chao
pubDatetime: 2022-12-05T16:18:53Z
modDatetime: 2022-12-26T06:05:56.066Z
title: "使用.NET Core 3.x 构建 RESTFUL Api"
slug: use-netcore-api-1
featured: true
draft: false
tags:
  - .Net
description: 首先需要明确一点的就是REST Api它不是一个标准，而是一种架构风格.
---

## 什么是WebApi?

- WebApi通常是指“使用HTTP协议并通过网络调用的API”，由于它使用了HTTP协议，所以需要通过URI信息来指定端点
- WebApi就是一个Web系统，通过访问URI可以与其进行信息交互
- 而常用的MVC模式是主要用来构建UI的架构模式。

## MVC映射为API？

- Model：它赋值处理程序数据的逻辑
- View：它是程序里复制展示数据的那部分。构建API的时候，View就是数据或资源的展示。通常使用JSON格式
- Controller，它复负责View和Model之间的交互
- 💡：需要注意的是，在配置服务的时候在core3.0以前可能写的是AddMvc，但是这个服务涉及了View视图以及TagHelper的一些功能，所以在做WebApi的时候用不到

```C#
public void ConfigureServices(IServiceCollection services)
{
    //services.AddMvc(); core 3.0以前是这样写的，这个服务包括了    TageHelper等 WebApi不需要的东西，所有3.0以后可以不这样写
    services.AddControllers();
}
```

## 管道?

- 就是客户端通过一些指令指向服务器端，在这个过程中呢，会经过一些手动配置的中间件，比如说路由中间件、静态资源中间件
- 从客户端出发到服务器端，将数据处理后，再由服务器端原路返回到客户端这样的一个过程
- 但是在请求的过程中也不排除中间件出现短路的情况，这样也就不会进入到第二个中间件了，而是直接返回到客户端

```C#
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    app.UseRouting();

    app.UseAuthorization();

    app.UseEndpoints(endpoints.MapControllers();
    });
}
```

## API对外合约? API消费者需要使用到三个概念

- 资源的标识（URI）
- HTTP方法（GET、POST）
- 有效载荷

API对外提供统一资源接口，业界对RESTful资源命名也有规则

## 关于如何标注路由属性 uri

```C#
using Microsoft.AspNetCore.Mvc;
using Routine.Api.Service;
using System;
using System.Threading.Tasks;

namespace Routine.Api.Controllers
{
    [ApiController] //好处：ApiController不是强制的
                    //1.会启用使用属性路由（Attribute Routing）
                    //2.自动HTTP 400响应
                    //3.推断参数的绑定源
                    //4.Multipart/form-data 请求推断
                    //5.错误状态代码的问题详细信息
    [Route("api/companies")]    //写法一
    //[Route("api/[controller]")]   //写法二：意思是相当于刨除了Controller后缀，获取前面的 Companies C可以是小写，如果你改名了那么你路由的uri也跟着变了（不建议这样写）
    public class CompaniesController:ControllerBase
    {
        private readonly ICompanyRepository _companyRepository;
        public CompaniesController(ICompanyRepository companyRepository)
        {
            _companyRepository = companyRepository ??
                                 throw new ArgumentNullException(nameof(companyRepository));
        }
        [HttpGet]
        //IActionResult定义了一些合约，它可以代表ActionResult返回的结果
        public async Task<IActionResult> GetCompanies()
        {
            var companies =await _companyRepository.GetCompaniesAsync();//读取出来的是List
            return Ok(companies);
        }

        [HttpGet("{companyId}")] // Controller标注了ApiController => uri=> api/companies/{companyId}
        public async Task<IActionResult> GetCompany(Guid companyId)
        {
            //判断该公司是否存在方法一：这种方法在处理并发请求时可能会出现错误，原因是查到之后，进行删除，进入company后也可能是404找不到了
            //var exists =await _companyRepository.CompanyExistsAsync(compamyId);
            //if (!exists)
            //{
            //    //不存在应该返回404
            //    return NotFound();
            //}
            var company = await _companyRepository.GetCompanyAsync(companyId);//读取出来的是List
            //方法二
            if (company==null)
            {
                return NotFound();
            }
            return Ok(company);
        }
    }
}
```
