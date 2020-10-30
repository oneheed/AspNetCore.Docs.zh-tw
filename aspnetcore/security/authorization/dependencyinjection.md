---
title: ASP.NET Core 中的需求處理常式中的相依性插入
author: rick-anderson
description: 瞭解如何使用相依性插入將授權需求處理常式插入 ASP.NET Core 的應用程式。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 6598a9c9cfd1e6597fffcc1aa0c53fa493532458
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060257"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="afc94-103">ASP.NET Core 中的需求處理常式中的相依性插入</span><span class="sxs-lookup"><span data-stu-id="afc94-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="afc94-104">使用相依性[插入](xref:fundamentals/dependency-injection)進行設定時，必須在服務集合中[註冊授權處理常式](xref:security/authorization/policies#handler-registration)。</span><span class="sxs-lookup"><span data-stu-id="afc94-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration using [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="afc94-105">假設您有想要在授權處理常式內評估的規則存放庫，而且該存放庫已在服務集合中註冊。</span><span class="sxs-lookup"><span data-stu-id="afc94-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="afc94-106">授權會解析並將其插入至函式。</span><span class="sxs-lookup"><span data-stu-id="afc94-106">Authorization resolves and injects that into the constructor.</span></span>

<span data-ttu-id="afc94-107">例如，使用 ASP。NET 的記錄基礎結構，插入 `ILoggerFactory` 處理常式中。</span><span class="sxs-lookup"><span data-stu-id="afc94-107">For example, to use ASP.NET's logging infrastructure, inject `ILoggerFactory` into the handler.</span></span> <span data-ttu-id="afc94-108">這類處理常式可能看起來像下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="afc94-108">Such a handler might look like the following code:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="afc94-109">先前的處理常式可以在任何 [服務存留期](/dotnet/core/extensions/dependency-injection#service-lifetimes)註冊。</span><span class="sxs-lookup"><span data-stu-id="afc94-109">The preceding handler can be registered with any [service lifetime](/dotnet/core/extensions/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="afc94-110">下列程式碼會使用 `AddSingleton` 來註冊先前的處理常式：</span><span class="sxs-lookup"><span data-stu-id="afc94-110">The following code uses `AddSingleton` to register the preceding handler:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="afc94-111">當應用程式啟動時，會建立處理常式的實例，而 DI 會將註冊的 `ILoggerFactory` 插入至函式。</span><span class="sxs-lookup"><span data-stu-id="afc94-111">An instance of the handler is created when the app starts, and DI injects the registered `ILoggerFactory` into the constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="afc94-112">使用 Entity Framework 的處理常式不應該註冊為 singleton。</span><span class="sxs-lookup"><span data-stu-id="afc94-112">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
