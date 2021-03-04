---
title: ASP.NET 核心相依性 Blazor 插入
author: guardrex
description: 瞭解 Blazor 應用程式如何將服務插入至元件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/19/2020
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
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: aefeac2f3a68a669b7c84cbeee2f6a4ec0621f6f
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109672"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="32d2e-103">ASP.NET 核心相依性 Blazor 插入</span><span class="sxs-lookup"><span data-stu-id="32d2e-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="32d2e-104">依 [Rainer Stropek](https://www.timecockpit.com) 和 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="32d2e-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="32d2e-105">[ (DI) ](xref:fundamentals/dependency-injection) 的相依性插入是存取集中位置所設定之服務的技術：</span><span class="sxs-lookup"><span data-stu-id="32d2e-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="32d2e-106">架構註冊的服務可以直接插入應用程式的元件中 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="32d2e-107">Blazor 應用程式會定義並註冊自訂服務，並透過 DI 讓它們可在整個應用程式中使用。</span><span class="sxs-lookup"><span data-stu-id="32d2e-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="32d2e-108">預設服務</span><span class="sxs-lookup"><span data-stu-id="32d2e-108">Default services</span></span>

<span data-ttu-id="32d2e-109">下表所示的服務通常用於 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="32d2e-109">The services shown in the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="32d2e-110">服務</span><span class="sxs-lookup"><span data-stu-id="32d2e-110">Service</span></span> | <span data-ttu-id="32d2e-111">存留期</span><span class="sxs-lookup"><span data-stu-id="32d2e-111">Lifetime</span></span> | <span data-ttu-id="32d2e-112">描述</span><span class="sxs-lookup"><span data-stu-id="32d2e-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="32d2e-113">具範圍</span><span class="sxs-lookup"><span data-stu-id="32d2e-113">Scoped</span></span> | <p><span data-ttu-id="32d2e-114">提供方法來傳送 HTTP 要求，以及從 URI 所識別的資源接收 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="32d2e-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="32d2e-115"><xref:System.Net.Http.HttpClient>應用程式中的實例會 Blazor WebAssembly 使用瀏覽器來處理背景中的 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="32d2e-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="32d2e-116">Blazor Server 依預設，應用程式不會包含 <xref:System.Net.Http.HttpClient> 已設定為服務的服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="32d2e-117">提供 <xref:System.Net.Http.HttpClient> 給 Blazor Server 應用程式。</span><span class="sxs-lookup"><span data-stu-id="32d2e-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="32d2e-118">如需詳細資訊，請參閱<xref:blazor/call-web-api>。</span><span class="sxs-lookup"><span data-stu-id="32d2e-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="32d2e-119"><xref:System.Net.Http.HttpClient>註冊為範圍服務，而非 singleton。</span><span class="sxs-lookup"><span data-stu-id="32d2e-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="32d2e-120">如需詳細資訊，請參閱 [服務存留期](#service-lifetime) 一節。</span><span class="sxs-lookup"><span data-stu-id="32d2e-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="32d2e-121">**Blazor WebAssembly**： Singleton</span><span class="sxs-lookup"><span data-stu-id="32d2e-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="32d2e-122">**Blazor Server**：限域</span><span class="sxs-lookup"><span data-stu-id="32d2e-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="32d2e-123">代表 javascript 呼叫會分派至其中的 JavaScript 執行時間實例。</span><span class="sxs-lookup"><span data-stu-id="32d2e-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="32d2e-124">如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="32d2e-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="32d2e-125">**Blazor WebAssembly**： Singleton</span><span class="sxs-lookup"><span data-stu-id="32d2e-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="32d2e-126">**Blazor Server**：限域</span><span class="sxs-lookup"><span data-stu-id="32d2e-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="32d2e-127">包含使用 Uri 和流覽狀態的協助程式。</span><span class="sxs-lookup"><span data-stu-id="32d2e-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="32d2e-128">如需詳細資訊，請參閱 [URI 和流覽狀態](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)協助程式。</span><span class="sxs-lookup"><span data-stu-id="32d2e-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="32d2e-129">自訂服務提供者不會自動提供表格中所列的預設服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="32d2e-130">如果您使用自訂服務提供者，而且需要表格中所顯示的任何服務，請將所需的服務加入至新的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="32d2e-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="32d2e-131">將服務新增至應用程式</span><span class="sxs-lookup"><span data-stu-id="32d2e-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="32d2e-132">在的方法中設定應用程式服務集合的服務 `Program.Main` `Program.cs` 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="32d2e-133">在下列範例中， `MyDependency` 會為 `IMyDependency` 執行註冊：</span><span class="sxs-lookup"><span data-stu-id="32d2e-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<IMyDependency, MyDependency>();
        ...

        await builder.Build().RunAsync();
    }
}
```

<span data-ttu-id="32d2e-134">建立主機之後，可從根 DI 範圍取得服務，再轉譯任何元件。</span><span class="sxs-lookup"><span data-stu-id="32d2e-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="32d2e-135">這有助於在轉譯內容之前執行初始化邏輯：</span><span class="sxs-lookup"><span data-stu-id="32d2e-135">This can be useful for running initialization logic before rendering content:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync();

        await host.RunAsync();
    }
}
```

