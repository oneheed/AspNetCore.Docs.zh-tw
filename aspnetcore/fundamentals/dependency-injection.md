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
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="541bc-103">.NET Core 中的相依性插入</span><span class="sxs-lookup"><span data-stu-id="541bc-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="541bc-104">作者： [Kirk Larkin](https://twitter.com/serpent5)、 [Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="541bc-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="541bc-105">ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。</span><span class="sxs-lookup"><span data-stu-id="541bc-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="541bc-106">如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="541bc-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="541bc-107">如需選項之相依性插入的詳細資訊，請參閱 <xref:fundamentals/configuration/options> 。</span><span class="sxs-lookup"><span data-stu-id="541bc-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="541bc-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="541bc-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="541bc-109">相依性插入概觀</span><span class="sxs-lookup"><span data-stu-id="541bc-109">Overview of dependency injection</span></span>

<span data-ttu-id="541bc-110">「相依性」\*\* 是另一個物件所需的任何物件。</span><span class="sxs-lookup"><span data-stu-id="541bc-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="541bc-111">檢查具有 `WriteMessage` 方法的下列 `MyDependency` 物件，應用程式中的其他類別相依於此物件：</span><span class="sxs-lookup"><span data-stu-id="541bc-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="541bc-112">您可以建立 `MyDependency` 類別的執行個體，讓 `WriteMessage` 方法可供類別使用。</span><span class="sxs-lookup"><span data-stu-id="541bc-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="541bc-113">`MyDependency` 類別是 `IndexModel` 類別的相依性：</span><span class="sxs-lookup"><span data-stu-id="541bc-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="541bc-114">該類別會建立 `MyDependency` 執行個體並直接相依於該執行個體。</span><span class="sxs-lookup"><span data-stu-id="541bc-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="541bc-115">程式碼相依性（例如前一個範例）有問題，應避免下列原因：</span><span class="sxs-lookup"><span data-stu-id="541bc-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="541bc-116">若要將 `MyDependency` 取代為不同的實作，必須修改該類別。</span><span class="sxs-lookup"><span data-stu-id="541bc-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="541bc-117">若 `MyDependency` 有相依性，那些相依性必須由該類別設定。</span><span class="sxs-lookup"><span data-stu-id="541bc-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="541bc-118">在具有多個相依於 `MyDependency` 之多個類別的大型專案中，設定程式碼在不同的應用程式之間會變得鬆散。</span><span class="sxs-lookup"><span data-stu-id="541bc-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="541bc-119">此實作難以進行單元測試。</span><span class="sxs-lookup"><span data-stu-id="541bc-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="541bc-120">應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。</span><span class="sxs-lookup"><span data-stu-id="541bc-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="541bc-121">相依性插入可透過下列方式解決這些問題：</span><span class="sxs-lookup"><span data-stu-id="541bc-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="541bc-122">使用介面或基底類別來將相依性資訊抽象化。</span><span class="sxs-lookup"><span data-stu-id="541bc-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="541bc-123">在服務容器中註冊相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="541bc-124">ASP.NET Core 提供內建服務容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="541bc-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="541bc-125">服務會在應用程式的 `Startup.ConfigureServices` 方法中註冊。</span><span class="sxs-lookup"><span data-stu-id="541bc-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="541bc-126">將服務「插入」\*\* 到服務使用位置之類別的建構函式。</span><span class="sxs-lookup"><span data-stu-id="541bc-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="541bc-127">架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。</span><span class="sxs-lookup"><span data-stu-id="541bc-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="541bc-128">在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 介面定義了服務提供給應用程式的方法：</span><span class="sxs-lookup"><span data-stu-id="541bc-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="541bc-129">這個介面是由具象型別 `MyDependency` 所實作：</span><span class="sxs-lookup"><span data-stu-id="541bc-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="541bc-130">在範例應用程式中，`IMyDependency` 服務是使用具象型別 `MyDependency` 所註冊。</span><span class="sxs-lookup"><span data-stu-id="541bc-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="541bc-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>方法會以限定範圍存留期（單一要求的存留期）註冊服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="541bc-132">將在此主題稍後將說明[服務存留期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="541bc-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="541bc-133">在範例應用程式中，會要求 `IMyDependency` 執行個體並使用它來呼叫服務的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="541bc-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="541bc-134">使用 DI 模式：</span><span class="sxs-lookup"><span data-stu-id="541bc-134">With the DI pattern:</span></span>

