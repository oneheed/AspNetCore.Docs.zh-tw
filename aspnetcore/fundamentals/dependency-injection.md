---
title: .NET Core 中的相依性插入
author: rick-anderson
description: 了解 ASP.NET Core 如何實作相依性插入以及如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ce6804a58c5b17b57732713acc3aeca15042da0a
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589708"
---
# <a name="dependency-injection-in-aspnet-core"></a>.NET Core 中的相依性插入

::: moniker range=">= aspnetcore-3.0"

[Kirk Larkin](https://twitter.com/serpent5)、 [Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。

如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。

如需在 web 應用程式以外的應用程式中使用相依性插入的相關資訊，請參閱 [.net 中](/dotnet/core/extensions/dependency-injection)的相依性插入。

如需有關選項之相依性插入的詳細資訊，請參閱 <xref:fundamentals/configuration/options> 。

本主題提供 ASP.NET Core 中的相依性插入的相關資訊。 使用相依性插入的主要檔包含在 .NET 的相依性 [插入](/dotnet/core/extensions/dependency-injection)中。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="overview-of-dependency-injection"></a>相依性插入概觀

相依 *性是另* 一個物件所依存的物件。 `MyDependency`使用其他類別所依存的方法檢查下列類別 `WriteMessage` ：

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

類別可以建立類別的實例 `MyDependency` ，以使用其 `WriteMessage` 方法。 在下列範例中， `MyDependency` 類別是類別的相依性 `IndexModel` ：

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

類別會建立和直接相依于 `MyDependency` 類別。 程式碼相依性（如上述範例所示）有問題，而且應該避免因下列原因：

* 若要以 `MyDependency` 不同的實作為取代， `IndexModel` 必須修改該類別。
* 如果 `MyDependency` 有相依性，則也必須由類別設定 `IndexModel` 。 在具有多個相依於 `MyDependency` 之多個類別的大型專案中，設定程式碼在不同的應用程式之間會變得鬆散。
* 此實作難以進行單元測試。 應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。

相依性插入可透過下列方式解決這些問題：

* 使用介面或基底類別來將相依性資訊抽象化。
* 在服務容器中註冊相依性。 ASP.NET Core 提供內建服務容器 <xref:System.IServiceProvider>。 服務通常會在應用程式的方法中註冊 `Startup.ConfigureServices` 。
* 將服務「插入」到服務使用位置之類別的建構函式。 架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。

在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples)中， `IMyDependency` 介面會定義 `WriteMessage` 方法：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

這個介面是由具象型別 `MyDependency` 所實作：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

