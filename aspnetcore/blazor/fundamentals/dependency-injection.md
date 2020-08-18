---
title: ASP.NET Core 相依性 Blazor 插入
author: guardrex
description: 瞭解 Blazor 應用程式如何將服務插入至元件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
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
ms.openlocfilehash: 40c47213021bf82150be2b41201b6af3228e4485
ms.sourcegitcommit: 4df445e7d49a99f81625430f728c28e5d6bf2107
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/15/2020
ms.locfileid: "88253560"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="bfaea-103">ASP.NET Core 相依性 Blazor 插入</span><span class="sxs-lookup"><span data-stu-id="bfaea-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="bfaea-104">依 [Rainer Stropek](https://www.timecockpit.com) 和 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="bfaea-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="bfaea-105">Blazor 支援 [ (DI) ](xref:fundamentals/dependency-injection)的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="bfaea-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="bfaea-106">應用程式可以使用內建服務，方法是將它們插入元件中。</span><span class="sxs-lookup"><span data-stu-id="bfaea-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="bfaea-107">應用程式也可以定義和註冊自訂服務，並透過 DI 讓它們可在整個應用程式中使用。</span><span class="sxs-lookup"><span data-stu-id="bfaea-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="bfaea-108">DI 是存取中央位置中設定之服務的一種技術。</span><span class="sxs-lookup"><span data-stu-id="bfaea-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="bfaea-109">這在應用程式中可能很有用 Blazor ：</span><span class="sxs-lookup"><span data-stu-id="bfaea-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="bfaea-110">在許多元件（稱為 *單一* 服務）之間共用服務類別的單一實例。</span><span class="sxs-lookup"><span data-stu-id="bfaea-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="bfaea-111">使用參考抽象概念將元件與具體的服務類別分離。</span><span class="sxs-lookup"><span data-stu-id="bfaea-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="bfaea-112">例如，請考慮 `IDataAccess` 用來存取應用程式中資料的介面。</span><span class="sxs-lookup"><span data-stu-id="bfaea-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="bfaea-113">介面是由實體類別所執行 `DataAccess` ，並在應用程式的服務容器中註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="bfaea-114">當元件使用 DI 來接收執行時 `IDataAccess` ，該元件不會與具象型別結合。</span><span class="sxs-lookup"><span data-stu-id="bfaea-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="bfaea-115">您可以交換實作為單元測試中的 mock 實作為。</span><span class="sxs-lookup"><span data-stu-id="bfaea-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="bfaea-116">預設服務</span><span class="sxs-lookup"><span data-stu-id="bfaea-116">Default services</span></span>

