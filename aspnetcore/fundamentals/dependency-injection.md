---
title: .NET Core 中的相依性插入
author: rick-anderson
description: 了解 ASP.NET Core 如何實作相依性插入以及如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: 0d8b349d0381e2902907ea841e07bbc96db5b847
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130633"
---
# <a name="dependency-injection-in-aspnet-core"></a>.NET Core 中的相依性插入

::: moniker range=">= aspnetcore-3.0"

作者： [Kirk Larkin](https://twitter.com/serpent5)、 [Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。

如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。

如需選項之相依性插入的詳細資訊，請參閱 <xref:fundamentals/configuration/options> 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="overview-of-dependency-injection"></a>相依性插入概觀

「相依性」** 是另一個物件所需的任何物件。 檢查具有 `WriteMessage` 方法的下列 `MyDependency` 物件，應用程式中的其他類別相依於此物件：

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

您可以建立 `MyDependency` 類別的執行個體，讓 `WriteMessage` 方法可供類別使用。 `MyDependency` 類別是 `IndexModel` 類別的相依性：

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

該類別會建立 `MyDependency` 執行個體並直接相依於該執行個體。 程式碼相依性（例如前一個範例）有問題，應避免下列原因：

* 若要將 `MyDependency` 取代為不同的實作，必須修改該類別。
* 若 `MyDependency` 有相依性，那些相依性必須由該類別設定。 在具有多個相依於 `MyDependency` 之多個類別的大型專案中，設定程式碼在不同的應用程式之間會變得鬆散。
* 此實作難以進行單元測試。 應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。

相依性插入可透過下列方式解決這些問題：

* 使用介面或基底類別來將相依性資訊抽象化。
* 在服務容器中註冊相依性。 ASP.NET Core 提供內建服務容器 <xref:System.IServiceProvider>。 服務會在應用程式的 `Startup.ConfigureServices` 方法中註冊。
* 將服務「插入」** 到服務使用位置之類別的建構函式。 架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。

在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 介面定義了服務提供給應用程式的方法：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

這個介面是由具象型別 `MyDependency` 所實作：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

在範例應用程式中，`IMyDependency` 服務是使用具象型別 `MyDependency` 所註冊。 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>方法會以限定範圍存留期（單一要求的存留期）註冊服務。 將在此主題稍後將說明[服務存留期](#service-lifetimes)。

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

在範例應用程式中，會要求 `IMyDependency` 執行個體並使用它來呼叫服務的 `WriteMessage` 方法：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

使用 DI 模式：

* 控制器不會使用具體類型 `MyDependency` ，只有介面 `IMyDependency` 。 這可讓您輕鬆地變更控制器所使用的執行，而不需要修改控制器。
* 控制器不會建立的實例 `MyDependency` ，它是由 DI 容器所建立。

您 `IMyDependency` 可以使用內建的記錄 API 來改善介面的執行：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

更新的 `ConfigureServices` 方法會註冊新的 `IMyDependency` 執行：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

`MyDependency2`<xref:Microsoft.Extensions.Logging.ILogger`1>在此函數中要求。 以鏈結方式使用相依性插入並非不尋常。 每個要求的相依性接著會要求其自己的相依性。 容器會解決圖形中的相依性，並傳回完全解析的服務。 必須先解析的相依性集合組通常稱為「相依性樹狀結構」**、「相依性圖形」** 或「物件圖形」**。

`ILogger<TCategoryName>`是[架構提供的服務](#framework-provided-services)。

容器會 `ILogger<TCategoryName>` 利用[ (泛型) 開放式類型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)來解決，而不必註冊每個[ (泛型) 結構化類型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。

在相依性插入術語中，服務：

* 通常是提供服務給應用程式中其他程式碼的物件，例如 `IMyDependency` 服務。
* 與 web 服務無關，雖然服務可能會使用 web 服務。

此架構提供健全的[記錄](xref:fundamentals/logging/index)系統。 這 `IMyDependency` 是為了示範基本的 DI 而撰寫的，而不是用來執行記錄。 大部分的應用程式都不需要撰寫記錄器。 下列程式碼示範如何使用預設記錄，而不需要在中註冊任何服務 `ConfigureServices` ：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

使用上述程式碼，不需要更新， `ConfigureServices` 因為[記錄](xref:fundamentals/logging/index)是由架構所提供：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a>插入至啟動的服務

`Startup`使用泛型主機 () 時，只有下列服務類型可以插入至此函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

服務可以插入 `Startup.Configure` ：

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

如需詳細資訊，請參閱 <xref:fundamentals/startup> 和[在啟動時存取](xref:fundamentals/configuration/index#access-configuration-in-startup)設定。

## <a name="register-additional-services-with-extension-methods"></a>以擴充方法註冊其他服務

當服務集合擴充方法可用來註冊服務時：

* 慣例是使用單一 `Add{SERVICE_NAME}` 擴充方法來註冊該服務所需的所有服務。
* 也會註冊相依的服務。

下列程式碼是由 Razor 使用個別使用者帳戶的頁面範本所產生，並說明如何使用擴充方法和，將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> ：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

如需詳細資訊，請參閱 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 和 <xref:security/authentication/identity>。

如需撰寫擴充方法來註冊服務的指示，請參閱[結合服務集合](#csc)一節。

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a>服務存留期

為每個已註冊的服務選擇適當的存留期。 ASP.NET Core 服務可以使用下列存留期進行設定：

### <a name="transient"></a>暫時性

每次從服務容器要求暫時性存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 時都會建立它們。 此存留期最適合用於輕量型的無狀態服務。

在處理要求的應用程式中，會在要求結束時處置暫時性服務。

### <a name="scoped"></a>具範圍

具範圍存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 會在每次用戶端要求 (連線) 時建立一次。

使用 Entity Framework Core 時， <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 擴充方法預設會 `DbContext` 註冊具有限定範圍存留期的類型。

在處理要求的應用程式中，會在要求結束時處置已設定範圍的服務。

在中介軟體中使用具有下列其中一種方法的範圍服務：

* 將服務插入 `Invoke` 或方法中 `InvokeAsync` 。 插入 by 函式[插入](xref:mvc/controllers/dependency-injection#constructor-injection)會在執行時間擲回例外狀況，因為它會強制服務的行為就像 singleton 一樣。 [存留期和註冊選項](#lifetime-and-registration-options)中的範例會使用 `InvokeAsync` 方法。
* 以[Factory 為基礎的中介軟體](<xref:fundamentals/middleware/extensibility>)。 <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 擴充方法會檢查中介軟體的已註冊類型是否實作 <xref:Microsoft.AspNetCore.Http.IMiddleware>。 如果是，系統會使用容器中已註冊的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 執行個體來解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 實作，而不是使用以慣例為基礎的中介軟體啟用邏輯。 中介軟體會註冊為應用程式服務容器中的範圍服務或暫時性服務。

如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。

請勿***從 singleton 解析已***設定範圍的服務。 處理後續要求時，它可能會導致服務有不正確的狀態。 可以：

* 從限定範圍或暫時性服務解析單一服務。
* 從另一個已設定範圍或暫時性的服務解析已設定範圍的服務。

根據預設，在開發環境中，從較長存留期的另一個服務解析服務時，會擲回例外狀況。 如需詳細資訊，請參閱[範圍驗證](#sv)。

### <a name="singleton"></a>單一

建立單一存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) ：

* 第一次要求時。
* 由開發人員在將實實例直接提供給容器時。 這種方法很少需要。

每個後續要求都會使用相同的執行個體。 如果應用程式需要單一行為，請允許服務容器管理服務的存留期。 請勿執行單一設計模式，並提供程式碼來處置 singleton。 服務永遠不應由從容器解析服務的程式碼來處置。 如果類型或 factory 註冊為 singleton，容器將會自動處置 singleton。

單一服務必須具備執行緒安全，而且通常用於無狀態服務。

在處理要求的應用程式中， <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 會在應用程式關閉時處置單一服務。 因為在關閉應用程式之前不會釋放記憶體，所以必須考慮使用單一實例的記憶體。

> [!WARNING]
> 請勿***從 singleton 解析已***設定範圍的服務。 處理後續要求時，它可能會導致服務有不正確的狀態。 從限定範圍或暫時性的服務解析單一服務是很好的。

## <a name="service-registration-methods"></a>服務註冊方法

服務註冊擴充方法提供在特定案例中很有用的多載。
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| 方法 | 自動<br>object<br>處置 | 多個<br>實作 | 傳遞引數 |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>範例：<br>`services.AddSingleton<IMyDep, MyDep>();` | 是 | 是 | 否 |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>範例：<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | 是 | 是 | 是 |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>範例：<br>`services.AddSingleton<MyDep>();` | 是 | 否 | 否 |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>範例：<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | 否 | 是 | 是 |
| `AddSingleton(new {IMPLEMENTATION})`<br>範例：<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | 否 | 否 | 是 |

如需類型處置的詳細資訊，請參閱[＜服務處置＞](#disposal-of-services)一節。 多個實作的常見案例是[模擬測試類型](xref:test/integration-tests#inject-mock-services)。

`TryAdd{LIFETIME}`如果尚未註冊此服務，方法會註冊該服務。

在下列範例中，第一行會為 `IMyDependency` 註冊 `MyDependency`。 第二行則沒有任何作用，因為 `IMyDependency` 已具有註冊的實作：

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

如需詳細資訊，請參閱

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

如果尚未有*相同類型*的執行， [TryAddEnumerable (ServiceDescriptor) ](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)方法會註冊服務。 多個服務會透過 `IEnumerable<{SERVICE}>` 解析。 註冊服務時，如果尚未加入相同類型的實例，開發人員就應該加入該實例。 一般而言，程式庫作者會使用 `TryAddEnumerable` 來避免在容器中註冊多個執行複本。

在下列範例中，第一行會為 `IMyDep1` 註冊 `MyDep`。 第二行會為 `IMyDep2` 註冊 `MyDep`。 第三行則沒有任何作用，因為 `IMyDep1` 已具有 `MyDep` 的已註冊實作：

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

服務註冊通常是獨立順序，但在註冊相同類型的多個執行中時除外。

`IServiceCollection`是的集合 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 。 下列程式碼示範如何使用函式來新增服務：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

`Add{LIFETIME}`方法會使用相同的方法。 例如，請參閱[要 AddScoped 的原始程式碼](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)。

### <a name="constructor-injection-behavior"></a>建構函式插入行為

服務可以透過兩個機制來解析：

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:
  * 在相依性插入容器中建立沒有服務註冊的物件。
  * 與架構功能搭配使用，例如標籤協助[程式、MVC](xref:mvc/views/tag-helpers/intro)控制器和[模型](xref:mvc/models/model-binding)系結器。

建構函式可以接受不是由相依性插入提供的引數，但引數必須指派預設值。

當服務由或解析時，程式的 `IServiceProvider` `ActivatorUtilities` [插入](xref:mvc/controllers/dependency-injection#constructor-injection)需要*公用*的函式。

當服務由解析時 `ActivatorUtilities` ，程式的[插入](xref:mvc/controllers/dependency-injection#constructor-injection)只需要有一個適用的函式。 支援建構函式多載，但只能有一個多載存在，其引數可以藉由相依性插入而完成。

## <a name="entity-framework-contexts"></a>Entity Framework 內容

因為一般會將 Web 應用程式資料庫作業範圍設定為用戶端要求，所以通常會使用[具範圍存留期](#service-lifetimes)將 Entity Framework 內容新增至服務容器。 如果在註冊資料庫內容時， [AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)多載未指定存留期，則會將預設存留期設定為範圍。 指定存留期的服務不應該使用存留期比服務還短的資料庫內容。

## <a name="lifetime-and-registration-options"></a>留期和註冊選項

若要示範存留期和註冊選項之間的差異，請考慮使用下列介面將工作表示為具有識別碼的作業 `OperationId` 。 根據為下列介面設定作業服務存留期的方式，當類別要求時，容器會提供相同或不同的服務實例：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

介面是在 `Operation` 類別中實作。 `Operation`如果未提供 GUID 的最後4個字元，則此函式會產生它：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

在 `Startup.ConfigureServices` 中，每個類型都會根據其具名存留期新增至容器：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

範例應用程式會示範要求內和之間的物件存留期。 範例應用程式 `IndexModel` 和中介軟體會要求每種 `IOperation` 類型，並記錄 `OperationId` 下列各項：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

中介軟體類似于 `IndexModel` 並解析相同的服務：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

已設定範圍的服務必須在 `InvokeAsync` 方法中解析：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

記錄器輸出會顯示：

* 「暫時性」** 物件一律不同。 `OperationId`和中介軟體的暫時性值不同 `IndexModel` 。
* 有*範圍*的物件在每個要求中都相同，但在每個要求中都不同。
* 每個要求的*單一*物件都是相同的。

若要減少記錄輸出，請在檔案的*appsettings.Development.js*中設定 "記錄： LogLevel： Microsoft： Error"：

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a>從主要呼叫服務

使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>，以解析應用程式範圍中的範圍服務。 這種方法可在啟動時存取已設定範圍的服務，以執行初始化工作：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

上述程式碼來自在頁面教學課程中[新增種子初始化運算式](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) Razor 。

下列範例示範如何在 `Program.Main` 中取得 `IMyDependency`：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a>範圍驗證

當應用程式在[開發環境](xref:fundamentals/environments)中執行並呼叫[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)來建立主機時，預設服務提供者會執行檢查以確認：

* 範圍服務不是直接或間接由開機服務提供者解析。
* 範圍服務不是直接或間接插入至單一服務。
* 暫時性服務不是直接或間接插入單次個體或範圍服務中。

根服務提供者會在呼叫 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 時建立。 當提供者啟動應用程式時，根服務提供者的存留期會對應到應用程式的存留期，並在應用程式關閉時處置。

範圍服務會由建立這些服務的容器處置。 如果已設定範圍的服務在根容器中建立，服務的存留期會有效地升級為 singleton，因為只有當應用程式關閉時，根容器才會處置它。 當呼叫 `BuildServiceProvider` 時，驗證服務範圍會攔截到這些情況。

如需詳細資訊，請參閱[範圍驗證](xref:fundamentals/host/web-host#scope-validation)。

## <a name="request-services"></a>要求服務

來自 `HttpContext`，在 ASP.NET Core 要求內提供的服務是透過 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公開。

要求服務代表您在應用程式中設定和要求的服務。 當物件指定相依性時，這些會由在 `RequestServices` 中找到的類型來滿足，而非 `ApplicationServices`。

一般而言，應用程式不應該直接使用這些屬性。 相反地，請透過類別的函式要求類別所需的類型，並允許架構插入相依性。 這會產生容易測試的類別。

ASP.NET Core 會根據要求建立範圍，並 `RequestServices` 公開限定範圍的服務提供者。 只要要求為作用中，所有已設定範圍的服務都有效。

> [!NOTE]
> 偏好要求相依性作為建構函式參數，而不要存取 `RequestServices` 集合。

## <a name="design-services-for-dependency-injection"></a>針對相依性插入設計服務

最佳做法是：

* 設計服務以使用相依性插入來取得其相依性。
* 避免具狀態的靜態類別和成員。 將應用程式設計成使用單一服務，以避免建立全域狀態。
* 避免直接在服務內具現化相依類別。 直接具現化會將程式碼耦合到特定實作。
* 讓應用程式類別維持在小型、情況良好且可輕鬆測試的狀態。

如果類別似乎有太多插入的相依性，這通常表示類別具有太多責任，而且違反[單一責任原則 (SRP) ](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。 將類別負責的某些部分移到新的類別，以嘗試重構類別。 請記住，頁面 Razor 頁面模型類別和 MVC 控制器類別應該專注于 UI 考慮。

### <a name="disposal-of-services"></a>處置服務

容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose*>。 服務永遠不應由從容器解析服務的任何程式碼來處置。 如果類型或處理站註冊為 singleton，則容器會處置 singleton。

在下列範例中，服務會建立服務容器並自動處置：

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

在每次重新整理索引頁面之後，debug 主控台會顯示下列輸出：

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a>服務容器不會建立服務
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
請考慮下列程式碼：

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

在上述程式碼中：

* 服務容器不會建立服務實例。
* 架構不知道預期的服務存留期。
* 架構不會自動處置服務。
* 如果服務未在開發人員程式碼中明確處置，它們會持續存在，直到應用程式關閉為止。

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>暫時性和共用實例的 IDisposable 指引

#### <a name="transient-limited-lifetime"></a>暫時性，有限的存留期

**案例**

應用程式需要在 <xref:System.IDisposable> 下列任一情況下具有暫時性存留期的實例：

* 實例會在根範圍中解析 (根容器) 。
* 應該在範圍結束之前處置實例。

**方案**

使用 factory 模式，在父範圍外建立實例。 在這種情況下，應用程式通常會有 `Create` 方法，直接呼叫最終型別的函式。 如果最終類型具有其他相依性，則 factory 可以：

* <xref:System.IServiceProvider>在其函式中接收。
* 使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 來具現化容器外的實例，同時使用容器來執行其相依性。

#### <a name="shared-instance-limited-lifetime"></a>共用實例，有限的存留期

**案例**

應用程式需要 <xref:System.IDisposable> 多個服務之間的共用實例，但 <xref:System.IDisposable> 應具有有限的存留期。

**方案**

註冊具有限定範圍存留期的實例。 使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 來啟動並建立新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 。 使用範圍的 <xref:System.IServiceProvider> 來取得所需的服務。 在存留期結束時處置範圍。

#### <a name="general-idisposable-guidelines"></a>一般 IDisposable 指導方針

* 不要向 <xref:System.IDisposable> 暫時性範圍註冊實例。 請改用 factory 模式。
* 請勿解析 <xref:System.IDisposable> 根範圍中的暫時性或範圍實例。 唯一的例外狀況是當應用程式建立/重新建立和處置時 <xref:System.IServiceProvider> ，這不是理想的模式。
* 透過 DI 接收相依性 <xref:System.IDisposable> 並不會要求接收者 <xref:System.IDisposable> 自行執行。 相依性的接收者不 <xref:System.IDisposable> 應呼叫 <xref:System.IDisposable.Dispose%2A> 該相依性上的。
* 範圍應該用來控制服務的存留期。 範圍不是階層式，而且範圍之間沒有特殊連接。

## <a name="default-service-container-replacement"></a>預設服務容器取代

內建的服務容器是設計用來滿足架構和大部分取用者應用程式的需求。 我們建議使用內建容器，除非您需要內建容器不支援的特定功能，例如：

* 屬性插入
* 根據名稱插入
* 子容器
* 自訂生命週期管理
* `Func<T>` 支援延遲初始設定
* 以慣例為基礎的註冊

下列協力廠商容器可以與 ASP.NET Core 應用程式搭配使用：

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [拉馬爾](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>執行緒安全

建立具備執行緒安全性的 singleton 服務。 如果 singleton 服務相依於暫時性服務，暫時性服務可能也需要具備執行緒安全性，取決於 singleton 如何使用它。

單一服務的 factory 方法（例如 AddSingleton (IServiceCollection 的第二個引數[ \<TService> ），Func \<IServiceProvider,TService>) ](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)不需要是安全線程。 就像型別 (`static`) 建構函式一樣，它一定會被單一執行緒呼叫一次。

## <a name="recommendations"></a>建議

* 不支援以 `async/await` 與 `Task` 為基礎的服務解析。 C # 不支援非同步函式。 建議的模式是以同步方式解析服務後使用非同步方法。
* 避免直接在服務容器中儲存資料與設定。 例如，使用者的購物車通常不應該新增至服務容器。 組態應該使用[選項模式](xref:fundamentals/configuration/options)。 同樣地，請避免只存在以允許存取某個其他物件的「資料持有者」物件。 最好是透過 DI 要求實際項目。
* 避免靜態存取服務。 例如，避免以靜態方式輸入[IApplicationBuilder microsoft.visualbasic.applicationservices.cantstartsingleinstanceexception](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ，以供) 的其他地方使用。
* 讓 DI factory 快速且同步。
* 避免使用「服務定位器模式」**。 例如，當您可以改用 DI 時，請勿叫用 <xref:System.IServiceProvider.GetService*> 來取得服務執行個體：

  **不正確：**

    ![不正確的程式碼](dependency-injection/_static/bad.png)

  **正確**：

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* 另一個要避免的服務定位器變化是插入在執行階段解析相依性的處理站。 這兩種做法都會混用[控制反轉](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。
* 避免以靜態方式存取 `HttpContext` (例如 [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。

<a name="ASP0000"></a>
* 避免 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> 在中呼叫 `ConfigureServices` 。 `BuildServiceProvider`當開發人員想要解析中的服務時，通常會呼叫 `ConfigureServices` 。 例如，假設您需要 go [從設定取得] 的情況 `LoginPath` 。 請避免下列程式碼：

  ![呼叫 BuildServiceProvider 的錯誤程式碼](~/fundamentals/dependency-injection/_static/badcodeX.png)

  在上圖中，選取底下的綠色波浪線會 `services.BuildServiceProvider` 顯示下列 ASP0000 警告：
    * ASP0000 從應用程式代碼呼叫 ' BuildServiceProvider ' 會產生一個額外的單一服務複本。 請考慮像是相依性插入服務作為「設定」參數的替代方案。

   呼叫 `BuildServiceProvider` 會建立第二個容器，它可以建立損毀的單次個體，並在多個容器中導致物件圖形的參考。 要取得的正確方式 `LoginPath` 是搭配使用選項模式與 DI：

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* 容器會攔截可處置的暫時性服務以供處置。 如果從最上層容器解析，這可能會變成記憶體流失。
* 啟用範圍驗證，以確保應用程式沒有範圍服務可捕捉單次個體。 如需詳細資訊，請參閱[範圍驗證](#scope-validation)。

就像所有的建議集，您可能會遇到需要忽略建議的情況。 例外狀況很罕見，大部分是在架構本身內的特殊案例。

DI 是靜態/全域物件存取模式的「替代」** 選項。 如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>DI 中的多租使用者建議模式

[Orchard Core](https://github.com/OrchardCMS/OrchardCore)提供多租使用者。 如需詳細資訊，請參閱[Orchard Core 檔](https://docs.orchardcore.net/en/dev/)。

https://github.com/OrchardCMS/OrchardCore.Samples如需如何使用 Orchard Core Framework 建立模組化和多租使用者應用程式的範例，而不含任何 CMS 特有的功能，請參閱範例應用程式。

## <a name="framework-provided-services"></a>架構提供的服務

`Startup.ConfigureServices`方法負責定義應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。 一開始， `IServiceCollection` 提供的會 `ConfigureServices` 根據主機的[設定方式](xref:fundamentals/index#host)，來擁有架構所定義的服務。 以 ASP.NET Core 範本為基礎的應用程式會有超過250個由架構註冊的服務。 下表列出架構註冊服務的小型範例。

| 服務類型 | 存留期 |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | 暫時性 |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | 單一 |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | 單一 |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | 單一 |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | 暫時性 |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | 單一 |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | 暫時性 |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | 單一 |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | 單一 |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | 單一 |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | 暫時性 |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | 單一 |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | 單一 |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | 單一 |

## <a name="additional-resources"></a>其他資源

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [在 ASP.NET Core 中處置 IDisposables 的四種方式](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [在 ASP.NET Core 使用 Dependency Injection 撰寫簡潔的程式碼 (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [明確相依性準則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [逆轉控制容器和相依性插入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) \(英文\)
* [How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (如何使用 ASP.NET Core DI 中的多個介面註冊服務)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者： [Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。

如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。

[查看或下載範例程式碼](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="overview-of-dependency-injection"></a>相依性插入概觀

「相依性」** 是另一個物件所需的任何物件。 檢查具有 `WriteMessage` 方法的下列 `MyDependency` 物件，應用程式中的其他類別相依於此物件：

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

您可以建立 `MyDependency` 類別的執行個體，讓 `WriteMessage` 方法可供類別使用。 `MyDependency` 類別是 `IndexModel` 類別的相依性：

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

該類別會建立 `MyDependency` 執行個體並直接相依於該執行個體。 程式碼相依性 (例如前一個範例) 有問題，因此基於下列原因應該避免使用：

* 若要將 `MyDependency` 取代為不同的實作，必須修改該類別。
* 若 `MyDependency` 有相依性，那些相依性必須由該類別設定。 在具有多個相依於 `MyDependency` 之多個類別的大型專案中，設定程式碼在不同的應用程式之間會變得鬆散。
* 此實作難以進行單元測試。 應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。

相依性插入可透過下列方式解決這些問題：

* 使用介面或基底類別來將相依性資訊抽象化。
* 在服務容器中註冊相依性。 ASP.NET Core 提供內建服務容器 <xref:System.IServiceProvider>。 服務會在應用程式的 `Startup.ConfigureServices` 方法中註冊。
* 將服務「插入」** 到服務使用位置之類別的建構函式。 架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。

在[範例應用程式](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 介面定義了服務提供給應用程式的方法：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

這個介面是由具象型別 `MyDependency` 所實作：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` 在其建構函式中會要求 <xref:Microsoft.Extensions.Logging.ILogger`1>。 以鏈結方式使用相依性插入並非不尋常。 每個要求的相依性接著會要求其自己的相依性。 容器會解決圖形中的相依性，並傳回完全解析的服務。 必須先解析的相依性集合組通常稱為「相依性樹狀結構」**、「相依性圖形」** 或「物件圖形」**。

`IMyDependency` 與 `ILogger<TCategoryName>` 必須在服務容器中註冊。 `IMyDependency` 是在 `Startup.ConfigureServices` 中註冊。 `ILogger<TCategoryName>` 是由記錄抽象基礎結構所註冊，所以它是預設由架構所註冊的[架構提供的服務](#framework-provided-services)。

容器會利用 [(泛型) 開放類型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) 解析 `ILogger<TCategoryName>`，讓您不需註冊每個 [(泛型) 建構的型別](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

在範例應用程式中，`IMyDependency` 服務是使用具象型別 `MyDependency` 所註冊。 註冊會將服務的存留期範圍限制為單一要求的存留期。 將在此主題稍後將說明[服務存留期](#service-lifetimes)。

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> 每個 `services.Add{SERVICE_NAME}` 擴充方法都會新增 (並且可能會設定) 服務。 例如，會 `services.AddMvc()` 加入服務 Razor 頁面，而 MVC 則需要。 建議應用程式遵循此慣例。 在 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空間中放置擴充方法，以封裝服務註冊群組。

如果服務的建構函式需要[內建類型](/dotnet/csharp/language-reference/keywords/built-in-types-table)，例如 `string`，可以使用[組態](xref:fundamentals/configuration/index)或[選項模式](xref:fundamentals/configuration/options)插入該類型：

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

服務執行個體是透過服務使用所在之類別建構函式所要求，而且會指派給私人欄位。 該欄位會視需要用來存取整個類別中的服務。

在範例應用程式中，會要求 `IMyDependency` 執行個體並使用它來呼叫服務的 `WriteMessage` 方法：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>插入至啟動的服務

`Startup`使用泛型主機 () 時，只有下列服務類型可以插入至此函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

服務可以插入 `Startup.Configure` ：

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

如需詳細資訊，請參閱<xref:fundamentals/startup>。

## <a name="framework-provided-services"></a>架構提供的服務

`Startup.ConfigureServices`方法負責定義應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。 一開始， `IServiceCollection` 提供的會 `ConfigureServices` 根據主機的[設定方式](xref:fundamentals/index#host)，來擁有架構所定義的服務。 以 ASP.NET Core 範本為基礎的應用程式，在架構中註冊數百項服務並不常見。 下表列出架構註冊服務的小型範例。

| 服務類型 | 存留期 |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | 暫時性 |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | 單一 |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | 單一 |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | 單一 |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | 暫時性 |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | 單一 |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | 暫時性 |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | 單一 |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | 單一 |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | 單一 |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | 暫時性 |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | 單一 |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | 單一 |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | 單一 |

## <a name="register-additional-services-with-extension-methods"></a>以擴充方法註冊其他服務

當可以使用服務集合擴充方法來註冊服務 (如果需要，也可以註冊其相依服務) 時，慣例是使用單一 `Add{SERVICE_NAME}` 擴充方法來註冊該服務要求的所有服務。 下列程式碼範例說明如何使用擴充方法[AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)和，將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

如需詳細資訊，請參閱 API 文件中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 類別。

## <a name="service-lifetimes"></a>服務存留期

為每個已註冊的服務選擇適當的存留期。 ASP.NET Core 服務可以使用下列存留期進行設定：

### <a name="transient"></a>暫時性

每次從服務容器要求暫時性存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 時都會建立它們。 此存留期最適合用於輕量型的無狀態服務。

在處理要求的應用程式中，會在要求結束時處置暫時性服務。

### <a name="scoped"></a>具範圍

具範圍存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 會在每次用戶端要求 (連線) 時建立一次。

在處理要求的應用程式中，會在要求結束時處置已設定範圍的服務。

> [!WARNING]
> 在中介軟體中使用具範圍服務時，請將該服務插入 `Invoke` 或 `InvokeAsync` 方法中。 不要透過函式[插入](xref:mvc/controllers/dependency-injection#constructor-injection)來插入，因為它會強制服務的行為就像 singleton 一樣。 如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。

### <a name="singleton"></a>單一

當第一次收到有關單一資料庫存留期服務的要求時 (或是當執行 `Startup.ConfigureServices` 且隨著服務註冊指定執行個體時)，即會建立單一資料庫存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)。 每個後續要求都會使用相同的執行個體。 若應用程式要求單一資料庫行為，建議您允許服務容器管理服務的存留期。 不要實作單一資料庫設計模式並為使用者提供可用來管理類別中物件存留期的程式碼。

在處理要求的應用程式中， <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 會在應用程式關閉時處置單一服務。

> [!WARNING]
> 從單一資料庫解析具範圍服務是非常危險的。 處理後續要求時，它可能會導致服務有不正確的狀態。

## <a name="service-registration-methods"></a>服務註冊方法

服務註冊擴充方法提供在特定案例中很有用的多載。

| 方法 | 自動<br>object<br>處置 | 多個<br>實作 | 傳遞引數 |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>範例：<br>`services.AddSingleton<IMyDep, MyDep>();` | 是 | 是 | 否 |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>範例：<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | 是 | 是 | 是 |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>範例：<br>`services.AddSingleton<MyDep>();` | 是 | 否 | 否 |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>範例：<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | 否 | 是 | 是 |
| `AddSingleton(new {IMPLEMENTATION})`<br>範例：<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | 否 | 否 | 是 |

如需類型處置的詳細資訊，請參閱[＜服務處置＞](#disposal-of-services)一節。 多個實作的常見案例是[模擬測試類型](xref:test/integration-tests#inject-mock-services)。

如果還未註冊任何實作，則 `TryAdd{LIFETIME}` 方法只會註冊服務。

在下列範例中，第一行會為 `IMyDependency` 註冊 `MyDependency`。 第二行則沒有任何作用，因為 `IMyDependency` 已具有註冊的實作：

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

如需詳細資訊，請參閱

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

如果還沒有「相同類型的」** 實作，則 [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法只會註冊服務。 多個服務會透過 `IEnumerable<{SERVICE}>` 解析。 註冊服務時，如果尚未新增相同類型的執行個體，則開發人員只會希望新增執行個體。 程式庫作者通常會使用此方法來避免註冊容器中某個執行個體的兩個複本。

在下列範例中，第一行會為 `IMyDep1` 註冊 `MyDep`。 第二行會為 `IMyDep2` 註冊 `MyDep`。 第三行則沒有任何作用，因為 `IMyDep1` 已具有 `MyDep` 的已註冊實作：

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>建構函式插入行為

服務可以透過兩個機制來解析：

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允許在相依性插入容器中不註冊服務時，建立物件。 搭配使用者面向抽象 (例如標籤協助程式、MVC 控制器與模型繫結器) 使用 `ActivatorUtilities`。

建構函式可以接受不是由相依性插入提供的引數，但引數必須指派預設值。

當服務由或解析時，程式的 `IServiceProvider` `ActivatorUtilities` [插入](xref:mvc/controllers/dependency-injection#constructor-injection)需要*公用*的函式。

當服務由解析時 `ActivatorUtilities` ，程式的[插入](xref:mvc/controllers/dependency-injection#constructor-injection)只需要有一個適用的函式。 支援建構函式多載，但只能有一個多載存在，其引數可以藉由相依性插入而完成。

## <a name="entity-framework-contexts"></a>Entity Framework 內容

因為一般會將 Web 應用程式資料庫作業範圍設定為用戶端要求，所以通常會使用[具範圍存留期](#service-lifetimes)將 Entity Framework 內容新增至服務容器。 如果在註冊資料庫內容時， [AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)多載未指定存留期，則會將預設存留期設定為範圍。 指定存留期的服務不應該使用存留期比服務還短的資料庫內容。

## <a name="lifetime-and-registration-options"></a>留期和註冊選項

為了示範這些存留期和註冊選項之間的差異，請考慮下列介面，以具有唯一識別碼 `OperationId` 的「作業」代表工作。 視如何針對下列介面設定作業服務存留期而定，當類別要求時，容器會提供相同或不同的服務執行個體：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

介面是在 `Operation` 類別中實作。 若未提供 GUID，`Operation` 建構函式會產生 GUID：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

會註冊相依於每種其他 `Operation` 類型的 `OperationService`。 當透過相依性插入要求 `OperationService` 時，它會根據相依服務的存留期擷取每個服務的新執行個體或現有執行個體。

* 當因收到來自容器的要求而建立暫時性服務時，`IOperationTransient` 服務的 `OperationId` 會與 `OperationService` 的 `OperationId` 不同。 `OperationService` 會收到 `IOperationTransient` 類別的新執行個體。 新執行個體會產生不同的 `OperationId`。
* 當因用戶端要求而建立具範圍的服務時，`IOperationScoped` 服務與用戶端要求中 `OperationService` 的 `OperationId` 皆相同。 在不同的用戶端要求中，兩個服務的 `OperationId` 值都不同。
* 當建立一次 singleton 與 singleton 執行個體服務，並在所有用戶端要求與所有服務中使用時，`OperationId` 在所有服務要求中會固定不變。

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

在 `Startup.ConfigureServices` 中，每個類型都會根據其具名存留期新增至容器：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

`IOperationSingletonInstance` 服務使用具有 `Guid.Empty` 已知識別碼的特定執行個體。 很明顯此型別是使用中 (其 GUID 是所有區域)。

範例應用程式示範個別要求內與個別要求之間的物件存留期。 範例應用程式的 `IndexModel` 會要求每種 `IOperation` 型別與 `OperationService`。 頁面接著會透過屬性指派來顯示所有頁面模型類別與服務的 `OperationId` 值：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

下面兩個輸出顯示兩個要求的結果：

**第一個要求：**

控制器作業：

暫時性： d233e165-f417-469b-a866-1cf1935d2518  
具範圍： 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9  
執行個體： 00000000-0000-0000-0000-000000000000

`OperationService` 作業：

暫時性： c6b049eb-1318-4e31-90f1-eb2dd849ff64  
具範圍： 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9  
執行個體： 00000000-0000-0000-0000-000000000000

**:第二個要求：**

控制器作業：

暫時性： b63bd538-0a37-4ff1-90ba-081c5138dda0  
具範圍： 31e820c5-4834-4d22-83fc-a60118acb9f4  
單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9  
執行個體： 00000000-0000-0000-0000-000000000000

`OperationService` 作業：

暫時性： c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
具範圍： 31e820c5-4834-4d22-83fc-a60118acb9f4  
單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9  
執行個體： 00000000-0000-0000-0000-000000000000

觀察哪些 `OperationId` 值在要求內以及要求之間不同：

* 「暫時性」** 物件一律不同。 第一個與第二個用戶端要求的暫時性 `OperationId` 值在兩個 `OperationService` 作業之間與各用戶端要求之間都不同。 新執行個體會提供給每個服務要求以及用戶端要求。
* 「具範圍」** 物件在同一個用戶端要求內皆相同，但在不同的用戶端要求之間則不同。
* *Singleton*不論是否 `Operation` 在中提供實例，每個物件和每個要求的單一物件都是相同的 `Startup.ConfigureServices` 。

## <a name="call-services-from-main"></a>從主要呼叫服務

使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>，以解析應用程式範圍中的範圍服務。 此法可用於在開機時存取範圍服務，以執行初始化工作。 下列範例示範如何在 `Program.Main` 中取得 `MyScopedService`：

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a>範圍驗證

當應用程式在開發環境中執行時，預設服務提供者會執行檢查以確認：

* 範圍服務不是直接或間接由開機服務提供者解析。
* 範圍服務不是直接或間接插入至單一服務。

根服務提供者會在呼叫 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 時建立。 當提供者啟動應用程式時，根服務提供者的存留期與應用程式/伺服器的存留期一致，並會在應用程式關閉時處置。

範圍服務會由建立這些服務的容器處置。 若是在根容器中建立範圍服務，因為當應用程式/伺服器關機時，服務只會由根容器處理，所以服務的存留期會提升為單一服務等級。 當呼叫 `BuildServiceProvider` 時，驗證服務範圍會攔截到這些情況。

如需詳細資訊，請參閱<xref:fundamentals/host/web-host#scope-validation>。   

## <a name="request-services"></a>要求服務

來自 `HttpContext`，在 ASP.NET Core 要求內提供的服務是透過 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公開。

要求服務代表您在應用程式中設定和要求的服務。 當物件指定相依性時，這些會由在 `RequestServices` 中找到的類型來滿足，而非 `ApplicationServices`。

一般而言，應用程式不應該直接使用這些屬性。 相反地，透過類別建構函式要求類別所需的型別，以及允許架構插入相依性。 這會產生容易測試的類別。

> [!NOTE]
> 偏好要求相依性作為建構函式參數，而不要存取 `RequestServices` 集合。

## <a name="design-services-for-dependency-injection"></a>針對相依性插入設計服務

最佳做法是：

* 設計服務以使用相依性插入來取得其相依性。
* 避免具狀態的靜態類別和成員。 將應用程式設計成使用單一服務，以避免建立全域狀態。
* 避免直接在服務內具現化相依類別。 直接具現化會將程式碼耦合到特定實作。
* 讓應用程式類別維持在小型、情況良好且可輕鬆測試的狀態。

若類別有太多插入的相依性，這通常表示類別有太多責任而且正違反[單一責任原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) \(英文\)。 將類別負責的某些部分移到新的類別，以嘗試重構類別。 請記住，頁面 Razor 頁面模型類別和 MVC 控制器類別應該專注于 UI 考慮。 商務規則和資料存取實作詳細資料應該保存在適合這些[分開考量](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Separation of Concerns) 類別中。

### <a name="disposal-of-services"></a>處置服務

容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose*>。 若執行個體由使用者程式碼新增到容器中，它將不會自動處置。

在下列範例中，服務會建立服務容器並自動處置：

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

在下例中︰

* 服務容器不會建立服務實例。
* 架構不知道預期的服務存留期。
* 架構不會自動處置服務。
* 如果服務未在開發人員程式碼中明確處置，它們會持續存在，直到應用程式關閉為止。

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>暫時性和共用實例的 IDisposable 指引

#### <a name="transient-limited-lifetime"></a>暫時性，有限的存留期

**案例**

應用程式需要在 <xref:System.IDisposable> 下列任一情況下具有暫時性存留期的實例：

* 實例會在根範圍中解析。
* 應該在範圍結束之前處置實例。

**方案**

使用 factory 模式，在父範圍外建立實例。 在這種情況下，應用程式通常會有 `Create` 方法，直接呼叫最終型別的函式。 如果最終類型具有其他相依性，則 factory 可以：

* <xref:System.IServiceProvider>在其函式中接收。
* 使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 來具現化容器外的實例，同時使用容器來執行其相依性。

#### <a name="shared-instance-limited-lifetime"></a>共用實例，有限的存留期

**案例**

應用程式需要 <xref:System.IDisposable> 多個服務之間的共用實例，但 <xref:System.IDisposable> 應具有有限的存留期。

**方案**

註冊具有限定範圍存留期的實例。 使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 來啟動並建立新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 。 使用範圍的 <xref:System.IServiceProvider> 來取得所需的服務。 在存留期結束時處置範圍。

#### <a name="general-guidelines"></a>一般準則

* 不要向 <xref:System.IDisposable> 暫時性範圍註冊實例。 請改用 factory 模式。
* 請勿解析 <xref:System.IDisposable> 根範圍中的暫時性或範圍實例。 唯一的例外狀況是當應用程式建立/重新建立和處置時 <xref:System.IServiceProvider> ，這不是理想的模式。
* 透過 DI 接收相依性 <xref:System.IDisposable> 並不會要求接收者 <xref:System.IDisposable> 自行執行。 相依性的接收者不 <xref:System.IDisposable> 應呼叫 <xref:System.IDisposable.Dispose%2A> 該相依性上的。
* 範圍應該用來控制服務的存留期。 範圍不是階層式，而且範圍之間沒有特殊連接。

## <a name="default-service-container-replacement"></a>預設服務容器取代

內建的服務容器是設計用來滿足架構和大部分取用者應用程式的需求。 我們建議使用內建容器，除非您需要內建容器不支援的特定功能，例如：

* 屬性插入
* 根據名稱插入
* 子容器
* 自訂生命週期管理
* `Func<T>` 支援延遲初始設定
* 以慣例為基礎的註冊

下列協力廠商容器可以與 ASP.NET Core 應用程式搭配使用：

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [拉馬爾](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>執行緒安全

建立具備執行緒安全性的 singleton 服務。 如果 singleton 服務相依於暫時性服務，暫時性服務可能也需要具備執行緒安全性，取決於 singleton 如何使用它。

單一服務的 factory 方法（例如 AddSingleton (IServiceCollection 的第二個引數[ \<TService> ），Func \<IServiceProvider,TService>) ](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)不需要是安全線程。 就像型別 (`static`) 建構函式一樣，它一定會被單一執行緒呼叫一次。

## <a name="recommendations"></a>建議

* 不支援以 `async/await` 與 `Task` 為基礎的服務解析。 C# 不支援非同步建構函式，因此建議的模式是以同步方式解析服務後使用非同步方法。

* 避免直接在服務容器中儲存資料與設定。 例如，使用者的購物車通常不應該新增至服務容器。 組態應該使用[選項模式](xref:fundamentals/configuration/options)。 同樣地，請避免只存在以允許存取某個其他物件的「資料持有者」物件。 最好是透過 DI 要求實際項目。
* 避免靜態存取服務。 例如，避免以靜態方式輸入[IApplicationBuilder microsoft.visualbasic.applicationservices.cantstartsingleinstanceexception](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ，以供其他地方使用。

* 避免使用*服務定位器模式*，這會混用控制策略的[反轉](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)。
  * <xref:System.IServiceProvider.GetService*>當您可以改用 DI 時，請勿叫用來取得服務實例：

    **不正確：**

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    **正確**：

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* 避免在使用時，插入用來解析相依性的 factory <xref:System.IServiceProvider.GetService*> 。
* 避免以靜態方式存取 `HttpContext` (例如 [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。

就像所有的建議集，您可能會遇到需要忽略建議的情況。 例外狀況很罕見，大部分是在架構本身內的特殊案例。

DI 是靜態/全域物件存取模式的「替代」** 選項。 如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。

## <a name="additional-resources"></a>其他資源

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [在 ASP.NET Core 中處置 IDisposables 的四種方式](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [在 ASP.NET Core 使用 Dependency Injection 撰寫簡潔的程式碼 (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [明確相依性準則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [逆轉控制容器和相依性插入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) \(英文\)
* [How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (如何使用 ASP.NET Core DI 中的多個介面註冊服務)

::: moniker-end
