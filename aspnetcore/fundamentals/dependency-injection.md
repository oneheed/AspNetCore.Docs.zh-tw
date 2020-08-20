---
title: .NET Core 中的相依性插入
author: rick-anderson
description: 了解 ASP.NET Core 如何實作相依性插入以及如何使用它。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ececea3c7cc2f0cdf39bbfd29feec061f9bc6764
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628790"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="c6ceb-103">.NET Core 中的相依性插入</span><span class="sxs-lookup"><span data-stu-id="c6ceb-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c6ceb-104">[Kirk Larkin](https://twitter.com/serpent5)、 [Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="c6ceb-105">ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="c6ceb-106">如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="c6ceb-107">如需有關選項之相依性插入的詳細資訊，請參閱 <xref:fundamentals/configuration/options> 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="c6ceb-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="c6ceb-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="c6ceb-109">相依性插入概觀</span><span class="sxs-lookup"><span data-stu-id="c6ceb-109">Overview of dependency injection</span></span>

<span data-ttu-id="c6ceb-110">「相依性」\*\* 是另一個物件所需的任何物件。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="c6ceb-111">檢查具有 `WriteMessage` 方法的下列 `MyDependency` 物件，應用程式中的其他類別相依於此物件：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="c6ceb-112">您可以建立 `MyDependency` 類別的執行個體，讓 `WriteMessage` 方法可供類別使用。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="c6ceb-113">`MyDependency` 類別是 `IndexModel` 類別的相依性：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="c6ceb-114">該類別會建立 `MyDependency` 執行個體並直接相依於該執行個體。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="c6ceb-115">程式碼相依性（如上述範例）有問題，因此應該避免因下列原因：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="c6ceb-116">若要將 `MyDependency` 取代為不同的實作，必須修改該類別。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="c6ceb-117">若 `MyDependency` 有相依性，那些相依性必須由該類別設定。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="c6ceb-118">在具有多個相依於 `MyDependency` 之多個類別的大型專案中，設定程式碼在不同的應用程式之間會變得鬆散。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="c6ceb-119">此實作難以進行單元測試。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="c6ceb-120">應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="c6ceb-121">相依性插入可透過下列方式解決這些問題：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="c6ceb-122">使用介面或基底類別來將相依性資訊抽象化。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="c6ceb-123">在服務容器中註冊相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="c6ceb-124">ASP.NET Core 提供內建服務容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="c6ceb-125">服務會在應用程式的 `Startup.ConfigureServices` 方法中註冊。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="c6ceb-126">將服務「插入」\*\* 到服務使用位置之類別的建構函式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="c6ceb-127">架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="c6ceb-128">在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 介面定義了服務提供給應用程式的方法：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="c6ceb-129">這個介面是由具象型別 `MyDependency` 所實作：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="c6ceb-130">在範例應用程式中，`IMyDependency` 服務是使用具象型別 `MyDependency` 所註冊。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="c6ceb-131"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>方法會使用範圍存留期（單一要求的存留期）來註冊服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="c6ceb-132">將在此主題稍後將說明[服務存留期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="c6ceb-133">在範例應用程式中，會要求 `IMyDependency` 執行個體並使用它來呼叫服務的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="c6ceb-134">使用 DI 模式：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-134">With the DI pattern:</span></span>

* <span data-ttu-id="c6ceb-135">控制器不會使用具象類型 `MyDependency` ，而只會使用介面 `IMyDependency` 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="c6ceb-136">這可讓您輕鬆地變更控制器所使用的執行，而不需要修改控制器。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="c6ceb-137">控制器不會建立的實例，而是 `MyDependency` 由 DI 容器建立。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="c6ceb-138">您 `IMyDependency` 可以使用內建的記錄 API 來改善介面的實作為：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="c6ceb-139">更新的 `ConfigureServices` 方法會註冊新的 `IMyDependency` 實作為：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="c6ceb-140">`MyDependency2`<xref:Microsoft.Extensions.Logging.ILogger`1>在函數中要求。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="c6ceb-141">以鏈結方式使用相依性插入並非不尋常。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="c6ceb-142">每個要求的相依性接著會要求其自己的相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="c6ceb-143">容器會解決圖形中的相依性，並傳回完全解析的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="c6ceb-144">必須先解析的相依性集合組通常稱為「相依性樹狀結構」**、「相依性圖形」** 或「物件圖形」\*\*。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="c6ceb-145">`ILogger<TCategoryName>` 是 [架構提供的服務](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="c6ceb-146">容器會 `ILogger<TCategoryName>` 利用 [ (泛型) 開放式](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)型別來解析，因此不需要註冊每個 [ (泛型) 結構類型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="c6ceb-147">在相依性插入術語中，服務：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="c6ceb-148">通常是將服務提供給應用程式中其他程式碼（例如服務）的物件 `IMyDependency` 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="c6ceb-149">與 web 服務無關，但服務可能會使用 web 服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="c6ceb-150">架構會提供健全的 [記錄](xref:fundamentals/logging/index) 系統。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="c6ceb-151">這 `IMyDependency` 是為了示範基本 DI 而撰寫的，而不是用來執行記錄。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="c6ceb-152">大部分的應用程式都不需要撰寫記錄器。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="c6ceb-153">下列程式碼示範如何使用預設記錄，這不需要在中註冊任何服務 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="c6ceb-154">使用上述程式碼不需要更新， `ConfigureServices` 因為 [記錄](xref:fundamentals/logging/index) 是由架構所提供：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="c6ceb-155">插入啟動的服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-155">Services injected into Startup</span></span>

<span data-ttu-id="c6ceb-156">`Startup`使用泛型主機 () 時，只可將下列服務類型插入至函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="c6ceb-157">服務可以插入至 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="c6ceb-158">如需詳細資訊，請參閱 <xref:fundamentals/startup> 和 [Access Configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="c6ceb-159">使用擴充方法註冊其他服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-159">Register additional services with extension methods</span></span>

<span data-ttu-id="c6ceb-160">當服務集合擴充方法可用來註冊服務時：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="c6ceb-161">慣例是使用單一 `Add{SERVICE_NAME}` 擴充方法來註冊該服務所需的所有服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="c6ceb-162">也會註冊相依的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-162">The dependent services are also registered.</span></span>

<span data-ttu-id="c6ceb-163">下列程式碼是由 Razor 使用個別使用者帳戶的 Pages 範本所產生，並示範如何使用擴充方法和來將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="c6ceb-164">如需詳細資訊，請參閱 <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> 和 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="c6ceb-165">如需撰寫擴充方法以註冊服務的指示，請參閱 [結合服務集合](#csc) 的一節。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="c6ceb-166">服務存留期</span><span class="sxs-lookup"><span data-stu-id="c6ceb-166">Service lifetimes</span></span>

<span data-ttu-id="c6ceb-167">為每個已註冊的服務選擇適當的存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="c6ceb-168">ASP.NET Core 服務可以使用下列存留期進行設定：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="c6ceb-169">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-169">Transient</span></span>

<span data-ttu-id="c6ceb-170">每次從服務容器要求暫時性存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 時都會建立它們。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="c6ceb-171">此存留期最適合用於輕量型的無狀態服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="c6ceb-172">在處理要求的應用程式中，會在要求結束時處置暫時性服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="c6ceb-173">具範圍</span><span class="sxs-lookup"><span data-stu-id="c6ceb-173">Scoped</span></span>

<span data-ttu-id="c6ceb-174">具範圍存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 會在每次用戶端要求 (連線) 時建立一次。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="c6ceb-175">使用 Entity Framework Core 時， <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 擴充方法預設會註冊 `DbContext` 具有範圍存留期的類型。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="c6ceb-176">在處理要求的應用程式中，會在要求結束時處置範圍服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="c6ceb-177">使用中介軟體中的範圍服務搭配下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="c6ceb-178">將服務插入 `Invoke` 或方法中 `InvokeAsync` 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="c6ceb-179">由函式 [插入](xref:mvc/controllers/dependency-injection#constructor-injection) 的插入會在執行時間擲回例外狀況，因為它會強制服務的行為就像 singleton 一樣。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="c6ceb-180">[存留期和註冊選項](#lifetime-and-registration-options)中的範例會使用 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="c6ceb-181">以[Factory 為基礎的中介軟體](<xref:fundamentals/middleware/extensibility>)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="c6ceb-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> 擴充方法會檢查中介軟體的已註冊類型是否實作 <xref:Microsoft.AspNetCore.Http.IMiddleware>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="c6ceb-183">如果是，系統會使用容器中已註冊的 <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> 執行個體來解析 <xref:Microsoft.AspNetCore.Http.IMiddleware> 實作，而不是使用以慣例為基礎的中介軟體啟用邏輯。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="c6ceb-184">中介軟體會註冊為應用程式服務容器中的範圍服務或暫時性服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="c6ceb-185">如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="c6ceb-186">請勿 ***從 singleton 解析已*** 設定範圍的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c6ceb-187">處理後續要求時，它可能會導致服務有不正確的狀態。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="c6ceb-188">您可以：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-188">It's fine to:</span></span>

* <span data-ttu-id="c6ceb-189">從範圍或暫時性服務解析單一服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="c6ceb-190">從另一個範圍或暫時性服務解析已設定範圍的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="c6ceb-191">根據預設，在開發環境中，從較長存留期的另一個服務解析服務，會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="c6ceb-192">如需詳細資訊，請參閱[範圍驗證](#sv)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="c6ceb-193">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-193">Singleton</span></span>

<span data-ttu-id="c6ceb-194">系統會建立單一存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="c6ceb-195">第一次要求時。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-195">The first time they're requested.</span></span>
* <span data-ttu-id="c6ceb-196">由開發人員直接將實實例提供給容器。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="c6ceb-197">這種方法很少需要。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-197">This approach is rarely needed.</span></span>

<span data-ttu-id="c6ceb-198">每個後續要求都會使用相同的執行個體。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="c6ceb-199">如果應用程式需要單一行為，請允許服務容器管理服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="c6ceb-200">請勿實行 singleton 設計模式，並提供程式碼來處置 singleton。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="c6ceb-201">服務絕對不能由從容器解析服務的程式碼處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="c6ceb-202">如果類型或 factory 註冊為 singleton，則容器會自動處置 singleton。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="c6ceb-203">單一服務必須是安全線程，而且通常用於無狀態服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="c6ceb-204">在處理要求的應用程式中，會在應用程式關閉時處置單一服務 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="c6ceb-205">因為在關閉應用程式之前不會釋出記憶體，所以必須考慮搭配 singleton 使用記憶體。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="c6ceb-206">請勿 ***從 singleton 解析已*** 設定範圍的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c6ceb-207">處理後續要求時，它可能會導致服務有不正確的狀態。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="c6ceb-208">從範圍或暫時性服務解析單一服務是很好的。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="c6ceb-209">服務註冊方法</span><span class="sxs-lookup"><span data-stu-id="c6ceb-209">Service registration methods</span></span>

<span data-ttu-id="c6ceb-210">服務註冊擴充方法提供在特定案例中很有用的多載。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="c6ceb-211">方法</span><span class="sxs-lookup"><span data-stu-id="c6ceb-211">Method</span></span> | <span data-ttu-id="c6ceb-212">自動</span><span class="sxs-lookup"><span data-stu-id="c6ceb-212">Automatic</span></span><br><span data-ttu-id="c6ceb-213">物件 (object)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-213">object</span></span><br><span data-ttu-id="c6ceb-214">處置</span><span class="sxs-lookup"><span data-stu-id="c6ceb-214">disposal</span></span> | <span data-ttu-id="c6ceb-215">多個</span><span class="sxs-lookup"><span data-stu-id="c6ceb-215">Multiple</span></span><br><span data-ttu-id="c6ceb-216">實作</span><span class="sxs-lookup"><span data-stu-id="c6ceb-216">implementations</span></span> | <span data-ttu-id="c6ceb-217">傳遞引數</span><span class="sxs-lookup"><span data-stu-id="c6ceb-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="c6ceb-218">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="c6ceb-219">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-219">Yes</span></span> | <span data-ttu-id="c6ceb-220">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-220">Yes</span></span> | <span data-ttu-id="c6ceb-221">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="c6ceb-222">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="c6ceb-223">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-223">Yes</span></span> | <span data-ttu-id="c6ceb-224">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-224">Yes</span></span> | <span data-ttu-id="c6ceb-225">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="c6ceb-226">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="c6ceb-227">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-227">Yes</span></span> | <span data-ttu-id="c6ceb-228">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-228">No</span></span> | <span data-ttu-id="c6ceb-229">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="c6ceb-230">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="c6ceb-231">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-231">No</span></span> | <span data-ttu-id="c6ceb-232">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-232">Yes</span></span> | <span data-ttu-id="c6ceb-233">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="c6ceb-234">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="c6ceb-235">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-235">No</span></span> | <span data-ttu-id="c6ceb-236">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-236">No</span></span> | <span data-ttu-id="c6ceb-237">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-237">Yes</span></span> |

<span data-ttu-id="c6ceb-238">如需類型處置的詳細資訊，請參閱[＜服務處置＞](#disposal-of-services)一節。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="c6ceb-239">多個實作的常見案例是[模擬測試類型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="c6ceb-240">`TryAdd{LIFETIME}` 如果尚未註冊任何已註冊的方法，方法會註冊服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="c6ceb-241">在下列範例中，第一行會為 `IMyDependency` 註冊 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="c6ceb-242">第二行則沒有任何作用，因為 `IMyDependency` 已具有註冊的實作：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="c6ceb-243">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="c6ceb-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="c6ceb-244">如果尚未有*相同類型*的實作， [TryAddEnumerable (ServiceDescriptor) ](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)方法會註冊服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="c6ceb-245">多個服務會透過 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="c6ceb-246">註冊服務時，如果尚未加入相同型別的實例，開發人員應該加入實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="c6ceb-247">一般而言，程式庫作者會使用 `TryAddEnumerable` 來避免在容器中註冊多個執行複本。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="c6ceb-248">在下列範例中，第一行會為 `IMyDep1` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="c6ceb-249">第二行會為 `IMyDep2` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="c6ceb-250">第三行則沒有任何作用，因為 `IMyDep1` 已具有 `MyDep` 的已註冊實作：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="c6ceb-251">除非註冊相同類型的多個執行，否則服務註冊通常會獨立排序。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="c6ceb-252">`IServiceCollection` 是的集合 <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="c6ceb-253">下列程式碼示範如何使用函式來加入服務：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="c6ceb-254">這些 `Add{LIFETIME}` 方法會使用相同的方法。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="c6ceb-255">例如，請參閱 [AddScoped 的原始程式碼](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="c6ceb-256">建構函式插入行為</span><span class="sxs-lookup"><span data-stu-id="c6ceb-256">Constructor injection behavior</span></span>

<span data-ttu-id="c6ceb-257">服務可以透過兩個機制來解析：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="c6ceb-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="c6ceb-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="c6ceb-259">在相依性插入容器中建立沒有服務註冊的物件。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="c6ceb-260">與架構功能搭配使用，例如標籤協助 [程式、MVC](xref:mvc/views/tag-helpers/intro)控制器和 [模型](xref:mvc/models/model-binding)系結器。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="c6ceb-261">建構函式可以接受不是由相依性插入提供的引數，但引數必須指派預設值。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="c6ceb-262">當或解決服務時 `IServiceProvider` `ActivatorUtilities` ，必須使用*公用*的函式來[插入](xref:mvc/controllers/dependency-injection#constructor-injection)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="c6ceb-263">當服務解析時，函式 `ActivatorUtilities` [插入](xref:mvc/controllers/dependency-injection#constructor-injection) 會要求只能有一個適用的函式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="c6ceb-264">支援建構函式多載，但只能有一個多載存在，其引數可以藉由相依性插入而完成。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="c6ceb-265">Entity Framework 內容</span><span class="sxs-lookup"><span data-stu-id="c6ceb-265">Entity Framework contexts</span></span>

<span data-ttu-id="c6ceb-266">因為一般會將 Web 應用程式資料庫作業範圍設定為用戶端要求，所以通常會使用[具範圍存留期](#service-lifetimes)將 Entity Framework 內容新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="c6ceb-267">如果在註冊資料庫內容時， [AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)多載未指定存留期，則預設存留期會限定範圍。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="c6ceb-268">指定存留期的服務不應該使用存留期比服務還短的資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="c6ceb-269">留期和註冊選項</span><span class="sxs-lookup"><span data-stu-id="c6ceb-269">Lifetime and registration options</span></span>

<span data-ttu-id="c6ceb-270">若要示範存留期和註冊選項之間的差異，請考慮使用下列介面，將工作表示為具有識別碼的作業 `OperationId` 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="c6ceb-271">根據作業的服務存留期如何針對下列介面設定，容器會在要求類別時提供相同或不同的服務實例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="c6ceb-272">介面是在 `Operation` 類別中實作。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="c6ceb-273">如果未提供 GUID，則此函式會 `Operation` 產生最後4個字元：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="c6ceb-274">在 `Startup.ConfigureServices` 中，每個類型都會根據其具名存留期新增至容器：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="c6ceb-275">範例應用程式會示範要求內和之間的物件存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="c6ceb-276">範例應用程式 `IndexModel` 和中介軟體會要求每種 `IOperation` 類型，並記錄 `OperationId` ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="c6ceb-277">中介軟體類似于 `IndexModel` 並解析相同的服務：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="c6ceb-278">您必須在方法中解析已設定範圍的服務 `InvokeAsync` ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="c6ceb-279">記錄器輸出顯示：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-279">The logger output shows:</span></span>

* <span data-ttu-id="c6ceb-280">「暫時性」\*\* 物件一律不同。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-280">*Transient* objects are always different.</span></span> <span data-ttu-id="c6ceb-281">在 `OperationId` 和中介軟體中，暫時性值是不同的 `IndexModel` 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="c6ceb-282">*範圍* 物件在每個要求中都相同，但在每個要求中都是不同的。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="c6ceb-283">每個要求的*單一*物件都相同。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="c6ceb-284">若要減少記錄輸出，請在檔案的 *appsettings.Development.js* 中設定 "記錄： LogLevel： Microsoft： Error"：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="c6ceb-285">從主要呼叫服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-285">Call services from main</span></span>

<span data-ttu-id="c6ceb-286">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>，以解析應用程式範圍中的範圍服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="c6ceb-287">在啟動時存取範圍服務以執行初始化工作時，此方法很有用：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

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

<span data-ttu-id="c6ceb-288">上述程式碼是在頁面教學課程中 [新增種子初始化運算式](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) Razor 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="c6ceb-289">下列範例示範如何在 `Program.Main` 中取得 `IMyDependency`：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="c6ceb-290">範圍驗證</span><span class="sxs-lookup"><span data-stu-id="c6ceb-290">Scope validation</span></span>

<span data-ttu-id="c6ceb-291">當應用程式在 [開發環境](xref:fundamentals/environments) 中執行，並呼叫 [>createdefaultbuilder](xref:fundamentals/host/generic-host#default-builder-settings) 來建立主機時，預設服務提供者會執行檢查，以確認：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="c6ceb-292">範圍服務不是直接或間接由開機服務提供者解析。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="c6ceb-293">範圍服務不是直接或間接插入至單一服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="c6ceb-294">暫時性服務不是直接或間接插入至 singleton 或範圍服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="c6ceb-295">根服務提供者會在呼叫 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 時建立。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="c6ceb-296">當提供者啟動應用程式時，根服務提供者的存留期會對應到應用程式的存留期，並在應用程式關閉時處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="c6ceb-297">範圍服務會由建立這些服務的容器處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="c6ceb-298">如果在根容器中建立範圍服務，服務的存留期會有效地升階為 singleton，因為它只會在應用程式關閉時由根容器處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="c6ceb-299">當呼叫 `BuildServiceProvider` 時，驗證服務範圍會攔截到這些情況。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="c6ceb-300">如需詳細資訊，請參閱[範圍驗證](xref:fundamentals/host/web-host#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="c6ceb-301">要求服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-301">Request Services</span></span>

<span data-ttu-id="c6ceb-302">來自 `HttpContext`，在 ASP.NET Core 要求內提供的服務是透過 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公開。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="c6ceb-303">要求服務代表您在應用程式中設定和要求的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="c6ceb-304">當物件指定相依性時，這些會由在 `RequestServices` 中找到的類型來滿足，而非 `ApplicationServices`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="c6ceb-305">一般而言，應用程式不應該直接使用這些屬性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="c6ceb-306">相反地，請透過類別的函式要求類別所需的類型，並允許架構插入相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="c6ceb-307">這會產生容易測試的類別。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="c6ceb-308">ASP.NET Core 會建立每個要求的範圍，並 `RequestServices` 公開範圍服務提供者。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="c6ceb-309">只要要求為作用中狀態，所有範圍的服務都有效。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="c6ceb-310">偏好要求相依性作為建構函式參數，而不要存取 `RequestServices` 集合。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="c6ceb-311">針對相依性插入設計服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-311">Design services for dependency injection</span></span>

<span data-ttu-id="c6ceb-312">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-312">Best practices are to:</span></span>

* <span data-ttu-id="c6ceb-313">設計服務以使用相依性插入來取得其相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="c6ceb-314">避免具狀態、靜態類別和成員。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="c6ceb-315">請改為使用單一服務來設計應用程式，以避免建立全域狀態。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="c6ceb-316">避免直接在服務內具現化相依類別。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="c6ceb-317">直接具現化會將程式碼耦合到特定實作。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="c6ceb-318">讓應用程式類別維持在小型、情況良好且可輕鬆測試的狀態。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="c6ceb-319">如果類別有太多插入的相依性，這通常是因為類別具有太多責任，而且違反了 [單一責任原則 (SRP) ](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="c6ceb-320">將類別負責的某些部分移到新的類別，以嘗試重構類別。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="c6ceb-321">請記住， Razor 頁面頁面模型類別和 MVC 控制器類別應該專注于 UI 的考慮。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="c6ceb-322">處置服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-322">Disposal of services</span></span>

<span data-ttu-id="c6ceb-323">容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="c6ceb-324">服務絕對不能由從容器解析服務的任何程式碼處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="c6ceb-325">如果類型或 factory 註冊為 singleton，則容器會處置 singleton。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="c6ceb-326">在下列範例中，服務是由服務容器建立並自動處置：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="c6ceb-327">在每次重新整理 [索引] 頁面之後，debug 主控台會顯示下列輸出：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="c6ceb-328">服務容器未建立的服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="c6ceb-329">請考慮下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="c6ceb-330">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-330">In the preceding code:</span></span>

* <span data-ttu-id="c6ceb-331">服務實例不是由服務容器所建立。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="c6ceb-332">架構不知道預期的服務存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="c6ceb-333">架構不會自動處置服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="c6ceb-334">如果未在開發人員程式碼中明確處置服務，它們會保存到應用程式關閉為止。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="c6ceb-335">暫時性和共用實例的 IDisposable 指引</span><span class="sxs-lookup"><span data-stu-id="c6ceb-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="c6ceb-336">暫時性、有限存留期</span><span class="sxs-lookup"><span data-stu-id="c6ceb-336">Transient, limited lifetime</span></span>

<span data-ttu-id="c6ceb-337">**案例**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-337">**Scenario**</span></span>

<span data-ttu-id="c6ceb-338">應用程式需要在 <xref:System.IDisposable> 下列任一案例中具有暫時性存留期的實例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="c6ceb-339"> (根容器) 的根範圍中解析實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="c6ceb-340">實例應該在範圍結束之前處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="c6ceb-341">**方案**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-341">**Solution**</span></span>

<span data-ttu-id="c6ceb-342">使用 factory 模式，在父範圍之外建立實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="c6ceb-343">在這種情況下，應用程式通常會有 `Create` 方法可直接呼叫最終型別的函式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="c6ceb-344">如果最終類型有其他相依性，factory 可以：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="c6ceb-345"><xref:System.IServiceProvider>在其函式中接收。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="c6ceb-346">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 可在容器外部具現化實例，同時使用容器作為其相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="c6ceb-347">共用實例，有限存留期</span><span class="sxs-lookup"><span data-stu-id="c6ceb-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="c6ceb-348">**案例**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-348">**Scenario**</span></span>

<span data-ttu-id="c6ceb-349">應用程式需要 <xref:System.IDisposable> 跨多個服務的共用實例，但是 <xref:System.IDisposable> 存留期應受限。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="c6ceb-350">**方案**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-350">**Solution**</span></span>

<span data-ttu-id="c6ceb-351">註冊具有範圍存留期的實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="c6ceb-352">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 啟動並建立新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="c6ceb-353">使用範圍 <xref:System.IServiceProvider> 來取得所需的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="c6ceb-354">在存留期結束時處置範圍。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="c6ceb-355">一般 IDisposable 指導方針</span><span class="sxs-lookup"><span data-stu-id="c6ceb-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="c6ceb-356">請勿向 <xref:System.IDisposable> 暫時性範圍登錄實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="c6ceb-357">請改用 factory 模式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="c6ceb-358">請勿在根範圍中解析暫時性或限定範圍 <xref:System.IDisposable> 的實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="c6ceb-359">唯一的一般例外狀況是當應用程式建立/重新建立並處置時 <xref:System.IServiceProvider> ，這不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="c6ceb-360">透過 DI 接收相依性 <xref:System.IDisposable> 不需要接收者 <xref:System.IDisposable> 自行執行。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="c6ceb-361">相依性的接收者不 <xref:System.IDisposable> 應呼叫 <xref:System.IDisposable.Dispose%2A> 該相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="c6ceb-362">範圍應該用來控制服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="c6ceb-363">範圍不是階層式，而且範圍之間沒有特殊連接。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="c6ceb-364">預設服務容器取代</span><span class="sxs-lookup"><span data-stu-id="c6ceb-364">Default service container replacement</span></span>

<span data-ttu-id="c6ceb-365">內建的服務容器是設計來滿足架構和大部分消費者應用程式的需求。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="c6ceb-366">除非您需要內建容器不支援的特定功能，否則建議使用內建容器，例如：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="c6ceb-367">屬性插入</span><span class="sxs-lookup"><span data-stu-id="c6ceb-367">Property injection</span></span>
* <span data-ttu-id="c6ceb-368">根據名稱插入</span><span class="sxs-lookup"><span data-stu-id="c6ceb-368">Injection based on name</span></span>
* <span data-ttu-id="c6ceb-369">子容器</span><span class="sxs-lookup"><span data-stu-id="c6ceb-369">Child containers</span></span>
* <span data-ttu-id="c6ceb-370">自訂生命週期管理</span><span class="sxs-lookup"><span data-stu-id="c6ceb-370">Custom lifetime management</span></span>
* <span data-ttu-id="c6ceb-371">`Func<T>` 支援延遲初始設定</span><span class="sxs-lookup"><span data-stu-id="c6ceb-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="c6ceb-372">以慣例為基礎的註冊</span><span class="sxs-lookup"><span data-stu-id="c6ceb-372">Convention-based registration</span></span>

<span data-ttu-id="c6ceb-373">下列協力廠商容器可搭配 ASP.NET Core apps 使用：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="c6ceb-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="c6ceb-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="c6ceb-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="c6ceb-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="c6ceb-376">Grace</span><span class="sxs-lookup"><span data-stu-id="c6ceb-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="c6ceb-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="c6ceb-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="c6ceb-378">拉馬爾</span><span class="sxs-lookup"><span data-stu-id="c6ceb-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="c6ceb-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="c6ceb-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="c6ceb-380">Unity</span><span class="sxs-lookup"><span data-stu-id="c6ceb-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="c6ceb-381">執行緒安全</span><span class="sxs-lookup"><span data-stu-id="c6ceb-381">Thread safety</span></span>

<span data-ttu-id="c6ceb-382">建立具備執行緒安全性的 singleton 服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="c6ceb-383">如果 singleton 服務相依於暫時性服務，暫時性服務可能也需要具備執行緒安全性，取決於 singleton 如何使用它。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="c6ceb-384">單一服務的 factory 方法（例如 >addsingleton 的第二個自 [變數 \<TService> (IServiceCollection、Func \<IServiceProvider,TService>) ](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)）不需要是安全線程。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="c6ceb-385">就像型別 (`static`) 建構函式一樣，它一定會被單一執行緒呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="c6ceb-386">建議</span><span class="sxs-lookup"><span data-stu-id="c6ceb-386">Recommendations</span></span>

* <span data-ttu-id="c6ceb-387">不支援以 `async/await` 與 `Task` 為基礎的服務解析。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="c6ceb-388">C # 不支援非同步函式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="c6ceb-389">建議的模式是以同步方式解析服務後使用非同步方法。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="c6ceb-390">避免直接在服務容器中儲存資料與設定。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="c6ceb-391">例如，使用者的購物車通常不應該新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="c6ceb-392">組態應該使用[選項模式](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c6ceb-393">同樣地，請避免只存在以允許存取某個其他物件的「資料持有者」物件。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="c6ceb-394">最好是透過 DI 要求實際項目。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="c6ceb-395">避免靜態存取服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-395">Avoid static access to services.</span></span> <span data-ttu-id="c6ceb-396">例如，避免以靜態方式輸入 [IApplicationBuilder >iapplicationbuilder.applicationservices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ，以便在其他地方使用) 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="c6ceb-397">讓 DI factory 保持快速且同步。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="c6ceb-398">避免使用「服務定位器模式」\*\*。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="c6ceb-399">例如，當您可以改用 DI 時，請勿叫用 <xref:System.IServiceProvider.GetService*> 來取得服務執行個體：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="c6ceb-400">**不正確：**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-400">**Incorrect:**</span></span>

    ![不正確的程式碼](dependency-injection/_static/bad.png)

  <span data-ttu-id="c6ceb-402">**正確**：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-402">**Correct**:</span></span>

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
* <span data-ttu-id="c6ceb-403">另一個要避免的服務定位器變化是插入在執行階段解析相依性的處理站。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="c6ceb-404">這兩種做法都會混用[控制反轉](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="c6ceb-405">避免以靜態方式存取 `HttpContext` (例如 [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="c6ceb-406">避免 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> 在中呼叫 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="c6ceb-407">呼叫 `BuildServiceProvider` 通常會在開發人員想要解析中的服務時發生 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="c6ceb-408">例如，請考慮您需要 go get from configuration 的情況 `LoginPath` 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="c6ceb-409">請避免下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-409">Avoid the following code:</span></span>

  ![呼叫 BuildServiceProvider 的錯誤程式碼](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="c6ceb-411">在上圖中，選取下綠色波浪線會 `services.BuildServiceProvider` 顯示下列 ASP0000 警告：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="c6ceb-412">從應用程式程式碼呼叫 ' BuildServiceProvider ' ASP0000 時，會產生另一個要建立的單一服務複本。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="c6ceb-413">請考慮將相依性插入服務作為「設定」參數的替代方案。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="c6ceb-414">呼叫 `BuildServiceProvider` 會建立第二個容器，它可以建立損毀的 singleton，並導致跨多個容器的物件圖形參考。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="c6ceb-415">若要取得正確的方式 `LoginPath` ，請使用選項模式搭配 DI：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="c6ceb-416">容器會捕獲可處置的暫時性服務以供處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="c6ceb-417">如果從最上層容器解析，這可能會導致記憶體流失。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="c6ceb-418">啟用範圍驗證，以確定應用程式沒有範圍服務捕獲 singleton。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="c6ceb-419">如需詳細資訊，請參閱[範圍驗證](#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="c6ceb-420">就像所有的建議集，您可能會遇到需要忽略建議的情況。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="c6ceb-421">例外狀況很罕見，大多是架構本身內的特殊案例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="c6ceb-422">DI 是靜態/全域物件存取模式的「替代」\*\* 選項。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="c6ceb-423">如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="c6ceb-424">DI 中多租使用者的建議模式</span><span class="sxs-lookup"><span data-stu-id="c6ceb-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="c6ceb-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) 提供多租使用者。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="c6ceb-426">如需詳細資訊，請參閱 [Orchard Core 檔](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="c6ceb-427">請參閱中的範例應用程式，以 https://github.com/OrchardCMS/OrchardCore.Samples 取得如何使用 Orchard Core Framework 來建立模組化和多租使用者應用程式的範例，而不需要任何 CMS 特定功能。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c6ceb-428">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-428">Framework-provided services</span></span>

<span data-ttu-id="c6ceb-429">`Startup.ConfigureServices`方法負責定義應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="c6ceb-430">一開始， `IServiceCollection` 提供給的是 `ConfigureServices` 由架構定義的服務，視 [主機的設定方式](xref:fundamentals/index#host)而定。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="c6ceb-431">以 ASP.NET Core 範本為基礎的應用程式有超過250的架構註冊的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="c6ceb-432">下表列出架構註冊服務的小型範例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="c6ceb-433">服務類型</span><span class="sxs-lookup"><span data-stu-id="c6ceb-433">Service Type</span></span> | <span data-ttu-id="c6ceb-434">存留期</span><span class="sxs-lookup"><span data-stu-id="c6ceb-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="c6ceb-435">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="c6ceb-436">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="c6ceb-437">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="c6ceb-438">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="c6ceb-439">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="c6ceb-440">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="c6ceb-441">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="c6ceb-442">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="c6ceb-443">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="c6ceb-444">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="c6ceb-445">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="c6ceb-446">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="c6ceb-447">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="c6ceb-448">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="c6ceb-449">其他資源</span><span class="sxs-lookup"><span data-stu-id="c6ceb-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="c6ceb-450">適用于 DI 應用程式開發的 NDC 會議模式</span><span class="sxs-lookup"><span data-stu-id="c6ceb-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="c6ceb-451">在 ASP.NET Core 中處置 IDisposables 的四種方式</span><span class="sxs-lookup"><span data-stu-id="c6ceb-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="c6ceb-452">在 ASP.NET Core 使用 Dependency Injection 撰寫簡潔的程式碼 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="c6ceb-453">明確相依性準則</span><span class="sxs-lookup"><span data-stu-id="c6ceb-453">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="c6ceb-454">[逆轉控制容器和相依性插入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-454">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* <span data-ttu-id="c6ceb-455">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (如何使用 ASP.NET Core DI 中的多個介面註冊服務)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-455">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c6ceb-456">[Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="c6ceb-457">ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="c6ceb-458">如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="c6ceb-459">[查看或下載範例程式碼](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="c6ceb-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="c6ceb-460">相依性插入概觀</span><span class="sxs-lookup"><span data-stu-id="c6ceb-460">Overview of dependency injection</span></span>

<span data-ttu-id="c6ceb-461">「相依性」\*\* 是另一個物件所需的任何物件。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="c6ceb-462">檢查具有 `WriteMessage` 方法的下列 `MyDependency` 物件，應用程式中的其他類別相依於此物件：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="c6ceb-463">您可以建立 `MyDependency` 類別的執行個體，讓 `WriteMessage` 方法可供類別使用。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="c6ceb-464">`MyDependency` 類別是 `IndexModel` 類別的相依性：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="c6ceb-465">該類別會建立 `MyDependency` 執行個體並直接相依於該執行個體。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="c6ceb-466">程式碼相依性 (例如前一個範例) 有問題，因此基於下列原因應該避免使用：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="c6ceb-467">若要將 `MyDependency` 取代為不同的實作，必須修改該類別。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="c6ceb-468">若 `MyDependency` 有相依性，那些相依性必須由該類別設定。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="c6ceb-469">在具有多個相依於 `MyDependency` 之多個類別的大型專案中，設定程式碼在不同的應用程式之間會變得鬆散。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="c6ceb-470">此實作難以進行單元測試。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="c6ceb-471">應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="c6ceb-472">相依性插入可透過下列方式解決這些問題：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="c6ceb-473">使用介面或基底類別來將相依性資訊抽象化。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="c6ceb-474">在服務容器中註冊相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="c6ceb-475">ASP.NET Core 提供內建服務容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="c6ceb-476">服務會在應用程式的 `Startup.ConfigureServices` 方法中註冊。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="c6ceb-477">將服務「插入」\*\* 到服務使用位置之類別的建構函式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="c6ceb-478">架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="c6ceb-479">在[範例應用程式](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 介面定義了服務提供給應用程式的方法：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="c6ceb-480">這個介面是由具象型別 `MyDependency` 所實作：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="c6ceb-481">`MyDependency` 在其建構函式中會要求 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="c6ceb-482">以鏈結方式使用相依性插入並非不尋常。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="c6ceb-483">每個要求的相依性接著會要求其自己的相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="c6ceb-484">容器會解決圖形中的相依性，並傳回完全解析的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="c6ceb-485">必須先解析的相依性集合組通常稱為「相依性樹狀結構」**、「相依性圖形」** 或「物件圖形」\*\*。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="c6ceb-486">`IMyDependency` 與 `ILogger<TCategoryName>` 必須在服務容器中註冊。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="c6ceb-487">`IMyDependency` 是在 `Startup.ConfigureServices` 中註冊。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c6ceb-488">`ILogger<TCategoryName>` 是由記錄抽象基礎結構所註冊，所以它是預設由架構所註冊的[架構提供的服務](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="c6ceb-489">容器會利用 [(泛型) 開放類型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) 解析 `ILogger<TCategoryName>`，讓您不需註冊每個 [(泛型) 建構的型別](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="c6ceb-490">在範例應用程式中，`IMyDependency` 服務是使用具象型別 `MyDependency` 所註冊。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="c6ceb-491">註冊會將服務的存留期範圍限制為單一要求的存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="c6ceb-492">將在此主題稍後將說明[服務存留期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="c6ceb-493">每個 `services.Add{SERVICE_NAME}` 擴充方法都會新增 (並且可能會設定) 服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="c6ceb-494">例如， `services.AddMvc()` 新增服務 Razor 頁面和 MVC 所需。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="c6ceb-495">建議應用程式遵循此慣例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="c6ceb-496">在 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空間中放置擴充方法，以封裝服務註冊群組。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="c6ceb-497">如果服務的建構函式需要[內建類型](/dotnet/csharp/language-reference/keywords/built-in-types-table)，例如 `string`，可以使用[組態](xref:fundamentals/configuration/index)或[選項模式](xref:fundamentals/configuration/options)插入該類型：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="c6ceb-498">服務執行個體是透過服務使用所在之類別建構函式所要求，而且會指派給私人欄位。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="c6ceb-499">該欄位會視需要用來存取整個類別中的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="c6ceb-500">在範例應用程式中，會要求 `IMyDependency` 執行個體並使用它來呼叫服務的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="c6ceb-501">插入啟動的服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-501">Services injected into Startup</span></span>

<span data-ttu-id="c6ceb-502">`Startup`使用泛型主機 () 時，只可將下列服務類型插入至函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="c6ceb-503">服務可以插入至 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="c6ceb-504">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="c6ceb-505">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-505">Framework-provided services</span></span>

<span data-ttu-id="c6ceb-506">`Startup.ConfigureServices`方法負責定義應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="c6ceb-507">一開始， `IServiceCollection` 提供給的是 `ConfigureServices` 由架構定義的服務，視 [主機的設定方式](xref:fundamentals/index#host)而定。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="c6ceb-508">以 ASP.NET Core 範本為基礎的應用程式並不罕見，因為這是由架構所註冊的數百項服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="c6ceb-509">下表列出架構註冊服務的小型範例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="c6ceb-510">服務類型</span><span class="sxs-lookup"><span data-stu-id="c6ceb-510">Service Type</span></span> | <span data-ttu-id="c6ceb-511">存留期</span><span class="sxs-lookup"><span data-stu-id="c6ceb-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="c6ceb-512">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="c6ceb-513">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="c6ceb-514">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="c6ceb-515">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="c6ceb-516">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="c6ceb-517">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="c6ceb-518">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="c6ceb-519">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="c6ceb-520">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="c6ceb-521">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="c6ceb-522">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="c6ceb-523">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="c6ceb-524">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="c6ceb-525">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="c6ceb-526">使用擴充方法註冊其他服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-526">Register additional services with extension methods</span></span>

<span data-ttu-id="c6ceb-527">當可以使用服務集合擴充方法來註冊服務 (如果需要，也可以註冊其相依服務) 時，慣例是使用單一 `Add{SERVICE_NAME}` 擴充方法來註冊該服務要求的所有服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="c6ceb-528">下列程式碼範例示範如何使用擴充方法[AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)和來將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> ：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="c6ceb-529">如需詳細資訊，請參閱 API 文件中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 類別。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="c6ceb-530">服務存留期</span><span class="sxs-lookup"><span data-stu-id="c6ceb-530">Service lifetimes</span></span>

<span data-ttu-id="c6ceb-531">為每個已註冊的服務選擇適當的存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="c6ceb-532">ASP.NET Core 服務可以使用下列存留期進行設定：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="c6ceb-533">暫時性</span><span class="sxs-lookup"><span data-stu-id="c6ceb-533">Transient</span></span>

<span data-ttu-id="c6ceb-534">每次從服務容器要求暫時性存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 時都會建立它們。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="c6ceb-535">此存留期最適合用於輕量型的無狀態服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="c6ceb-536">在處理要求的應用程式中，會在要求結束時處置暫時性服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="c6ceb-537">具範圍</span><span class="sxs-lookup"><span data-stu-id="c6ceb-537">Scoped</span></span>

<span data-ttu-id="c6ceb-538">具範圍存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 會在每次用戶端要求 (連線) 時建立一次。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="c6ceb-539">在處理要求的應用程式中，會在要求結束時處置範圍服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="c6ceb-540">在中介軟體中使用具範圍服務時，請將該服務插入 `Invoke` 或 `InvokeAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="c6ceb-541">不要插入 via 函式 [插入](xref:mvc/controllers/dependency-injection#constructor-injection) ，因為它會強制服務的行為就像 singleton 一樣。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="c6ceb-542">如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="c6ceb-543">單一</span><span class="sxs-lookup"><span data-stu-id="c6ceb-543">Singleton</span></span>

<span data-ttu-id="c6ceb-544">當第一次收到有關單一資料庫存留期服務的要求時 (或是當執行 `Startup.ConfigureServices` 且隨著服務註冊指定執行個體時)，即會建立單一資料庫存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="c6ceb-545">每個後續要求都會使用相同的執行個體。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="c6ceb-546">若應用程式要求單一資料庫行為，建議您允許服務容器管理服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="c6ceb-547">不要實作單一資料庫設計模式並為使用者提供可用來管理類別中物件存留期的程式碼。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="c6ceb-548">在處理要求的應用程式中，會在應用程式關閉時處置單一服務 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="c6ceb-549">從單一資料庫解析具範圍服務是非常危險的。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="c6ceb-550">處理後續要求時，它可能會導致服務有不正確的狀態。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="c6ceb-551">服務註冊方法</span><span class="sxs-lookup"><span data-stu-id="c6ceb-551">Service registration methods</span></span>

<span data-ttu-id="c6ceb-552">服務註冊擴充方法提供在特定案例中很有用的多載。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="c6ceb-553">方法</span><span class="sxs-lookup"><span data-stu-id="c6ceb-553">Method</span></span> | <span data-ttu-id="c6ceb-554">自動</span><span class="sxs-lookup"><span data-stu-id="c6ceb-554">Automatic</span></span><br><span data-ttu-id="c6ceb-555">物件 (object)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-555">object</span></span><br><span data-ttu-id="c6ceb-556">處置</span><span class="sxs-lookup"><span data-stu-id="c6ceb-556">disposal</span></span> | <span data-ttu-id="c6ceb-557">多個</span><span class="sxs-lookup"><span data-stu-id="c6ceb-557">Multiple</span></span><br><span data-ttu-id="c6ceb-558">實作</span><span class="sxs-lookup"><span data-stu-id="c6ceb-558">implementations</span></span> | <span data-ttu-id="c6ceb-559">傳遞引數</span><span class="sxs-lookup"><span data-stu-id="c6ceb-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="c6ceb-560">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="c6ceb-561">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-561">Yes</span></span> | <span data-ttu-id="c6ceb-562">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-562">Yes</span></span> | <span data-ttu-id="c6ceb-563">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="c6ceb-564">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="c6ceb-565">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-565">Yes</span></span> | <span data-ttu-id="c6ceb-566">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-566">Yes</span></span> | <span data-ttu-id="c6ceb-567">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="c6ceb-568">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="c6ceb-569">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-569">Yes</span></span> | <span data-ttu-id="c6ceb-570">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-570">No</span></span> | <span data-ttu-id="c6ceb-571">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="c6ceb-572">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="c6ceb-573">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-573">No</span></span> | <span data-ttu-id="c6ceb-574">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-574">Yes</span></span> | <span data-ttu-id="c6ceb-575">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="c6ceb-576">範例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="c6ceb-577">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-577">No</span></span> | <span data-ttu-id="c6ceb-578">否</span><span class="sxs-lookup"><span data-stu-id="c6ceb-578">No</span></span> | <span data-ttu-id="c6ceb-579">是</span><span class="sxs-lookup"><span data-stu-id="c6ceb-579">Yes</span></span> |

<span data-ttu-id="c6ceb-580">如需類型處置的詳細資訊，請參閱[＜服務處置＞](#disposal-of-services)一節。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="c6ceb-581">多個實作的常見案例是[模擬測試類型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="c6ceb-582">如果還未註冊任何實作，則 `TryAdd{LIFETIME}` 方法只會註冊服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="c6ceb-583">在下列範例中，第一行會為 `IMyDependency` 註冊 `MyDependency`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="c6ceb-584">第二行則沒有任何作用，因為 `IMyDependency` 已具有註冊的實作：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="c6ceb-585">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="c6ceb-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="c6ceb-586">如果還沒有「相同類型的」\*\* 實作，則 [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法只會註冊服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="c6ceb-587">多個服務會透過 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="c6ceb-588">註冊服務時，如果尚未新增相同類型的執行個體，則開發人員只會希望新增執行個體。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="c6ceb-589">程式庫作者通常會使用此方法來避免註冊容器中某個執行個體的兩個複本。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="c6ceb-590">在下列範例中，第一行會為 `IMyDep1` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="c6ceb-591">第二行會為 `IMyDep2` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="c6ceb-592">第三行則沒有任何作用，因為 `IMyDep1` 已具有 `MyDep` 的已註冊實作：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="c6ceb-593">建構函式插入行為</span><span class="sxs-lookup"><span data-stu-id="c6ceb-593">Constructor injection behavior</span></span>

<span data-ttu-id="c6ceb-594">服務可以透過兩個機制來解析：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="c6ceb-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允許在相依性插入容器中建立物件，而不註冊服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="c6ceb-596">搭配使用者面向抽象 (例如標籤協助程式、MVC 控制器與模型繫結器) 使用 `ActivatorUtilities`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="c6ceb-597">建構函式可以接受不是由相依性插入提供的引數，但引數必須指派預設值。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="c6ceb-598">當或解決服務時 `IServiceProvider` `ActivatorUtilities` ，必須使用*公用*的函式來[插入](xref:mvc/controllers/dependency-injection#constructor-injection)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="c6ceb-599">當服務解析時，函式 `ActivatorUtilities` [插入](xref:mvc/controllers/dependency-injection#constructor-injection) 會要求只能有一個適用的函式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="c6ceb-600">支援建構函式多載，但只能有一個多載存在，其引數可以藉由相依性插入而完成。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="c6ceb-601">Entity Framework 內容</span><span class="sxs-lookup"><span data-stu-id="c6ceb-601">Entity Framework contexts</span></span>

<span data-ttu-id="c6ceb-602">因為一般會將 Web 應用程式資料庫作業範圍設定為用戶端要求，所以通常會使用[具範圍存留期](#service-lifetimes)將 Entity Framework 內容新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="c6ceb-603">如果在註冊資料庫內容時， [AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)多載未指定存留期，則預設存留期會限定範圍。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="c6ceb-604">指定存留期的服務不應該使用存留期比服務還短的資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="c6ceb-605">留期和註冊選項</span><span class="sxs-lookup"><span data-stu-id="c6ceb-605">Lifetime and registration options</span></span>

<span data-ttu-id="c6ceb-606">為了示範這些存留期和註冊選項之間的差異，請考慮下列介面，以具有唯一識別碼 `OperationId` 的「作業」代表工作。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="c6ceb-607">視如何針對下列介面設定作業服務存留期而定，當類別要求時，容器會提供相同或不同的服務執行個體：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="c6ceb-608">介面是在 `Operation` 類別中實作。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="c6ceb-609">若未提供 GUID，`Operation` 建構函式會產生 GUID：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="c6ceb-610">會註冊相依於每種其他 `Operation` 類型的 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="c6ceb-611">當透過相依性插入要求 `OperationService` 時，它會根據相依服務的存留期擷取每個服務的新執行個體或現有執行個體。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="c6ceb-612">當因收到來自容器的要求而建立暫時性服務時，`IOperationTransient` 服務的 `OperationId` 會與 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="c6ceb-613">`OperationService` 會收到 `IOperationTransient` 類別的新執行個體。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="c6ceb-614">新執行個體會產生不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="c6ceb-615">當因用戶端要求而建立具範圍的服務時，`IOperationScoped` 服務與用戶端要求中 `OperationService` 的 `OperationId` 皆相同。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="c6ceb-616">在不同的用戶端要求中，兩個服務的 `OperationId` 值都不同。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="c6ceb-617">當建立一次 singleton 與 singleton 執行個體服務，並在所有用戶端要求與所有服務中使用時，`OperationId` 在所有服務要求中會固定不變。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="c6ceb-618">在 `Startup.ConfigureServices` 中，每個類型都會根據其具名存留期新增至容器：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="c6ceb-619">`IOperationSingletonInstance` 服務使用具有 `Guid.Empty` 已知識別碼的特定執行個體。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="c6ceb-620">很明顯此型別是使用中 (其 GUID 是所有區域)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="c6ceb-621">範例應用程式示範個別要求內與個別要求之間的物件存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="c6ceb-622">範例應用程式的 `IndexModel` 會要求每種 `IOperation` 型別與 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="c6ceb-623">頁面接著會透過屬性指派來顯示所有頁面模型類別與服務的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="c6ceb-624">下面兩個輸出顯示兩個要求的結果：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="c6ceb-625">**第一個要求：**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-625">**First request:**</span></span>

<span data-ttu-id="c6ceb-626">控制器作業：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-626">Controller operations:</span></span>

<span data-ttu-id="c6ceb-627">暫時性： d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="c6ceb-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="c6ceb-628">具範圍： 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c6ceb-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c6ceb-629">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c6ceb-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c6ceb-630">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c6ceb-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c6ceb-631">`OperationService` 作業：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-631">`OperationService` operations:</span></span>

<span data-ttu-id="c6ceb-632">暫時性： c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="c6ceb-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="c6ceb-633">具範圍： 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="c6ceb-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="c6ceb-634">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c6ceb-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c6ceb-635">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c6ceb-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c6ceb-636">**:第二個要求：**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-636">**Second request:**</span></span>

<span data-ttu-id="c6ceb-637">控制器作業：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-637">Controller operations:</span></span>

<span data-ttu-id="c6ceb-638">暫時性： b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="c6ceb-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="c6ceb-639">具範圍： 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c6ceb-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c6ceb-640">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c6ceb-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c6ceb-641">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c6ceb-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c6ceb-642">`OperationService` 作業：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-642">`OperationService` operations:</span></span>

<span data-ttu-id="c6ceb-643">暫時性： c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="c6ceb-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="c6ceb-644">具範圍： 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="c6ceb-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="c6ceb-645">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="c6ceb-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="c6ceb-646">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="c6ceb-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="c6ceb-647">觀察哪些 `OperationId` 值在要求內以及要求之間不同：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="c6ceb-648">「暫時性」\*\* 物件一律不同。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-648">*Transient* objects are always different.</span></span> <span data-ttu-id="c6ceb-649">第一個與第二個用戶端要求的暫時性 `OperationId` 值在兩個 `OperationService` 作業之間與各用戶端要求之間都不同。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="c6ceb-650">新執行個體會提供給每個服務要求以及用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="c6ceb-651">「具範圍」\*\* 物件在同一個用戶端要求內皆相同，但在不同的用戶端要求之間則不同。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="c6ceb-652">無論中是否有提供實例，每個物件和每個要求的*單一*物件都會相同 `Operation` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="c6ceb-653">從主要呼叫服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-653">Call services from main</span></span>

<span data-ttu-id="c6ceb-654">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>，以解析應用程式範圍中的範圍服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="c6ceb-655">此法可用於在開機時存取範圍服務，以執行初始化工作。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="c6ceb-656">下列範例示範如何在 `Program.Main` 中取得 `MyScopedService`：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="c6ceb-657">範圍驗證</span><span class="sxs-lookup"><span data-stu-id="c6ceb-657">Scope validation</span></span>

<span data-ttu-id="c6ceb-658">當應用程式在開發環境中執行時，預設服務提供者會執行檢查以確認：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="c6ceb-659">範圍服務不是直接或間接由開機服務提供者解析。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="c6ceb-660">範圍服務不是直接或間接插入至單一服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="c6ceb-661">根服務提供者會在呼叫 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 時建立。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="c6ceb-662">當提供者啟動應用程式時，根服務提供者的存留期與應用程式/伺服器的存留期一致，並會在應用程式關閉時處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="c6ceb-663">範圍服務會由建立這些服務的容器處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="c6ceb-664">若是在根容器中建立範圍服務，因為當應用程式/伺服器關機時，服務只會由根容器處理，所以服務的存留期會提升為單一服務等級。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="c6ceb-665">當呼叫 `BuildServiceProvider` 時，驗證服務範圍會攔截到這些情況。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="c6ceb-666">如需詳細資訊，請參閱<xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="c6ceb-667">要求服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-667">Request Services</span></span>

<span data-ttu-id="c6ceb-668">來自 `HttpContext`，在 ASP.NET Core 要求內提供的服務是透過 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公開。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="c6ceb-669">要求服務代表您在應用程式中設定和要求的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="c6ceb-670">當物件指定相依性時，這些會由在 `RequestServices` 中找到的類型來滿足，而非 `ApplicationServices`。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="c6ceb-671">一般而言，應用程式不應該直接使用這些屬性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="c6ceb-672">相反地，透過類別建構函式要求類別所需的型別，以及允許架構插入相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="c6ceb-673">這會產生容易測試的類別。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="c6ceb-674">偏好要求相依性作為建構函式參數，而不要存取 `RequestServices` 集合。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="c6ceb-675">針對相依性插入設計服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-675">Design services for dependency injection</span></span>

<span data-ttu-id="c6ceb-676">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-676">Best practices are to:</span></span>

* <span data-ttu-id="c6ceb-677">設計服務以使用相依性插入來取得其相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="c6ceb-678">避免具狀態、靜態類別和成員。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="c6ceb-679">請改為使用單一服務來設計應用程式，以避免建立全域狀態。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="c6ceb-680">避免直接在服務內具現化相依類別。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="c6ceb-681">直接具現化會將程式碼耦合到特定實作。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="c6ceb-682">讓應用程式類別維持在小型、情況良好且可輕鬆測試的狀態。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="c6ceb-683">若類別有太多插入的相依性，這通常表示類別有太多責任而且正違反[單一責任原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="c6ceb-684">將類別負責的某些部分移到新的類別，以嘗試重構類別。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="c6ceb-685">請記住， Razor 頁面頁面模型類別和 MVC 控制器類別應該專注于 UI 的考慮。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="c6ceb-686">商務規則和資料存取實作詳細資料應該保存在適合這些[分開考量](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Separation of Concerns) 類別中。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="c6ceb-687">處置服務</span><span class="sxs-lookup"><span data-stu-id="c6ceb-687">Disposal of services</span></span>

<span data-ttu-id="c6ceb-688">容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="c6ceb-689">若執行個體由使用者程式碼新增到容器中，它將不會自動處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="c6ceb-690">在下列範例中，服務是由服務容器建立並自動處置：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="c6ceb-691">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="c6ceb-691">In the following example:</span></span>

* <span data-ttu-id="c6ceb-692">服務實例不是由服務容器所建立。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="c6ceb-693">架構不知道預期的服務存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="c6ceb-694">架構不會自動處置服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="c6ceb-695">如果未在開發人員程式碼中明確處置服務，它們會保存到應用程式關閉為止。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="c6ceb-696">暫時性和共用實例的 IDisposable 指引</span><span class="sxs-lookup"><span data-stu-id="c6ceb-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="c6ceb-697">暫時性、有限存留期</span><span class="sxs-lookup"><span data-stu-id="c6ceb-697">Transient, limited lifetime</span></span>

<span data-ttu-id="c6ceb-698">**案例**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-698">**Scenario**</span></span>

<span data-ttu-id="c6ceb-699">應用程式需要在 <xref:System.IDisposable> 下列任一案例中具有暫時性存留期的實例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="c6ceb-700">此實例會在根範圍中解析。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="c6ceb-701">實例應該在範圍結束之前處置。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="c6ceb-702">**方案**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-702">**Solution**</span></span>

<span data-ttu-id="c6ceb-703">使用 factory 模式，在父範圍之外建立實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="c6ceb-704">在這種情況下，應用程式通常會有 `Create` 方法可直接呼叫最終型別的函式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="c6ceb-705">如果最終類型有其他相依性，factory 可以：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="c6ceb-706"><xref:System.IServiceProvider>在其函式中接收。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="c6ceb-707">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 可在容器外部具現化實例，同時使用容器作為其相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="c6ceb-708">共用實例，有限存留期</span><span class="sxs-lookup"><span data-stu-id="c6ceb-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="c6ceb-709">**案例**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-709">**Scenario**</span></span>

<span data-ttu-id="c6ceb-710">應用程式需要 <xref:System.IDisposable> 跨多個服務的共用實例，但是 <xref:System.IDisposable> 存留期應受限。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="c6ceb-711">**方案**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-711">**Solution**</span></span>

<span data-ttu-id="c6ceb-712">註冊具有範圍存留期的實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="c6ceb-713">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 啟動並建立新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="c6ceb-714">使用範圍 <xref:System.IServiceProvider> 來取得所需的服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="c6ceb-715">在存留期結束時處置範圍。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="c6ceb-716">一般準則</span><span class="sxs-lookup"><span data-stu-id="c6ceb-716">General Guidelines</span></span>

* <span data-ttu-id="c6ceb-717">請勿向 <xref:System.IDisposable> 暫時性範圍登錄實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="c6ceb-718">請改用 factory 模式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="c6ceb-719">請勿在根範圍中解析暫時性或限定範圍 <xref:System.IDisposable> 的實例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="c6ceb-720">唯一的一般例外狀況是當應用程式建立/重新建立並處置時 <xref:System.IServiceProvider> ，這不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="c6ceb-721">透過 DI 接收相依性 <xref:System.IDisposable> 不需要接收者 <xref:System.IDisposable> 自行執行。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="c6ceb-722">相依性的接收者不 <xref:System.IDisposable> 應呼叫 <xref:System.IDisposable.Dispose%2A> 該相依性。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="c6ceb-723">範圍應該用來控制服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="c6ceb-724">範圍不是階層式，而且範圍之間沒有特殊連接。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="c6ceb-725">預設服務容器取代</span><span class="sxs-lookup"><span data-stu-id="c6ceb-725">Default service container replacement</span></span>

<span data-ttu-id="c6ceb-726">內建的服務容器是設計來滿足架構和大部分消費者應用程式的需求。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="c6ceb-727">除非您需要內建容器不支援的特定功能，否則建議使用內建容器，例如：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="c6ceb-728">屬性插入</span><span class="sxs-lookup"><span data-stu-id="c6ceb-728">Property injection</span></span>
* <span data-ttu-id="c6ceb-729">根據名稱插入</span><span class="sxs-lookup"><span data-stu-id="c6ceb-729">Injection based on name</span></span>
* <span data-ttu-id="c6ceb-730">子容器</span><span class="sxs-lookup"><span data-stu-id="c6ceb-730">Child containers</span></span>
* <span data-ttu-id="c6ceb-731">自訂生命週期管理</span><span class="sxs-lookup"><span data-stu-id="c6ceb-731">Custom lifetime management</span></span>
* <span data-ttu-id="c6ceb-732">`Func<T>` 支援延遲初始設定</span><span class="sxs-lookup"><span data-stu-id="c6ceb-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="c6ceb-733">以慣例為基礎的註冊</span><span class="sxs-lookup"><span data-stu-id="c6ceb-733">Convention-based registration</span></span>

<span data-ttu-id="c6ceb-734">下列協力廠商容器可搭配 ASP.NET Core apps 使用：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="c6ceb-735">Autofac</span><span class="sxs-lookup"><span data-stu-id="c6ceb-735">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="c6ceb-736">DryIoc</span><span class="sxs-lookup"><span data-stu-id="c6ceb-736">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="c6ceb-737">Grace</span><span class="sxs-lookup"><span data-stu-id="c6ceb-737">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="c6ceb-738">LightInject</span><span class="sxs-lookup"><span data-stu-id="c6ceb-738">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="c6ceb-739">拉馬爾</span><span class="sxs-lookup"><span data-stu-id="c6ceb-739">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="c6ceb-740">Stashbox</span><span class="sxs-lookup"><span data-stu-id="c6ceb-740">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="c6ceb-741">Unity</span><span class="sxs-lookup"><span data-stu-id="c6ceb-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="c6ceb-742">執行緒安全</span><span class="sxs-lookup"><span data-stu-id="c6ceb-742">Thread safety</span></span>

<span data-ttu-id="c6ceb-743">建立具備執行緒安全性的 singleton 服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="c6ceb-744">如果 singleton 服務相依於暫時性服務，暫時性服務可能也需要具備執行緒安全性，取決於 singleton 如何使用它。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="c6ceb-745">單一服務的 factory 方法（例如 >addsingleton 的第二個自 [變數 \<TService> (IServiceCollection、Func \<IServiceProvider,TService>) ](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)）不需要是安全線程。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="c6ceb-746">就像型別 (`static`) 建構函式一樣，它一定會被單一執行緒呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="c6ceb-747">建議</span><span class="sxs-lookup"><span data-stu-id="c6ceb-747">Recommendations</span></span>

* <span data-ttu-id="c6ceb-748">不支援以 `async/await` 與 `Task` 為基礎的服務解析。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="c6ceb-749">C# 不支援非同步建構函式，因此建議的模式是以同步方式解析服務後使用非同步方法。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="c6ceb-750">避免直接在服務容器中儲存資料與設定。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="c6ceb-751">例如，使用者的購物車通常不應該新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="c6ceb-752">組態應該使用[選項模式](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="c6ceb-753">同樣地，請避免只存在以允許存取某個其他物件的「資料持有者」物件。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="c6ceb-754">最好是透過 DI 要求實際項目。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="c6ceb-755">避免靜態存取服務。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-755">Avoid static access to services.</span></span> <span data-ttu-id="c6ceb-756">例如，請避免以靜態方式輸入 [IApplicationBuilder >iapplicationbuilder.applicationservices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ，以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="c6ceb-757">請避免使用 *服務定位器模式*，其混合 [了控制策略的反轉](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="c6ceb-758"><xref:System.IServiceProvider.GetService*>當您可以改用 DI 時，請勿叫用來取得服務實例：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="c6ceb-759">**不正確：**</span><span class="sxs-lookup"><span data-stu-id="c6ceb-759">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="c6ceb-760">**正確**：</span><span class="sxs-lookup"><span data-stu-id="c6ceb-760">**Correct**:</span></span>

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

* <span data-ttu-id="c6ceb-761">避免插入在執行時間使用來解析相依性的 factory <xref:System.IServiceProvider.GetService*> 。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="c6ceb-762">避免以靜態方式存取 `HttpContext` (例如 [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="c6ceb-763">就像所有的建議集，您可能會遇到需要忽略建議的情況。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="c6ceb-764">例外狀況很罕見，大多是架構本身內的特殊案例。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="c6ceb-765">DI 是靜態/全域物件存取模式的「替代」\*\* 選項。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="c6ceb-766">如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。</span><span class="sxs-lookup"><span data-stu-id="c6ceb-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c6ceb-767">其他資源</span><span class="sxs-lookup"><span data-stu-id="c6ceb-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="c6ceb-768">在 ASP.NET Core 中處置 IDisposables 的四種方式</span><span class="sxs-lookup"><span data-stu-id="c6ceb-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="c6ceb-769">在 ASP.NET Core 使用 Dependency Injection 撰寫簡潔的程式碼 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="c6ceb-770">明確相依性準則</span><span class="sxs-lookup"><span data-stu-id="c6ceb-770">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="c6ceb-771">[逆轉控制容器和相依性插入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-771">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* <span data-ttu-id="c6ceb-772">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (如何使用 ASP.NET Core DI 中的多個介面註冊服務)</span><span class="sxs-lookup"><span data-stu-id="c6ceb-772">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