<span data-ttu-id="32d2e-136">主機會提供應用程式的中央設定實例。</span><span class="sxs-lookup"><span data-stu-id="32d2e-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="32d2e-137">根據上述範例，氣象服務的 URL 會從預設的設定來源傳遞 (例如， `appsettings.json`) 到 `InitializeWeatherAsync` ：</span><span class="sxs-lookup"><span data-stu-id="32d2e-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        ...
        builder.Services.AddSingleton<WeatherService>();
        ...

        var host = builder.Build();

        var weatherService = host.Services.GetRequiredService<WeatherService>();
        await weatherService.InitializeWeatherAsync(
            host.Configuration["WeatherServiceUrl"]);

        await host.RunAsync();
    }
}
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="32d2e-138">建立新的應用程式之後，請檢查 `Startup.ConfigureServices` 中的方法 `Startup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="32d2e-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="32d2e-139"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>會傳遞方法 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ，這是[服務描述](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor)元物件的清單。</span><span class="sxs-lookup"><span data-stu-id="32d2e-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="32d2e-140">服務是藉 `ConfigureServices` 由提供服務描述項給服務集合，在方法中新增。</span><span class="sxs-lookup"><span data-stu-id="32d2e-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="32d2e-141">下列範例示範 `IDataAccess` 介面和其具體實作為的概念 `DataAccess` ：</span><span class="sxs-lookup"><span data-stu-id="32d2e-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="32d2e-142">服務存留期</span><span class="sxs-lookup"><span data-stu-id="32d2e-142">Service lifetime</span></span>

<span data-ttu-id="32d2e-143">您可以使用下表所示的存留期來設定服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="32d2e-144">存留期</span><span class="sxs-lookup"><span data-stu-id="32d2e-144">Lifetime</span></span> | <span data-ttu-id="32d2e-145">描述</span><span class="sxs-lookup"><span data-stu-id="32d2e-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="32d2e-146">Blazor WebAssembly 應用程式目前不具有 DI 範圍的概念。</span><span class="sxs-lookup"><span data-stu-id="32d2e-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="32d2e-147">`Scoped`-註冊的服務行為類似 `Singleton` 服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="32d2e-148">裝載 Blazor Server 模型支援 `Scoped` 跨 HTTP 要求的存留期，而不 SignalR 是在用戶端上載入的元件之間的連接/線路訊息之間的存留期。</span><span class="sxs-lookup"><span data-stu-id="32d2e-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SignalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="32d2e-149">Razor應用程式的頁面或 MVC 部分會以一般方式來處理已設定範圍的服務，並在頁面或視圖之間流覽，或從頁面或視圖切換至元件時，在 *每個 HTTP 要求* 上重新建立服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="32d2e-150">在用戶端上流覽元件時，不會重建已設定範圍的服務，而伺服器的通訊會透過使用者的線路連線來進行， SignalR 而不是透過 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="32d2e-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="32d2e-151">在用戶端上的下列元件案例中，會重建範圍服務，因為系統會為使用者建立新的線路：</span><span class="sxs-lookup"><span data-stu-id="32d2e-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="32d2e-152">使用者關閉瀏覽器視窗。</span><span class="sxs-lookup"><span data-stu-id="32d2e-152">The user closes the browser's window.</span></span> <span data-ttu-id="32d2e-153">使用者會開啟新視窗，並流覽回應用程式。</span><span class="sxs-lookup"><span data-stu-id="32d2e-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="32d2e-154">使用者會在瀏覽器視窗中關閉應用程式的最後一個索引標籤。</span><span class="sxs-lookup"><span data-stu-id="32d2e-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="32d2e-155">使用者會開啟新的索引標籤，並流覽回應用程式。</span><span class="sxs-lookup"><span data-stu-id="32d2e-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="32d2e-156">使用者選取瀏覽器的 [重載/重新整理] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="32d2e-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="32d2e-157">如需在應用程式中跨範圍服務保留使用者狀態的詳細資訊 Blazor Server ，請參閱 <xref:blazor/hosting-models?pivots=server> 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="32d2e-158">DI 會建立服務的 *單一實例* 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="32d2e-159">所有需要服務的元件 `Singleton` 都會收到相同服務的實例。</span><span class="sxs-lookup"><span data-stu-id="32d2e-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="32d2e-160">每當元件 `Transient` 從服務容器取得服務的實例時，就會收到服務的 *新實例* 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="32d2e-161">DI 系統是以 ASP.NET Core 中的 DI 系統為基礎。</span><span class="sxs-lookup"><span data-stu-id="32d2e-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="32d2e-162">如需詳細資訊，請參閱<xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="32d2e-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="32d2e-163">要求元件中的服務</span><span class="sxs-lookup"><span data-stu-id="32d2e-163">Request a service in a component</span></span>

<span data-ttu-id="32d2e-164">將服務加入至服務集合之後，請使用指示詞將服務插入元件中 [`@inject`](xref:mvc/views/razor#inject) Razor ，其中有兩個參數：</span><span class="sxs-lookup"><span data-stu-id="32d2e-164">After services are added to the service collection, inject the services into the components using the [`@inject`](xref:mvc/views/razor#inject) Razor directive, which has two parameters:</span></span>

* <span data-ttu-id="32d2e-165">類型：要插入的服務類型。</span><span class="sxs-lookup"><span data-stu-id="32d2e-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="32d2e-166">屬性：接收插入的 app service 之屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="32d2e-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="32d2e-167">屬性不需要手動建立。</span><span class="sxs-lookup"><span data-stu-id="32d2e-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="32d2e-168">編譯器會建立屬性。</span><span class="sxs-lookup"><span data-stu-id="32d2e-168">The compiler creates the property.</span></span>

<span data-ttu-id="32d2e-169">如需詳細資訊，請參閱<xref:mvc/views/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="32d2e-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="32d2e-170">使用多個 [`@inject`](xref:mvc/views/razor#inject) 語句插入不同的服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="32d2e-171">下列範例顯示如何使用 [`@inject`](xref:mvc/views/razor#inject) 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="32d2e-172">執行的服務 `Services.IDataAccess` 會插入元件的屬性中 `DataRepository` 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="32d2e-173">請注意，程式碼只會使用 `IDataAccess` 抽象概念：</span><span class="sxs-lookup"><span data-stu-id="32d2e-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/dependency-injection/CustomerList.razor?name=snippet&highlight=2,19)]

::: moniker-end

<span data-ttu-id="32d2e-174">就內部而言，產生的屬性 (`DataRepository`) 會使用[ `[Inject]` 屬性](xref:Microsoft.AspNetCore.Components.InjectAttribute)。</span><span class="sxs-lookup"><span data-stu-id="32d2e-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span></span> <span data-ttu-id="32d2e-175">一般而言，不會直接使用此屬性。</span><span class="sxs-lookup"><span data-stu-id="32d2e-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="32d2e-176">如果基類是元件的必要項，而且基類也需要插入的屬性，請手動加入[ `[Inject]` 屬性](xref:Microsoft.AspNetCore.Components.InjectAttribute)：</span><span class="sxs-lookup"><span data-stu-id="32d2e-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute):</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="32d2e-177">在衍生自基類的元件中， [`@inject`](xref:mvc/views/razor#inject) 不需要指示詞。</span><span class="sxs-lookup"><span data-stu-id="32d2e-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="32d2e-178"><xref:Microsoft.AspNetCore.Components.InjectAttribute>基類的是已足夠的：</span><span class="sxs-lookup"><span data-stu-id="32d2e-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="32d2e-179">使用服務中的 DI</span><span class="sxs-lookup"><span data-stu-id="32d2e-179">Use DI in services</span></span>

<span data-ttu-id="32d2e-180">複雜的服務可能需要其他服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-180">Complex services might require additional services.</span></span> <span data-ttu-id="32d2e-181">在下列範例中， `DataAccess` 需要 <xref:System.Net.Http.HttpClient> 預設服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-181">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="32d2e-182">[`@inject`](xref:mvc/views/razor#inject) (或[ `[Inject]` 屬性](xref:Microsoft.AspNetCore.Components.InjectAttribute)) 無法在服務中使用。</span><span class="sxs-lookup"><span data-stu-id="32d2e-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute)) isn't available for use in services.</span></span> <span data-ttu-id="32d2e-183">必須改為使用函式 *插入*。</span><span class="sxs-lookup"><span data-stu-id="32d2e-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="32d2e-184">將參數加入至服務的函式，即可新增必要的服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="32d2e-185">當 DI 建立服務時，它會辨識其在函式中所需的服務，並據以提供它們。</span><span class="sxs-lookup"><span data-stu-id="32d2e-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="32d2e-186">在下列範例中，此函式會接收 <xref:System.Net.Http.HttpClient> VIA DI 的。</span><span class="sxs-lookup"><span data-stu-id="32d2e-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="32d2e-187"><xref:System.Net.Http.HttpClient> 是預設服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="32d2e-188">函式插入的必要條件：</span><span class="sxs-lookup"><span data-stu-id="32d2e-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="32d2e-189">必須有一個函式，其引數可由 DI 完成。</span><span class="sxs-lookup"><span data-stu-id="32d2e-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="32d2e-190">如果指定預設值，則允許 DI 未涵蓋的其他參數。</span><span class="sxs-lookup"><span data-stu-id="32d2e-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="32d2e-191">適用的函式必須是 `public` 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="32d2e-192">其中一個適用的函式必須存在。</span><span class="sxs-lookup"><span data-stu-id="32d2e-192">One applicable constructor must exist.</span></span> <span data-ttu-id="32d2e-193">如果不明確，DI 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="32d2e-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="32d2e-194">管理 DI 範圍的公用程式基底元件類別</span><span class="sxs-lookup"><span data-stu-id="32d2e-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="32d2e-195">在 ASP.NET Core 應用程式中，範圍服務的範圍通常是目前的要求。</span><span class="sxs-lookup"><span data-stu-id="32d2e-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="32d2e-196">在要求完成之後，DI 系統會處置任何範圍或暫時性的服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="32d2e-197">在 Blazor Server 應用程式中，要求範圍會持續進行用戶端連線的持續時間，這可能會導致暫時性和範圍服務的時間比預期更長。</span><span class="sxs-lookup"><span data-stu-id="32d2e-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="32d2e-198">在 Blazor WebAssembly 應用程式中，以限域存留期註冊的服務會被視為 singleton，所以它們的存留時間比一般 ASP.NET Core 應用程式中的範圍服務長。</span><span class="sxs-lookup"><span data-stu-id="32d2e-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="32d2e-199">若要在應用程式中偵測可處置的暫時性服務，請參閱偵測 [暫時性可處置專案](#detect-transient-disposables) 一節。</span><span class="sxs-lookup"><span data-stu-id="32d2e-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="32d2e-200">在應用程式中限制服務存留期的方法 Blazor 是使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 類型。</span><span class="sxs-lookup"><span data-stu-id="32d2e-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="32d2e-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是衍生自的抽象型別 <xref:Microsoft.AspNetCore.Components.ComponentBase> ，它會建立對應至元件存留期的 DI 範圍。</span><span class="sxs-lookup"><span data-stu-id="32d2e-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="32d2e-202">使用此範圍，您可以使用具有範圍存留期的 DI 服務，並讓它們存留（只要元件的話）。</span><span class="sxs-lookup"><span data-stu-id="32d2e-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="32d2e-203">當元件損毀時，元件範圍服務提供者的服務也會一併處置。</span><span class="sxs-lookup"><span data-stu-id="32d2e-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="32d2e-204">這適用于下列服務：</span><span class="sxs-lookup"><span data-stu-id="32d2e-204">This can be useful for services that:</span></span>

* <span data-ttu-id="32d2e-205">應該在元件中重複使用，因為暫時性存留期不適當。</span><span class="sxs-lookup"><span data-stu-id="32d2e-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="32d2e-206">不應該在元件之間共用，因為 singleton 存留期不適當。</span><span class="sxs-lookup"><span data-stu-id="32d2e-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="32d2e-207">有兩種版本的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 類型可供使用：</span><span class="sxs-lookup"><span data-stu-id="32d2e-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="32d2e-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 這是 <xref:Microsoft.AspNetCore.Components.ComponentBase> 具有 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 類型之 protected 屬性之類型的抽象、可處置的子系 <xref:System.IServiceProvider> 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="32d2e-209">您可以使用此提供者來解析範圍為元件存留期的服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="32d2e-210">使用插入至元件的 DI 服務， [`@inject`](xref:mvc/views/razor#inject) 或不在元件的範圍中建立[ `[Inject]` 屬性](xref:Microsoft.AspNetCore.Components.InjectAttribute)。</span><span class="sxs-lookup"><span data-stu-id="32d2e-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]` attribute](xref:Microsoft.AspNetCore.Components.InjectAttribute) aren't created in the component's scope.</span></span> <span data-ttu-id="32d2e-211">若要使用元件的範圍，必須使用或來解析 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 服務 <xref:System.IServiceProvider.GetService%2A> 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="32d2e-212">使用提供者解析的任何服務 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 都有從相同範圍提供的相依性。</span><span class="sxs-lookup"><span data-stu-id="32d2e-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  ::: moniker range=">= aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

  ::: moniker range="< aspnetcore-5.0"

  [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/dependency-injection/Preferences.razor?name=snippet&highlight=3,20-21)]

  ::: moniker-end