<span data-ttu-id="bfaea-117">預設服務會自動加入至應用程式的服務集合。</span><span class="sxs-lookup"><span data-stu-id="bfaea-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="bfaea-118">服務</span><span class="sxs-lookup"><span data-stu-id="bfaea-118">Service</span></span> | <span data-ttu-id="bfaea-119">存留期</span><span class="sxs-lookup"><span data-stu-id="bfaea-119">Lifetime</span></span> | <span data-ttu-id="bfaea-120">描述</span><span class="sxs-lookup"><span data-stu-id="bfaea-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="bfaea-121">具範圍</span><span class="sxs-lookup"><span data-stu-id="bfaea-121">Scoped</span></span> | <span data-ttu-id="bfaea-122">提供方法來傳送 HTTP 要求，以及從 URI 所識別的資源接收 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="bfaea-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="bfaea-123"><xref:System.Net.Http.HttpClient>應用程式中的實例會 Blazor WebAssembly 使用瀏覽器來處理背景中的 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="bfaea-123">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="bfaea-124">Blazor Server 依預設，應用程式不會包含 <xref:System.Net.Http.HttpClient> 已設定為服務的服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-124">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="bfaea-125">提供 <xref:System.Net.Http.HttpClient> 給 Blazor Server 應用程式。</span><span class="sxs-lookup"><span data-stu-id="bfaea-125">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span><br><br><span data-ttu-id="bfaea-126">如需詳細資訊，請參閱<xref:blazor/call-web-api>。</span><span class="sxs-lookup"><span data-stu-id="bfaea-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="bfaea-127">單一 (Blazor WebAssembly) </span><span class="sxs-lookup"><span data-stu-id="bfaea-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="bfaea-128">限域 (Blazor Server) </span><span class="sxs-lookup"><span data-stu-id="bfaea-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="bfaea-129">代表 javascript 呼叫會分派至其中的 JavaScript 執行時間實例。</span><span class="sxs-lookup"><span data-stu-id="bfaea-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="bfaea-130">如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="bfaea-130">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="bfaea-131">單一 (Blazor WebAssembly) </span><span class="sxs-lookup"><span data-stu-id="bfaea-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="bfaea-132">限域 (Blazor Server) </span><span class="sxs-lookup"><span data-stu-id="bfaea-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="bfaea-133">包含使用 Uri 和流覽狀態的協助程式。</span><span class="sxs-lookup"><span data-stu-id="bfaea-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="bfaea-134">如需詳細資訊，請參閱 [URI 和流覽狀態](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)協助程式。</span><span class="sxs-lookup"><span data-stu-id="bfaea-134">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="bfaea-135">自訂服務提供者不會自動提供表格中所列的預設服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="bfaea-136">如果您使用自訂服務提供者，而且需要表格中所顯示的任何服務，請將所需的服務加入至新的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="bfaea-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="bfaea-137">將服務新增至應用程式</span><span class="sxs-lookup"><span data-stu-id="bfaea-137">Add services to an app</span></span>

### Blazor WebAssembly

<span data-ttu-id="bfaea-138">在的方法中設定應用程式服務集合的服務 `Main` `Program.cs` 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-138">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="bfaea-139">在下列範例中， `MyDependency` 會為 `IMyDependency` 執行註冊：</span><span class="sxs-lookup"><span data-stu-id="bfaea-139">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="bfaea-140">建立主機之後，就可以從根 DI 範圍存取服務，再轉譯任何元件。</span><span class="sxs-lookup"><span data-stu-id="bfaea-140">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="bfaea-141">這有助於在轉譯內容之前執行初始化邏輯：</span><span class="sxs-lookup"><span data-stu-id="bfaea-141">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="bfaea-142">主機也會提供應用程式的中央設定實例。</span><span class="sxs-lookup"><span data-stu-id="bfaea-142">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="bfaea-143">根據上述範例，氣象服務的 URL 會從預設的設定來源傳遞 (例如， `appsettings.json`) 到 `InitializeWeatherAsync` ：</span><span class="sxs-lookup"><span data-stu-id="bfaea-143">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

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

<span data-ttu-id="bfaea-144">建立新的應用程式之後，請檢查 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="bfaea-144">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="bfaea-145"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>會傳遞方法 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ，這是 () 的服務描述元物件清單 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-145">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="bfaea-146">服務是藉 `ConfigureServices` 由提供服務描述項給服務集合，在方法中新增。</span><span class="sxs-lookup"><span data-stu-id="bfaea-146">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="bfaea-147">下列範例示範 `IDataAccess` 介面和其具體實作為的概念 `DataAccess` ：</span><span class="sxs-lookup"><span data-stu-id="bfaea-147">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="bfaea-148">服務存留期</span><span class="sxs-lookup"><span data-stu-id="bfaea-148">Service lifetime</span></span>

