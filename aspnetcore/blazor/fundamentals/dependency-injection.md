---
title: 'ASP.NET Core 相依性 :::no-loc(Blazor)::: 插入'
author: guardrex
description: '瞭解 :::no-loc(Blazor)::: 應用程式如何將服務插入至元件。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/fundamentals/dependency-injection
ms.openlocfilehash: 32228cc98b4650d5871369511808e519a4f65be4
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343672"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="2d7cb-103">ASP.NET Core 相依性 :::no-loc(Blazor)::: 插入</span><span class="sxs-lookup"><span data-stu-id="2d7cb-103">ASP.NET Core :::no-loc(Blazor)::: dependency injection</span></span>

<span data-ttu-id="2d7cb-104">依 [Rainer Stropek](https://www.timecockpit.com) 和 [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="2d7cb-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="2d7cb-105">:::no-loc(Blazor)::: 支援 [ (DI) ](xref:fundamentals/dependency-injection)的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-105">:::no-loc(Blazor)::: supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2d7cb-106">應用程式可以使用內建服務，方法是將它們插入元件中。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="2d7cb-107">應用程式也可以定義和註冊自訂服務，並透過 DI 讓它們可在整個應用程式中使用。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="2d7cb-108">DI 是存取中央位置中設定之服務的一種技術。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="2d7cb-109">這在應用程式中可能很有用 :::no-loc(Blazor)::: ：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-109">This can be useful in :::no-loc(Blazor)::: apps to:</span></span>

* <span data-ttu-id="2d7cb-110">在許多元件（稱為 *單一* 服務）之間共用服務類別的單一實例。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="2d7cb-111">使用參考抽象概念將元件與具體的服務類別分離。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="2d7cb-112">例如，請考慮 `IDataAccess` 用來存取應用程式中資料的介面。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="2d7cb-113">介面是由實體類別所執行 `DataAccess` ，並在應用程式的服務容器中註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="2d7cb-114">當元件使用 DI 來接收執行時 `IDataAccess` ，該元件不會與具象型別結合。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="2d7cb-115">您可以交換實作為單元測試中的 mock 實作為。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="2d7cb-116">預設服務</span><span class="sxs-lookup"><span data-stu-id="2d7cb-116">Default services</span></span>

<span data-ttu-id="2d7cb-117">預設服務會自動加入至應用程式的服務集合。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="2d7cb-118">服務</span><span class="sxs-lookup"><span data-stu-id="2d7cb-118">Service</span></span> | <span data-ttu-id="2d7cb-119">存留期</span><span class="sxs-lookup"><span data-stu-id="2d7cb-119">Lifetime</span></span> | <span data-ttu-id="2d7cb-120">描述</span><span class="sxs-lookup"><span data-stu-id="2d7cb-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="2d7cb-121">具範圍</span><span class="sxs-lookup"><span data-stu-id="2d7cb-121">Scoped</span></span> | <span data-ttu-id="2d7cb-122">提供方法來傳送 HTTP 要求，以及從 URI 所識別的資源接收 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="2d7cb-123"><xref:System.Net.Http.HttpClient>應用程式中的實例會 :::no-loc(Blazor WebAssembly)::: 使用瀏覽器來處理背景中的 HTTP 流量。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-123">The instance of <xref:System.Net.Http.HttpClient> in a :::no-loc(Blazor WebAssembly)::: app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="2d7cb-124">:::no-loc(Blazor Server)::: 依預設，應用程式不會包含 <xref:System.Net.Http.HttpClient> 已設定為服務的服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-124">:::no-loc(Blazor Server)::: apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="2d7cb-125">提供 <xref:System.Net.Http.HttpClient> 給 :::no-loc(Blazor Server)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-125">Provide an <xref:System.Net.Http.HttpClient> to a :::no-loc(Blazor Server)::: app.</span></span><br><br><span data-ttu-id="2d7cb-126">如需詳細資訊，請參閱<xref:blazor/call-web-api>。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-126">For more information, see <xref:blazor/call-web-api>.</span></span><br><br><span data-ttu-id="2d7cb-127"><xref:System.Net.Http.HttpClient>註冊為範圍服務，而非 singleton。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-127">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="2d7cb-128">如需詳細資訊，請參閱 [服務存留期](#service-lifetime) 一節。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-128">For more information, see the [Service lifetime](#service-lifetime) section.</span></span> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <span data-ttu-id="2d7cb-129">單一 (:::no-loc(Blazor WebAssembly):::) </span><span class="sxs-lookup"><span data-stu-id="2d7cb-129">Singleton (:::no-loc(Blazor WebAssembly):::)</span></span><br><span data-ttu-id="2d7cb-130">限域 (:::no-loc(Blazor Server):::) </span><span class="sxs-lookup"><span data-stu-id="2d7cb-130">Scoped (:::no-loc(Blazor Server):::)</span></span> | <span data-ttu-id="2d7cb-131">代表 javascript 呼叫會分派至其中的 JavaScript 執行時間實例。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-131">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="2d7cb-132">如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-132">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <span data-ttu-id="2d7cb-133">單一 (:::no-loc(Blazor WebAssembly):::) </span><span class="sxs-lookup"><span data-stu-id="2d7cb-133">Singleton (:::no-loc(Blazor WebAssembly):::)</span></span><br><span data-ttu-id="2d7cb-134">限域 (:::no-loc(Blazor Server):::) </span><span class="sxs-lookup"><span data-stu-id="2d7cb-134">Scoped (:::no-loc(Blazor Server):::)</span></span> | <span data-ttu-id="2d7cb-135">包含使用 Uri 和流覽狀態的協助程式。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-135">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="2d7cb-136">如需詳細資訊，請參閱 [URI 和流覽狀態](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers)協助程式。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-136">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="2d7cb-137">自訂服務提供者不會自動提供表格中所列的預設服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-137">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="2d7cb-138">如果您使用自訂服務提供者，而且需要表格中所顯示的任何服務，請將所需的服務加入至新的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-138">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="2d7cb-139">將服務新增至應用程式</span><span class="sxs-lookup"><span data-stu-id="2d7cb-139">Add services to an app</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="2d7cb-140">在的方法中設定應用程式服務集合的服務 `Main` `Program.cs` 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-140">Configure services for the app's service collection in the `Main` method of `Program.cs`.</span></span> <span data-ttu-id="2d7cb-141">在下列範例中， `MyDependency` 會為 `IMyDependency` 執行註冊：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-141">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

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

<span data-ttu-id="2d7cb-142">建立主機之後，就可以從根 DI 範圍存取服務，再轉譯任何元件。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-142">Once the host is built, services can be accessed from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="2d7cb-143">這有助於在轉譯內容之前執行初始化邏輯：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-143">This can be useful for running initialization logic before rendering content:</span></span>

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

<span data-ttu-id="2d7cb-144">主機也會提供應用程式的中央設定實例。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-144">The host also provides a central configuration instance for the app.</span></span> <span data-ttu-id="2d7cb-145">根據上述範例，氣象服務的 URL 會從預設的設定來源傳遞 (例如， `:::no-loc(appsettings.json):::`) 到 `InitializeWeatherAsync` ：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-145">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `:::no-loc(appsettings.json):::`) to `InitializeWeatherAsync`:</span></span>

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

### :::no-loc(Blazor Server):::

<span data-ttu-id="2d7cb-146">建立新的應用程式之後，請檢查 `Startup.ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-146">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="2d7cb-147"><xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A>會傳遞方法 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ，這是 () 的服務描述元物件清單 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-147">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="2d7cb-148">服務是藉 `ConfigureServices` 由提供服務描述項給服務集合，在方法中新增。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-148">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="2d7cb-149">下列範例示範 `IDataAccess` 介面和其具體實作為的概念 `DataAccess` ：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-149">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a><span data-ttu-id="2d7cb-150">服務存留期</span><span class="sxs-lookup"><span data-stu-id="2d7cb-150">Service lifetime</span></span>

<span data-ttu-id="2d7cb-151">您可以使用下表所示的存留期來設定服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-151">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="2d7cb-152">存留期</span><span class="sxs-lookup"><span data-stu-id="2d7cb-152">Lifetime</span></span> | <span data-ttu-id="2d7cb-153">描述</span><span class="sxs-lookup"><span data-stu-id="2d7cb-153">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <span data-ttu-id="2d7cb-154">:::no-loc(Blazor WebAssembly)::: 應用程式目前不具有 DI 範圍的概念。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-154">:::no-loc(Blazor WebAssembly)::: apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="2d7cb-155">`Scoped`-註冊的服務行為類似 `Singleton` 服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-155">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="2d7cb-156">不過， :::no-loc(Blazor Server)::: 裝載模型支援 `Scoped` 存留期。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-156">However, the :::no-loc(Blazor Server)::: hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="2d7cb-157">在 :::no-loc(Blazor Server)::: 應用程式中，範圍服務註冊的範圍為 *連接* 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-157">In :::no-loc(Blazor Server)::: apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="2d7cb-158">基於這個理由，即使目前的意圖是要在應用程式的瀏覽器中執行用戶端，也最好使用範圍服務來作為應範圍為目前使用者的服務 :::no-loc(Blazor WebAssembly)::: 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-158">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser in a :::no-loc(Blazor WebAssembly)::: app.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="2d7cb-159">DI 會建立服務的 *單一實例* 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-159">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="2d7cb-160">所有需要服務的元件 `Singleton` 都會收到相同服務的實例。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-160">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="2d7cb-161">每當元件 `Transient` 從服務容器取得服務的實例時，就會收到服務的 *新實例* 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-161">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="2d7cb-162">DI 系統是以 ASP.NET Core 中的 DI 系統為基礎。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-162">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="2d7cb-163">如需詳細資訊，請參閱<xref:fundamentals/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-163">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="2d7cb-164">要求元件中的服務</span><span class="sxs-lookup"><span data-stu-id="2d7cb-164">Request a service in a component</span></span>

<span data-ttu-id="2d7cb-165">將服務加入至服務集合之後，請使用[ \@ 插入](xref:mvc/views/razor#inject)指示詞將服務插入至元件 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-165">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) :::no-loc(Razor)::: directive.</span></span> <span data-ttu-id="2d7cb-166">[`@inject`](xref:mvc/views/razor#inject) 有兩個參數：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-166">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="2d7cb-167">類型：要插入的服務類型。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-167">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="2d7cb-168">屬性：接收插入的 app service 之屬性的名稱。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-168">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="2d7cb-169">屬性不需要手動建立。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-169">The property doesn't require manual creation.</span></span> <span data-ttu-id="2d7cb-170">編譯器會建立屬性。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-170">The compiler creates the property.</span></span>

<span data-ttu-id="2d7cb-171">如需詳細資訊，請參閱<xref:mvc/views/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-171">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="2d7cb-172">使用多個 [`@inject`](xref:mvc/views/razor#inject) 語句插入不同的服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-172">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="2d7cb-173">下列範例顯示如何使用 [`@inject`](xref:mvc/views/razor#inject) 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-173">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="2d7cb-174">執行的服務 `Services.IDataAccess` 會插入元件的屬性中 `DataRepository` 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-174">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="2d7cb-175">請注意，程式碼只會使用 `IDataAccess` 抽象概念：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-175">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="2d7cb-176">就內部而言，產生的屬性 (`DataRepository`) 會使用 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 屬性。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-176">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="2d7cb-177">一般而言，不會直接使用此屬性。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-177">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="2d7cb-178">如果基類是元件的必要項，而且基類也需要插入的屬性，請手動加入 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 屬性：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-178">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="2d7cb-179">在衍生自基類的元件中， [`@inject`](xref:mvc/views/razor#inject) 不需要指示詞。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-179">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="2d7cb-180"><xref:Microsoft.AspNetCore.Components.InjectAttribute>基類的是已足夠的：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-180">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="2d7cb-181">使用服務中的 DI</span><span class="sxs-lookup"><span data-stu-id="2d7cb-181">Use DI in services</span></span>

<span data-ttu-id="2d7cb-182">複雜的服務可能需要其他服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-182">Complex services might require additional services.</span></span> <span data-ttu-id="2d7cb-183">在先前的範例中， `DataAccess` 可能需要 <xref:System.Net.Http.HttpClient> 預設服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-183">In the prior example, `DataAccess` might require the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="2d7cb-184">[`@inject`](xref:mvc/views/razor#inject) (或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 屬性) 無法在服務中使用。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-184">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="2d7cb-185">必須改為使用函式 *插入* 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-185">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="2d7cb-186">將參數加入至服務的函式，即可新增必要的服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-186">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="2d7cb-187">當 DI 建立服務時，它會辨識其在函式中所需的服務，並據以提供它們。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-187">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="2d7cb-188">在下列範例中，此函式會接收 <xref:System.Net.Http.HttpClient> VIA DI 的。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-188">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="2d7cb-189"><xref:System.Net.Http.HttpClient> 是預設服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-189"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

```csharp
public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

<span data-ttu-id="2d7cb-190">函式插入的必要條件：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-190">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="2d7cb-191">必須有一個函式，其引數可由 DI 完成。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-191">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="2d7cb-192">如果指定預設值，則允許 DI 未涵蓋的其他參數。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-192">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="2d7cb-193">適用的函式必須是 `public` 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-193">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="2d7cb-194">其中一個適用的函式必須存在。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-194">One applicable constructor must exist.</span></span> <span data-ttu-id="2d7cb-195">如果不明確，DI 會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-195">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="2d7cb-196">管理 DI 範圍的公用程式基底元件類別</span><span class="sxs-lookup"><span data-stu-id="2d7cb-196">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="2d7cb-197">在 ASP.NET Core 應用程式中，範圍服務的範圍通常是目前的要求。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-197">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="2d7cb-198">在要求完成之後，DI 系統會處置任何範圍或暫時性的服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-198">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="2d7cb-199">在 :::no-loc(Blazor Server)::: 應用程式中，要求範圍會持續進行用戶端連線的持續時間，這可能會導致暫時性和範圍服務的時間比預期更長。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-199">In :::no-loc(Blazor Server)::: apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="2d7cb-200">在 :::no-loc(Blazor WebAssembly)::: 應用程式中，以限域存留期註冊的服務會被視為 singleton，所以它們的存留時間比一般 ASP.NET Core 應用程式中的範圍服務長。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-200">In :::no-loc(Blazor WebAssembly)::: apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="2d7cb-201">若要在應用程式中偵測可處置的暫時性服務，請參閱偵測 [暫時性可處置專案](#detect-transient-disposables) 一節。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-201">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="2d7cb-202">在應用程式中限制服務存留期的方法 :::no-loc(Blazor)::: 是使用 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 類型。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-202">An approach that limits a service lifetime in :::no-loc(Blazor)::: apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="2d7cb-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 是衍生自的抽象型別 <xref:Microsoft.AspNetCore.Components.ComponentBase> ，它會建立對應至元件存留期的 DI 範圍。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-203"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="2d7cb-204">使用此範圍，您可以使用具有範圍存留期的 DI 服務，並讓它們存留（只要元件的話）。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-204">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="2d7cb-205">當元件損毀時，元件範圍服務提供者的服務也會一併處置。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-205">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="2d7cb-206">這適用于下列服務：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-206">This can be useful for services that:</span></span>

* <span data-ttu-id="2d7cb-207">應該在元件中重複使用，因為暫時性存留期不適當。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-207">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="2d7cb-208">不應該在元件之間共用，因為 singleton 存留期不適當。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-208">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="2d7cb-209">有兩種版本的 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 類型可供使用：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-209">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="2d7cb-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> 這是 <xref:Microsoft.AspNetCore.Components.ComponentBase> 具有 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 類型之 protected 屬性之類型的抽象、可處置的子系 <xref:System.IServiceProvider> 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-210"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="2d7cb-211">您可以使用此提供者來解析範圍為元件存留期的服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-211">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="2d7cb-212">使用插入至元件的 DI 服務， [`@inject`](xref:mvc/views/razor#inject) 或 [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) 不在元件的範圍中建立屬性。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-212">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="2d7cb-213">若要使用元件的範圍，必須使用或來解析 <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> 服務 <xref:System.IServiceProvider.GetService%2A> 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-213">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="2d7cb-214">使用提供者解析的任何服務 <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> 都有從相同範圍提供的相依性。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-214">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

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

* <span data-ttu-id="2d7cb-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> 衍生自 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 並加入 <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> 屬性，該屬性會從已設定 `T` 範圍的 DI 提供者傳回的實例。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-215"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="2d7cb-216"><xref:System.IServiceProvider>當應用程式從 DI 容器使用元件的範圍時，此類型可方便存取範圍服務，而不需要使用的實例。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-216">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="2d7cb-217"><xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>屬性可供使用，因此應用程式可以視需要取得其他類型的服務。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-217">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

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

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="2d7cb-218">從 DI 使用 Entity Framework Core (EF Core) DbCoNtext</span><span class="sxs-lookup"><span data-stu-id="2d7cb-218">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="2d7cb-219">如需詳細資訊，請參閱<xref:blazor/blazor-server-ef-core>。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-219">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

## <a name="detect-transient-disposables"></a><span data-ttu-id="2d7cb-220">偵測暫時性可處置專案</span><span class="sxs-lookup"><span data-stu-id="2d7cb-220">Detect transient disposables</span></span>

<span data-ttu-id="2d7cb-221">下列範例示範如何在應使用的應用程式中偵測可處置的暫時性服務 <xref:Microsoft.AspNetCore.Components.OwningComponentBase> 。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-221">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="2d7cb-222">如需詳細資訊，請參閱 [管理 DI 領域的公用程式基底元件類別](#utility-base-component-classes-to-manage-a-di-scope) 一節。</span><span class="sxs-lookup"><span data-stu-id="2d7cb-222">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="2d7cb-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="2d7cb-223">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="2d7cb-224">`TransientDisposable` () 偵測到下列範例中的 `Program.cs` ：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-224">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/wasm-program.cs?highlight=6,9,17,22-25)]

### :::no-loc(Blazor Server):::

<span data-ttu-id="2d7cb-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="2d7cb-225">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="2d7cb-226">`Program`:</span><span class="sxs-lookup"><span data-stu-id="2d7cb-226">`Program`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-program.cs?highlight=3)]

<span data-ttu-id="2d7cb-227">`TransientDependency` () 偵測到下列範例中的 `Startup.cs` ：</span><span class="sxs-lookup"><span data-stu-id="2d7cb-227">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/server-startup.cs?highlight=6-8,11-32)]

## <a name="additional-resources"></a><span data-ttu-id="2d7cb-228">其他資源</span><span class="sxs-lookup"><span data-stu-id="2d7cb-228">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="2d7cb-229">`IDisposable` 暫時性和共用實例的指引</span><span class="sxs-lookup"><span data-stu-id="2d7cb-229">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