* <span data-ttu-id="32d2e-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> 衍生自 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 並加入 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 屬性，該屬性會從已設定 `T` 範圍的 DI 提供者傳回的實例。</span><span class="sxs-lookup"><span data-stu-id="32d2e-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="32d2e-214"><xref:System.IServiceProvider>當應用程式從 DI 容器使用元件的範圍時，此類型可方便存取範圍服務，而不需要使用的實例。</span><span class="sxs-lookup"><span data-stu-id="32d2e-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="32d2e-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>屬性可供使用，因此應用程式可以視需要取得其他類型的服務。</span><span class="sxs-lookup"><span data-stu-id="32d2e-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="32d2e-216">從 DI 使用 Entity Framework Core (EF Core) DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="32d2e-216">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="32d2e-217">如需詳細資訊，請參閱<xref:blazor/blazor-server-ef-core>。</span><span class="sxs-lookup"><span data-stu-id="32d2e-217">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="32d2e-218">偵測暫時性可處置專案</span><span class="sxs-lookup"><span data-stu-id="32d2e-218">Detect transient disposables</span></span>

<span data-ttu-id="32d2e-219">下列範例示範如何在應使用的應用程式中偵測可處置的暫時性服務 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-219">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="32d2e-220">如需詳細資訊，請參閱 [管理 DI 領域的公用程式基底元件類別](#utility-base-component-classes-to-manage-a-di-scope) 一節。</span><span class="sxs-lookup"><span data-stu-id="32d2e-220">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="32d2e-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="32d2e-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