範例應用程式會 `IMyDependency` 使用具象類型來註冊服務 `MyDependency` 。 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>方法會使用範圍存留期（單一要求的存留期）來註冊服務。 將在此主題稍後將說明[服務存留期](#service-lifetimes)。

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

在範例應用程式中， `IMyDependency` 會要求服務並用來呼叫 `WriteMessage` 方法：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

藉由使用 DI 模式，控制器：

* 不使用具象型別 `MyDependency` ，只使用它所執行的 `IMyDependency` 介面。 這可讓您輕鬆地變更控制器所使用的執行，而不需要修改控制器。
* 不會建立的實例 `MyDependency` ，而是由 DI 容器所建立。

您 `IMyDependency` 可以使用內建的記錄 API 來改善介面的實作為：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

更新的 `ConfigureServices` 方法會註冊新的 `IMyDependency` 實作為：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

`MyDependency2` 相依于 <xref:Microsoft.Extensions.Logging.ILogger%601> 它在函式中要求的。 `ILogger<TCategoryName>` 是 [架構提供的服務](#framework-provided-services)。

以鏈結方式使用相依性插入並非不尋常。 每個要求的相依性接著會要求其自己的相依性。 容器會解決圖形中的相依性，並傳回完全解析的服務。 必須先解析的相依性集合組通常稱為「相依性樹狀結構」、「相依性圖形」或「物件圖形」。

容器會 `ILogger<TCategoryName>` 利用 [ (泛型) 開放式](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)型別來解析，因此不需要註冊每個 [ (泛型) 結構類型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。

在相依性插入術語中，服務：

* 通常是為其他物件（例如服務）提供服務的物件 `IMyDependency` 。
* 與 web 服務無關，但服務可能會使用 web 服務。

架構會提供健全的 [記錄](xref:fundamentals/logging/index) 系統。 `IMyDependency`上述範例中所示的執行是為了示範基本 DI 而撰寫的，而不是用來執行記錄。 大部分的應用程式都不需要撰寫記錄器。 下列程式碼示範如何使用預設記錄，這不需要在中註冊任何服務 `ConfigureServices` ：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

使用上述程式碼不需要更新 `ConfigureServices` ，因為 [記錄](xref:fundamentals/logging/index) 是由架構提供。

## <a name="services-injected-into-startup"></a>插入啟動的服務

服務可以插入至函式 `Startup` 和 `Startup.Configure` 方法。

`Startup`使用泛型主機 () 時，只可將下列服務插入至函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

任何向 DI 容器註冊的服務都可以插入方法中 `Startup.Configure` ：

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

如需詳細資訊，請參閱 <xref:fundamentals/startup> 和 [Access Configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup)。

## <a name="register-groups-of-services-with-extension-methods"></a>使用擴充方法來註冊服務群組

ASP.NET Core framework 會使用註冊一組相關服務的慣例。 慣例是使用單一 `Add{GROUP_NAME}` 擴充方法來註冊架構功能所需的所有服務。 例如， <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> 擴充方法會註冊 MVC 控制器所需的服務。

下列程式碼是由 Razor 使用個別使用者帳戶的 Pages 範本所產生，並示範如何使用擴充方法和來將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> ：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a>服務存留期

在[.net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入中的[服務存留期](/dotnet/core/extensions/dependency-injection#service-lifetimes)

若要在中介軟體中使用範圍服務，請使用下列其中一種方法：

* 將服務插入中介軟體的 `Invoke` 或 `InvokeAsync` 方法。 使用函式 [插入](xref:mvc/controllers/dependency-injection#constructor-injection) 會擲回執行時間例外狀況，因為它會強制範圍服務的行為就像 singleton 一樣。 [ [存留期和註冊選項](#lifetime-and-registration-options) ] 區段中的範例會示範 `InvokeAsync` 方法。
* 使用以 [Factory 為基礎的中介軟體](xref:fundamentals/middleware/extensibility)。 使用此方法註冊的中介軟體會根據用戶端要求啟動 (連接) ，可讓範圍服務插入中介軟體的 `InvokeAsync` 方法。

如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。

## <a name="service-registration-methods"></a>服務註冊方法

在[.net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入中的[服務註冊方法](/dotnet/core/extensions/dependency-injection#service-registration-methods)

 [模擬類型以進行測試](xref:test/integration-tests#inject-mock-services)時，通常會使用多個實作為。

註冊只有實作為型別的服務，相當於向相同的實和服務型別註冊該服務。 這就是為什麼無法使用未採用明確服務類型的方法來註冊多個服務的服務。 這些方法可以註冊服務的多個 *實例* ，但它們都有相同的 *實* 作為型別。

您可以使用上述任何一種服務註冊方法來註冊相同服務類型的多個服務實例。 在下列範例中， `AddSingleton` 使用 `IMyDependency` 做為服務類型呼叫兩次。 第二個呼叫會 `AddSingleton` 在解析為時覆寫上一個 `IMyDependency` ，並在透過來解析多個服務時加入至上一個 `IEnumerable<IMyDependency>` 。 服務會依照其在解析時所註冊的順序顯示 `IEnumerable<{SERVICE}>` 。

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a>建構函式插入行為

在[.net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入中的函式[插入行為](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)

## <a name="entity-framework-contexts"></a>Entity Framework 內容

根據預設，會使用限 [域存留期](#service-lifetimes) 將 Entity Framework 內容新增至服務容器，因為 web 應用程式資料庫作業的範圍通常是用戶端要求。 若要使用不同的存留期，請使用多載來指定存留期 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 。 給定存留期的服務不應使用存留期短于服務存留期的資料庫內容。

## <a name="lifetime-and-registration-options"></a>留期和註冊選項

若要示範服務存留期與其註冊選項之間的差異，請考慮使用下列介面，將工作表示為具有識別碼的作業 `OperationId` 。 根據作業的服務存留期如何針對下列介面進行設定，容器會在要求類別時提供相同或不同的服務實例：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

下列類別會執行 `Operation` 上述所有介面。 此函式會 `Operation` 產生 GUID，並將最後4個字元儲存在 `OperationId` 屬性中：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`Startup.ConfigureServices`方法會根據指定的存留期，建立類別的多個註冊 `Operation` ：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

範例應用程式會示範在要求內和之間的物件存留期。 `IndexModel`和中介軟體會要求每種 `IOperation` 類型，並記錄 `OperationId` 每個類型的：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

類似于 `IndexModel` ，中介軟體會解析相同的服務：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

您必須在方法中解析已設定範圍的服務 `InvokeAsync` ：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

記錄器輸出顯示：

* 「暫時性」 物件一律不同。 在 `OperationId` 中介軟體的和中，暫時性值是不同的 `IndexModel` 。
* 每個要求的 *範圍* 物件都相同，但在每個要求中都是不同的。
* 每個要求的 *單一* 物件都相同。

若要減少記錄輸出，請在檔案的 *appsettings.Development.js* 中設定 "記錄： LogLevel： Microsoft： Error"：

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a>從主要呼叫服務

使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) 建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>，以解析應用程式範圍中的範圍服務。 此法可用於在開機時存取範圍服務，以執行初始化工作。

下列範例示範如何存取範圍 `IMyDependency` 服務，並在中呼叫其 `WriteMessage` 方法 `Program.Main` ：

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a>範圍驗證

在[.net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入中的函式[插入行為](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)

如需詳細資訊，請參閱[範圍驗證](xref:fundamentals/host/web-host#scope-validation)。

## <a name="request-services"></a>要求服務

ASP.NET 核心要求內可用的服務會透過 [RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公開。 從要求內要求服務時，會從集合解析服務及其相依性 `RequestServices` 。

架構會為每個要求建立一個範圍，並 `RequestServices` 公開範圍服務提供者。 只要要求為作用中狀態，所有範圍的服務都有效。

> [!NOTE]
> 偏好將相依性要求為函式參數，以解析集合中的服務 `RequestServices` 。 這會導致更容易測試的類別。

## <a name="design-services-for-dependency-injection"></a>針對相依性插入設計服務

針對相依性插入設計服務時：

* 避免具狀態、靜態類別和成員。 請改為使用單一服務來設計應用程式，以避免建立全域狀態。
* 避免直接在服務內具現化相依類別。 直接具現化會將程式碼耦合到特定實作。
* 使服務更小、妥善組成且輕鬆地進行測試。

如果類別有許多插入的相依性，可能是因為類別具有太多責任，而且違反了 [單一責任原則 (SRP) ](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。 嘗試將其部分責任移至新的類別，以重構類別。 請記住， Razor 頁面頁面模型類別和 MVC 控制器類別應該專注于 UI 的考慮。

### <a name="disposal-of-services"></a>處置服務

容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose%2A>。 從容器解析的服務絕對不能由開發人員處置。 如果類型或 factory 註冊為 singleton，則容器會自動處置 singleton。

在下列範例中，服務是由服務容器建立並自動處置：

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

在每次重新整理 [索引] 頁面之後，debug 主控台會顯示下列輸出：

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a>服務容器未建立的服務

請考慮下列程式碼：

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

在上述程式碼中：

* 服務實例不是由服務容器所建立。
* 架構不會自動處置服務。
* 開發人員負責處置服務。

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>暫時性和共用實例的 IDisposable 指引

在 .NET 中的相依性[插入](/dotnet/core/extensions/dependency-injection)中查看[暫時性和共用實例的 IDisposable 指導](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances)方針

## <a name="default-service-container-replacement"></a>預設服務容器取代

查看 .NET 中的相依性[插入](/dotnet/core/extensions/dependency-injection)中的[預設服務容器取代](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement)

## <a name="recommendations"></a>建議

[在 .net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入的[建議](/dotnet/core/extensions/dependency-injection-guidelines#recommendations)

* 避免使用「服務定位器模式」。 例如，當您可以改用 DI 時，請勿叫用 <xref:System.IServiceProvider.GetService%2A> 來取得服務執行個體：

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
* 避免 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> 在中呼叫 `ConfigureServices` 。 呼叫 `BuildServiceProvider` 通常會在開發人員想要解析中的服務時發生 `ConfigureServices` 。 例如，請考慮從設定載入的情況 `LoginPath` 。 請避免下列方法：

  ![呼叫 BuildServiceProvider 的錯誤程式碼](~/fundamentals/dependency-injection/_static/badcodeX.png)

  在上圖中，選取下綠色波浪線會 `services.BuildServiceProvider` 顯示下列 ASP0000 警告：

  > 從應用程式程式碼呼叫 ' BuildServiceProvider ' ASP0000 時，會產生另一個要建立的單一服務複本。 請考慮將相依性插入服務作為「設定」參數的替代方案。

  呼叫 `BuildServiceProvider` 會建立第二個容器，它可以建立損毀的 singleton，並導致跨多個容器的物件圖形參考。

  若要取得正確的方式 `LoginPath` ，請使用選項模式內建的 DI 支援：

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* 容器會捕獲可處置的暫時性服務以供處置。 如果從最上層容器解析，這可能會導致記憶體流失。
* 啟用範圍驗證，以確定應用程式沒有可捕獲範圍服務的 singleton。 如需詳細資訊，請參閱[範圍驗證](#scope-validation)。

就像所有的建議集，您可能會遇到需要忽略建議的情況。 例外狀況很罕見，大多是架構本身內的特殊案例。

DI 是靜態/全域物件存取模式的「替代」選項。 如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>DI 中多租使用者的建議模式

[Orchard core](https://github.com/OrchardCMS/OrchardCore) 是一種應用程式架構，可在 ASP.NET Core 上建立模組化、多租使用者應用程式。 如需詳細資訊，請參閱 [Orchard Core 檔](https://docs.orchardcore.net/en/dev/)。

如需如何使用 Orchard Core Framework 來建立模組化和多租使用者應用程式的範例，請參閱 [Orchard core 範例](https://github.com/OrchardCMS/OrchardCore.Samples) ，而不需要任何 CMS 專屬的功能。

## <a name="framework-provided-services"></a>架構提供的服務

`Startup.ConfigureServices`方法會註冊應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。 一開始， `IServiceCollection` 提供給的是 `ConfigureServices` 由架構定義的服務，視 [主機的設定方式](xref:fundamentals/index#host)而定。 針對以 ASP.NET Core 範本為基礎的應用程式，架構會註冊250以上的服務。 

下表列出這些架構註冊服務的小型範例：

| 服務類型                                                                                    | 存留期  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | 暫時性 |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | 單一 |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | 單一 |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | 單一 |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | 暫時性 |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | 單一 |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | 暫時性 |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | 單一 |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | 單一 |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | 單一 |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | 暫時性 |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | 單一 |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | 單一 |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | 單一 |

## <a name="additional-resources"></a>其他資源

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [適用于 DI 應用程式開發的 NDC 會議模式](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [在 ASP.NET Core 中處置 IDisposables 的四種方式](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [在 ASP.NET Core 使用 Dependency Injection 撰寫簡潔的程式碼 (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [明確相依性準則](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [逆轉控制容器和相依性插入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) \(英文\)
* [How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (如何使用 ASP.NET Core DI 中的多個介面註冊服務)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。

如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="overview-of-dependency-injection"></a>相依性插入概觀

「相依性」是另一個物件所需的任何物件。 檢查具有 `WriteMessage` 方法的下列 `MyDependency` 物件，應用程式中的其他類別相依於此物件：

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
* 將服務「插入」到服務使用位置之類別的建構函式。 架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。

在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 介面定義了服務提供給應用程式的方法：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

這個介面是由具象型別 `MyDependency` 所實作：

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` 在其建構函式中會要求 <xref:Microsoft.Extensions.Logging.ILogger`1>。 以鏈結方式使用相依性插入並非不尋常。 每個要求的相依性接著會要求其自己的相依性。 容器會解決圖形中的相依性，並傳回完全解析的服務。 必須先解析的相依性集合組通常稱為「相依性樹狀結構」、「相依性圖形」或「物件圖形」。

`IMyDependency` 與 `ILogger<TCategoryName>` 必須在服務容器中註冊。 `IMyDependency` 是在 `Startup.ConfigureServices` 中註冊。 `ILogger<TCategoryName>` 是由記錄抽象基礎結構所註冊，所以它是預設由架構所註冊的[架構提供的服務](#framework-provided-services)。

容器會利用 [(泛型) 開放類型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) 解析 `ILogger<TCategoryName>`，讓您不需註冊每個 [(泛型) 建構的型別](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

在範例應用程式中，`IMyDependency` 服務是使用具象型別 `MyDependency` 所註冊。 註冊會將服務的存留期範圍限制為單一要求的存留期。 將在此主題稍後將說明[服務存留期](#service-lifetimes)。

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> 每個 `services.Add{SERVICE_NAME}` 擴充方法會新增並可能設定服務。 例如，、 `services.AddControllersWithViews` `services.AddRazorPages` 和會 `services.AddControllers` 新增 ASP.NET Core 應用程式所需的服務。 建議應用程式遵循此慣例。 在 <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 命名空間中放置擴充方法，以封裝服務註冊群組。 包括 DI 擴充方法的命名空間部分， `Microsoft.Extensions.DependencyInjection` 也包括：
>
> * 可讓它們在 [IntelliSense](/visualstudio/ide/using-intellisense) 中顯示，而不需要新增其他 `using` 區塊。
> * 防止類別中過多的 `using` 語句 `Startup` ，通常會從這些擴充方法呼叫這些擴充方法。

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

## <a name="services-injected-into-startup"></a>插入啟動的服務

`Startup`使用泛型主機 () 時，只可將下列服務類型插入至函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

服務可以插入至 `Startup.Configure` ：

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

如需詳細資訊，請參閱<xref:fundamentals/startup>。

## <a name="framework-provided-services"></a>架構提供的服務

`Startup.ConfigureServices`方法負責定義應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。 一開始， `IServiceCollection` 提供給的是 `ConfigureServices` 由架構定義的服務，視 [主機的設定方式](xref:fundamentals/index#host)而定。 以 ASP.NET Core 範本為基礎的應用程式在架構中註冊了數百項服務並不罕見。 下表列出架構註冊服務的小型範例。

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

## <a name="register-additional-services-with-extension-methods"></a>使用擴充方法註冊其他服務

當可以使用服務集合擴充方法來註冊服務 (如果需要，也可以註冊其相依服務) 時，慣例是使用單一 `Add{SERVICE_NAME}` 擴充方法來註冊該服務要求的所有服務。 下列程式碼範例示範如何使用擴充方法[AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)和來將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> ：

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

在處理要求的應用程式中，會在要求結束時處置範圍服務。

> [!WARNING]
> 在中介軟體中使用具範圍服務時，請將該服務插入 `Invoke` 或 `InvokeAsync` 方法中。 不要插入 via 函式 [插入](xref:mvc/controllers/dependency-injection#constructor-injection) ，因為它會強制服務的行為就像 singleton 一樣。 如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。

### <a name="singleton"></a>單一

當第一次收到有關單一資料庫存留期服務的要求時 (或是當執行 `Startup.ConfigureServices` 且隨著服務註冊指定執行個體時)，即會建立單一資料庫存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)。 每個後續要求都會使用相同的執行個體。 若應用程式要求單一資料庫行為，建議您允許服務容器管理服務的存留期。 不要實作單一資料庫設計模式並為使用者提供可用來管理類別中物件存留期的程式碼。

在處理要求的應用程式中，會在應用程式關閉時處置單一服務 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 。

> [!WARNING]
> 從單一資料庫解析具範圍服務是非常危險的。 處理後續要求時，它可能會導致服務有不正確的狀態。

## <a name="service-registration-methods"></a>服務註冊方法

服務註冊擴充方法提供在特定案例中很有用的多載。

| 方法 | 自動<br>物件 (object)<br>處置 | 多個<br>實作 | 傳遞引數 |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>範例：<br>`services.AddSingleton<IMyDep, MyDep>();` | 是 | 是 | 否 |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>範例：<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | 是 | 是 | 是 |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>範例：<br>`services.AddSingleton<MyDep>();` | 是 | 否 | 否 |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>範例：<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | 否 | 是 | 是 |
| `AddSingleton(new {IMPLEMENTATION})`<br>範例：<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | 否 | 否 | 是 |

如需類型處置的詳細資訊，請參閱[＜服務處置＞](#disposal-of-services)一節。 多個實作的常見案例是[模擬測試類型](xref:test/integration-tests#inject-mock-services)。

註冊只有實作為型別的服務，相當於向相同的實和服務型別註冊該服務。 這就是為什麼無法使用未採用明確服務類型的方法來註冊多個服務的服務。 這些方法可以註冊服務的多個 *實例* ，但它們都有相同的 *實* 作為型別。

您可以使用上述任何一種服務註冊方法來註冊相同服務類型的多個服務實例。 在下列範例中， `AddSingleton` 使用 `IMyDependency` 做為服務類型呼叫兩次。 第二個呼叫會 `AddSingleton` 在解析為時覆寫上一個 `IMyDependency` ，並在透過來解析多個服務時加入至上一個 `IEnumerable<IMyDependency>` 。 服務會依照其在解析時所註冊的順序顯示 `IEnumerable<{SERVICE}>` 。

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

此架構也會提供 `TryAdd{LIFETIME}` 擴充方法，只有在尚未註冊任何執行時，才會註冊服務。

在下列範例中，呼叫以註冊為的 `AddSingleton` `MyDependency` 實作為的 `IMyDependency` 。 的呼叫 `TryAddSingleton` 沒有任何作用，因為 `IMyDependency` 已經有已註冊的實作為。

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

如需詳細資訊，請參閱

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

如果還沒有「相同類型的」實作，則 [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法只會註冊服務。 多個服務會透過 `IEnumerable<{SERVICE}>` 解析。 註冊服務時，如果尚未新增相同類型的執行個體，則開發人員只會希望新增執行個體。 程式庫作者通常會使用此方法來避免註冊容器中某個執行個體的兩個複本。

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
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允許在相依性插入容器中建立物件，而不註冊服務。 搭配使用者面向抽象 (例如標籤協助程式、MVC 控制器與模型繫結器) 使用 `ActivatorUtilities`。

建構函式可以接受不是由相依性插入提供的引數，但引數必須指派預設值。

當或解決服務時 `IServiceProvider` `ActivatorUtilities` ，必須使用 *公用* 的函式來 [插入](xref:mvc/controllers/dependency-injection#constructor-injection)。

當服務解析時，函式 `ActivatorUtilities` [插入](xref:mvc/controllers/dependency-injection#constructor-injection) 會要求只能有一個適用的函式。 支援建構函式多載，但只能有一個多載存在，其引數可以藉由相依性插入而完成。

## <a name="entity-framework-contexts"></a>Entity Framework 內容

因為一般會將 Web 應用程式資料庫作業範圍設定為用戶端要求，所以通常會使用[具範圍存留期](#service-lifetimes)將 Entity Framework 內容新增至服務容器。 如果在註冊資料庫內容時， [AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)多載未指定存留期，則預設存留期會限定範圍。 指定存留期的服務不應該使用存留期比服務還短的資料庫內容。

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

* 「暫時性」 物件一律不同。 第一個與第二個用戶端要求的暫時性 `OperationId` 值在兩個 `OperationService` 作業之間與各用戶端要求之間都不同。 新執行個體會提供給每個服務要求以及用戶端要求。
* 「具範圍」物件在同一個用戶端要求內皆相同，但在不同的用戶端要求之間則不同。
* 無論中是否有提供實例，每個物件和每個要求的 *單一* 物件都會相同 `Operation` `Startup.ConfigureServices` 。

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
* 避免具狀態、靜態類別和成員。 請改為使用單一服務來設計應用程式，以避免建立全域狀態。
* 避免直接在服務內具現化相依類別。 直接具現化會將程式碼耦合到特定實作。
* 讓應用程式類別維持在小型、情況良好且可輕鬆測試的狀態。

若類別有太多插入的相依性，這通常表示類別有太多責任而且正違反[單一責任原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) \(英文\)。 將類別負責的某些部分移到新的類別，以嘗試重構類別。 請記住， Razor 頁面頁面模型類別和 MVC 控制器類別應該專注于 UI 的考慮。 商務規則和資料存取實作詳細資料應該保存在適合這些[分開考量](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Separation of Concerns) 類別中。

### <a name="disposal-of-services"></a>處置服務

容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose*>。 若執行個體由使用者程式碼新增到容器中，它將不會自動處置。

在下列範例中，服務是由服務容器建立並自動處置：

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

* 服務實例不是由服務容器所建立。
* 架構不知道預期的服務存留期。
* 架構不會自動處置服務。
* 如果未在開發人員程式碼中明確處置服務，它們會保存到應用程式關閉為止。

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

#### <a name="transient-limited-lifetime"></a>暫時性、有限存留期

**案例**

應用程式需要在 <xref:System.IDisposable> 下列任一案例中具有暫時性存留期的實例：

* 此實例會在根範圍中解析。
* 實例應該在範圍結束之前處置。

**方案**

使用 factory 模式，在父範圍之外建立實例。 在這種情況下，應用程式通常會有 `Create` 方法可直接呼叫最終型別的函式。 如果最終類型有其他相依性，factory 可以：

* <xref:System.IServiceProvider>在其函式中接收。
* 使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 可在容器外部具現化實例，同時使用容器作為其相依性。

#### <a name="shared-instance-limited-lifetime"></a>共用實例，有限存留期

**案例**

應用程式需要 <xref:System.IDisposable> 跨多個服務的共用實例，但是 <xref:System.IDisposable> 存留期應受限。

**方案**

註冊具有範圍存留期的實例。 使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 啟動並建立新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 。 使用範圍 <xref:System.IServiceProvider> 來取得所需的服務。 在存留期結束時處置範圍。

#### <a name="general-guidelines"></a>一般準則

* 請勿向 <xref:System.IDisposable> 暫時性範圍登錄實例。 請改用 factory 模式。
* 請勿在根範圍中解析暫時性或限定範圍 <xref:System.IDisposable> 的實例。 唯一的一般例外狀況是當應用程式建立/重新建立並處置時 <xref:System.IServiceProvider> ，這不是理想的模式。
* 透過 DI 接收相依性 <xref:System.IDisposable> 不需要接收者 <xref:System.IDisposable> 自行執行。 相依性的接收者不 <xref:System.IDisposable> 應呼叫 <xref:System.IDisposable.Dispose%2A> 該相依性。
* 範圍應該用來控制服務的存留期。 範圍不是階層式，而且範圍之間沒有特殊連接。

## <a name="default-service-container-replacement"></a>預設服務容器取代

內建的服務容器是設計來滿足架構和大部分消費者應用程式的需求。 除非您需要內建容器不支援的特定功能，否則建議使用內建容器，例如：

* 屬性插入
* 根據名稱插入
* 子容器
* 自訂生命週期管理
* `Func<T>` 支援延遲初始設定
* 以慣例為基礎的註冊

下列協力廠商容器可與 ASP.NET Core 應用程式搭配使用：

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [拉馬爾](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>執行緒安全

建立具備執行緒安全性的 singleton 服務。 如果 singleton 服務相依於暫時性服務，暫時性服務可能也需要具備執行緒安全性，取決於 singleton 如何使用它。

單一服務的 factory 方法（例如 >addsingleton 的第二個自 [變數 \<TService> (IServiceCollection、Func \<IServiceProvider,TService>) ](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)）不需要是安全線程。 就像型別 (`static`) 建構函式一樣，它一定會被單一執行緒呼叫一次。

## <a name="recommendations"></a>建議

* 不支援以 `async/await` 與 `Task` 為基礎的服務解析。 C# 不支援非同步建構函式，因此建議的模式是以同步方式解析服務後使用非同步方法。

* 避免直接在服務容器中儲存資料與設定。 例如，使用者的購物車通常不應該新增至服務容器。 組態應該使用[選項模式](xref:fundamentals/configuration/options)。 同樣地，請避免只存在以允許存取某個其他物件的「資料持有者」物件。 最好是透過 DI 要求實際項目。
* 避免靜態存取服務。 例如，請避免以靜態方式輸入 [IApplicationBuilder >iapplicationbuilder.applicationservices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ，以便在其他地方使用。

* 請避免使用 *服務定位器模式*，其混合 [了控制策略的反轉](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 。
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

* 避免插入在執行時間使用來解析相依性的 factory <xref:System.IServiceProvider.GetService*> 。
* 避免以靜態方式存取 `HttpContext` (例如 [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。

就像所有的建議集，您可能會遇到需要忽略建議的情況。 例外狀況很罕見，大多是架構本身內的特殊案例。

DI 是靜態/全域物件存取模式的「替代」選項。 如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。

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