<span data-ttu-id="bfaea-149">您可以使用下表所示的存留期來設定服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-149">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="bfaea-150">存留期</span><span class="sxs-lookup"><span data-stu-id="bfaea-150">Lifetime</span></span> | <span data-ttu-id="bfaea-151">描述</span><span class="sxs-lookup"><span data-stu-id="bfaea-151">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="bfaea-152">Blazor WebAssembly 應用程式目前不具有 DI 範圍的概念。</span><span class="sxs-lookup"><span data-stu-id="bfaea-152">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="bfaea-153">`Scoped`-註冊的服務行為類似 `Singleton` 服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-153">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="bfaea-154">不過， Blazor Server 裝載模型支援 `Scoped` 存留期。</span><span class="sxs-lookup"><span data-stu-id="bfaea-154">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="bfaea-155">在 Blazor Server 應用程式中，範圍服務註冊的範圍為 *連接*。</span><span class="sxs-lookup"><span data-stu-id="bfaea-155">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="bfaea-156">基於這個理由，即使目前的目的是要在瀏覽器中執行用戶端，也最好使用範圍服務來作為應範圍為目前使用者的服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-156">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="bfaea-157">DI 會建立服務的 *單一實例* 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-157">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="bfaea-158">所有需要服務的元件 `Singleton` 都會收到相同服務的實例。</span><span class="sxs-lookup"><span data-stu-id="bfaea-158">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="bfaea-159">每當元件 `Transient` 從服務容器取得服務的實例時，就會收到服務的 *新實例* 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-159">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="bfaea-160">DI 系統是以 ASP.NET Core 中的 DI 系統為基礎。</span><span class="sxs-lookup"><span data-stu-id="bfaea-160">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="bfaea-161">如需詳細資訊，請參閱<xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="bfaea-161">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="bfaea-162">要求元件中的服務</span><span class="sxs-lookup"><span data-stu-id="bfaea-162">Request a service in a component</span></span>