<span data-ttu-id="32d2e-222">`TransientDisposable` () 偵測到下列範例中的 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="32d2e-222">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("#app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.DetectIncorrectUsageOfTransients();
        builder.RootComponents.Add<App>("app");

        builder.Services.AddTransient<TransientDisposable>();
        builder.Services.AddScoped(sp =>
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

        var host = builder.Build();
        host.EnableTransientDisposableDetection();
        await host.RunAsync();
    }
}

public class TransientDisposable : IDisposable
{
    public void Dispose() => throw new NotImplementedException();
}
```

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="32d2e-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="32d2e-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/dependency-injection/DetectIncorrectUsagesOfTransientDisposables.cs)]

::: moniker-end

<span data-ttu-id="32d2e-224">將命名空間加入 <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 至 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="32d2e-224">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="32d2e-225">`Program.CreateHostBuilder` `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="32d2e-225">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .DetectIncorrectUsageOfTransients()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="32d2e-226">`TransientDependency` () 偵測到下列範例中的 `Startup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="32d2e-226">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
    services.AddServerSideBlazor();
    services.AddSingleton<WeatherForecastService>();
    services.AddTransient<TransientDependency>();
    services.AddTransient<ITransitiveTransientDisposableDependency, 
        TransitiveTransientDisposableDependency>();
}

public class TransitiveTransientDisposableDependency 
    : ITransitiveTransientDisposableDependency, IDisposable
{
    public void Dispose() { }
}

public interface ITransitiveTransientDisposableDependency
{
}

public class TransientDependency
{
    private readonly ITransitiveTransientDisposableDependency 
        _transitiveTransientDisposableDependency;

    public TransientDependency(ITransitiveTransientDisposableDependency 
        transitiveTransientDisposableDependency)
    {
        _transitiveTransientDisposableDependency = 
            transitiveTransientDisposableDependency;
    }
}
```

