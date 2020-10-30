---
title: ASP.NET Core 相依性 Blazor 插入
author: guardrex
description: 瞭解 Blazor 應用程式如何將服務插入至元件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 7b573c0635aafe7efcf56185c250dbab836e6cf4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056018"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>ASP.NET Core 相依性 Blazor 插入

依 [Rainer Stropek](https://www.timecockpit.com) 和 [Mike Rousos](https://github.com/mjrousos)

Blazor 支援 [ (DI) ](xref:fundamentals/dependency-injection)的相依性插入。 應用程式可以使用內建服務，方法是將它們插入元件中。 應用程式也可以定義和註冊自訂服務，並透過 DI 讓它們可在整個應用程式中使用。

DI 是存取中央位置中設定之服務的一種技術。 這在應用程式中可能很有用 Blazor ：

* 在許多元件（稱為 *單一* 服務）之間共用服務類別的單一實例。
* 使用參考抽象概念將元件與具體的服務類別分離。 例如，請考慮 `IDataAccess` 用來存取應用程式中資料的介面。 介面是由實體類別所執行 `DataAccess` ，並在應用程式的服務容器中註冊為服務。 當元件使用 DI 來接收執行時 `IDataAccess` ，該元件不會與具象型別結合。 您可以交換實作為單元測試中的 mock 實作為。

## <a name="default-services"></a>預設服務

預設服務會自動加入至應用程式的服務集合。

| 服務 | 存留期 | 描述 |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | 具範圍 | 提供方法來傳送 HTTP 要求，以及從 URI 所識別的資源接收 HTTP 回應。<br><br><xref:System.Net.Http.HttpClient>應用程式中的實例會 Blazor WebAssembly 使用瀏覽器來處理背景中的 HTTP 流量。<br><br>Blazor Server 依預設，應用程式不會包含 <xref:System.Net.Http.HttpClient> 已設定為服務的服務。 提供 <xref:System.Net.Http.HttpClient> 給 Blazor Server 應用程式。<br><br>如需詳細資訊，請參閱<xref:blazor/call-web-api>。 |
| <xref:Microsoft.JSInterop.IJSRuntime> | 單一 (Blazor WebAssembly) <br>限域 (Blazor Server)  | 代表 javascript 呼叫會分派至其中的 JavaScript 執行時間實例。 如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet>。 |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | 單一 (Blazor WebAssembly) <br>限域 (Blazor Server)  | 包含使用 Uri 和流覽狀態的協助程式。 如需詳細資訊，請參閱 [URI 和流覽狀態](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)協助程式。 |

自訂服務提供者不會自動提供表格中所列的預設服務。 如果您使用自訂服務提供者，而且需要表格中所顯示的任何服務，請將所需的服務加入至新的服務提供者。

## <a name="add-services-to-an-app"></a>將服務新增至應用程式

### Blazor WebAssembly

在的方法中設定應用程式服務集合的服務 `Main` `Program.cs` 。 在下列範例中， `MyDependency` 會為 `IMyDependency` 執行註冊：

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
using Microsoft.Extensions.DependencyInjection;

public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        await builder.Build().RunAsync();
    }
}
```

建立主機之後，就可以從根 DI 範圍存取服務，再轉譯任何元件。 這有助於在轉譯內容之前執行初始化邏輯：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

主機也會提供應用程式的中央設定實例。 根據上述範例，氣象服務的 URL 會從預設的設定來源傳遞 (例如， `appsettings.json`) 到 `InitializeWeatherAsync` ：

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.Services.AddSingleton<WeatherService>();
        builder.RootComponents.Add<App>("app");
        
        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

### Blazor Server

建立新的應用程式之後，請檢查 `Startup.ConfigureServices` 方法：

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>會傳遞方法 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ，這是 () 的服務描述元物件清單 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 。 服務是藉 `ConfigureServices` 由提供服務描述項給服務集合，在方法中新增。 下列範例示範 `IDataAccess` 介面和其具體實作為的概念 `DataAccess` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>服務存留期

您可以使用下表所示的存留期來設定服務。

| 存留期 | 描述 |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly 應用程式目前不具有 DI 範圍的概念。 `Scoped`-註冊的服務行為類似 `Singleton` 服務。 不過， Blazor Server 裝載模型支援 `Scoped` 存留期。 在 Blazor Server 應用程式中，範圍服務註冊的範圍為 *連接* 。 基於這個理由，即使目前的目的是要在瀏覽器中執行用戶端，也最好使用範圍服務來作為應範圍為目前使用者的服務。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | DI 會建立服務的 *單一實例* 。 所有需要服務的元件 `Singleton` 都會收到相同服務的實例。 |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | 每當元件 `Transient` 從服務容器取得服務的實例時，就會收到服務的 *新實例* 。 |

DI 系統是以 ASP.NET Core 中的 DI 系統為基礎。 如需詳細資訊，請參閱<xref:fundamentals/dependency-injection>。

## <a name="request-a-service-in-a-component"></a>要求元件中的服務

將服務加入至服務集合之後，請使用[ \@ 插入](xref:mvc/views/razor#inject)指示詞將服務插入至元件 Razor 。 [`@inject`](xref:mvc/views/razor#inject) 有兩個參數：

* 類型：要插入的服務類型。
* 屬性：接收插入的 app service 之屬性的名稱。 屬性不需要手動建立。 編譯器會建立屬性。

如需詳細資訊，請參閱<xref:mvc/views/dependency-injection>。

使用多個 [`@inject`](xref:mvc/views/razor#inject) 語句插入不同的服務。

下列範例顯示如何使用 [`@inject`](xref:mvc/views/razor#inject) 。 執行的服務 `Services.IDataAccess` 會插入元件的屬性中 `DataRepository` 。 請注意，程式碼只會使用 `IDataAccess` 抽象概念：

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

就內部而言，產生的屬性 (`DataRepository`) 會使用 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 屬性。 一般而言，不會直接使用此屬性。 如果基類是元件的必要項，而且基類也需要插入的屬性，請手動加入 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 屬性：

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

在衍生自基類的元件中， [`@inject`](xref:mvc/views/razor#inject) 不需要指示詞。 <xref:Microsoft.AspNetCore.Components.InjectAttribute>基類的是已足夠的：

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>使用服務中的 DI

複雜的服務可能需要其他服務。 在先前的範例中， `DataAccess` 可能需要 <xref:System.Net.Http.HttpClient> 預設服務。 [`@inject`](xref:mvc/views/razor#inject) (或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 屬性) 無法在服務中使用。 必須改為使用函式 *插入* 。 將參數加入至服務的函式，即可新增必要的服務。 當 DI 建立服務時，它會辨識其在函式中所需的服務，並據以提供它們。 在下列範例中，此函式會接收 <xref:System.Net.Http.HttpClient> VIA DI 的。 <xref:System.Net.Http.HttpClient> 是預設服務。

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

函式插入的必要條件：

* 必須有一個函式，其引數可由 DI 完成。 如果指定預設值，則允許 DI 未涵蓋的其他參數。
* 適用的函式必須是 `public` 。
* 其中一個適用的函式必須存在。 如果不明確，DI 會擲回例外狀況。

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>管理 DI 範圍的公用程式基底元件類別

在 ASP.NET Core 應用程式中，範圍服務的範圍通常是目前的要求。 在要求完成之後，DI 系統會處置任何範圍或暫時性的服務。 在 Blazor Server 應用程式中，要求範圍會持續進行用戶端連線的持續時間，這可能會導致暫時性和範圍服務的時間比預期更長。 在 Blazor WebAssembly 應用程式中，以限域存留期註冊的服務會被視為 singleton，所以它們的存留時間比一般 ASP.NET Core 應用程式中的範圍服務長。

> [!NOTE]
> 若要在應用程式中偵測可處置的暫時性服務，請參閱偵測 [暫時性可處置專案](#detect-transient-disposables) 一節。

在應用程式中限制服務存留期的方法 Blazor 是使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 類型。 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是衍生自的抽象型別 <xref:Microsoft.AspNetCore.Components.ComponentBase> ，它會建立對應至元件存留期的 DI 範圍。 使用此範圍，您可以使用具有範圍存留期的 DI 服務，並讓它們存留（只要元件的話）。 當元件損毀時，元件範圍服務提供者的服務也會一併處置。 這適用于下列服務：

* 應該在元件中重複使用，因為暫時性存留期不適當。
* 不應該在元件之間共用，因為 singleton 存留期不適當。

有兩種版本的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 類型可供使用：

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 這是 <xref:Microsoft.AspNetCore.Components.ComponentBase> 具有 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 類型之 protected 屬性之類型的抽象、可處置的子系 <xref:System.IServiceProvider> 。 您可以使用此提供者來解析範圍為元件存留期的服務。

  使用插入至元件的 DI 服務， [`@inject`](xref:mvc/views/razor#inject) 或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 不在元件的範圍中建立屬性。 若要使用元件的範圍，必須使用或來解析 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 服務 <xref:System.IServiceProvider.GetService%2A> 。 使用提供者解析的任何服務 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 都有從相同範圍提供的相依性。

  ```razor
  @page "/preferences"
  @using Microsoft.Extensions.DependencyInjection
  @inherits OwningComponentBase

  <h1>User (@UserService.Name)</h1>

  <ul>
      @foreach (var setting in SettingService.GetSettings())
      {
          <li>@setting.SettingName: @setting.SettingValue</li>
      }
  </ul>

  @code {
      private IUserService UserService { get; set; }
      private ISettingService SettingService { get; set; }

      protected override void OnInitialized()
      {
          UserService = ScopedServices.GetRequiredService<IUserService>();
          SettingService = ScopedServices.GetRequiredService<ISettingService>();
      }
  }
  ```

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> 衍生自 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 並加入 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 屬性，該屬性會從已設定 `T` 範圍的 DI 提供者傳回的實例。 <xref:System.IServiceProvider>當應用程式從 DI 容器使用元件的範圍時，此類型可方便存取範圍服務，而不需要使用的實例。 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>屬性可供使用，因此應用程式可以視需要取得其他類型的服務。

  ```razor
  @page "/users"
  @attribute [Authorize]
  @inherits OwningComponentBase<AppDbContext>

  <h1>Users (@Service.Users.Count())</h1>

  <ul>
      @foreach (var user in Service.Users)
      {
          <li>@user.UserName</li>
      }
  </ul>
  ```

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>從 DI 使用 Entity Framework Core (EF Core) DbCoNtext

如需詳細資訊，請參閱<xref:blazor/blazor-server-ef-core>。

## <a name="detect-transient-disposables"></a>偵測暫時性可處置專案

下列範例示範如何在應使用的應用程式中偵測可處置的暫時性服務 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 。 如需詳細資訊，請參閱 [管理 DI 領域的公用程式基底元件類別](#utility-base-component-classes-to-manage-a-di-scope) 一節。

### Blazor WebAssembly

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

`TransientDisposable` () 偵測到下列範例中的 `Program.cs` ：

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

`Program`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

`TransientDependency` () 偵測到下列範例中的 `Startup.cs` ：

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a>其他資源

* <xref:fundamentals/dependency-injection>
* [`IDisposable` 暫時性和共用實例的指引](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