<span data-ttu-id="bfaea-163">將服務加入至服務集合之後，請使用[ \@ 插入](xref:mvc/views/razor#inject)指示詞將服務插入至元件 Razor 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-163">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="bfaea-164">[`@inject`](xref:mvc/views/razor#inject) 有兩個參數：</span><span class="sxs-lookup"><span data-stu-id="bfaea-164">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="bfaea-165">類型：要插入的服務類型。</span><span class="sxs-lookup"><span data-stu-id="bfaea-165">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="bfaea-166">屬性：接收插入的 app service 之屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="bfaea-166">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="bfaea-167">屬性不需要手動建立。</span><span class="sxs-lookup"><span data-stu-id="bfaea-167">The property doesn't require manual creation.</span></span> <span data-ttu-id="bfaea-168">編譯器會建立屬性。</span><span class="sxs-lookup"><span data-stu-id="bfaea-168">The compiler creates the property.</span></span>

<span data-ttu-id="bfaea-169">如需詳細資訊，請參閱<xref:mvc/views/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="bfaea-169">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="bfaea-170">使用多個 [`@inject`](xref:mvc/views/razor#inject) 語句插入不同的服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-170">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="bfaea-171">下列範例顯示如何使用 [`@inject`](xref:mvc/views/razor#inject) 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-171">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="bfaea-172">執行的服務 `Services.IDataAccess` 會插入元件的屬性中 `DataRepository` 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-172">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="bfaea-173">請注意，程式碼只會使用 `IDataAccess` 抽象概念：</span><span class="sxs-lookup"><span data-stu-id="bfaea-173">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="bfaea-174">就內部而言，產生的屬性 (`DataRepository`) 會使用 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="bfaea-174">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="bfaea-175">一般而言，不會直接使用此屬性。</span><span class="sxs-lookup"><span data-stu-id="bfaea-175">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="bfaea-176">如果基類是元件的必要項，而且基類也需要插入的屬性，請手動加入 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 屬性：</span><span class="sxs-lookup"><span data-stu-id="bfaea-176">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="bfaea-177">在衍生自基類的元件中， [`@inject`](xref:mvc/views/razor#inject) 不需要指示詞。</span><span class="sxs-lookup"><span data-stu-id="bfaea-177">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="bfaea-178"><xref:Microsoft.AspNetCore.Components.InjectAttribute>基類的是已足夠的：</span><span class="sxs-lookup"><span data-stu-id="bfaea-178">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="bfaea-179">使用服務中的 DI</span><span class="sxs-lookup"><span data-stu-id="bfaea-179">Use DI in services</span></span>

<span data-ttu-id="bfaea-180">複雜的服務可能需要其他服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-180">Complex services might require additional services.</span></span> <span data-ttu-id="bfaea-181">在先前的範例中， `DataAccess` 可能需要 <xref:System.Net.Http.HttpClient> 預設服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-181">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="bfaea-182">[`@inject`](xref:mvc/views/razor#inject) (或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 屬性) 無法在服務中使用。</span><span class="sxs-lookup"><span data-stu-id="bfaea-182">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="bfaea-183">必須改為使用函式*插入*。</span><span class="sxs-lookup"><span data-stu-id="bfaea-183">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="bfaea-184">將參數加入至服務的函式，即可新增必要的服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-184">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="bfaea-185">當 DI 建立服務時，它會辨識其在函式中所需的服務，並據以提供它們。</span><span class="sxs-lookup"><span data-stu-id="bfaea-185">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="bfaea-186">在下列範例中，此函式會接收 <xref:System.Net.Http.HttpClient> VIA DI 的。</span><span class="sxs-lookup"><span data-stu-id="bfaea-186">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="bfaea-187"><xref:System.Net.Http.HttpClient> 是預設服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-187"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient client)
    {
        ...
    }
}
```

<span data-ttu-id="bfaea-188">函式插入的必要條件：</span><span class="sxs-lookup"><span data-stu-id="bfaea-188">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="bfaea-189">必須有一個函式，其引數可由 DI 完成。</span><span class="sxs-lookup"><span data-stu-id="bfaea-189">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="bfaea-190">如果指定預設值，則允許 DI 未涵蓋的其他參數。</span><span class="sxs-lookup"><span data-stu-id="bfaea-190">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="bfaea-191">適用的函式必須是 `public` 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-191">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="bfaea-192">其中一個適用的函式必須存在。</span><span class="sxs-lookup"><span data-stu-id="bfaea-192">One applicable constructor must exist.</span></span> <span data-ttu-id="bfaea-193">如果不明確，DI 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="bfaea-193">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="bfaea-194">管理 DI 範圍的公用程式基底元件類別</span><span class="sxs-lookup"><span data-stu-id="bfaea-194">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="bfaea-195">在 ASP.NET Core 應用程式中，範圍服務的範圍通常是目前的要求。</span><span class="sxs-lookup"><span data-stu-id="bfaea-195">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="bfaea-196">在要求完成之後，DI 系統會處置任何範圍或暫時性的服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-196">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="bfaea-197">在 Blazor Server 應用程式中，要求範圍會持續進行用戶端連線的持續時間，這可能會導致暫時性和範圍服務的時間比預期更長。</span><span class="sxs-lookup"><span data-stu-id="bfaea-197">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="bfaea-198">在 Blazor WebAssembly 應用程式中，以限域存留期註冊的服務會被視為 singleton，所以它們的存留時間比一般 ASP.NET Core 應用程式中的範圍服務長。</span><span class="sxs-lookup"><span data-stu-id="bfaea-198">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="bfaea-199">若要在應用程式中偵測可處置的暫時性服務，請參閱偵測 [暫時性可處置專案](#detect-transient-disposables) 一節。</span><span class="sxs-lookup"><span data-stu-id="bfaea-199">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="bfaea-200">在應用程式中限制服務存留期的方法 Blazor 是使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 類型。</span><span class="sxs-lookup"><span data-stu-id="bfaea-200">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="bfaea-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是衍生自的抽象型別 <xref:Microsoft.AspNetCore.Components.ComponentBase> ，它會建立對應至元件存留期的 DI 範圍。</span><span class="sxs-lookup"><span data-stu-id="bfaea-201"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="bfaea-202">使用此範圍，您可以使用具有範圍存留期的 DI 服務，並讓它們存留（只要元件的話）。</span><span class="sxs-lookup"><span data-stu-id="bfaea-202">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="bfaea-203">當元件損毀時，元件範圍服務提供者的服務也會一併處置。</span><span class="sxs-lookup"><span data-stu-id="bfaea-203">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="bfaea-204">這適用于下列服務：</span><span class="sxs-lookup"><span data-stu-id="bfaea-204">This can be useful for services that:</span></span>

* <span data-ttu-id="bfaea-205">應該在元件中重複使用，因為暫時性存留期不適當。</span><span class="sxs-lookup"><span data-stu-id="bfaea-205">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="bfaea-206">不應該在元件之間共用，因為 singleton 存留期不適當。</span><span class="sxs-lookup"><span data-stu-id="bfaea-206">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="bfaea-207">有兩種版本的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 類型可供使用：</span><span class="sxs-lookup"><span data-stu-id="bfaea-207">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="bfaea-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 這是 <xref:Microsoft.AspNetCore.Components.ComponentBase> 具有 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 類型之 protected 屬性之類型的抽象、可處置的子系 <xref:System.IServiceProvider> 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-208"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="bfaea-209">您可以使用此提供者來解析範圍為元件存留期的服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-209">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="bfaea-210">使用插入至元件的 DI 服務， [`@inject`](xref:mvc/views/razor#inject) 或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 不在元件的範圍中建立屬性。</span><span class="sxs-lookup"><span data-stu-id="bfaea-210">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="bfaea-211">若要使用元件的範圍，必須使用或來解析 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 服務 <xref:System.IServiceProvider.GetService%2A> 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-211">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="bfaea-212">使用提供者解析的任何服務 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 都有從相同範圍提供的相依性。</span><span class="sxs-lookup"><span data-stu-id="bfaea-212">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="bfaea-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> 衍生自 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 並加入 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 屬性，該屬性會從已設定 `T` 範圍的 DI 提供者傳回的實例。</span><span class="sxs-lookup"><span data-stu-id="bfaea-213"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="bfaea-214"><xref:System.IServiceProvider>當應用程式從 DI 容器使用元件的範圍時，此類型可方便存取範圍服務，而不需要使用的實例。</span><span class="sxs-lookup"><span data-stu-id="bfaea-214">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="bfaea-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>屬性可供使用，因此應用程式可以視需要取得其他類型的服務。</span><span class="sxs-lookup"><span data-stu-id="bfaea-215">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="bfaea-216">從 DI 使用 Entity Framework Core (EF Core) DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="bfaea-216">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="bfaea-217">如需詳細資訊，請參閱<xref:blazor/blazor-server-ef-core>。</span><span class="sxs-lookup"><span data-stu-id="bfaea-217">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="bfaea-218">偵測暫時性可處置專案</span><span class="sxs-lookup"><span data-stu-id="bfaea-218">Detect transient disposables</span></span>

<span data-ttu-id="bfaea-219">下列範例示範如何在應使用的應用程式中偵測可處置的暫時性服務 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 。</span><span class="sxs-lookup"><span data-stu-id="bfaea-219">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="bfaea-220">如需詳細資訊，請參閱 [管理 DI 領域的公用程式基底元件類別](#utility-base-component-classes-to-manage-a-di-scope) 一節。</span><span class="sxs-lookup"><span data-stu-id="bfaea-220">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### Blazor WebAssembly

<span data-ttu-id="bfaea-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="bfaea-221">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="bfaea-222">`TransientDisposable` () 偵測到下列範例中的 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="bfaea-222">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### Blazor Server

<span data-ttu-id="bfaea-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="bfaea-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="bfaea-224">`Program`:</span><span class="sxs-lookup"><span data-stu-id="bfaea-224">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="bfaea-225">`TransientDependency` () 偵測到下列範例中的 `Startup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="bfaea-225">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="bfaea-226">其他資源</span><span class="sxs-lookup"><span data-stu-id="bfaea-226">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="bfaea-227">`IDisposable` 暫時性和共用實例的指引</span><span class="sxs-lookup"><span data-stu-id="bfaea-227">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