* <span data-ttu-id="541bc-135">控制器不會使用具體類型 `MyDependency` ，只有介面 `IMyDependency` 。</span><span class="sxs-lookup"><span data-stu-id="541bc-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="541bc-136">這可讓您輕鬆地變更控制器所使用的執行，而不需要修改控制器。</span><span class="sxs-lookup"><span data-stu-id="541bc-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="541bc-137">控制器不會建立的實例 `MyDependency` ，它是由 DI 容器所建立。</span><span class="sxs-lookup"><span data-stu-id="541bc-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="541bc-138">您 `IMyDependency` 可以使用內建的記錄 API 來改善介面的執行：</span><span class="sxs-lookup"><span data-stu-id="541bc-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="541bc-139">更新的 `ConfigureServices` 方法會註冊新的 `IMyDependency` 執行：</span><span class="sxs-lookup"><span data-stu-id="541bc-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="541bc-140">`MyDependency2`<xref:Microsoft.Extensions.Logging.ILogger`1>在此函數中要求。</span><span class="sxs-lookup"><span data-stu-id="541bc-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="541bc-141">以鏈結方式使用相依性插入並非不尋常。</span><span class="sxs-lookup"><span data-stu-id="541bc-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="541bc-142">每個要求的相依性接著會要求其自己的相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="541bc-143">容器會解決圖形中的相依性，並傳回完全解析的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="541bc-144">必須先解析的相依性集合組通常稱為「相依性樹狀結構」**、「相依性圖形」** 或「物件圖形」\*\*。</span><span class="sxs-lookup"><span data-stu-id="541bc-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="541bc-145">`ILogger<TCategoryName>`是[架構提供的服務](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="541bc-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="541bc-146">容器會 `ILogger<TCategoryName>` 利用[ (泛型) 開放式類型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)來解決，而不必註冊每個[ (泛型) 結構化類型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。</span><span class="sxs-lookup"><span data-stu-id="541bc-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="541bc-147">在相依性插入術語中，服務：</span><span class="sxs-lookup"><span data-stu-id="541bc-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="541bc-148">通常是提供服務給應用程式中其他程式碼的物件，例如 `IMyDependency` 服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="541bc-149">與 web 服務無關，雖然服務可能會使用 web 服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="541bc-150">此架構提供健全的[記錄](xref:fundamentals/logging/index)系統。</span><span class="sxs-lookup"><span data-stu-id="541bc-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="541bc-151">這 `IMyDependency` 是為了示範基本的 DI 而撰寫的，而不是用來執行記錄。</span><span class="sxs-lookup"><span data-stu-id="541bc-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="541bc-152">大部分的應用程式都不需要撰寫記錄器。</span><span class="sxs-lookup"><span data-stu-id="541bc-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="541bc-153">下列程式碼示範如何使用預設記錄，而不需要在中註冊任何服務 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="541bc-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="541bc-154">使用上述程式碼，不需要更新， `ConfigureServices` 因為[記錄](xref:fundamentals/logging/index)是由架構所提供：</span><span class="sxs-lookup"><span data-stu-id="541bc-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="541bc-155">插入至啟動的服務</span><span class="sxs-lookup"><span data-stu-id="541bc-155">Services injected into Startup</span></span>

<span data-ttu-id="541bc-156">`Startup`使用泛型主機 () 時，只有下列服務類型可以插入至此函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="541bc-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="541bc-157">服務可以插入 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="541bc-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="541bc-158">如需詳細資訊，請參閱 <xref:fundamentals/startup> 和[在啟動時存取](xref:fundamentals/configuration/index#access-configuration-in-startup)設定。</span><span class="sxs-lookup"><span data-stu-id="541bc-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="541bc-159">以擴充方法註冊其他服務</span><span class="sxs-lookup"><span data-stu-id="541bc-159">Register additional services with extension methods</span></span>

<span data-ttu-id="541bc-160">當服務集合擴充方法可用來註冊服務時：</span><span class="sxs-lookup"><span data-stu-id="541bc-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="541bc-161">慣例是使用單一 `Add{SERVICE_NAME}` 擴充方法來註冊該服務所需的所有服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="541bc-162">也會註冊相依的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-162">The dependent services are also registered.</span></span>

<span data-ttu-id="541bc-163">下列程式碼是由 Razor 使用個別使用者帳戶的頁面範本所產生，並說明如何使用擴充方法和，將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> ：</span><span class="sxs-lookup"><span data-stu-id="541bc-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="541bc-164">如需詳細資訊，請參閱 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 和 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="541bc-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="541bc-165">如需撰寫擴充方法來註冊服務的指示，請參閱[結合服務集合](#csc)一節。</span><span class="sxs-lookup"><span data-stu-id="541bc-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="541bc-166">服務存留期</span><span class="sxs-lookup"><span data-stu-id="541bc-166">Service lifetimes</span></span>

<span data-ttu-id="541bc-167">為每個已註冊的服務選擇適當的存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="541bc-168">ASP.NET Core 服務可以使用下列存留期進行設定：</span><span class="sxs-lookup"><span data-stu-id="541bc-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="541bc-169">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-169">Transient</span></span>

<span data-ttu-id="541bc-170">每次從服務容器要求暫時性存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 時都會建立它們。</span><span class="sxs-lookup"><span data-stu-id="541bc-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="541bc-171">此存留期最適合用於輕量型的無狀態服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="541bc-172">在處理要求的應用程式中，會在要求結束時處置暫時性服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="541bc-173">具範圍</span><span class="sxs-lookup"><span data-stu-id="541bc-173">Scoped</span></span>

<span data-ttu-id="541bc-174">具範圍存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 會在每次用戶端要求 (連線) 時建立一次。</span><span class="sxs-lookup"><span data-stu-id="541bc-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="541bc-175">使用 Entity Framework Core 時， <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 擴充方法預設會 `DbContext` 註冊具有限定範圍存留期的類型。</span><span class="sxs-lookup"><span data-stu-id="541bc-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="541bc-176">在處理要求的應用程式中，會在要求結束時處置已設定範圍的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="541bc-177">在中介軟體中使用具有下列其中一種方法的範圍服務：</span><span class="sxs-lookup"><span data-stu-id="541bc-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="541bc-178">將服務插入 `Invoke` 或方法中 `InvokeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="541bc-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="541bc-179">插入 by 函式[插入](xref:mvc/controllers/dependency-injection#constructor-injection)會在執行時間擲回例外狀況，因為它會強制服務的行為就像 singleton 一樣。</span><span class="sxs-lookup"><span data-stu-id="541bc-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="541bc-180">[存留期和註冊選項](#lifetime-and-registration-options)中的範例會使用 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="541bc-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="541bc-181">以[Factory 為基礎的中介軟體](<xref:fundamentals/middleware/extensibility>)。</span><span class="sxs-lookup"><span data-stu-id="541bc-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="541bc-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 擴充方法會檢查中介軟體的已註冊類型是否實作 <xref:Microsoft.AspNetCore.Http.IMiddleware>。</span><span class="sxs-lookup"><span data-stu-id="541bc-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="541bc-183">如果是，系統會使用容器中已註冊的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 執行個體來解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 實作，而不是使用以慣例為基礎的中介軟體啟用邏輯。</span><span class="sxs-lookup"><span data-stu-id="541bc-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="541bc-184">中介軟體會註冊為應用程式服務容器中的範圍服務或暫時性服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="541bc-185">如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="541bc-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="541bc-186">請勿***從 singleton 解析已***設定範圍的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="541bc-187">處理後續要求時，它可能會導致服務有不正確的狀態。</span><span class="sxs-lookup"><span data-stu-id="541bc-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="541bc-188">可以：</span><span class="sxs-lookup"><span data-stu-id="541bc-188">It's fine to:</span></span>

* <span data-ttu-id="541bc-189">從限定範圍或暫時性服務解析單一服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="541bc-190">從另一個已設定範圍或暫時性的服務解析已設定範圍的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="541bc-191">根據預設，在開發環境中，從較長存留期的另一個服務解析服務時，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="541bc-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="541bc-192">如需詳細資訊，請參閱[範圍驗證](#sv)。</span><span class="sxs-lookup"><span data-stu-id="541bc-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="541bc-193">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-193">Singleton</span></span>

<span data-ttu-id="541bc-194">建立單一存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) ：</span><span class="sxs-lookup"><span data-stu-id="541bc-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="541bc-195">第一次要求時。</span><span class="sxs-lookup"><span data-stu-id="541bc-195">The first time they're requested.</span></span>
* <span data-ttu-id="541bc-196">由開發人員在將實實例直接提供給容器時。</span><span class="sxs-lookup"><span data-stu-id="541bc-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="541bc-197">這種方法很少需要。</span><span class="sxs-lookup"><span data-stu-id="541bc-197">This approach is rarely needed.</span></span>

<span data-ttu-id="541bc-198">每個後續要求都會使用相同的執行個體。</span><span class="sxs-lookup"><span data-stu-id="541bc-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="541bc-199">如果應用程式需要單一行為，請允許服務容器管理服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="541bc-200">請勿執行單一設計模式，並提供程式碼來處置 singleton。</span><span class="sxs-lookup"><span data-stu-id="541bc-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="541bc-201">服務永遠不應由從容器解析服務的程式碼來處置。</span><span class="sxs-lookup"><span data-stu-id="541bc-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="541bc-202">如果類型或 factory 註冊為 singleton，容器將會自動處置 singleton。</span><span class="sxs-lookup"><span data-stu-id="541bc-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="541bc-203">單一服務必須具備執行緒安全，而且通常用於無狀態服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="541bc-204">在處理要求的應用程式中， <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 會在應用程式關閉時處置單一服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="541bc-205">因為在關閉應用程式之前不會釋放記憶體，所以必須考慮使用單一實例的記憶體。</span><span class="sxs-lookup"><span data-stu-id="541bc-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="541bc-206">請勿***從 singleton 解析已***設定範圍的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="541bc-207">處理後續要求時，它可能會導致服務有不正確的狀態。</span><span class="sxs-lookup"><span data-stu-id="541bc-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="541bc-208">從限定範圍或暫時性的服務解析單一服務是很好的。</span><span class="sxs-lookup"><span data-stu-id="541bc-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="541bc-209">服務註冊方法</span><span class="sxs-lookup"><span data-stu-id="541bc-209">Service registration methods</span></span>

<span data-ttu-id="541bc-210">服務註冊擴充方法提供在特定案例中很有用的多載。</span><span class="sxs-lookup"><span data-stu-id="541bc-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="541bc-211">方法</span><span class="sxs-lookup"><span data-stu-id="541bc-211">Method</span></span> | <span data-ttu-id="541bc-212">自動</span><span class="sxs-lookup"><span data-stu-id="541bc-212">Automatic</span></span><br><span data-ttu-id="541bc-213">object</span><span class="sxs-lookup"><span data-stu-id="541bc-213">object</span></span><br><span data-ttu-id="541bc-214">處置</span><span class="sxs-lookup"><span data-stu-id="541bc-214">disposal</span></span> | <span data-ttu-id="541bc-215">多個</span><span class="sxs-lookup"><span data-stu-id="541bc-215">Multiple</span></span><br><span data-ttu-id="541bc-216">實作</span><span class="sxs-lookup"><span data-stu-id="541bc-216">implementations</span></span> | <span data-ttu-id="541bc-217">傳遞引數</span><span class="sxs-lookup"><span data-stu-id="541bc-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="541bc-218">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="541bc-219">是</span><span class="sxs-lookup"><span data-stu-id="541bc-219">Yes</span></span> | <span data-ttu-id="541bc-220">是</span><span class="sxs-lookup"><span data-stu-id="541bc-220">Yes</span></span> | <span data-ttu-id="541bc-221">否</span><span class="sxs-lookup"><span data-stu-id="541bc-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="541bc-222">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="541bc-223">是</span><span class="sxs-lookup"><span data-stu-id="541bc-223">Yes</span></span> | <span data-ttu-id="541bc-224">是</span><span class="sxs-lookup"><span data-stu-id="541bc-224">Yes</span></span> | <span data-ttu-id="541bc-225">是</span><span class="sxs-lookup"><span data-stu-id="541bc-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="541bc-226">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="541bc-227">是</span><span class="sxs-lookup"><span data-stu-id="541bc-227">Yes</span></span> | <span data-ttu-id="541bc-228">否</span><span class="sxs-lookup"><span data-stu-id="541bc-228">No</span></span> | <span data-ttu-id="541bc-229">否</span><span class="sxs-lookup"><span data-stu-id="541bc-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="541bc-230">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="541bc-231">否</span><span class="sxs-lookup"><span data-stu-id="541bc-231">No</span></span> | <span data-ttu-id="541bc-232">是</span><span class="sxs-lookup"><span data-stu-id="541bc-232">Yes</span></span> | <span data-ttu-id="541bc-233">是</span><span class="sxs-lookup"><span data-stu-id="541bc-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="541bc-234">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="541bc-235">否</span><span class="sxs-lookup"><span data-stu-id="541bc-235">No</span></span> | <span data-ttu-id="541bc-236">否</span><span class="sxs-lookup"><span data-stu-id="541bc-236">No</span></span> | <span data-ttu-id="541bc-237">是</span><span class="sxs-lookup"><span data-stu-id="541bc-237">Yes</span></span> |

<span data-ttu-id="541bc-238">如需類型處置的詳細資訊，請參閱[＜服務處置＞](#disposal-of-services)一節。</span><span class="sxs-lookup"><span data-stu-id="541bc-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="541bc-239">多個實作的常見案例是[模擬測試類型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="541bc-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="541bc-240">`TryAdd{LIFETIME}`如果尚未註冊此服務，方法會註冊該服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="541bc-241">在下列範例中，第一行會為 `IMyDependency` 註冊 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="541bc-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="541bc-242">第二行則沒有任何作用，因為 `IMyDependency` 已具有註冊的實作：</span><span class="sxs-lookup"><span data-stu-id="541bc-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="541bc-243">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="541bc-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="541bc-244">如果尚未有*相同類型*的執行， [TryAddEnumerable (ServiceDescriptor) ](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)方法會註冊服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="541bc-245">多個服務會透過 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="541bc-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="541bc-246">註冊服務時，如果尚未加入相同類型的實例，開發人員就應該加入該實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="541bc-247">一般而言，程式庫作者會使用 `TryAddEnumerable` 來避免在容器中註冊多個執行複本。</span><span class="sxs-lookup"><span data-stu-id="541bc-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="541bc-248">在下列範例中，第一行會為 `IMyDep1` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="541bc-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="541bc-249">第二行會為 `IMyDep2` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="541bc-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="541bc-250">第三行則沒有任何作用，因為 `IMyDep1` 已具有 `MyDep` 的已註冊實作：</span><span class="sxs-lookup"><span data-stu-id="541bc-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="541bc-251">服務註冊通常是獨立順序，但在註冊相同類型的多個執行中時除外。</span><span class="sxs-lookup"><span data-stu-id="541bc-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="541bc-252">`IServiceCollection`是的集合 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 。</span><span class="sxs-lookup"><span data-stu-id="541bc-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="541bc-253">下列程式碼示範如何使用函式來新增服務：</span><span class="sxs-lookup"><span data-stu-id="541bc-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="541bc-254">`Add{LIFETIME}`方法會使用相同的方法。</span><span class="sxs-lookup"><span data-stu-id="541bc-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="541bc-255">例如，請參閱[要 AddScoped 的原始程式碼](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)。</span><span class="sxs-lookup"><span data-stu-id="541bc-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="541bc-256">建構函式插入行為</span><span class="sxs-lookup"><span data-stu-id="541bc-256">Constructor injection behavior</span></span>

<span data-ttu-id="541bc-257">服務可以透過兩個機制來解析：</span><span class="sxs-lookup"><span data-stu-id="541bc-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="541bc-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="541bc-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="541bc-259">在相依性插入容器中建立沒有服務註冊的物件。</span><span class="sxs-lookup"><span data-stu-id="541bc-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="541bc-260">與架構功能搭配使用，例如標籤協助[程式、MVC](xref:mvc/views/tag-helpers/intro)控制器和[模型](xref:mvc/models/model-binding)系結器。</span><span class="sxs-lookup"><span data-stu-id="541bc-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="541bc-261">建構函式可以接受不是由相依性插入提供的引數，但引數必須指派預設值。</span><span class="sxs-lookup"><span data-stu-id="541bc-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="541bc-262">當服務由或解析時，程式的 `IServiceProvider` `ActivatorUtilities` [插入](xref:mvc/controllers/dependency-injection#constructor-injection)需要*公用*的函式。</span><span class="sxs-lookup"><span data-stu-id="541bc-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="541bc-263">當服務由解析時 `ActivatorUtilities` ，程式的[插入](xref:mvc/controllers/dependency-injection#constructor-injection)只需要有一個適用的函式。</span><span class="sxs-lookup"><span data-stu-id="541bc-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="541bc-264">支援建構函式多載，但只能有一個多載存在，其引數可以藉由相依性插入而完成。</span><span class="sxs-lookup"><span data-stu-id="541bc-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="541bc-265">Entity Framework 內容</span><span class="sxs-lookup"><span data-stu-id="541bc-265">Entity Framework contexts</span></span>

<span data-ttu-id="541bc-266">因為一般會將 Web 應用程式資料庫作業範圍設定為用戶端要求，所以通常會使用[具範圍存留期](#service-lifetimes)將 Entity Framework 內容新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="541bc-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="541bc-267">如果在註冊資料庫內容時， [AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)多載未指定存留期，則會將預設存留期設定為範圍。</span><span class="sxs-lookup"><span data-stu-id="541bc-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="541bc-268">指定存留期的服務不應該使用存留期比服務還短的資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="541bc-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="541bc-269">留期和註冊選項</span><span class="sxs-lookup"><span data-stu-id="541bc-269">Lifetime and registration options</span></span>

<span data-ttu-id="541bc-270">若要示範存留期和註冊選項之間的差異，請考慮使用下列介面將工作表示為具有識別碼的作業 `OperationId` 。</span><span class="sxs-lookup"><span data-stu-id="541bc-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="541bc-271">根據為下列介面設定作業服務存留期的方式，當類別要求時，容器會提供相同或不同的服務實例：</span><span class="sxs-lookup"><span data-stu-id="541bc-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="541bc-272">介面是在 `Operation` 類別中實作。</span><span class="sxs-lookup"><span data-stu-id="541bc-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="541bc-273">`Operation`如果未提供 GUID 的最後4個字元，則此函式會產生它：</span><span class="sxs-lookup"><span data-stu-id="541bc-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="541bc-274">在 `Startup.ConfigureServices` 中，每個類型都會根據其具名存留期新增至容器：</span><span class="sxs-lookup"><span data-stu-id="541bc-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="541bc-275">範例應用程式會示範要求內和之間的物件存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="541bc-276">範例應用程式 `IndexModel` 和中介軟體會要求每種 `IOperation` 類型，並記錄 `OperationId` 下列各項：</span><span class="sxs-lookup"><span data-stu-id="541bc-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="541bc-277">中介軟體類似于 `IndexModel` 並解析相同的服務：</span><span class="sxs-lookup"><span data-stu-id="541bc-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="541bc-278">已設定範圍的服務必須在 `InvokeAsync` 方法中解析：</span><span class="sxs-lookup"><span data-stu-id="541bc-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="541bc-279">記錄器輸出會顯示：</span><span class="sxs-lookup"><span data-stu-id="541bc-279">The logger output shows:</span></span>

* <span data-ttu-id="541bc-280">「暫時性」\*\* 物件一律不同。</span><span class="sxs-lookup"><span data-stu-id="541bc-280">*Transient* objects are always different.</span></span> <span data-ttu-id="541bc-281">`OperationId`和中介軟體的暫時性值不同 `IndexModel` 。</span><span class="sxs-lookup"><span data-stu-id="541bc-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="541bc-282">有*範圍*的物件在每個要求中都相同，但在每個要求中都不同。</span><span class="sxs-lookup"><span data-stu-id="541bc-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="541bc-283">每個要求的*單一*物件都是相同的。</span><span class="sxs-lookup"><span data-stu-id="541bc-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="541bc-284">若要減少記錄輸出，請在檔案的*appsettings.Development.js*中設定 "記錄： LogLevel： Microsoft： Error"：</span><span class="sxs-lookup"><span data-stu-id="541bc-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="541bc-285">從主要呼叫服務</span><span class="sxs-lookup"><span data-stu-id="541bc-285">Call services from main</span></span>

<span data-ttu-id="541bc-286">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>，以解析應用程式範圍中的範圍服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="541bc-287">這種方法可在啟動時存取已設定範圍的服務，以執行初始化工作：</span><span class="sxs-lookup"><span data-stu-id="541bc-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

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

<span data-ttu-id="541bc-288">上述程式碼來自在頁面教學課程中[新增種子初始化運算式](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) Razor 。</span><span class="sxs-lookup"><span data-stu-id="541bc-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="541bc-289">下列範例示範如何在 `Program.Main` 中取得 `IMyDependency`：</span><span class="sxs-lookup"><span data-stu-id="541bc-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="541bc-290">範圍驗證</span><span class="sxs-lookup"><span data-stu-id="541bc-290">Scope validation</span></span>

<span data-ttu-id="541bc-291">當應用程式在[開發環境](xref:fundamentals/environments)中執行並呼叫[CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)來建立主機時，預設服務提供者會執行檢查以確認：</span><span class="sxs-lookup"><span data-stu-id="541bc-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="541bc-292">範圍服務不是直接或間接由開機服務提供者解析。</span><span class="sxs-lookup"><span data-stu-id="541bc-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="541bc-293">範圍服務不是直接或間接插入至單一服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="541bc-294">暫時性服務不是直接或間接插入單次個體或範圍服務中。</span><span class="sxs-lookup"><span data-stu-id="541bc-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="541bc-295">根服務提供者會在呼叫 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 時建立。</span><span class="sxs-lookup"><span data-stu-id="541bc-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="541bc-296">當提供者啟動應用程式時，根服務提供者的存留期會對應到應用程式的存留期，並在應用程式關閉時處置。</span><span class="sxs-lookup"><span data-stu-id="541bc-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="541bc-297">範圍服務會由建立這些服務的容器處置。</span><span class="sxs-lookup"><span data-stu-id="541bc-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="541bc-298">如果已設定範圍的服務在根容器中建立，服務的存留期會有效地升級為 singleton，因為只有當應用程式關閉時，根容器才會處置它。</span><span class="sxs-lookup"><span data-stu-id="541bc-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="541bc-299">當呼叫 `BuildServiceProvider` 時，驗證服務範圍會攔截到這些情況。</span><span class="sxs-lookup"><span data-stu-id="541bc-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="541bc-300">如需詳細資訊，請參閱[範圍驗證](xref:fundamentals/host/web-host#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="541bc-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="541bc-301">要求服務</span><span class="sxs-lookup"><span data-stu-id="541bc-301">Request Services</span></span>

<span data-ttu-id="541bc-302">來自 `HttpContext`，在 ASP.NET Core 要求內提供的服務是透過 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公開。</span><span class="sxs-lookup"><span data-stu-id="541bc-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="541bc-303">要求服務代表您在應用程式中設定和要求的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="541bc-304">當物件指定相依性時，這些會由在 `RequestServices` 中找到的類型來滿足，而非 `ApplicationServices`。</span><span class="sxs-lookup"><span data-stu-id="541bc-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="541bc-305">一般而言，應用程式不應該直接使用這些屬性。</span><span class="sxs-lookup"><span data-stu-id="541bc-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="541bc-306">相反地，請透過類別的函式要求類別所需的類型，並允許架構插入相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="541bc-307">這會產生容易測試的類別。</span><span class="sxs-lookup"><span data-stu-id="541bc-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="541bc-308">ASP.NET Core 會根據要求建立範圍，並 `RequestServices` 公開限定範圍的服務提供者。</span><span class="sxs-lookup"><span data-stu-id="541bc-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="541bc-309">只要要求為作用中，所有已設定範圍的服務都有效。</span><span class="sxs-lookup"><span data-stu-id="541bc-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="541bc-310">偏好要求相依性作為建構函式參數，而不要存取 `RequestServices` 集合。</span><span class="sxs-lookup"><span data-stu-id="541bc-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="541bc-311">針對相依性插入設計服務</span><span class="sxs-lookup"><span data-stu-id="541bc-311">Design services for dependency injection</span></span>

<span data-ttu-id="541bc-312">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="541bc-312">Best practices are to:</span></span>

* <span data-ttu-id="541bc-313">設計服務以使用相依性插入來取得其相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="541bc-314">避免具狀態的靜態類別和成員。</span><span class="sxs-lookup"><span data-stu-id="541bc-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="541bc-315">將應用程式設計成使用單一服務，以避免建立全域狀態。</span><span class="sxs-lookup"><span data-stu-id="541bc-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="541bc-316">避免直接在服務內具現化相依類別。</span><span class="sxs-lookup"><span data-stu-id="541bc-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="541bc-317">直接具現化會將程式碼耦合到特定實作。</span><span class="sxs-lookup"><span data-stu-id="541bc-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="541bc-318">讓應用程式類別維持在小型、情況良好且可輕鬆測試的狀態。</span><span class="sxs-lookup"><span data-stu-id="541bc-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="541bc-319">如果類別似乎有太多插入的相依性，這通常表示類別具有太多責任，而且違反[單一責任原則 (SRP) ](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="541bc-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="541bc-320">將類別負責的某些部分移到新的類別，以嘗試重構類別。</span><span class="sxs-lookup"><span data-stu-id="541bc-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="541bc-321">請記住，頁面 Razor 頁面模型類別和 MVC 控制器類別應該專注于 UI 考慮。</span><span class="sxs-lookup"><span data-stu-id="541bc-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="541bc-322">處置服務</span><span class="sxs-lookup"><span data-stu-id="541bc-322">Disposal of services</span></span>

<span data-ttu-id="541bc-323">容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="541bc-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="541bc-324">服務永遠不應由從容器解析服務的任何程式碼來處置。</span><span class="sxs-lookup"><span data-stu-id="541bc-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="541bc-325">如果類型或處理站註冊為 singleton，則容器會處置 singleton。</span><span class="sxs-lookup"><span data-stu-id="541bc-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="541bc-326">在下列範例中，服務會建立服務容器並自動處置：</span><span class="sxs-lookup"><span data-stu-id="541bc-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="541bc-327">在每次重新整理索引頁面之後，debug 主控台會顯示下列輸出：</span><span class="sxs-lookup"><span data-stu-id="541bc-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="541bc-328">服務容器不會建立服務</span><span class="sxs-lookup"><span data-stu-id="541bc-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="541bc-329">請考慮下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="541bc-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="541bc-330">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="541bc-330">In the preceding code:</span></span>

* <span data-ttu-id="541bc-331">服務容器不會建立服務實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="541bc-332">架構不知道預期的服務存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="541bc-333">架構不會自動處置服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="541bc-334">如果服務未在開發人員程式碼中明確處置，它們會持續存在，直到應用程式關閉為止。</span><span class="sxs-lookup"><span data-stu-id="541bc-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="541bc-335">暫時性和共用實例的 IDisposable 指引</span><span class="sxs-lookup"><span data-stu-id="541bc-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="541bc-336">暫時性，有限的存留期</span><span class="sxs-lookup"><span data-stu-id="541bc-336">Transient, limited lifetime</span></span>

<span data-ttu-id="541bc-337">**案例**</span><span class="sxs-lookup"><span data-stu-id="541bc-337">**Scenario**</span></span>

<span data-ttu-id="541bc-338">應用程式需要在 <xref:System.IDisposable> 下列任一情況下具有暫時性存留期的實例：</span><span class="sxs-lookup"><span data-stu-id="541bc-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="541bc-339">實例會在根範圍中解析 (根容器) 。</span><span class="sxs-lookup"><span data-stu-id="541bc-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="541bc-340">應該在範圍結束之前處置實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="541bc-341">**方案**</span><span class="sxs-lookup"><span data-stu-id="541bc-341">**Solution**</span></span>

<span data-ttu-id="541bc-342">使用 factory 模式，在父範圍外建立實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="541bc-343">在這種情況下，應用程式通常會有 `Create` 方法，直接呼叫最終型別的函式。</span><span class="sxs-lookup"><span data-stu-id="541bc-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="541bc-344">如果最終類型具有其他相依性，則 factory 可以：</span><span class="sxs-lookup"><span data-stu-id="541bc-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="541bc-345"><xref:System.IServiceProvider>在其函式中接收。</span><span class="sxs-lookup"><span data-stu-id="541bc-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="541bc-346">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 來具現化容器外的實例，同時使用容器來執行其相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="541bc-347">共用實例，有限的存留期</span><span class="sxs-lookup"><span data-stu-id="541bc-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="541bc-348">**案例**</span><span class="sxs-lookup"><span data-stu-id="541bc-348">**Scenario**</span></span>

<span data-ttu-id="541bc-349">應用程式需要 <xref:System.IDisposable> 多個服務之間的共用實例，但 <xref:System.IDisposable> 應具有有限的存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="541bc-350">**方案**</span><span class="sxs-lookup"><span data-stu-id="541bc-350">**Solution**</span></span>

<span data-ttu-id="541bc-351">註冊具有限定範圍存留期的實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="541bc-352">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 來啟動並建立新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 。</span><span class="sxs-lookup"><span data-stu-id="541bc-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="541bc-353">使用範圍的 <xref:System.IServiceProvider> 來取得所需的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="541bc-354">在存留期結束時處置範圍。</span><span class="sxs-lookup"><span data-stu-id="541bc-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="541bc-355">一般 IDisposable 指導方針</span><span class="sxs-lookup"><span data-stu-id="541bc-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="541bc-356">不要向 <xref:System.IDisposable> 暫時性範圍註冊實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="541bc-357">請改用 factory 模式。</span><span class="sxs-lookup"><span data-stu-id="541bc-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="541bc-358">請勿解析 <xref:System.IDisposable> 根範圍中的暫時性或範圍實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="541bc-359">唯一的例外狀況是當應用程式建立/重新建立和處置時 <xref:System.IServiceProvider> ，這不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="541bc-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="541bc-360">透過 DI 接收相依性 <xref:System.IDisposable> 並不會要求接收者 <xref:System.IDisposable> 自行執行。</span><span class="sxs-lookup"><span data-stu-id="541bc-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="541bc-361">相依性的接收者不 <xref:System.IDisposable> 應呼叫 <xref:System.IDisposable.Dispose%2A> 該相依性上的。</span><span class="sxs-lookup"><span data-stu-id="541bc-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="541bc-362">範圍應該用來控制服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="541bc-363">範圍不是階層式，而且範圍之間沒有特殊連接。</span><span class="sxs-lookup"><span data-stu-id="541bc-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="541bc-364">預設服務容器取代</span><span class="sxs-lookup"><span data-stu-id="541bc-364">Default service container replacement</span></span>

<span data-ttu-id="541bc-365">內建的服務容器是設計用來滿足架構和大部分取用者應用程式的需求。</span><span class="sxs-lookup"><span data-stu-id="541bc-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="541bc-366">我們建議使用內建容器，除非您需要內建容器不支援的特定功能，例如：</span><span class="sxs-lookup"><span data-stu-id="541bc-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="541bc-367">屬性插入</span><span class="sxs-lookup"><span data-stu-id="541bc-367">Property injection</span></span>
* <span data-ttu-id="541bc-368">根據名稱插入</span><span class="sxs-lookup"><span data-stu-id="541bc-368">Injection based on name</span></span>
* <span data-ttu-id="541bc-369">子容器</span><span class="sxs-lookup"><span data-stu-id="541bc-369">Child containers</span></span>
* <span data-ttu-id="541bc-370">自訂生命週期管理</span><span class="sxs-lookup"><span data-stu-id="541bc-370">Custom lifetime management</span></span>
* <span data-ttu-id="541bc-371">`Func<T>` 支援延遲初始設定</span><span class="sxs-lookup"><span data-stu-id="541bc-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="541bc-372">以慣例為基礎的註冊</span><span class="sxs-lookup"><span data-stu-id="541bc-372">Convention-based registration</span></span>

<span data-ttu-id="541bc-373">下列協力廠商容器可以與 ASP.NET Core 應用程式搭配使用：</span><span class="sxs-lookup"><span data-stu-id="541bc-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="541bc-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="541bc-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="541bc-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="541bc-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="541bc-376">Grace</span><span class="sxs-lookup"><span data-stu-id="541bc-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="541bc-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="541bc-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="541bc-378">拉馬爾</span><span class="sxs-lookup"><span data-stu-id="541bc-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="541bc-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="541bc-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="541bc-380">Unity</span><span class="sxs-lookup"><span data-stu-id="541bc-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="541bc-381">執行緒安全</span><span class="sxs-lookup"><span data-stu-id="541bc-381">Thread safety</span></span>

<span data-ttu-id="541bc-382">建立具備執行緒安全性的 singleton 服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="541bc-383">如果 singleton 服務相依於暫時性服務，暫時性服務可能也需要具備執行緒安全性，取決於 singleton 如何使用它。</span><span class="sxs-lookup"><span data-stu-id="541bc-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="541bc-384">單一服務的 factory 方法（例如 AddSingleton (IServiceCollection 的第二個引數[ \<TService> ），Func \<IServiceProvider,TService>) ](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)不需要是安全線程。</span><span class="sxs-lookup"><span data-stu-id="541bc-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="541bc-385">就像型別 (`static`) 建構函式一樣，它一定會被單一執行緒呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="541bc-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="541bc-386">建議</span><span class="sxs-lookup"><span data-stu-id="541bc-386">Recommendations</span></span>

* <span data-ttu-id="541bc-387">不支援以 `async/await` 與 `Task` 為基礎的服務解析。</span><span class="sxs-lookup"><span data-stu-id="541bc-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="541bc-388">C # 不支援非同步函式。</span><span class="sxs-lookup"><span data-stu-id="541bc-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="541bc-389">建議的模式是以同步方式解析服務後使用非同步方法。</span><span class="sxs-lookup"><span data-stu-id="541bc-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="541bc-390">避免直接在服務容器中儲存資料與設定。</span><span class="sxs-lookup"><span data-stu-id="541bc-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="541bc-391">例如，使用者的購物車通常不應該新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="541bc-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="541bc-392">組態應該使用[選項模式](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="541bc-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="541bc-393">同樣地，請避免只存在以允許存取某個其他物件的「資料持有者」物件。</span><span class="sxs-lookup"><span data-stu-id="541bc-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="541bc-394">最好是透過 DI 要求實際項目。</span><span class="sxs-lookup"><span data-stu-id="541bc-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="541bc-395">避免靜態存取服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-395">Avoid static access to services.</span></span> <span data-ttu-id="541bc-396">例如，避免以靜態方式輸入[IApplicationBuilder microsoft.visualbasic.applicationservices.cantstartsingleinstanceexception](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ，以供) 的其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="541bc-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="541bc-397">讓 DI factory 快速且同步。</span><span class="sxs-lookup"><span data-stu-id="541bc-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="541bc-398">避免使用「服務定位器模式」\*\*。</span><span class="sxs-lookup"><span data-stu-id="541bc-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="541bc-399">例如，當您可以改用 DI 時，請勿叫用 <xref:System.IServiceProvider.GetService*> 來取得服務執行個體：</span><span class="sxs-lookup"><span data-stu-id="541bc-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="541bc-400">**不正確：**</span><span class="sxs-lookup"><span data-stu-id="541bc-400">**Incorrect:**</span></span>

    ![不正確的程式碼](dependency-injection/_static/bad.png)

  <span data-ttu-id="541bc-402">**正確**：</span><span class="sxs-lookup"><span data-stu-id="541bc-402">**Correct**:</span></span>

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
* <span data-ttu-id="541bc-403">另一個要避免的服務定位器變化是插入在執行階段解析相依性的處理站。</span><span class="sxs-lookup"><span data-stu-id="541bc-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="541bc-404">這兩種做法都會混用[控制反轉](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="541bc-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="541bc-405">避免以靜態方式存取 `HttpContext` (例如 [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="541bc-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="541bc-406">避免 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> 在中呼叫 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="541bc-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="541bc-407">`BuildServiceProvider`當開發人員想要解析中的服務時，通常會呼叫 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="541bc-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="541bc-408">例如，假設您需要 go [從設定取得] 的情況 `LoginPath` 。</span><span class="sxs-lookup"><span data-stu-id="541bc-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="541bc-409">請避免下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="541bc-409">Avoid the following code:</span></span>

  ![呼叫 BuildServiceProvider 的錯誤程式碼](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="541bc-411">在上圖中，選取底下的綠色波浪線會 `services.BuildServiceProvider` 顯示下列 ASP0000 警告：</span><span class="sxs-lookup"><span data-stu-id="541bc-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="541bc-412">ASP0000 從應用程式代碼呼叫 ' BuildServiceProvider ' 會產生一個額外的單一服務複本。</span><span class="sxs-lookup"><span data-stu-id="541bc-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="541bc-413">請考慮像是相依性插入服務作為「設定」參數的替代方案。</span><span class="sxs-lookup"><span data-stu-id="541bc-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="541bc-414">呼叫 `BuildServiceProvider` 會建立第二個容器，它可以建立損毀的單次個體，並在多個容器中導致物件圖形的參考。</span><span class="sxs-lookup"><span data-stu-id="541bc-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="541bc-415">要取得的正確方式 `LoginPath` 是搭配使用選項模式與 DI：</span><span class="sxs-lookup"><span data-stu-id="541bc-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="541bc-416">容器會攔截可處置的暫時性服務以供處置。</span><span class="sxs-lookup"><span data-stu-id="541bc-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="541bc-417">如果從最上層容器解析，這可能會變成記憶體流失。</span><span class="sxs-lookup"><span data-stu-id="541bc-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="541bc-418">啟用範圍驗證，以確保應用程式沒有範圍服務可捕捉單次個體。</span><span class="sxs-lookup"><span data-stu-id="541bc-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="541bc-419">如需詳細資訊，請參閱[範圍驗證](#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="541bc-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="541bc-420">就像所有的建議集，您可能會遇到需要忽略建議的情況。</span><span class="sxs-lookup"><span data-stu-id="541bc-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="541bc-421">例外狀況很罕見，大部分是在架構本身內的特殊案例。</span><span class="sxs-lookup"><span data-stu-id="541bc-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="541bc-422">DI 是靜態/全域物件存取模式的「替代」\*\* 選項。</span><span class="sxs-lookup"><span data-stu-id="541bc-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="541bc-423">如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。</span><span class="sxs-lookup"><span data-stu-id="541bc-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="541bc-424">DI 中的多租使用者建議模式</span><span class="sxs-lookup"><span data-stu-id="541bc-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="541bc-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore)提供多租使用者。</span><span class="sxs-lookup"><span data-stu-id="541bc-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="541bc-426">如需詳細資訊，請參閱[Orchard Core 檔](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="541bc-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="541bc-427">https://github.com/OrchardCMS/OrchardCore.Samples如需如何使用 Orchard Core Framework 建立模組化和多租使用者應用程式的範例，而不含任何 CMS 特有的功能，請參閱範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="541bc-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="541bc-428">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="541bc-428">Framework-provided services</span></span>

<span data-ttu-id="541bc-429">`Startup.ConfigureServices`方法負責定義應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。</span><span class="sxs-lookup"><span data-stu-id="541bc-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="541bc-430">一開始， `IServiceCollection` 提供的會 `ConfigureServices` 根據主機的[設定方式](xref:fundamentals/index#host)，來擁有架構所定義的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="541bc-431">以 ASP.NET Core 範本為基礎的應用程式會有超過250個由架構註冊的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="541bc-432">下表列出架構註冊服務的小型範例。</span><span class="sxs-lookup"><span data-stu-id="541bc-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="541bc-433">服務類型</span><span class="sxs-lookup"><span data-stu-id="541bc-433">Service Type</span></span> | <span data-ttu-id="541bc-434">存留期</span><span class="sxs-lookup"><span data-stu-id="541bc-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="541bc-435">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="541bc-436">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="541bc-437">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="541bc-438">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="541bc-439">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="541bc-440">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="541bc-441">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="541bc-442">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="541bc-443">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="541bc-444">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="541bc-445">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="541bc-446">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="541bc-447">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="541bc-448">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="541bc-449">其他資源</span><span class="sxs-lookup"><span data-stu-id="541bc-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="541bc-450">在 ASP.NET Core 中處置 IDisposables 的四種方式</span><span class="sxs-lookup"><span data-stu-id="541bc-450">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="541bc-451">在 ASP.NET Core 使用 Dependency Injection 撰寫簡潔的程式碼 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="541bc-451">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="541bc-452">明確相依性準則</span><span class="sxs-lookup"><span data-stu-id="541bc-452">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="541bc-453">[逆轉控制容器和相依性插入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="541bc-453">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* <span data-ttu-id="541bc-454">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (如何使用 ASP.NET Core DI 中的多個介面註冊服務)</span><span class="sxs-lookup"><span data-stu-id="541bc-454">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="541bc-455">作者： [Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="541bc-455">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="541bc-456">ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。</span><span class="sxs-lookup"><span data-stu-id="541bc-456">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="541bc-457">如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="541bc-457">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="541bc-458">[查看或下載範例程式碼](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="541bc-458">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="541bc-459">相依性插入概觀</span><span class="sxs-lookup"><span data-stu-id="541bc-459">Overview of dependency injection</span></span>

<span data-ttu-id="541bc-460">「相依性」\*\* 是另一個物件所需的任何物件。</span><span class="sxs-lookup"><span data-stu-id="541bc-460">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="541bc-461">檢查具有 `WriteMessage` 方法的下列 `MyDependency` 物件，應用程式中的其他類別相依於此物件：</span><span class="sxs-lookup"><span data-stu-id="541bc-461">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="541bc-462">您可以建立 `MyDependency` 類別的執行個體，讓 `WriteMessage` 方法可供類別使用。</span><span class="sxs-lookup"><span data-stu-id="541bc-462">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="541bc-463">`MyDependency` 類別是 `IndexModel` 類別的相依性：</span><span class="sxs-lookup"><span data-stu-id="541bc-463">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="541bc-464">該類別會建立 `MyDependency` 執行個體並直接相依於該執行個體。</span><span class="sxs-lookup"><span data-stu-id="541bc-464">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="541bc-465">程式碼相依性 (例如前一個範例) 有問題，因此基於下列原因應該避免使用：</span><span class="sxs-lookup"><span data-stu-id="541bc-465">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="541bc-466">若要將 `MyDependency` 取代為不同的實作，必須修改該類別。</span><span class="sxs-lookup"><span data-stu-id="541bc-466">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="541bc-467">若 `MyDependency` 有相依性，那些相依性必須由該類別設定。</span><span class="sxs-lookup"><span data-stu-id="541bc-467">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="541bc-468">在具有多個相依於 `MyDependency` 之多個類別的大型專案中，設定程式碼在不同的應用程式之間會變得鬆散。</span><span class="sxs-lookup"><span data-stu-id="541bc-468">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="541bc-469">此實作難以進行單元測試。</span><span class="sxs-lookup"><span data-stu-id="541bc-469">This implementation is difficult to unit test.</span></span> <span data-ttu-id="541bc-470">應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。</span><span class="sxs-lookup"><span data-stu-id="541bc-470">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="541bc-471">相依性插入可透過下列方式解決這些問題：</span><span class="sxs-lookup"><span data-stu-id="541bc-471">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="541bc-472">使用介面或基底類別來將相依性資訊抽象化。</span><span class="sxs-lookup"><span data-stu-id="541bc-472">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="541bc-473">在服務容器中註冊相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-473">Registration of the dependency in a service container.</span></span> <span data-ttu-id="541bc-474">ASP.NET Core 提供內建服務容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="541bc-474">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="541bc-475">服務會在應用程式的 `Startup.ConfigureServices` 方法中註冊。</span><span class="sxs-lookup"><span data-stu-id="541bc-475">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="541bc-476">將服務「插入」\*\* 到服務使用位置之類別的建構函式。</span><span class="sxs-lookup"><span data-stu-id="541bc-476">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="541bc-477">架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。</span><span class="sxs-lookup"><span data-stu-id="541bc-477">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="541bc-478">在[範例應用程式](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 介面定義了服務提供給應用程式的方法：</span><span class="sxs-lookup"><span data-stu-id="541bc-478">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="541bc-479">這個介面是由具象型別 `MyDependency` 所實作：</span><span class="sxs-lookup"><span data-stu-id="541bc-479">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="541bc-480">`MyDependency` 在其建構函式中會要求 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="541bc-480">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="541bc-481">以鏈結方式使用相依性插入並非不尋常。</span><span class="sxs-lookup"><span data-stu-id="541bc-481">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="541bc-482">每個要求的相依性接著會要求其自己的相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-482">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="541bc-483">容器會解決圖形中的相依性，並傳回完全解析的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-483">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="541bc-484">必須先解析的相依性集合組通常稱為「相依性樹狀結構」**、「相依性圖形」** 或「物件圖形」\*\*。</span><span class="sxs-lookup"><span data-stu-id="541bc-484">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="541bc-485">`IMyDependency` 與 `ILogger<TCategoryName>` 必須在服務容器中註冊。</span><span class="sxs-lookup"><span data-stu-id="541bc-485">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="541bc-486">`IMyDependency` 是在 `Startup.ConfigureServices` 中註冊。</span><span class="sxs-lookup"><span data-stu-id="541bc-486">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="541bc-487">`ILogger<TCategoryName>` 是由記錄抽象基礎結構所註冊，所以它是預設由架構所註冊的[架構提供的服務](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="541bc-487">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="541bc-488">容器會利用 [(泛型) 開放類型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) 解析 `ILogger<TCategoryName>`，讓您不需註冊每個 [(泛型) 建構的型別](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="541bc-488">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="541bc-489">在範例應用程式中，`IMyDependency` 服務是使用具象型別 `MyDependency` 所註冊。</span><span class="sxs-lookup"><span data-stu-id="541bc-489">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="541bc-490">註冊會將服務的存留期範圍限制為單一要求的存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-490">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="541bc-491">將在此主題稍後將說明[服務存留期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="541bc-491">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="541bc-492">每個 `services.Add{SERVICE_NAME}` 擴充方法都會新增 (並且可能會設定) 服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-492">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="541bc-493">例如，會 `services.AddMvc()` 加入服務 Razor 頁面，而 MVC 則需要。</span><span class="sxs-lookup"><span data-stu-id="541bc-493">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="541bc-494">建議應用程式遵循此慣例。</span><span class="sxs-lookup"><span data-stu-id="541bc-494">We recommended that apps follow this convention.</span></span> <span data-ttu-id="541bc-495">在 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空間中放置擴充方法，以封裝服務註冊群組。</span><span class="sxs-lookup"><span data-stu-id="541bc-495">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="541bc-496">如果服務的建構函式需要[內建類型](/dotnet/csharp/language-reference/keywords/built-in-types-table)，例如 `string`，可以使用[組態](xref:fundamentals/configuration/index)或[選項模式](xref:fundamentals/configuration/options)插入該類型：</span><span class="sxs-lookup"><span data-stu-id="541bc-496">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="541bc-497">服務執行個體是透過服務使用所在之類別建構函式所要求，而且會指派給私人欄位。</span><span class="sxs-lookup"><span data-stu-id="541bc-497">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="541bc-498">該欄位會視需要用來存取整個類別中的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-498">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="541bc-499">在範例應用程式中，會要求 `IMyDependency` 執行個體並使用它來呼叫服務的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="541bc-499">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="541bc-500">插入至啟動的服務</span><span class="sxs-lookup"><span data-stu-id="541bc-500">Services injected into Startup</span></span>

<span data-ttu-id="541bc-501">`Startup`使用泛型主機 () 時，只有下列服務類型可以插入至此函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="541bc-501">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="541bc-502">服務可以插入 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="541bc-502">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="541bc-503">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="541bc-503">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="541bc-504">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="541bc-504">Framework-provided services</span></span>

<span data-ttu-id="541bc-505">`Startup.ConfigureServices`方法負責定義應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。</span><span class="sxs-lookup"><span data-stu-id="541bc-505">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="541bc-506">一開始， `IServiceCollection` 提供的會 `ConfigureServices` 根據主機的[設定方式](xref:fundamentals/index#host)，來擁有架構所定義的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-506">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="541bc-507">以 ASP.NET Core 範本為基礎的應用程式，在架構中註冊數百項服務並不常見。</span><span class="sxs-lookup"><span data-stu-id="541bc-507">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="541bc-508">下表列出架構註冊服務的小型範例。</span><span class="sxs-lookup"><span data-stu-id="541bc-508">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="541bc-509">服務類型</span><span class="sxs-lookup"><span data-stu-id="541bc-509">Service Type</span></span> | <span data-ttu-id="541bc-510">存留期</span><span class="sxs-lookup"><span data-stu-id="541bc-510">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="541bc-511">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-511">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="541bc-512">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="541bc-513">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="541bc-514">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="541bc-515">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-515">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="541bc-516">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-516">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="541bc-517">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-517">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="541bc-518">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="541bc-519">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="541bc-520">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="541bc-521">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-521">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="541bc-522">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="541bc-523">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="541bc-524">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-524">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="541bc-525">以擴充方法註冊其他服務</span><span class="sxs-lookup"><span data-stu-id="541bc-525">Register additional services with extension methods</span></span>

<span data-ttu-id="541bc-526">當可以使用服務集合擴充方法來註冊服務 (如果需要，也可以註冊其相依服務) 時，慣例是使用單一 `Add{SERVICE_NAME}` 擴充方法來註冊該服務要求的所有服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-526">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="541bc-527">下列程式碼範例說明如何使用擴充方法[AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)和，將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> ：</span><span class="sxs-lookup"><span data-stu-id="541bc-527">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="541bc-528">如需詳細資訊，請參閱 API 文件中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 類別。</span><span class="sxs-lookup"><span data-stu-id="541bc-528">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="541bc-529">服務存留期</span><span class="sxs-lookup"><span data-stu-id="541bc-529">Service lifetimes</span></span>

<span data-ttu-id="541bc-530">為每個已註冊的服務選擇適當的存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-530">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="541bc-531">ASP.NET Core 服務可以使用下列存留期進行設定：</span><span class="sxs-lookup"><span data-stu-id="541bc-531">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="541bc-532">暫時性</span><span class="sxs-lookup"><span data-stu-id="541bc-532">Transient</span></span>

<span data-ttu-id="541bc-533">每次從服務容器要求暫時性存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 時都會建立它們。</span><span class="sxs-lookup"><span data-stu-id="541bc-533">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="541bc-534">此存留期最適合用於輕量型的無狀態服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-534">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="541bc-535">在處理要求的應用程式中，會在要求結束時處置暫時性服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-535">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="541bc-536">具範圍</span><span class="sxs-lookup"><span data-stu-id="541bc-536">Scoped</span></span>

<span data-ttu-id="541bc-537">具範圍存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 會在每次用戶端要求 (連線) 時建立一次。</span><span class="sxs-lookup"><span data-stu-id="541bc-537">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="541bc-538">在處理要求的應用程式中，會在要求結束時處置已設定範圍的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-538">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="541bc-539">在中介軟體中使用具範圍服務時，請將該服務插入 `Invoke` 或 `InvokeAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="541bc-539">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="541bc-540">不要透過函式[插入](xref:mvc/controllers/dependency-injection#constructor-injection)來插入，因為它會強制服務的行為就像 singleton 一樣。</span><span class="sxs-lookup"><span data-stu-id="541bc-540">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="541bc-541">如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="541bc-541">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="541bc-542">單一</span><span class="sxs-lookup"><span data-stu-id="541bc-542">Singleton</span></span>

<span data-ttu-id="541bc-543">當第一次收到有關單一資料庫存留期服務的要求時 (或是當執行 `Startup.ConfigureServices` 且隨著服務註冊指定執行個體時)，即會建立單一資料庫存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)。</span><span class="sxs-lookup"><span data-stu-id="541bc-543">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="541bc-544">每個後續要求都會使用相同的執行個體。</span><span class="sxs-lookup"><span data-stu-id="541bc-544">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="541bc-545">若應用程式要求單一資料庫行為，建議您允許服務容器管理服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-545">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="541bc-546">不要實作單一資料庫設計模式並為使用者提供可用來管理類別中物件存留期的程式碼。</span><span class="sxs-lookup"><span data-stu-id="541bc-546">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="541bc-547">在處理要求的應用程式中， <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 會在應用程式關閉時處置單一服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-547">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="541bc-548">從單一資料庫解析具範圍服務是非常危險的。</span><span class="sxs-lookup"><span data-stu-id="541bc-548">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="541bc-549">處理後續要求時，它可能會導致服務有不正確的狀態。</span><span class="sxs-lookup"><span data-stu-id="541bc-549">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="541bc-550">服務註冊方法</span><span class="sxs-lookup"><span data-stu-id="541bc-550">Service registration methods</span></span>

<span data-ttu-id="541bc-551">服務註冊擴充方法提供在特定案例中很有用的多載。</span><span class="sxs-lookup"><span data-stu-id="541bc-551">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="541bc-552">方法</span><span class="sxs-lookup"><span data-stu-id="541bc-552">Method</span></span> | <span data-ttu-id="541bc-553">自動</span><span class="sxs-lookup"><span data-stu-id="541bc-553">Automatic</span></span><br><span data-ttu-id="541bc-554">object</span><span class="sxs-lookup"><span data-stu-id="541bc-554">object</span></span><br><span data-ttu-id="541bc-555">處置</span><span class="sxs-lookup"><span data-stu-id="541bc-555">disposal</span></span> | <span data-ttu-id="541bc-556">多個</span><span class="sxs-lookup"><span data-stu-id="541bc-556">Multiple</span></span><br><span data-ttu-id="541bc-557">實作</span><span class="sxs-lookup"><span data-stu-id="541bc-557">implementations</span></span> | <span data-ttu-id="541bc-558">傳遞引數</span><span class="sxs-lookup"><span data-stu-id="541bc-558">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="541bc-559">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-559">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="541bc-560">是</span><span class="sxs-lookup"><span data-stu-id="541bc-560">Yes</span></span> | <span data-ttu-id="541bc-561">是</span><span class="sxs-lookup"><span data-stu-id="541bc-561">Yes</span></span> | <span data-ttu-id="541bc-562">否</span><span class="sxs-lookup"><span data-stu-id="541bc-562">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="541bc-563">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-563">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="541bc-564">是</span><span class="sxs-lookup"><span data-stu-id="541bc-564">Yes</span></span> | <span data-ttu-id="541bc-565">是</span><span class="sxs-lookup"><span data-stu-id="541bc-565">Yes</span></span> | <span data-ttu-id="541bc-566">是</span><span class="sxs-lookup"><span data-stu-id="541bc-566">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="541bc-567">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-567">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="541bc-568">是</span><span class="sxs-lookup"><span data-stu-id="541bc-568">Yes</span></span> | <span data-ttu-id="541bc-569">否</span><span class="sxs-lookup"><span data-stu-id="541bc-569">No</span></span> | <span data-ttu-id="541bc-570">否</span><span class="sxs-lookup"><span data-stu-id="541bc-570">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="541bc-571">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-571">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="541bc-572">否</span><span class="sxs-lookup"><span data-stu-id="541bc-572">No</span></span> | <span data-ttu-id="541bc-573">是</span><span class="sxs-lookup"><span data-stu-id="541bc-573">Yes</span></span> | <span data-ttu-id="541bc-574">是</span><span class="sxs-lookup"><span data-stu-id="541bc-574">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="541bc-575">範例：</span><span class="sxs-lookup"><span data-stu-id="541bc-575">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="541bc-576">否</span><span class="sxs-lookup"><span data-stu-id="541bc-576">No</span></span> | <span data-ttu-id="541bc-577">否</span><span class="sxs-lookup"><span data-stu-id="541bc-577">No</span></span> | <span data-ttu-id="541bc-578">是</span><span class="sxs-lookup"><span data-stu-id="541bc-578">Yes</span></span> |

<span data-ttu-id="541bc-579">如需類型處置的詳細資訊，請參閱[＜服務處置＞](#disposal-of-services)一節。</span><span class="sxs-lookup"><span data-stu-id="541bc-579">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="541bc-580">多個實作的常見案例是[模擬測試類型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="541bc-580">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="541bc-581">如果還未註冊任何實作，則 `TryAdd{LIFETIME}` 方法只會註冊服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-581">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="541bc-582">在下列範例中，第一行會為 `IMyDependency` 註冊 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="541bc-582">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="541bc-583">第二行則沒有任何作用，因為 `IMyDependency` 已具有註冊的實作：</span><span class="sxs-lookup"><span data-stu-id="541bc-583">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="541bc-584">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="541bc-584">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="541bc-585">如果還沒有「相同類型的」\*\* 實作，則 [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法只會註冊服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-585">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="541bc-586">多個服務會透過 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="541bc-586">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="541bc-587">註冊服務時，如果尚未新增相同類型的執行個體，則開發人員只會希望新增執行個體。</span><span class="sxs-lookup"><span data-stu-id="541bc-587">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="541bc-588">程式庫作者通常會使用此方法來避免註冊容器中某個執行個體的兩個複本。</span><span class="sxs-lookup"><span data-stu-id="541bc-588">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="541bc-589">在下列範例中，第一行會為 `IMyDep1` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="541bc-589">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="541bc-590">第二行會為 `IMyDep2` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="541bc-590">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="541bc-591">第三行則沒有任何作用，因為 `IMyDep1` 已具有 `MyDep` 的已註冊實作：</span><span class="sxs-lookup"><span data-stu-id="541bc-591">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="541bc-592">建構函式插入行為</span><span class="sxs-lookup"><span data-stu-id="541bc-592">Constructor injection behavior</span></span>

<span data-ttu-id="541bc-593">服務可以透過兩個機制來解析：</span><span class="sxs-lookup"><span data-stu-id="541bc-593">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="541bc-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允許在相依性插入容器中不註冊服務時，建立物件。</span><span class="sxs-lookup"><span data-stu-id="541bc-594"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="541bc-595">搭配使用者面向抽象 (例如標籤協助程式、MVC 控制器與模型繫結器) 使用 `ActivatorUtilities`。</span><span class="sxs-lookup"><span data-stu-id="541bc-595">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="541bc-596">建構函式可以接受不是由相依性插入提供的引數，但引數必須指派預設值。</span><span class="sxs-lookup"><span data-stu-id="541bc-596">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="541bc-597">當服務由或解析時，程式的 `IServiceProvider` `ActivatorUtilities` [插入](xref:mvc/controllers/dependency-injection#constructor-injection)需要*公用*的函式。</span><span class="sxs-lookup"><span data-stu-id="541bc-597">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="541bc-598">當服務由解析時 `ActivatorUtilities` ，程式的[插入](xref:mvc/controllers/dependency-injection#constructor-injection)只需要有一個適用的函式。</span><span class="sxs-lookup"><span data-stu-id="541bc-598">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="541bc-599">支援建構函式多載，但只能有一個多載存在，其引數可以藉由相依性插入而完成。</span><span class="sxs-lookup"><span data-stu-id="541bc-599">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="541bc-600">Entity Framework 內容</span><span class="sxs-lookup"><span data-stu-id="541bc-600">Entity Framework contexts</span></span>

<span data-ttu-id="541bc-601">因為一般會將 Web 應用程式資料庫作業範圍設定為用戶端要求，所以通常會使用[具範圍存留期](#service-lifetimes)將 Entity Framework 內容新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="541bc-601">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="541bc-602">如果在註冊資料庫內容時， [AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)多載未指定存留期，則會將預設存留期設定為範圍。</span><span class="sxs-lookup"><span data-stu-id="541bc-602">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="541bc-603">指定存留期的服務不應該使用存留期比服務還短的資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="541bc-603">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="541bc-604">留期和註冊選項</span><span class="sxs-lookup"><span data-stu-id="541bc-604">Lifetime and registration options</span></span>

<span data-ttu-id="541bc-605">為了示範這些存留期和註冊選項之間的差異，請考慮下列介面，以具有唯一識別碼 `OperationId` 的「作業」代表工作。</span><span class="sxs-lookup"><span data-stu-id="541bc-605">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="541bc-606">視如何針對下列介面設定作業服務存留期而定，當類別要求時，容器會提供相同或不同的服務執行個體：</span><span class="sxs-lookup"><span data-stu-id="541bc-606">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="541bc-607">介面是在 `Operation` 類別中實作。</span><span class="sxs-lookup"><span data-stu-id="541bc-607">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="541bc-608">若未提供 GUID，`Operation` 建構函式會產生 GUID：</span><span class="sxs-lookup"><span data-stu-id="541bc-608">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="541bc-609">會註冊相依於每種其他 `Operation` 類型的 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="541bc-609">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="541bc-610">當透過相依性插入要求 `OperationService` 時，它會根據相依服務的存留期擷取每個服務的新執行個體或現有執行個體。</span><span class="sxs-lookup"><span data-stu-id="541bc-610">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="541bc-611">當因收到來自容器的要求而建立暫時性服務時，`IOperationTransient` 服務的 `OperationId` 會與 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="541bc-611">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="541bc-612">`OperationService` 會收到 `IOperationTransient` 類別的新執行個體。</span><span class="sxs-lookup"><span data-stu-id="541bc-612">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="541bc-613">新執行個體會產生不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="541bc-613">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="541bc-614">當因用戶端要求而建立具範圍的服務時，`IOperationScoped` 服務與用戶端要求中 `OperationService` 的 `OperationId` 皆相同。</span><span class="sxs-lookup"><span data-stu-id="541bc-614">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="541bc-615">在不同的用戶端要求中，兩個服務的 `OperationId` 值都不同。</span><span class="sxs-lookup"><span data-stu-id="541bc-615">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="541bc-616">當建立一次 singleton 與 singleton 執行個體服務，並在所有用戶端要求與所有服務中使用時，`OperationId` 在所有服務要求中會固定不變。</span><span class="sxs-lookup"><span data-stu-id="541bc-616">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="541bc-617">在 `Startup.ConfigureServices` 中，每個類型都會根據其具名存留期新增至容器：</span><span class="sxs-lookup"><span data-stu-id="541bc-617">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="541bc-618">`IOperationSingletonInstance` 服務使用具有 `Guid.Empty` 已知識別碼的特定執行個體。</span><span class="sxs-lookup"><span data-stu-id="541bc-618">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="541bc-619">很明顯此型別是使用中 (其 GUID 是所有區域)。</span><span class="sxs-lookup"><span data-stu-id="541bc-619">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="541bc-620">範例應用程式示範個別要求內與個別要求之間的物件存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-620">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="541bc-621">範例應用程式的 `IndexModel` 會要求每種 `IOperation` 型別與 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="541bc-621">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="541bc-622">頁面接著會透過屬性指派來顯示所有頁面模型類別與服務的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="541bc-622">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="541bc-623">下面兩個輸出顯示兩個要求的結果：</span><span class="sxs-lookup"><span data-stu-id="541bc-623">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="541bc-624">**第一個要求：**</span><span class="sxs-lookup"><span data-stu-id="541bc-624">**First request:**</span></span>

<span data-ttu-id="541bc-625">控制器作業：</span><span class="sxs-lookup"><span data-stu-id="541bc-625">Controller operations:</span></span>

<span data-ttu-id="541bc-626">暫時性： d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="541bc-626">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="541bc-627">具範圍： 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="541bc-627">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="541bc-628">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="541bc-628">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="541bc-629">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="541bc-629">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="541bc-630">`OperationService` 作業：</span><span class="sxs-lookup"><span data-stu-id="541bc-630">`OperationService` operations:</span></span>

<span data-ttu-id="541bc-631">暫時性： c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="541bc-631">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="541bc-632">具範圍： 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="541bc-632">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="541bc-633">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="541bc-633">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="541bc-634">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="541bc-634">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="541bc-635">**:第二個要求：**</span><span class="sxs-lookup"><span data-stu-id="541bc-635">**Second request:**</span></span>

<span data-ttu-id="541bc-636">控制器作業：</span><span class="sxs-lookup"><span data-stu-id="541bc-636">Controller operations:</span></span>

<span data-ttu-id="541bc-637">暫時性： b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="541bc-637">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="541bc-638">具範圍： 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="541bc-638">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="541bc-639">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="541bc-639">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="541bc-640">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="541bc-640">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="541bc-641">`OperationService` 作業：</span><span class="sxs-lookup"><span data-stu-id="541bc-641">`OperationService` operations:</span></span>

<span data-ttu-id="541bc-642">暫時性： c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="541bc-642">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="541bc-643">具範圍： 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="541bc-643">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="541bc-644">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="541bc-644">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="541bc-645">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="541bc-645">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="541bc-646">觀察哪些 `OperationId` 值在要求內以及要求之間不同：</span><span class="sxs-lookup"><span data-stu-id="541bc-646">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="541bc-647">「暫時性」\*\* 物件一律不同。</span><span class="sxs-lookup"><span data-stu-id="541bc-647">*Transient* objects are always different.</span></span> <span data-ttu-id="541bc-648">第一個與第二個用戶端要求的暫時性 `OperationId` 值在兩個 `OperationService` 作業之間與各用戶端要求之間都不同。</span><span class="sxs-lookup"><span data-stu-id="541bc-648">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="541bc-649">新執行個體會提供給每個服務要求以及用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="541bc-649">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="541bc-650">「具範圍」\*\* 物件在同一個用戶端要求內皆相同，但在不同的用戶端要求之間則不同。</span><span class="sxs-lookup"><span data-stu-id="541bc-650">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="541bc-651">*Singleton*不論是否 `Operation` 在中提供實例，每個物件和每個要求的單一物件都是相同的 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="541bc-651">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="541bc-652">從主要呼叫服務</span><span class="sxs-lookup"><span data-stu-id="541bc-652">Call services from main</span></span>

<span data-ttu-id="541bc-653">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>，以解析應用程式範圍中的範圍服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-653">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="541bc-654">此法可用於在開機時存取範圍服務，以執行初始化工作。</span><span class="sxs-lookup"><span data-stu-id="541bc-654">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="541bc-655">下列範例示範如何在 `Program.Main` 中取得 `MyScopedService`：</span><span class="sxs-lookup"><span data-stu-id="541bc-655">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="541bc-656">範圍驗證</span><span class="sxs-lookup"><span data-stu-id="541bc-656">Scope validation</span></span>

<span data-ttu-id="541bc-657">當應用程式在開發環境中執行時，預設服務提供者會執行檢查以確認：</span><span class="sxs-lookup"><span data-stu-id="541bc-657">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="541bc-658">範圍服務不是直接或間接由開機服務提供者解析。</span><span class="sxs-lookup"><span data-stu-id="541bc-658">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="541bc-659">範圍服務不是直接或間接插入至單一服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-659">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="541bc-660">根服務提供者會在呼叫 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 時建立。</span><span class="sxs-lookup"><span data-stu-id="541bc-660">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="541bc-661">當提供者啟動應用程式時，根服務提供者的存留期與應用程式/伺服器的存留期一致，並會在應用程式關閉時處置。</span><span class="sxs-lookup"><span data-stu-id="541bc-661">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="541bc-662">範圍服務會由建立這些服務的容器處置。</span><span class="sxs-lookup"><span data-stu-id="541bc-662">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="541bc-663">若是在根容器中建立範圍服務，因為當應用程式/伺服器關機時，服務只會由根容器處理，所以服務的存留期會提升為單一服務等級。</span><span class="sxs-lookup"><span data-stu-id="541bc-663">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="541bc-664">當呼叫 `BuildServiceProvider` 時，驗證服務範圍會攔截到這些情況。</span><span class="sxs-lookup"><span data-stu-id="541bc-664">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="541bc-665">如需詳細資訊，請參閱<xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="541bc-665">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="541bc-666">要求服務</span><span class="sxs-lookup"><span data-stu-id="541bc-666">Request Services</span></span>

<span data-ttu-id="541bc-667">來自 `HttpContext`，在 ASP.NET Core 要求內提供的服務是透過 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公開。</span><span class="sxs-lookup"><span data-stu-id="541bc-667">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="541bc-668">要求服務代表您在應用程式中設定和要求的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-668">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="541bc-669">當物件指定相依性時，這些會由在 `RequestServices` 中找到的類型來滿足，而非 `ApplicationServices`。</span><span class="sxs-lookup"><span data-stu-id="541bc-669">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="541bc-670">一般而言，應用程式不應該直接使用這些屬性。</span><span class="sxs-lookup"><span data-stu-id="541bc-670">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="541bc-671">相反地，透過類別建構函式要求類別所需的型別，以及允許架構插入相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-671">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="541bc-672">這會產生容易測試的類別。</span><span class="sxs-lookup"><span data-stu-id="541bc-672">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="541bc-673">偏好要求相依性作為建構函式參數，而不要存取 `RequestServices` 集合。</span><span class="sxs-lookup"><span data-stu-id="541bc-673">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="541bc-674">針對相依性插入設計服務</span><span class="sxs-lookup"><span data-stu-id="541bc-674">Design services for dependency injection</span></span>

<span data-ttu-id="541bc-675">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="541bc-675">Best practices are to:</span></span>

* <span data-ttu-id="541bc-676">設計服務以使用相依性插入來取得其相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-676">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="541bc-677">避免具狀態的靜態類別和成員。</span><span class="sxs-lookup"><span data-stu-id="541bc-677">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="541bc-678">將應用程式設計成使用單一服務，以避免建立全域狀態。</span><span class="sxs-lookup"><span data-stu-id="541bc-678">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="541bc-679">避免直接在服務內具現化相依類別。</span><span class="sxs-lookup"><span data-stu-id="541bc-679">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="541bc-680">直接具現化會將程式碼耦合到特定實作。</span><span class="sxs-lookup"><span data-stu-id="541bc-680">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="541bc-681">讓應用程式類別維持在小型、情況良好且可輕鬆測試的狀態。</span><span class="sxs-lookup"><span data-stu-id="541bc-681">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="541bc-682">若類別有太多插入的相依性，這通常表示類別有太多責任而且正違反[單一責任原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="541bc-682">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="541bc-683">將類別負責的某些部分移到新的類別，以嘗試重構類別。</span><span class="sxs-lookup"><span data-stu-id="541bc-683">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="541bc-684">請記住，頁面 Razor 頁面模型類別和 MVC 控制器類別應該專注于 UI 考慮。</span><span class="sxs-lookup"><span data-stu-id="541bc-684">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="541bc-685">商務規則和資料存取實作詳細資料應該保存在適合這些[分開考量](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Separation of Concerns) 類別中。</span><span class="sxs-lookup"><span data-stu-id="541bc-685">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="541bc-686">處置服務</span><span class="sxs-lookup"><span data-stu-id="541bc-686">Disposal of services</span></span>

<span data-ttu-id="541bc-687">容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="541bc-687">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="541bc-688">若執行個體由使用者程式碼新增到容器中，它將不會自動處置。</span><span class="sxs-lookup"><span data-stu-id="541bc-688">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="541bc-689">在下列範例中，服務會建立服務容器並自動處置：</span><span class="sxs-lookup"><span data-stu-id="541bc-689">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="541bc-690">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="541bc-690">In the following example:</span></span>

* <span data-ttu-id="541bc-691">服務容器不會建立服務實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-691">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="541bc-692">架構不知道預期的服務存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-692">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="541bc-693">架構不會自動處置服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-693">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="541bc-694">如果服務未在開發人員程式碼中明確處置，它們會持續存在，直到應用程式關閉為止。</span><span class="sxs-lookup"><span data-stu-id="541bc-694">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="541bc-695">暫時性和共用實例的 IDisposable 指引</span><span class="sxs-lookup"><span data-stu-id="541bc-695">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="541bc-696">暫時性，有限的存留期</span><span class="sxs-lookup"><span data-stu-id="541bc-696">Transient, limited lifetime</span></span>

<span data-ttu-id="541bc-697">**案例**</span><span class="sxs-lookup"><span data-stu-id="541bc-697">**Scenario**</span></span>

<span data-ttu-id="541bc-698">應用程式需要在 <xref:System.IDisposable> 下列任一情況下具有暫時性存留期的實例：</span><span class="sxs-lookup"><span data-stu-id="541bc-698">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="541bc-699">實例會在根範圍中解析。</span><span class="sxs-lookup"><span data-stu-id="541bc-699">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="541bc-700">應該在範圍結束之前處置實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-700">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="541bc-701">**方案**</span><span class="sxs-lookup"><span data-stu-id="541bc-701">**Solution**</span></span>

<span data-ttu-id="541bc-702">使用 factory 模式，在父範圍外建立實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-702">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="541bc-703">在這種情況下，應用程式通常會有 `Create` 方法，直接呼叫最終型別的函式。</span><span class="sxs-lookup"><span data-stu-id="541bc-703">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="541bc-704">如果最終類型具有其他相依性，則 factory 可以：</span><span class="sxs-lookup"><span data-stu-id="541bc-704">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="541bc-705"><xref:System.IServiceProvider>在其函式中接收。</span><span class="sxs-lookup"><span data-stu-id="541bc-705">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="541bc-706">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 來具現化容器外的實例，同時使用容器來執行其相依性。</span><span class="sxs-lookup"><span data-stu-id="541bc-706">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="541bc-707">共用實例，有限的存留期</span><span class="sxs-lookup"><span data-stu-id="541bc-707">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="541bc-708">**案例**</span><span class="sxs-lookup"><span data-stu-id="541bc-708">**Scenario**</span></span>

<span data-ttu-id="541bc-709">應用程式需要 <xref:System.IDisposable> 多個服務之間的共用實例，但 <xref:System.IDisposable> 應具有有限的存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-709">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="541bc-710">**方案**</span><span class="sxs-lookup"><span data-stu-id="541bc-710">**Solution**</span></span>

<span data-ttu-id="541bc-711">註冊具有限定範圍存留期的實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-711">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="541bc-712">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 來啟動並建立新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 。</span><span class="sxs-lookup"><span data-stu-id="541bc-712">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="541bc-713">使用範圍的 <xref:System.IServiceProvider> 來取得所需的服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-713">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="541bc-714">在存留期結束時處置範圍。</span><span class="sxs-lookup"><span data-stu-id="541bc-714">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="541bc-715">一般準則</span><span class="sxs-lookup"><span data-stu-id="541bc-715">General Guidelines</span></span>

* <span data-ttu-id="541bc-716">不要向 <xref:System.IDisposable> 暫時性範圍註冊實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-716">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="541bc-717">請改用 factory 模式。</span><span class="sxs-lookup"><span data-stu-id="541bc-717">Use the factory pattern instead.</span></span>
* <span data-ttu-id="541bc-718">請勿解析 <xref:System.IDisposable> 根範圍中的暫時性或範圍實例。</span><span class="sxs-lookup"><span data-stu-id="541bc-718">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="541bc-719">唯一的例外狀況是當應用程式建立/重新建立和處置時 <xref:System.IServiceProvider> ，這不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="541bc-719">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="541bc-720">透過 DI 接收相依性 <xref:System.IDisposable> 並不會要求接收者 <xref:System.IDisposable> 自行執行。</span><span class="sxs-lookup"><span data-stu-id="541bc-720">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="541bc-721">相依性的接收者不 <xref:System.IDisposable> 應呼叫 <xref:System.IDisposable.Dispose%2A> 該相依性上的。</span><span class="sxs-lookup"><span data-stu-id="541bc-721">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="541bc-722">範圍應該用來控制服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="541bc-722">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="541bc-723">範圍不是階層式，而且範圍之間沒有特殊連接。</span><span class="sxs-lookup"><span data-stu-id="541bc-723">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="541bc-724">預設服務容器取代</span><span class="sxs-lookup"><span data-stu-id="541bc-724">Default service container replacement</span></span>

<span data-ttu-id="541bc-725">內建的服務容器是設計用來滿足架構和大部分取用者應用程式的需求。</span><span class="sxs-lookup"><span data-stu-id="541bc-725">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="541bc-726">我們建議使用內建容器，除非您需要內建容器不支援的特定功能，例如：</span><span class="sxs-lookup"><span data-stu-id="541bc-726">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="541bc-727">屬性插入</span><span class="sxs-lookup"><span data-stu-id="541bc-727">Property injection</span></span>
* <span data-ttu-id="541bc-728">根據名稱插入</span><span class="sxs-lookup"><span data-stu-id="541bc-728">Injection based on name</span></span>
* <span data-ttu-id="541bc-729">子容器</span><span class="sxs-lookup"><span data-stu-id="541bc-729">Child containers</span></span>
* <span data-ttu-id="541bc-730">自訂生命週期管理</span><span class="sxs-lookup"><span data-stu-id="541bc-730">Custom lifetime management</span></span>
* <span data-ttu-id="541bc-731">`Func<T>` 支援延遲初始設定</span><span class="sxs-lookup"><span data-stu-id="541bc-731">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="541bc-732">以慣例為基礎的註冊</span><span class="sxs-lookup"><span data-stu-id="541bc-732">Convention-based registration</span></span>

<span data-ttu-id="541bc-733">下列協力廠商容器可以與 ASP.NET Core 應用程式搭配使用：</span><span class="sxs-lookup"><span data-stu-id="541bc-733">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="541bc-734">Autofac</span><span class="sxs-lookup"><span data-stu-id="541bc-734">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="541bc-735">DryIoc</span><span class="sxs-lookup"><span data-stu-id="541bc-735">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="541bc-736">Grace</span><span class="sxs-lookup"><span data-stu-id="541bc-736">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="541bc-737">LightInject</span><span class="sxs-lookup"><span data-stu-id="541bc-737">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="541bc-738">拉馬爾</span><span class="sxs-lookup"><span data-stu-id="541bc-738">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="541bc-739">Stashbox</span><span class="sxs-lookup"><span data-stu-id="541bc-739">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="541bc-740">Unity</span><span class="sxs-lookup"><span data-stu-id="541bc-740">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="541bc-741">執行緒安全</span><span class="sxs-lookup"><span data-stu-id="541bc-741">Thread safety</span></span>

<span data-ttu-id="541bc-742">建立具備執行緒安全性的 singleton 服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-742">Create thread-safe singleton services.</span></span> <span data-ttu-id="541bc-743">如果 singleton 服務相依於暫時性服務，暫時性服務可能也需要具備執行緒安全性，取決於 singleton 如何使用它。</span><span class="sxs-lookup"><span data-stu-id="541bc-743">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="541bc-744">單一服務的 factory 方法（例如 AddSingleton (IServiceCollection 的第二個引數[ \<TService> ），Func \<IServiceProvider,TService>) ](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)不需要是安全線程。</span><span class="sxs-lookup"><span data-stu-id="541bc-744">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="541bc-745">就像型別 (`static`) 建構函式一樣，它一定會被單一執行緒呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="541bc-745">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="541bc-746">建議</span><span class="sxs-lookup"><span data-stu-id="541bc-746">Recommendations</span></span>

* <span data-ttu-id="541bc-747">不支援以 `async/await` 與 `Task` 為基礎的服務解析。</span><span class="sxs-lookup"><span data-stu-id="541bc-747">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="541bc-748">C# 不支援非同步建構函式，因此建議的模式是以同步方式解析服務後使用非同步方法。</span><span class="sxs-lookup"><span data-stu-id="541bc-748">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="541bc-749">避免直接在服務容器中儲存資料與設定。</span><span class="sxs-lookup"><span data-stu-id="541bc-749">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="541bc-750">例如，使用者的購物車通常不應該新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="541bc-750">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="541bc-751">組態應該使用[選項模式](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="541bc-751">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="541bc-752">同樣地，請避免只存在以允許存取某個其他物件的「資料持有者」物件。</span><span class="sxs-lookup"><span data-stu-id="541bc-752">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="541bc-753">最好是透過 DI 要求實際項目。</span><span class="sxs-lookup"><span data-stu-id="541bc-753">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="541bc-754">避免靜態存取服務。</span><span class="sxs-lookup"><span data-stu-id="541bc-754">Avoid static access to services.</span></span> <span data-ttu-id="541bc-755">例如，避免以靜態方式輸入[IApplicationBuilder microsoft.visualbasic.applicationservices.cantstartsingleinstanceexception](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ，以供其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="541bc-755">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="541bc-756">避免使用*服務定位器模式*，這會混用控制策略的[反轉](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)。</span><span class="sxs-lookup"><span data-stu-id="541bc-756">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="541bc-757"><xref:System.IServiceProvider.GetService*>當您可以改用 DI 時，請勿叫用來取得服務實例：</span><span class="sxs-lookup"><span data-stu-id="541bc-757">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="541bc-758">**不正確：**</span><span class="sxs-lookup"><span data-stu-id="541bc-758">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="541bc-759">**正確**：</span><span class="sxs-lookup"><span data-stu-id="541bc-759">**Correct**:</span></span>

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

* <span data-ttu-id="541bc-760">避免在使用時，插入用來解析相依性的 factory <xref:System.IServiceProvider.GetService*> 。</span><span class="sxs-lookup"><span data-stu-id="541bc-760">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="541bc-761">避免以靜態方式存取 `HttpContext` (例如 [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="541bc-761">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="541bc-762">就像所有的建議集，您可能會遇到需要忽略建議的情況。</span><span class="sxs-lookup"><span data-stu-id="541bc-762">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="541bc-763">例外狀況很罕見，大部分是在架構本身內的特殊案例。</span><span class="sxs-lookup"><span data-stu-id="541bc-763">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="541bc-764">DI 是靜態/全域物件存取模式的「替代」\*\* 選項。</span><span class="sxs-lookup"><span data-stu-id="541bc-764">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="541bc-765">如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。</span><span class="sxs-lookup"><span data-stu-id="541bc-765">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="541bc-766">其他資源</span><span class="sxs-lookup"><span data-stu-id="541bc-766">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="541bc-767">在 ASP.NET Core 中處置 IDisposables 的四種方式</span><span class="sxs-lookup"><span data-stu-id="541bc-767">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="541bc-768">在 ASP.NET Core 使用 Dependency Injection 撰寫簡潔的程式碼 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="541bc-768">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="541bc-769">明確相依性準則</span><span class="sxs-lookup"><span data-stu-id="541bc-769">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="541bc-770">[逆轉控制容器和相依性插入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="541bc-770">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* <span data-ttu-id="541bc-771">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (如何使用 ASP.NET Core DI 中的多個介面註冊服務)</span><span class="sxs-lookup"><span data-stu-id="541bc-771">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