::: zone-end

<span data-ttu-id="32d2e-227">應用程式可以在不擲回例外狀況的情況下註冊暫時性可處置專案。</span><span class="sxs-lookup"><span data-stu-id="32d2e-227">The app can register transient disposables without throwing an exception.</span></span> <span data-ttu-id="32d2e-228">不過，嘗試在中解析暫時性可處置的結果 <xref:System.InvalidOperationException> ，如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="32d2e-228">However, attempting to resolve a transient disposable results in an <xref:System.InvalidOperationException>, as the following example shows.</span></span>

<span data-ttu-id="32d2e-229">`Pages/TransientDisposable.razor`:</span><span class="sxs-lookup"><span data-stu-id="32d2e-229">`Pages/TransientDisposable.razor`:</span></span>

```razor
@page "/transient-disposable"
@inject TransientDisposable TransientDisposable

<h1>Transient Disposable Detection</h1>
```

<span data-ttu-id="32d2e-230">流覽至中的 `TransientDisposable` 元件 `/transient-disposable` ， <xref:System.InvalidOperationException> 當架構嘗試建立的實例時，就會擲回 `TransientDisposable` 。</span><span class="sxs-lookup"><span data-stu-id="32d2e-230">Navigate to the `TransientDisposable` component at `/transient-disposable` and an <xref:System.InvalidOperationException> is thrown when the framework attempts to construct an instance of `TransientDisposable`:</span></span>

> <span data-ttu-id="32d2e-231">InvalidOperationException：嘗試在錯誤的範圍內解析暫時性可處置的服務 TransientDisposable。</span><span class="sxs-lookup"><span data-stu-id="32d2e-231">System.InvalidOperationException: Trying to resolve transient disposable service TransientDisposable in the wrong scope.</span></span> <span data-ttu-id="32d2e-232">\<T>針對您嘗試解析的服務 ' t '，請使用 ' OwningComponentBase ' 元件基底類別。</span><span class="sxs-lookup"><span data-stu-id="32d2e-232">Use an 'OwningComponentBase\<T>' component base class for the service 'T' you are trying to resolve.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="32d2e-233">其他資源</span><span class="sxs-lookup"><span data-stu-id="32d2e-233">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="32d2e-234">`IDisposable` 暫時性和共用實例的指引</span><span class="sxs-lookup"><span data-stu-id="32d2e-234">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
