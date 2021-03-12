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
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="775db-103">.NET Core 中的相依性插入</span><span class="sxs-lookup"><span data-stu-id="775db-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="775db-104">[Kirk Larkin](https://twitter.com/serpent5)、 [Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="775db-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="775db-105">ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。</span><span class="sxs-lookup"><span data-stu-id="775db-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="775db-106">如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="775db-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="775db-107">如需在 web 應用程式以外的應用程式中使用相依性插入的相關資訊，請參閱 [.net 中](/dotnet/core/extensions/dependency-injection)的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="775db-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="775db-108">如需有關選項之相依性插入的詳細資訊，請參閱 <xref:fundamentals/configuration/options> 。</span><span class="sxs-lookup"><span data-stu-id="775db-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="775db-109">本主題提供 ASP.NET Core 中的相依性插入的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="775db-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="775db-110">使用相依性插入的主要檔包含在 .NET 的相依性 [插入](/dotnet/core/extensions/dependency-injection)中。</span><span class="sxs-lookup"><span data-stu-id="775db-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="775db-111">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="775db-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="775db-112">相依性插入概觀</span><span class="sxs-lookup"><span data-stu-id="775db-112">Overview of dependency injection</span></span>

<span data-ttu-id="775db-113">相依 *性是另* 一個物件所依存的物件。</span><span class="sxs-lookup"><span data-stu-id="775db-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="775db-114">`MyDependency`使用其他類別所依存的方法檢查下列類別 `WriteMessage` ：</span><span class="sxs-lookup"><span data-stu-id="775db-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="775db-115">類別可以建立類別的實例 `MyDependency` ，以使用其 `WriteMessage` 方法。</span><span class="sxs-lookup"><span data-stu-id="775db-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="775db-116">在下列範例中， `MyDependency` 類別是類別的相依性 `IndexModel` ：</span><span class="sxs-lookup"><span data-stu-id="775db-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="775db-117">類別會建立和直接相依于 `MyDependency` 類別。</span><span class="sxs-lookup"><span data-stu-id="775db-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="775db-118">程式碼相依性（如上述範例所示）有問題，而且應該避免因下列原因：</span><span class="sxs-lookup"><span data-stu-id="775db-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="775db-119">若要以 `MyDependency` 不同的實作為取代， `IndexModel` 必須修改該類別。</span><span class="sxs-lookup"><span data-stu-id="775db-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="775db-120">如果 `MyDependency` 有相依性，則也必須由類別設定 `IndexModel` 。</span><span class="sxs-lookup"><span data-stu-id="775db-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="775db-121">在具有多個相依於 `MyDependency` 之多個類別的大型專案中，設定程式碼在不同的應用程式之間會變得鬆散。</span><span class="sxs-lookup"><span data-stu-id="775db-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="775db-122">此實作難以進行單元測試。</span><span class="sxs-lookup"><span data-stu-id="775db-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="775db-123">應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。</span><span class="sxs-lookup"><span data-stu-id="775db-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="775db-124">相依性插入可透過下列方式解決這些問題：</span><span class="sxs-lookup"><span data-stu-id="775db-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="775db-125">使用介面或基底類別來將相依性資訊抽象化。</span><span class="sxs-lookup"><span data-stu-id="775db-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="775db-126">在服務容器中註冊相依性。</span><span class="sxs-lookup"><span data-stu-id="775db-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="775db-127">ASP.NET Core 提供內建服務容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="775db-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="775db-128">服務通常會在應用程式的方法中註冊 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="775db-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="775db-129">將服務「插入」到服務使用位置之類別的建構函式。</span><span class="sxs-lookup"><span data-stu-id="775db-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="775db-130">架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。</span><span class="sxs-lookup"><span data-stu-id="775db-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="775db-131">在 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples)中， `IMyDependency` 介面會定義 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="775db-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="775db-132">這個介面是由具象型別 `MyDependency` 所實作：</span><span class="sxs-lookup"><span data-stu-id="775db-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="775db-133">範例應用程式會 `IMyDependency` 使用具象類型來註冊服務 `MyDependency` 。</span><span class="sxs-lookup"><span data-stu-id="775db-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="775db-134"><xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>方法會使用範圍存留期（單一要求的存留期）來註冊服務。</span><span class="sxs-lookup"><span data-stu-id="775db-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="775db-135">將在此主題稍後將說明[服務存留期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="775db-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="775db-136">在範例應用程式中， `IMyDependency` 會要求服務並用來呼叫 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="775db-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="775db-137">藉由使用 DI 模式，控制器：</span><span class="sxs-lookup"><span data-stu-id="775db-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="775db-138">不使用具象型別 `MyDependency` ，只使用它所執行的 `IMyDependency` 介面。</span><span class="sxs-lookup"><span data-stu-id="775db-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="775db-139">這可讓您輕鬆地變更控制器所使用的執行，而不需要修改控制器。</span><span class="sxs-lookup"><span data-stu-id="775db-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="775db-140">不會建立的實例 `MyDependency` ，而是由 DI 容器所建立。</span><span class="sxs-lookup"><span data-stu-id="775db-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="775db-141">您 `IMyDependency` 可以使用內建的記錄 API 來改善介面的實作為：</span><span class="sxs-lookup"><span data-stu-id="775db-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="775db-142">更新的 `ConfigureServices` 方法會註冊新的 `IMyDependency` 實作為：</span><span class="sxs-lookup"><span data-stu-id="775db-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="775db-143">`MyDependency2` 相依于 <xref:Microsoft.Extensions.Logging.ILogger%601> 它在函式中要求的。</span><span class="sxs-lookup"><span data-stu-id="775db-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="775db-144">`ILogger<TCategoryName>` 是 [架構提供的服務](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="775db-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="775db-145">以鏈結方式使用相依性插入並非不尋常。</span><span class="sxs-lookup"><span data-stu-id="775db-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="775db-146">每個要求的相依性接著會要求其自己的相依性。</span><span class="sxs-lookup"><span data-stu-id="775db-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="775db-147">容器會解決圖形中的相依性，並傳回完全解析的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="775db-148">必須先解析的相依性集合組通常稱為「相依性樹狀結構」、「相依性圖形」或「物件圖形」。</span><span class="sxs-lookup"><span data-stu-id="775db-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="775db-149">容器會 `ILogger<TCategoryName>` 利用 [ (泛型) 開放式](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types)型別來解析，因此不需要註冊每個 [ (泛型) 結構類型](/dotnet/csharp/language-reference/language-specification/types#constructed-types)。</span><span class="sxs-lookup"><span data-stu-id="775db-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="775db-150">在相依性插入術語中，服務：</span><span class="sxs-lookup"><span data-stu-id="775db-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="775db-151">通常是為其他物件（例如服務）提供服務的物件 `IMyDependency` 。</span><span class="sxs-lookup"><span data-stu-id="775db-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="775db-152">與 web 服務無關，但服務可能會使用 web 服務。</span><span class="sxs-lookup"><span data-stu-id="775db-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="775db-153">架構會提供健全的 [記錄](xref:fundamentals/logging/index) 系統。</span><span class="sxs-lookup"><span data-stu-id="775db-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="775db-154">`IMyDependency`上述範例中所示的執行是為了示範基本 DI 而撰寫的，而不是用來執行記錄。</span><span class="sxs-lookup"><span data-stu-id="775db-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="775db-155">大部分的應用程式都不需要撰寫記錄器。</span><span class="sxs-lookup"><span data-stu-id="775db-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="775db-156">下列程式碼示範如何使用預設記錄，這不需要在中註冊任何服務 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="775db-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="775db-157">使用上述程式碼不需要更新 `ConfigureServices` ，因為 [記錄](xref:fundamentals/logging/index) 是由架構提供。</span><span class="sxs-lookup"><span data-stu-id="775db-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="775db-158">插入啟動的服務</span><span class="sxs-lookup"><span data-stu-id="775db-158">Services injected into Startup</span></span>

<span data-ttu-id="775db-159">服務可以插入至函式 `Startup` 和 `Startup.Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="775db-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="775db-160">`Startup`使用泛型主機 () 時，只可將下列服務插入至函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="775db-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="775db-161">任何向 DI 容器註冊的服務都可以插入方法中 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="775db-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="775db-162">如需詳細資訊，請參閱 <xref:fundamentals/startup> 和 [Access Configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup)。</span><span class="sxs-lookup"><span data-stu-id="775db-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="775db-163">使用擴充方法來註冊服務群組</span><span class="sxs-lookup"><span data-stu-id="775db-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="775db-164">ASP.NET Core framework 會使用註冊一組相關服務的慣例。</span><span class="sxs-lookup"><span data-stu-id="775db-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="775db-165">慣例是使用單一 `Add{GROUP_NAME}` 擴充方法來註冊架構功能所需的所有服務。</span><span class="sxs-lookup"><span data-stu-id="775db-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="775db-166">例如， <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> 擴充方法會註冊 MVC 控制器所需的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-166">For example, the <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers%2A> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="775db-167">下列程式碼是由 Razor 使用個別使用者帳戶的 Pages 範本所產生，並示範如何使用擴充方法和來將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> ：</span><span class="sxs-lookup"><span data-stu-id="775db-167">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="775db-168">服務存留期</span><span class="sxs-lookup"><span data-stu-id="775db-168">Service lifetimes</span></span>

<span data-ttu-id="775db-169">在[.net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入中的[服務存留期](/dotnet/core/extensions/dependency-injection#service-lifetimes)</span><span class="sxs-lookup"><span data-stu-id="775db-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="775db-170">若要在中介軟體中使用範圍服務，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="775db-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="775db-171">將服務插入中介軟體的 `Invoke` 或 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="775db-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="775db-172">使用函式 [插入](xref:mvc/controllers/dependency-injection#constructor-injection) 會擲回執行時間例外狀況，因為它會強制範圍服務的行為就像 singleton 一樣。</span><span class="sxs-lookup"><span data-stu-id="775db-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="775db-173">[ [存留期和註冊選項](#lifetime-and-registration-options) ] 區段中的範例會示範 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="775db-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="775db-174">使用以 [Factory 為基礎的中介軟體](xref:fundamentals/middleware/extensibility)。</span><span class="sxs-lookup"><span data-stu-id="775db-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="775db-175">使用此方法註冊的中介軟體會根據用戶端要求啟動 (連接) ，可讓範圍服務插入中介軟體的 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="775db-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="775db-176">如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="775db-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="775db-177">服務註冊方法</span><span class="sxs-lookup"><span data-stu-id="775db-177">Service registration methods</span></span>

<span data-ttu-id="775db-178">在[.net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入中的[服務註冊方法](/dotnet/core/extensions/dependency-injection#service-registration-methods)</span><span class="sxs-lookup"><span data-stu-id="775db-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="775db-179">[模擬類型以進行測試](xref:test/integration-tests#inject-mock-services)時，通常會使用多個實作為。</span><span class="sxs-lookup"><span data-stu-id="775db-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="775db-180">註冊只有實作為型別的服務，相當於向相同的實和服務型別註冊該服務。</span><span class="sxs-lookup"><span data-stu-id="775db-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="775db-181">這就是為什麼無法使用未採用明確服務類型的方法來註冊多個服務的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="775db-182">這些方法可以註冊服務的多個 *實例* ，但它們都有相同的 *實* 作為型別。</span><span class="sxs-lookup"><span data-stu-id="775db-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="775db-183">您可以使用上述任何一種服務註冊方法來註冊相同服務類型的多個服務實例。</span><span class="sxs-lookup"><span data-stu-id="775db-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="775db-184">在下列範例中， `AddSingleton` 使用 `IMyDependency` 做為服務類型呼叫兩次。</span><span class="sxs-lookup"><span data-stu-id="775db-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="775db-185">第二個呼叫會 `AddSingleton` 在解析為時覆寫上一個 `IMyDependency` ，並在透過來解析多個服務時加入至上一個 `IEnumerable<IMyDependency>` 。</span><span class="sxs-lookup"><span data-stu-id="775db-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="775db-186">服務會依照其在解析時所註冊的順序顯示 `IEnumerable<{SERVICE}>` 。</span><span class="sxs-lookup"><span data-stu-id="775db-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

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

## <a name="constructor-injection-behavior"></a><span data-ttu-id="775db-187">建構函式插入行為</span><span class="sxs-lookup"><span data-stu-id="775db-187">Constructor injection behavior</span></span>

<span data-ttu-id="775db-188">在[.net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入中的函式[插入行為](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)</span><span class="sxs-lookup"><span data-stu-id="775db-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="775db-189">Entity Framework 內容</span><span class="sxs-lookup"><span data-stu-id="775db-189">Entity Framework contexts</span></span>

<span data-ttu-id="775db-190">根據預設，會使用限 [域存留期](#service-lifetimes) 將 Entity Framework 內容新增至服務容器，因為 web 應用程式資料庫作業的範圍通常是用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="775db-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="775db-191">若要使用不同的存留期，請使用多載來指定存留期 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> 。</span><span class="sxs-lookup"><span data-stu-id="775db-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="775db-192">給定存留期的服務不應使用存留期短于服務存留期的資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="775db-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="775db-193">留期和註冊選項</span><span class="sxs-lookup"><span data-stu-id="775db-193">Lifetime and registration options</span></span>

<span data-ttu-id="775db-194">若要示範服務存留期與其註冊選項之間的差異，請考慮使用下列介面，將工作表示為具有識別碼的作業 `OperationId` 。</span><span class="sxs-lookup"><span data-stu-id="775db-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="775db-195">根據作業的服務存留期如何針對下列介面進行設定，容器會在要求類別時提供相同或不同的服務實例：</span><span class="sxs-lookup"><span data-stu-id="775db-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="775db-196">下列類別會執行 `Operation` 上述所有介面。</span><span class="sxs-lookup"><span data-stu-id="775db-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="775db-197">此函式會 `Operation` 產生 GUID，並將最後4個字元儲存在 `OperationId` 屬性中：</span><span class="sxs-lookup"><span data-stu-id="775db-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="775db-198">`Startup.ConfigureServices`方法會根據指定的存留期，建立類別的多個註冊 `Operation` ：</span><span class="sxs-lookup"><span data-stu-id="775db-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="775db-199">範例應用程式會示範在要求內和之間的物件存留期。</span><span class="sxs-lookup"><span data-stu-id="775db-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="775db-200">`IndexModel`和中介軟體會要求每種 `IOperation` 類型，並記錄 `OperationId` 每個類型的：</span><span class="sxs-lookup"><span data-stu-id="775db-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="775db-201">類似于 `IndexModel` ，中介軟體會解析相同的服務：</span><span class="sxs-lookup"><span data-stu-id="775db-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="775db-202">您必須在方法中解析已設定範圍的服務 `InvokeAsync` ：</span><span class="sxs-lookup"><span data-stu-id="775db-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="775db-203">記錄器輸出顯示：</span><span class="sxs-lookup"><span data-stu-id="775db-203">The logger output shows:</span></span>

* <span data-ttu-id="775db-204">「暫時性」 物件一律不同。</span><span class="sxs-lookup"><span data-stu-id="775db-204">*Transient* objects are always different.</span></span> <span data-ttu-id="775db-205">在 `OperationId` 中介軟體的和中，暫時性值是不同的 `IndexModel` 。</span><span class="sxs-lookup"><span data-stu-id="775db-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="775db-206">每個要求的 *範圍* 物件都相同，但在每個要求中都是不同的。</span><span class="sxs-lookup"><span data-stu-id="775db-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="775db-207">每個要求的 *單一* 物件都相同。</span><span class="sxs-lookup"><span data-stu-id="775db-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="775db-208">若要減少記錄輸出，請在檔案的 *appsettings.Development.js* 中設定 "記錄： LogLevel： Microsoft： Error"：</span><span class="sxs-lookup"><span data-stu-id="775db-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="775db-209">從主要呼叫服務</span><span class="sxs-lookup"><span data-stu-id="775db-209">Call services from main</span></span>

<span data-ttu-id="775db-210">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) 建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>，以解析應用程式範圍中的範圍服務。</span><span class="sxs-lookup"><span data-stu-id="775db-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="775db-211">此法可用於在開機時存取範圍服務，以執行初始化工作。</span><span class="sxs-lookup"><span data-stu-id="775db-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="775db-212">下列範例示範如何存取範圍 `IMyDependency` 服務，並在中呼叫其 `WriteMessage` 方法 `Program.Main` ：</span><span class="sxs-lookup"><span data-stu-id="775db-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="775db-213">範圍驗證</span><span class="sxs-lookup"><span data-stu-id="775db-213">Scope validation</span></span>

<span data-ttu-id="775db-214">在[.net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入中的函式[插入行為](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior)</span><span class="sxs-lookup"><span data-stu-id="775db-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="775db-215">如需詳細資訊，請參閱[範圍驗證](xref:fundamentals/host/web-host#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="775db-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="775db-216">要求服務</span><span class="sxs-lookup"><span data-stu-id="775db-216">Request Services</span></span>

<span data-ttu-id="775db-217">ASP.NET 核心要求內可用的服務會透過 [RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公開。</span><span class="sxs-lookup"><span data-stu-id="775db-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="775db-218">從要求內要求服務時，會從集合解析服務及其相依性 `RequestServices` 。</span><span class="sxs-lookup"><span data-stu-id="775db-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="775db-219">架構會為每個要求建立一個範圍，並 `RequestServices` 公開範圍服務提供者。</span><span class="sxs-lookup"><span data-stu-id="775db-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="775db-220">只要要求為作用中狀態，所有範圍的服務都有效。</span><span class="sxs-lookup"><span data-stu-id="775db-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="775db-221">偏好將相依性要求為函式參數，以解析集合中的服務 `RequestServices` 。</span><span class="sxs-lookup"><span data-stu-id="775db-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="775db-222">這會導致更容易測試的類別。</span><span class="sxs-lookup"><span data-stu-id="775db-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="775db-223">針對相依性插入設計服務</span><span class="sxs-lookup"><span data-stu-id="775db-223">Design services for dependency injection</span></span>

<span data-ttu-id="775db-224">針對相依性插入設計服務時：</span><span class="sxs-lookup"><span data-stu-id="775db-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="775db-225">避免具狀態、靜態類別和成員。</span><span class="sxs-lookup"><span data-stu-id="775db-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="775db-226">請改為使用單一服務來設計應用程式，以避免建立全域狀態。</span><span class="sxs-lookup"><span data-stu-id="775db-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="775db-227">避免直接在服務內具現化相依類別。</span><span class="sxs-lookup"><span data-stu-id="775db-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="775db-228">直接具現化會將程式碼耦合到特定實作。</span><span class="sxs-lookup"><span data-stu-id="775db-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="775db-229">使服務更小、妥善組成且輕鬆地進行測試。</span><span class="sxs-lookup"><span data-stu-id="775db-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="775db-230">如果類別有許多插入的相依性，可能是因為類別具有太多責任，而且違反了 [單一責任原則 (SRP) ](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility)。</span><span class="sxs-lookup"><span data-stu-id="775db-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="775db-231">嘗試將其部分責任移至新的類別，以重構類別。</span><span class="sxs-lookup"><span data-stu-id="775db-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="775db-232">請記住， Razor 頁面頁面模型類別和 MVC 控制器類別應該專注于 UI 的考慮。</span><span class="sxs-lookup"><span data-stu-id="775db-232">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="775db-233">處置服務</span><span class="sxs-lookup"><span data-stu-id="775db-233">Disposal of services</span></span>

<span data-ttu-id="775db-234">容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose%2A>。</span><span class="sxs-lookup"><span data-stu-id="775db-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="775db-235">從容器解析的服務絕對不能由開發人員處置。</span><span class="sxs-lookup"><span data-stu-id="775db-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="775db-236">如果類型或 factory 註冊為 singleton，則容器會自動處置 singleton。</span><span class="sxs-lookup"><span data-stu-id="775db-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="775db-237">在下列範例中，服務是由服務容器建立並自動處置：</span><span class="sxs-lookup"><span data-stu-id="775db-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="775db-238">在每次重新整理 [索引] 頁面之後，debug 主控台會顯示下列輸出：</span><span class="sxs-lookup"><span data-stu-id="775db-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="775db-239">服務容器未建立的服務</span><span class="sxs-lookup"><span data-stu-id="775db-239">Services not created by the service container</span></span>

<span data-ttu-id="775db-240">請考慮下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="775db-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="775db-241">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="775db-241">In the preceding code:</span></span>

* <span data-ttu-id="775db-242">服務實例不是由服務容器所建立。</span><span class="sxs-lookup"><span data-stu-id="775db-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="775db-243">架構不會自動處置服務。</span><span class="sxs-lookup"><span data-stu-id="775db-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="775db-244">開發人員負責處置服務。</span><span class="sxs-lookup"><span data-stu-id="775db-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="775db-245">暫時性和共用實例的 IDisposable 指引</span><span class="sxs-lookup"><span data-stu-id="775db-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="775db-246">在 .NET 中的相依性[插入](/dotnet/core/extensions/dependency-injection)中查看[暫時性和共用實例的 IDisposable 指導](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances)方針</span><span class="sxs-lookup"><span data-stu-id="775db-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="775db-247">預設服務容器取代</span><span class="sxs-lookup"><span data-stu-id="775db-247">Default service container replacement</span></span>

<span data-ttu-id="775db-248">查看 .NET 中的相依性[插入](/dotnet/core/extensions/dependency-injection)中的[預設服務容器取代](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement)</span><span class="sxs-lookup"><span data-stu-id="775db-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="775db-249">建議</span><span class="sxs-lookup"><span data-stu-id="775db-249">Recommendations</span></span>

<span data-ttu-id="775db-250">[在 .net 中](/dotnet/core/extensions/dependency-injection)查看相依性插入的[建議](/dotnet/core/extensions/dependency-injection-guidelines#recommendations)</span><span class="sxs-lookup"><span data-stu-id="775db-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="775db-251">避免使用「服務定位器模式」。</span><span class="sxs-lookup"><span data-stu-id="775db-251">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="775db-252">例如，當您可以改用 DI 時，請勿叫用 <xref:System.IServiceProvider.GetService%2A> 來取得服務執行個體：</span><span class="sxs-lookup"><span data-stu-id="775db-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="775db-253">**不正確：**</span><span class="sxs-lookup"><span data-stu-id="775db-253">**Incorrect:**</span></span>

    ![不正確的程式碼](dependency-injection/_static/bad.png)

  <span data-ttu-id="775db-255">**正確**：</span><span class="sxs-lookup"><span data-stu-id="775db-255">**Correct**:</span></span>

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
* <span data-ttu-id="775db-256">另一個要避免的服務定位器變化是插入在執行階段解析相依性的處理站。</span><span class="sxs-lookup"><span data-stu-id="775db-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="775db-257">這兩種做法都會混用[控制反轉](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion)策略。</span><span class="sxs-lookup"><span data-stu-id="775db-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="775db-258">避免以靜態方式存取 `HttpContext` (例如 [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="775db-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="775db-259">避免 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> 在中呼叫 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="775db-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="775db-260">呼叫 `BuildServiceProvider` 通常會在開發人員想要解析中的服務時發生 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="775db-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="775db-261">例如，請考慮從設定載入的情況 `LoginPath` 。</span><span class="sxs-lookup"><span data-stu-id="775db-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="775db-262">請避免下列方法：</span><span class="sxs-lookup"><span data-stu-id="775db-262">Avoid the following approach:</span></span>

  ![呼叫 BuildServiceProvider 的錯誤程式碼](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="775db-264">在上圖中，選取下綠色波浪線會 `services.BuildServiceProvider` 顯示下列 ASP0000 警告：</span><span class="sxs-lookup"><span data-stu-id="775db-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="775db-265">從應用程式程式碼呼叫 ' BuildServiceProvider ' ASP0000 時，會產生另一個要建立的單一服務複本。</span><span class="sxs-lookup"><span data-stu-id="775db-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="775db-266">請考慮將相依性插入服務作為「設定」參數的替代方案。</span><span class="sxs-lookup"><span data-stu-id="775db-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="775db-267">呼叫 `BuildServiceProvider` 會建立第二個容器，它可以建立損毀的 singleton，並導致跨多個容器的物件圖形參考。</span><span class="sxs-lookup"><span data-stu-id="775db-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="775db-268">若要取得正確的方式 `LoginPath` ，請使用選項模式內建的 DI 支援：</span><span class="sxs-lookup"><span data-stu-id="775db-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="775db-269">容器會捕獲可處置的暫時性服務以供處置。</span><span class="sxs-lookup"><span data-stu-id="775db-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="775db-270">如果從最上層容器解析，這可能會導致記憶體流失。</span><span class="sxs-lookup"><span data-stu-id="775db-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="775db-271">啟用範圍驗證，以確定應用程式沒有可捕獲範圍服務的 singleton。</span><span class="sxs-lookup"><span data-stu-id="775db-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="775db-272">如需詳細資訊，請參閱[範圍驗證](#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="775db-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="775db-273">就像所有的建議集，您可能會遇到需要忽略建議的情況。</span><span class="sxs-lookup"><span data-stu-id="775db-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="775db-274">例外狀況很罕見，大多是架構本身內的特殊案例。</span><span class="sxs-lookup"><span data-stu-id="775db-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="775db-275">DI 是靜態/全域物件存取模式的「替代」選項。</span><span class="sxs-lookup"><span data-stu-id="775db-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="775db-276">如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。</span><span class="sxs-lookup"><span data-stu-id="775db-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="775db-277">DI 中多租使用者的建議模式</span><span class="sxs-lookup"><span data-stu-id="775db-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="775db-278">[Orchard core](https://github.com/OrchardCMS/OrchardCore) 是一種應用程式架構，可在 ASP.NET Core 上建立模組化、多租使用者應用程式。</span><span class="sxs-lookup"><span data-stu-id="775db-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="775db-279">如需詳細資訊，請參閱 [Orchard Core 檔](https://docs.orchardcore.net/en/dev/)。</span><span class="sxs-lookup"><span data-stu-id="775db-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="775db-280">如需如何使用 Orchard Core Framework 來建立模組化和多租使用者應用程式的範例，請參閱 [Orchard core 範例](https://github.com/OrchardCMS/OrchardCore.Samples) ，而不需要任何 CMS 專屬的功能。</span><span class="sxs-lookup"><span data-stu-id="775db-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="775db-281">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="775db-281">Framework-provided services</span></span>

<span data-ttu-id="775db-282">`Startup.ConfigureServices`方法會註冊應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。</span><span class="sxs-lookup"><span data-stu-id="775db-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="775db-283">一開始， `IServiceCollection` 提供給的是 `ConfigureServices` 由架構定義的服務，視 [主機的設定方式](xref:fundamentals/index#host)而定。</span><span class="sxs-lookup"><span data-stu-id="775db-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="775db-284">針對以 ASP.NET Core 範本為基礎的應用程式，架構會註冊250以上的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="775db-285">下表列出這些架構註冊服務的小型範例：</span><span class="sxs-lookup"><span data-stu-id="775db-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="775db-286">服務類型</span><span class="sxs-lookup"><span data-stu-id="775db-286">Service Type</span></span>                                                                                    | <span data-ttu-id="775db-287">存留期</span><span class="sxs-lookup"><span data-stu-id="775db-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="775db-288">暫時性</span><span class="sxs-lookup"><span data-stu-id="775db-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="775db-289">單一</span><span class="sxs-lookup"><span data-stu-id="775db-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="775db-290">單一</span><span class="sxs-lookup"><span data-stu-id="775db-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="775db-291">單一</span><span class="sxs-lookup"><span data-stu-id="775db-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="775db-292">暫時性</span><span class="sxs-lookup"><span data-stu-id="775db-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="775db-293">單一</span><span class="sxs-lookup"><span data-stu-id="775db-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="775db-294">暫時性</span><span class="sxs-lookup"><span data-stu-id="775db-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="775db-295">單一</span><span class="sxs-lookup"><span data-stu-id="775db-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="775db-296">單一</span><span class="sxs-lookup"><span data-stu-id="775db-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="775db-297">單一</span><span class="sxs-lookup"><span data-stu-id="775db-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="775db-298">暫時性</span><span class="sxs-lookup"><span data-stu-id="775db-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="775db-299">單一</span><span class="sxs-lookup"><span data-stu-id="775db-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="775db-300">單一</span><span class="sxs-lookup"><span data-stu-id="775db-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="775db-301">單一</span><span class="sxs-lookup"><span data-stu-id="775db-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="775db-302">其他資源</span><span class="sxs-lookup"><span data-stu-id="775db-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="775db-303">適用于 DI 應用程式開發的 NDC 會議模式</span><span class="sxs-lookup"><span data-stu-id="775db-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="775db-304">在 ASP.NET Core 中處置 IDisposables 的四種方式</span><span class="sxs-lookup"><span data-stu-id="775db-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="775db-305">在 ASP.NET Core 使用 Dependency Injection 撰寫簡潔的程式碼 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="775db-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="775db-306">明確相依性準則</span><span class="sxs-lookup"><span data-stu-id="775db-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="775db-307">[逆轉控制容器和相依性插入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="775db-307">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* <span data-ttu-id="775db-308">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (如何使用 ASP.NET Core DI 中的多個介面註冊服務)</span><span class="sxs-lookup"><span data-stu-id="775db-308">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="775db-309">[Steve Smith](https://ardalis.com/)、 [Scott Addie](https://scottaddie.com)和[Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="775db-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="775db-310">ASP.NET Core 支援相依性插入 (DI) 軟體設計模式，這是在類別及其相依性之間達成[控制反轉 (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 的技術。</span><span class="sxs-lookup"><span data-stu-id="775db-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="775db-311">如需有關 MVC 控制器內相依性插入的特定詳細資訊，請參閱 <xref:mvc/controllers/dependency-injection>。</span><span class="sxs-lookup"><span data-stu-id="775db-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="775db-312">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="775db-312">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="775db-313">相依性插入概觀</span><span class="sxs-lookup"><span data-stu-id="775db-313">Overview of dependency injection</span></span>

<span data-ttu-id="775db-314">「相依性」是另一個物件所需的任何物件。</span><span class="sxs-lookup"><span data-stu-id="775db-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="775db-315">檢查具有 `WriteMessage` 方法的下列 `MyDependency` 物件，應用程式中的其他類別相依於此物件：</span><span class="sxs-lookup"><span data-stu-id="775db-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="775db-316">您可以建立 `MyDependency` 類別的執行個體，讓 `WriteMessage` 方法可供類別使用。</span><span class="sxs-lookup"><span data-stu-id="775db-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="775db-317">`MyDependency` 類別是 `IndexModel` 類別的相依性：</span><span class="sxs-lookup"><span data-stu-id="775db-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="775db-318">該類別會建立 `MyDependency` 執行個體並直接相依於該執行個體。</span><span class="sxs-lookup"><span data-stu-id="775db-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="775db-319">程式碼相依性 (例如前一個範例) 有問題，因此基於下列原因應該避免使用：</span><span class="sxs-lookup"><span data-stu-id="775db-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="775db-320">若要將 `MyDependency` 取代為不同的實作，必須修改該類別。</span><span class="sxs-lookup"><span data-stu-id="775db-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="775db-321">若 `MyDependency` 有相依性，那些相依性必須由該類別設定。</span><span class="sxs-lookup"><span data-stu-id="775db-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="775db-322">在具有多個相依於 `MyDependency` 之多個類別的大型專案中，設定程式碼在不同的應用程式之間會變得鬆散。</span><span class="sxs-lookup"><span data-stu-id="775db-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="775db-323">此實作難以進行單元測試。</span><span class="sxs-lookup"><span data-stu-id="775db-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="775db-324">應用程式應該使用模擬 (Mock) 或虛設常式 (Stub) `MyDependency` 類別，這在使用此方法時無法使用。</span><span class="sxs-lookup"><span data-stu-id="775db-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="775db-325">相依性插入可透過下列方式解決這些問題：</span><span class="sxs-lookup"><span data-stu-id="775db-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="775db-326">使用介面或基底類別來將相依性資訊抽象化。</span><span class="sxs-lookup"><span data-stu-id="775db-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="775db-327">在服務容器中註冊相依性。</span><span class="sxs-lookup"><span data-stu-id="775db-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="775db-328">ASP.NET Core 提供內建服務容器 <xref:System.IServiceProvider>。</span><span class="sxs-lookup"><span data-stu-id="775db-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="775db-329">服務會在應用程式的 `Startup.ConfigureServices` 方法中註冊。</span><span class="sxs-lookup"><span data-stu-id="775db-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="775db-330">將服務「插入」到服務使用位置之類別的建構函式。</span><span class="sxs-lookup"><span data-stu-id="775db-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="775db-331">架構會負責建立相依性的執行個體，並在不再需要時將它捨棄。</span><span class="sxs-lookup"><span data-stu-id="775db-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="775db-332">在[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples)中，`IMyDependency` 介面定義了服務提供給應用程式的方法：</span><span class="sxs-lookup"><span data-stu-id="775db-332">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="775db-333">這個介面是由具象型別 `MyDependency` 所實作：</span><span class="sxs-lookup"><span data-stu-id="775db-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="775db-334">`MyDependency` 在其建構函式中會要求 <xref:Microsoft.Extensions.Logging.ILogger`1>。</span><span class="sxs-lookup"><span data-stu-id="775db-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="775db-335">以鏈結方式使用相依性插入並非不尋常。</span><span class="sxs-lookup"><span data-stu-id="775db-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="775db-336">每個要求的相依性接著會要求其自己的相依性。</span><span class="sxs-lookup"><span data-stu-id="775db-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="775db-337">容器會解決圖形中的相依性，並傳回完全解析的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="775db-338">必須先解析的相依性集合組通常稱為「相依性樹狀結構」、「相依性圖形」或「物件圖形」。</span><span class="sxs-lookup"><span data-stu-id="775db-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="775db-339">`IMyDependency` 與 `ILogger<TCategoryName>` 必須在服務容器中註冊。</span><span class="sxs-lookup"><span data-stu-id="775db-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="775db-340">`IMyDependency` 是在 `Startup.ConfigureServices` 中註冊。</span><span class="sxs-lookup"><span data-stu-id="775db-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="775db-341">`ILogger<TCategoryName>` 是由記錄抽象基礎結構所註冊，所以它是預設由架構所註冊的[架構提供的服務](#framework-provided-services)。</span><span class="sxs-lookup"><span data-stu-id="775db-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="775db-342">容器會利用 [(泛型) 開放類型](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) 解析 `ILogger<TCategoryName>`，讓您不需註冊每個 [(泛型) 建構的型別](/dotnet/csharp/language-reference/language-specification/types#constructed-types)：</span><span class="sxs-lookup"><span data-stu-id="775db-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="775db-343">在範例應用程式中，`IMyDependency` 服務是使用具象型別 `MyDependency` 所註冊。</span><span class="sxs-lookup"><span data-stu-id="775db-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="775db-344">註冊會將服務的存留期範圍限制為單一要求的存留期。</span><span class="sxs-lookup"><span data-stu-id="775db-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="775db-345">將在此主題稍後將說明[服務存留期](#service-lifetimes)。</span><span class="sxs-lookup"><span data-stu-id="775db-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="775db-346">每個 `services.Add{SERVICE_NAME}` 擴充方法會新增並可能設定服務。</span><span class="sxs-lookup"><span data-stu-id="775db-346">Each `services.Add{SERVICE_NAME}` extension method adds, and potentially configures, services.</span></span> <span data-ttu-id="775db-347">例如，、 `services.AddControllersWithViews` `services.AddRazorPages` 和會 `services.AddControllers` 新增 ASP.NET Core 應用程式所需的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-347">For example, `services.AddControllersWithViews`, `services.AddRazorPages`, and `services.AddControllers` adds the services ASP.NET Core apps require.</span></span> <span data-ttu-id="775db-348">建議應用程式遵循此慣例。</span><span class="sxs-lookup"><span data-stu-id="775db-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="775db-349">在 <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 命名空間中放置擴充方法，以封裝服務註冊群組。</span><span class="sxs-lookup"><span data-stu-id="775db-349">Place extension methods in the <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> namespace to encapsulate groups of service registrations.</span></span> <span data-ttu-id="775db-350">包括 DI 擴充方法的命名空間部分， `Microsoft.Extensions.DependencyInjection` 也包括：</span><span class="sxs-lookup"><span data-stu-id="775db-350">Including the namespace portion `Microsoft.Extensions.DependencyInjection` for DI extension methods also:</span></span>
>
> * <span data-ttu-id="775db-351">可讓它們在 [IntelliSense](/visualstudio/ide/using-intellisense) 中顯示，而不需要新增其他 `using` 區塊。</span><span class="sxs-lookup"><span data-stu-id="775db-351">Allows them to be displayed in [IntelliSense](/visualstudio/ide/using-intellisense) without adding additional `using` blocks.</span></span>
> * <span data-ttu-id="775db-352">防止類別中過多的 `using` 語句 `Startup` ，通常會從這些擴充方法呼叫這些擴充方法。</span><span class="sxs-lookup"><span data-stu-id="775db-352">Prevents excessive `using` statements in the `Startup` class where these extension methods are typically called from.</span></span>

<span data-ttu-id="775db-353">如果服務的建構函式需要[內建類型](/dotnet/csharp/language-reference/keywords/built-in-types-table)，例如 `string`，可以使用[組態](xref:fundamentals/configuration/index)或[選項模式](xref:fundamentals/configuration/options)插入該類型：</span><span class="sxs-lookup"><span data-stu-id="775db-353">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="775db-354">服務執行個體是透過服務使用所在之類別建構函式所要求，而且會指派給私人欄位。</span><span class="sxs-lookup"><span data-stu-id="775db-354">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="775db-355">該欄位會視需要用來存取整個類別中的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-355">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="775db-356">在範例應用程式中，會要求 `IMyDependency` 執行個體並使用它來呼叫服務的 `WriteMessage` 方法：</span><span class="sxs-lookup"><span data-stu-id="775db-356">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="775db-357">插入啟動的服務</span><span class="sxs-lookup"><span data-stu-id="775db-357">Services injected into Startup</span></span>

<span data-ttu-id="775db-358">`Startup`使用泛型主機 () 時，只可將下列服務類型插入至函式 <xref:Microsoft.Extensions.Hosting.IHostBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="775db-358">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="775db-359">服務可以插入至 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="775db-359">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="775db-360">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="775db-360">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="775db-361">架構提供的服務</span><span class="sxs-lookup"><span data-stu-id="775db-361">Framework-provided services</span></span>

<span data-ttu-id="775db-362">`Startup.ConfigureServices`方法負責定義應用程式所使用的服務，包括平臺功能，例如 Entity Framework Core 和 ASP.NET CORE MVC。</span><span class="sxs-lookup"><span data-stu-id="775db-362">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="775db-363">一開始， `IServiceCollection` 提供給的是 `ConfigureServices` 由架構定義的服務，視 [主機的設定方式](xref:fundamentals/index#host)而定。</span><span class="sxs-lookup"><span data-stu-id="775db-363">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="775db-364">以 ASP.NET Core 範本為基礎的應用程式在架構中註冊了數百項服務並不罕見。</span><span class="sxs-lookup"><span data-stu-id="775db-364">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="775db-365">下表列出架構註冊服務的小型範例。</span><span class="sxs-lookup"><span data-stu-id="775db-365">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="775db-366">服務類型</span><span class="sxs-lookup"><span data-stu-id="775db-366">Service Type</span></span> | <span data-ttu-id="775db-367">存留期</span><span class="sxs-lookup"><span data-stu-id="775db-367">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="775db-368">暫時性</span><span class="sxs-lookup"><span data-stu-id="775db-368">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="775db-369">單一</span><span class="sxs-lookup"><span data-stu-id="775db-369">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="775db-370">單一</span><span class="sxs-lookup"><span data-stu-id="775db-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="775db-371">單一</span><span class="sxs-lookup"><span data-stu-id="775db-371">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="775db-372">暫時性</span><span class="sxs-lookup"><span data-stu-id="775db-372">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="775db-373">單一</span><span class="sxs-lookup"><span data-stu-id="775db-373">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="775db-374">暫時性</span><span class="sxs-lookup"><span data-stu-id="775db-374">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="775db-375">單一</span><span class="sxs-lookup"><span data-stu-id="775db-375">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="775db-376">單一</span><span class="sxs-lookup"><span data-stu-id="775db-376">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="775db-377">單一</span><span class="sxs-lookup"><span data-stu-id="775db-377">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="775db-378">暫時性</span><span class="sxs-lookup"><span data-stu-id="775db-378">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="775db-379">單一</span><span class="sxs-lookup"><span data-stu-id="775db-379">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="775db-380">單一</span><span class="sxs-lookup"><span data-stu-id="775db-380">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="775db-381">單一</span><span class="sxs-lookup"><span data-stu-id="775db-381">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="775db-382">使用擴充方法註冊其他服務</span><span class="sxs-lookup"><span data-stu-id="775db-382">Register additional services with extension methods</span></span>

<span data-ttu-id="775db-383">當可以使用服務集合擴充方法來註冊服務 (如果需要，也可以註冊其相依服務) 時，慣例是使用單一 `Add{SERVICE_NAME}` 擴充方法來註冊該服務要求的所有服務。</span><span class="sxs-lookup"><span data-stu-id="775db-383">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="775db-384">下列程式碼範例示範如何使用擴充方法[AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)和來將其他服務新增至容器 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> ：</span><span class="sxs-lookup"><span data-stu-id="775db-384">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="775db-385">如需詳細資訊，請參閱 API 文件中的 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> 類別。</span><span class="sxs-lookup"><span data-stu-id="775db-385">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="775db-386">服務存留期</span><span class="sxs-lookup"><span data-stu-id="775db-386">Service lifetimes</span></span>

<span data-ttu-id="775db-387">為每個已註冊的服務選擇適當的存留期。</span><span class="sxs-lookup"><span data-stu-id="775db-387">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="775db-388">ASP.NET Core 服務可以使用下列存留期進行設定：</span><span class="sxs-lookup"><span data-stu-id="775db-388">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="775db-389">暫時性</span><span class="sxs-lookup"><span data-stu-id="775db-389">Transient</span></span>

<span data-ttu-id="775db-390">每次從服務容器要求暫時性存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) 時都會建立它們。</span><span class="sxs-lookup"><span data-stu-id="775db-390">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="775db-391">此存留期最適合用於輕量型的無狀態服務。</span><span class="sxs-lookup"><span data-stu-id="775db-391">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="775db-392">在處理要求的應用程式中，會在要求結束時處置暫時性服務。</span><span class="sxs-lookup"><span data-stu-id="775db-392">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="775db-393">具範圍</span><span class="sxs-lookup"><span data-stu-id="775db-393">Scoped</span></span>

<span data-ttu-id="775db-394">具範圍存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) 會在每次用戶端要求 (連線) 時建立一次。</span><span class="sxs-lookup"><span data-stu-id="775db-394">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="775db-395">在處理要求的應用程式中，會在要求結束時處置範圍服務。</span><span class="sxs-lookup"><span data-stu-id="775db-395">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="775db-396">在中介軟體中使用具範圍服務時，請將該服務插入 `Invoke` 或 `InvokeAsync` 方法中。</span><span class="sxs-lookup"><span data-stu-id="775db-396">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="775db-397">不要插入 via 函式 [插入](xref:mvc/controllers/dependency-injection#constructor-injection) ，因為它會強制服務的行為就像 singleton 一樣。</span><span class="sxs-lookup"><span data-stu-id="775db-397">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="775db-398">如需詳細資訊，請參閱<xref:fundamentals/middleware/write#per-request-middleware-dependencies>。</span><span class="sxs-lookup"><span data-stu-id="775db-398">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="775db-399">單一</span><span class="sxs-lookup"><span data-stu-id="775db-399">Singleton</span></span>

<span data-ttu-id="775db-400">當第一次收到有關單一資料庫存留期服務的要求時 (或是當執行 `Startup.ConfigureServices` 且隨著服務註冊指定執行個體時)，即會建立單一資料庫存留期服務 (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>)。</span><span class="sxs-lookup"><span data-stu-id="775db-400">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="775db-401">每個後續要求都會使用相同的執行個體。</span><span class="sxs-lookup"><span data-stu-id="775db-401">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="775db-402">若應用程式要求單一資料庫行為，建議您允許服務容器管理服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="775db-402">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="775db-403">不要實作單一資料庫設計模式並為使用者提供可用來管理類別中物件存留期的程式碼。</span><span class="sxs-lookup"><span data-stu-id="775db-403">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="775db-404">在處理要求的應用程式中，會在應用程式關閉時處置單一服務 <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> 。</span><span class="sxs-lookup"><span data-stu-id="775db-404">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="775db-405">從單一資料庫解析具範圍服務是非常危險的。</span><span class="sxs-lookup"><span data-stu-id="775db-405">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="775db-406">處理後續要求時，它可能會導致服務有不正確的狀態。</span><span class="sxs-lookup"><span data-stu-id="775db-406">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="775db-407">服務註冊方法</span><span class="sxs-lookup"><span data-stu-id="775db-407">Service registration methods</span></span>

<span data-ttu-id="775db-408">服務註冊擴充方法提供在特定案例中很有用的多載。</span><span class="sxs-lookup"><span data-stu-id="775db-408">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="775db-409">方法</span><span class="sxs-lookup"><span data-stu-id="775db-409">Method</span></span> | <span data-ttu-id="775db-410">自動</span><span class="sxs-lookup"><span data-stu-id="775db-410">Automatic</span></span><br><span data-ttu-id="775db-411">物件 (object)</span><span class="sxs-lookup"><span data-stu-id="775db-411">object</span></span><br><span data-ttu-id="775db-412">處置</span><span class="sxs-lookup"><span data-stu-id="775db-412">disposal</span></span> | <span data-ttu-id="775db-413">多個</span><span class="sxs-lookup"><span data-stu-id="775db-413">Multiple</span></span><br><span data-ttu-id="775db-414">實作</span><span class="sxs-lookup"><span data-stu-id="775db-414">implementations</span></span> | <span data-ttu-id="775db-415">傳遞引數</span><span class="sxs-lookup"><span data-stu-id="775db-415">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="775db-416">範例：</span><span class="sxs-lookup"><span data-stu-id="775db-416">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="775db-417">是</span><span class="sxs-lookup"><span data-stu-id="775db-417">Yes</span></span> | <span data-ttu-id="775db-418">是</span><span class="sxs-lookup"><span data-stu-id="775db-418">Yes</span></span> | <span data-ttu-id="775db-419">否</span><span class="sxs-lookup"><span data-stu-id="775db-419">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="775db-420">範例：</span><span class="sxs-lookup"><span data-stu-id="775db-420">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="775db-421">是</span><span class="sxs-lookup"><span data-stu-id="775db-421">Yes</span></span> | <span data-ttu-id="775db-422">是</span><span class="sxs-lookup"><span data-stu-id="775db-422">Yes</span></span> | <span data-ttu-id="775db-423">是</span><span class="sxs-lookup"><span data-stu-id="775db-423">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="775db-424">範例：</span><span class="sxs-lookup"><span data-stu-id="775db-424">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="775db-425">是</span><span class="sxs-lookup"><span data-stu-id="775db-425">Yes</span></span> | <span data-ttu-id="775db-426">否</span><span class="sxs-lookup"><span data-stu-id="775db-426">No</span></span> | <span data-ttu-id="775db-427">否</span><span class="sxs-lookup"><span data-stu-id="775db-427">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="775db-428">範例：</span><span class="sxs-lookup"><span data-stu-id="775db-428">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="775db-429">否</span><span class="sxs-lookup"><span data-stu-id="775db-429">No</span></span> | <span data-ttu-id="775db-430">是</span><span class="sxs-lookup"><span data-stu-id="775db-430">Yes</span></span> | <span data-ttu-id="775db-431">是</span><span class="sxs-lookup"><span data-stu-id="775db-431">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="775db-432">範例：</span><span class="sxs-lookup"><span data-stu-id="775db-432">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="775db-433">否</span><span class="sxs-lookup"><span data-stu-id="775db-433">No</span></span> | <span data-ttu-id="775db-434">否</span><span class="sxs-lookup"><span data-stu-id="775db-434">No</span></span> | <span data-ttu-id="775db-435">是</span><span class="sxs-lookup"><span data-stu-id="775db-435">Yes</span></span> |

<span data-ttu-id="775db-436">如需類型處置的詳細資訊，請參閱[＜服務處置＞](#disposal-of-services)一節。</span><span class="sxs-lookup"><span data-stu-id="775db-436">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="775db-437">多個實作的常見案例是[模擬測試類型](xref:test/integration-tests#inject-mock-services)。</span><span class="sxs-lookup"><span data-stu-id="775db-437">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="775db-438">註冊只有實作為型別的服務，相當於向相同的實和服務型別註冊該服務。</span><span class="sxs-lookup"><span data-stu-id="775db-438">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="775db-439">這就是為什麼無法使用未採用明確服務類型的方法來註冊多個服務的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-439">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="775db-440">這些方法可以註冊服務的多個 *實例* ，但它們都有相同的 *實* 作為型別。</span><span class="sxs-lookup"><span data-stu-id="775db-440">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="775db-441">您可以使用上述任何一種服務註冊方法來註冊相同服務類型的多個服務實例。</span><span class="sxs-lookup"><span data-stu-id="775db-441">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="775db-442">在下列範例中， `AddSingleton` 使用 `IMyDependency` 做為服務類型呼叫兩次。</span><span class="sxs-lookup"><span data-stu-id="775db-442">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="775db-443">第二個呼叫會 `AddSingleton` 在解析為時覆寫上一個 `IMyDependency` ，並在透過來解析多個服務時加入至上一個 `IEnumerable<IMyDependency>` 。</span><span class="sxs-lookup"><span data-stu-id="775db-443">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="775db-444">服務會依照其在解析時所註冊的順序顯示 `IEnumerable<{SERVICE}>` 。</span><span class="sxs-lookup"><span data-stu-id="775db-444">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

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

<span data-ttu-id="775db-445">此架構也會提供 `TryAdd{LIFETIME}` 擴充方法，只有在尚未註冊任何執行時，才會註冊服務。</span><span class="sxs-lookup"><span data-stu-id="775db-445">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="775db-446">在下列範例中，呼叫以註冊為的 `AddSingleton` `MyDependency` 實作為的 `IMyDependency` 。</span><span class="sxs-lookup"><span data-stu-id="775db-446">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="775db-447">的呼叫 `TryAddSingleton` 沒有任何作用，因為 `IMyDependency` 已經有已註冊的實作為。</span><span class="sxs-lookup"><span data-stu-id="775db-447">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

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

<span data-ttu-id="775db-448">如需詳細資訊，請參閱</span><span class="sxs-lookup"><span data-stu-id="775db-448">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="775db-449">如果還沒有「相同類型的」實作，則 [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) 方法只會註冊服務。</span><span class="sxs-lookup"><span data-stu-id="775db-449">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="775db-450">多個服務會透過 `IEnumerable<{SERVICE}>` 解析。</span><span class="sxs-lookup"><span data-stu-id="775db-450">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="775db-451">註冊服務時，如果尚未新增相同類型的執行個體，則開發人員只會希望新增執行個體。</span><span class="sxs-lookup"><span data-stu-id="775db-451">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="775db-452">程式庫作者通常會使用此方法來避免註冊容器中某個執行個體的兩個複本。</span><span class="sxs-lookup"><span data-stu-id="775db-452">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="775db-453">在下列範例中，第一行會為 `IMyDep1` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="775db-453">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="775db-454">第二行會為 `IMyDep2` 註冊 `MyDep`。</span><span class="sxs-lookup"><span data-stu-id="775db-454">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="775db-455">第三行則沒有任何作用，因為 `IMyDep1` 已具有 `MyDep` 的已註冊實作：</span><span class="sxs-lookup"><span data-stu-id="775db-455">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="775db-456">建構函式插入行為</span><span class="sxs-lookup"><span data-stu-id="775db-456">Constructor injection behavior</span></span>

<span data-ttu-id="775db-457">服務可以透過兩個機制來解析：</span><span class="sxs-lookup"><span data-stu-id="775db-457">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="775db-458"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>：允許在相依性插入容器中建立物件，而不註冊服務。</span><span class="sxs-lookup"><span data-stu-id="775db-458"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="775db-459">搭配使用者面向抽象 (例如標籤協助程式、MVC 控制器與模型繫結器) 使用 `ActivatorUtilities`。</span><span class="sxs-lookup"><span data-stu-id="775db-459">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="775db-460">建構函式可以接受不是由相依性插入提供的引數，但引數必須指派預設值。</span><span class="sxs-lookup"><span data-stu-id="775db-460">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="775db-461">當或解決服務時 `IServiceProvider` `ActivatorUtilities` ，必須使用 *公用* 的函式來 [插入](xref:mvc/controllers/dependency-injection#constructor-injection)。</span><span class="sxs-lookup"><span data-stu-id="775db-461">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="775db-462">當服務解析時，函式 `ActivatorUtilities` [插入](xref:mvc/controllers/dependency-injection#constructor-injection) 會要求只能有一個適用的函式。</span><span class="sxs-lookup"><span data-stu-id="775db-462">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="775db-463">支援建構函式多載，但只能有一個多載存在，其引數可以藉由相依性插入而完成。</span><span class="sxs-lookup"><span data-stu-id="775db-463">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="775db-464">Entity Framework 內容</span><span class="sxs-lookup"><span data-stu-id="775db-464">Entity Framework contexts</span></span>

<span data-ttu-id="775db-465">因為一般會將 Web 應用程式資料庫作業範圍設定為用戶端要求，所以通常會使用[具範圍存留期](#service-lifetimes)將 Entity Framework 內容新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="775db-465">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="775db-466">如果在註冊資料庫內容時， [AddDbCoNtext \<TContext> ](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)多載未指定存留期，則預設存留期會限定範圍。</span><span class="sxs-lookup"><span data-stu-id="775db-466">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="775db-467">指定存留期的服務不應該使用存留期比服務還短的資料庫內容。</span><span class="sxs-lookup"><span data-stu-id="775db-467">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="775db-468">留期和註冊選項</span><span class="sxs-lookup"><span data-stu-id="775db-468">Lifetime and registration options</span></span>

<span data-ttu-id="775db-469">為了示範這些存留期和註冊選項之間的差異，請考慮下列介面，以具有唯一識別碼 `OperationId` 的「作業」代表工作。</span><span class="sxs-lookup"><span data-stu-id="775db-469">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="775db-470">視如何針對下列介面設定作業服務存留期而定，當類別要求時，容器會提供相同或不同的服務執行個體：</span><span class="sxs-lookup"><span data-stu-id="775db-470">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="775db-471">介面是在 `Operation` 類別中實作。</span><span class="sxs-lookup"><span data-stu-id="775db-471">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="775db-472">若未提供 GUID，`Operation` 建構函式會產生 GUID：</span><span class="sxs-lookup"><span data-stu-id="775db-472">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="775db-473">會註冊相依於每種其他 `Operation` 類型的 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="775db-473">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="775db-474">當透過相依性插入要求 `OperationService` 時，它會根據相依服務的存留期擷取每個服務的新執行個體或現有執行個體。</span><span class="sxs-lookup"><span data-stu-id="775db-474">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="775db-475">當因收到來自容器的要求而建立暫時性服務時，`IOperationTransient` 服務的 `OperationId` 會與 `OperationService` 的 `OperationId` 不同。</span><span class="sxs-lookup"><span data-stu-id="775db-475">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="775db-476">`OperationService` 會收到 `IOperationTransient` 類別的新執行個體。</span><span class="sxs-lookup"><span data-stu-id="775db-476">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="775db-477">新執行個體會產生不同的 `OperationId`。</span><span class="sxs-lookup"><span data-stu-id="775db-477">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="775db-478">當因用戶端要求而建立具範圍的服務時，`IOperationScoped` 服務與用戶端要求中 `OperationService` 的 `OperationId` 皆相同。</span><span class="sxs-lookup"><span data-stu-id="775db-478">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="775db-479">在不同的用戶端要求中，兩個服務的 `OperationId` 值都不同。</span><span class="sxs-lookup"><span data-stu-id="775db-479">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="775db-480">當建立一次 singleton 與 singleton 執行個體服務，並在所有用戶端要求與所有服務中使用時，`OperationId` 在所有服務要求中會固定不變。</span><span class="sxs-lookup"><span data-stu-id="775db-480">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="775db-481">在 `Startup.ConfigureServices` 中，每個類型都會根據其具名存留期新增至容器：</span><span class="sxs-lookup"><span data-stu-id="775db-481">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="775db-482">`IOperationSingletonInstance` 服務使用具有 `Guid.Empty` 已知識別碼的特定執行個體。</span><span class="sxs-lookup"><span data-stu-id="775db-482">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="775db-483">很明顯此型別是使用中 (其 GUID 是所有區域)。</span><span class="sxs-lookup"><span data-stu-id="775db-483">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="775db-484">範例應用程式示範個別要求內與個別要求之間的物件存留期。</span><span class="sxs-lookup"><span data-stu-id="775db-484">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="775db-485">範例應用程式的 `IndexModel` 會要求每種 `IOperation` 型別與 `OperationService`。</span><span class="sxs-lookup"><span data-stu-id="775db-485">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="775db-486">頁面接著會透過屬性指派來顯示所有頁面模型類別與服務的 `OperationId` 值：</span><span class="sxs-lookup"><span data-stu-id="775db-486">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="775db-487">下面兩個輸出顯示兩個要求的結果：</span><span class="sxs-lookup"><span data-stu-id="775db-487">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="775db-488">**第一個要求：**</span><span class="sxs-lookup"><span data-stu-id="775db-488">**First request:**</span></span>

<span data-ttu-id="775db-489">控制器作業：</span><span class="sxs-lookup"><span data-stu-id="775db-489">Controller operations:</span></span>

<span data-ttu-id="775db-490">暫時性： d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="775db-490">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="775db-491">具範圍： 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="775db-491">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="775db-492">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="775db-492">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="775db-493">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="775db-493">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="775db-494">`OperationService` 作業：</span><span class="sxs-lookup"><span data-stu-id="775db-494">`OperationService` operations:</span></span>

<span data-ttu-id="775db-495">暫時性： c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="775db-495">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="775db-496">具範圍： 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="775db-496">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="775db-497">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="775db-497">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="775db-498">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="775db-498">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="775db-499">**:第二個要求：**</span><span class="sxs-lookup"><span data-stu-id="775db-499">**Second request:**</span></span>

<span data-ttu-id="775db-500">控制器作業：</span><span class="sxs-lookup"><span data-stu-id="775db-500">Controller operations:</span></span>

<span data-ttu-id="775db-501">暫時性： b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="775db-501">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="775db-502">具範圍： 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="775db-502">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="775db-503">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="775db-503">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="775db-504">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="775db-504">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="775db-505">`OperationService` 作業：</span><span class="sxs-lookup"><span data-stu-id="775db-505">`OperationService` operations:</span></span>

<span data-ttu-id="775db-506">暫時性： c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="775db-506">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="775db-507">具範圍： 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="775db-507">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="775db-508">單一資料庫： 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="775db-508">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="775db-509">執行個體： 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="775db-509">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="775db-510">觀察哪些 `OperationId` 值在要求內以及要求之間不同：</span><span class="sxs-lookup"><span data-stu-id="775db-510">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="775db-511">「暫時性」 物件一律不同。</span><span class="sxs-lookup"><span data-stu-id="775db-511">*Transient* objects are always different.</span></span> <span data-ttu-id="775db-512">第一個與第二個用戶端要求的暫時性 `OperationId` 值在兩個 `OperationService` 作業之間與各用戶端要求之間都不同。</span><span class="sxs-lookup"><span data-stu-id="775db-512">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="775db-513">新執行個體會提供給每個服務要求以及用戶端要求。</span><span class="sxs-lookup"><span data-stu-id="775db-513">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="775db-514">「具範圍」物件在同一個用戶端要求內皆相同，但在不同的用戶端要求之間則不同。</span><span class="sxs-lookup"><span data-stu-id="775db-514">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="775db-515">無論中是否有提供實例，每個物件和每個要求的 *單一* 物件都會相同 `Operation` `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="775db-515">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="775db-516">從主要呼叫服務</span><span class="sxs-lookup"><span data-stu-id="775db-516">Call services from main</span></span>

<span data-ttu-id="775db-517">使用 [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) 建立 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>，以解析應用程式範圍中的範圍服務。</span><span class="sxs-lookup"><span data-stu-id="775db-517">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="775db-518">此法可用於在開機時存取範圍服務，以執行初始化工作。</span><span class="sxs-lookup"><span data-stu-id="775db-518">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="775db-519">下列範例示範如何在 `Program.Main` 中取得 `MyScopedService`：</span><span class="sxs-lookup"><span data-stu-id="775db-519">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="775db-520">範圍驗證</span><span class="sxs-lookup"><span data-stu-id="775db-520">Scope validation</span></span>

<span data-ttu-id="775db-521">當應用程式在開發環境中執行時，預設服務提供者會執行檢查以確認：</span><span class="sxs-lookup"><span data-stu-id="775db-521">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="775db-522">範圍服務不是直接或間接由開機服務提供者解析。</span><span class="sxs-lookup"><span data-stu-id="775db-522">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="775db-523">範圍服務不是直接或間接插入至單一服務。</span><span class="sxs-lookup"><span data-stu-id="775db-523">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="775db-524">根服務提供者會在呼叫 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> 時建立。</span><span class="sxs-lookup"><span data-stu-id="775db-524">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="775db-525">當提供者啟動應用程式時，根服務提供者的存留期與應用程式/伺服器的存留期一致，並會在應用程式關閉時處置。</span><span class="sxs-lookup"><span data-stu-id="775db-525">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="775db-526">範圍服務會由建立這些服務的容器處置。</span><span class="sxs-lookup"><span data-stu-id="775db-526">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="775db-527">若是在根容器中建立範圍服務，因為當應用程式/伺服器關機時，服務只會由根容器處理，所以服務的存留期會提升為單一服務等級。</span><span class="sxs-lookup"><span data-stu-id="775db-527">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="775db-528">當呼叫 `BuildServiceProvider` 時，驗證服務範圍會攔截到這些情況。</span><span class="sxs-lookup"><span data-stu-id="775db-528">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="775db-529">如需詳細資訊，請參閱<xref:fundamentals/host/web-host#scope-validation>。</span><span class="sxs-lookup"><span data-stu-id="775db-529">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="775db-530">要求服務</span><span class="sxs-lookup"><span data-stu-id="775db-530">Request Services</span></span>

<span data-ttu-id="775db-531">來自 `HttpContext`，在 ASP.NET Core 要求內提供的服務是透過 [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 集合公開。</span><span class="sxs-lookup"><span data-stu-id="775db-531">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="775db-532">要求服務代表您在應用程式中設定和要求的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-532">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="775db-533">當物件指定相依性時，這些會由在 `RequestServices` 中找到的類型來滿足，而非 `ApplicationServices`。</span><span class="sxs-lookup"><span data-stu-id="775db-533">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="775db-534">一般而言，應用程式不應該直接使用這些屬性。</span><span class="sxs-lookup"><span data-stu-id="775db-534">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="775db-535">相反地，透過類別建構函式要求類別所需的型別，以及允許架構插入相依性。</span><span class="sxs-lookup"><span data-stu-id="775db-535">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="775db-536">這會產生容易測試的類別。</span><span class="sxs-lookup"><span data-stu-id="775db-536">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="775db-537">偏好要求相依性作為建構函式參數，而不要存取 `RequestServices` 集合。</span><span class="sxs-lookup"><span data-stu-id="775db-537">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="775db-538">針對相依性插入設計服務</span><span class="sxs-lookup"><span data-stu-id="775db-538">Design services for dependency injection</span></span>

<span data-ttu-id="775db-539">最佳做法是：</span><span class="sxs-lookup"><span data-stu-id="775db-539">Best practices are to:</span></span>

* <span data-ttu-id="775db-540">設計服務以使用相依性插入來取得其相依性。</span><span class="sxs-lookup"><span data-stu-id="775db-540">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="775db-541">避免具狀態、靜態類別和成員。</span><span class="sxs-lookup"><span data-stu-id="775db-541">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="775db-542">請改為使用單一服務來設計應用程式，以避免建立全域狀態。</span><span class="sxs-lookup"><span data-stu-id="775db-542">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="775db-543">避免直接在服務內具現化相依類別。</span><span class="sxs-lookup"><span data-stu-id="775db-543">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="775db-544">直接具現化會將程式碼耦合到特定實作。</span><span class="sxs-lookup"><span data-stu-id="775db-544">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="775db-545">讓應用程式類別維持在小型、情況良好且可輕鬆測試的狀態。</span><span class="sxs-lookup"><span data-stu-id="775db-545">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="775db-546">若類別有太多插入的相依性，這通常表示類別有太多責任而且正違反[單一責任原則 (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="775db-546">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="775db-547">將類別負責的某些部分移到新的類別，以嘗試重構類別。</span><span class="sxs-lookup"><span data-stu-id="775db-547">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="775db-548">請記住， Razor 頁面頁面模型類別和 MVC 控制器類別應該專注于 UI 的考慮。</span><span class="sxs-lookup"><span data-stu-id="775db-548">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="775db-549">商務規則和資料存取實作詳細資料應該保存在適合這些[分開考量](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Separation of Concerns) 類別中。</span><span class="sxs-lookup"><span data-stu-id="775db-549">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="775db-550">處置服務</span><span class="sxs-lookup"><span data-stu-id="775db-550">Disposal of services</span></span>

<span data-ttu-id="775db-551">容器會為它建立的 <xref:System.IDisposable> 類型呼叫 <xref:System.IDisposable.Dispose*>。</span><span class="sxs-lookup"><span data-stu-id="775db-551">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="775db-552">若執行個體由使用者程式碼新增到容器中，它將不會自動處置。</span><span class="sxs-lookup"><span data-stu-id="775db-552">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="775db-553">在下列範例中，服務是由服務容器建立並自動處置：</span><span class="sxs-lookup"><span data-stu-id="775db-553">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="775db-554">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="775db-554">In the following example:</span></span>

* <span data-ttu-id="775db-555">服務實例不是由服務容器所建立。</span><span class="sxs-lookup"><span data-stu-id="775db-555">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="775db-556">架構不知道預期的服務存留期。</span><span class="sxs-lookup"><span data-stu-id="775db-556">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="775db-557">架構不會自動處置服務。</span><span class="sxs-lookup"><span data-stu-id="775db-557">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="775db-558">如果未在開發人員程式碼中明確處置服務，它們會保存到應用程式關閉為止。</span><span class="sxs-lookup"><span data-stu-id="775db-558">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="775db-559">暫時性和共用實例的 IDisposable 指引</span><span class="sxs-lookup"><span data-stu-id="775db-559">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="775db-560">暫時性、有限存留期</span><span class="sxs-lookup"><span data-stu-id="775db-560">Transient, limited lifetime</span></span>

<span data-ttu-id="775db-561">**案例**</span><span class="sxs-lookup"><span data-stu-id="775db-561">**Scenario**</span></span>

<span data-ttu-id="775db-562">應用程式需要在 <xref:System.IDisposable> 下列任一案例中具有暫時性存留期的實例：</span><span class="sxs-lookup"><span data-stu-id="775db-562">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="775db-563">此實例會在根範圍中解析。</span><span class="sxs-lookup"><span data-stu-id="775db-563">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="775db-564">實例應該在範圍結束之前處置。</span><span class="sxs-lookup"><span data-stu-id="775db-564">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="775db-565">**方案**</span><span class="sxs-lookup"><span data-stu-id="775db-565">**Solution**</span></span>

<span data-ttu-id="775db-566">使用 factory 模式，在父範圍之外建立實例。</span><span class="sxs-lookup"><span data-stu-id="775db-566">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="775db-567">在這種情況下，應用程式通常會有 `Create` 方法可直接呼叫最終型別的函式。</span><span class="sxs-lookup"><span data-stu-id="775db-567">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="775db-568">如果最終類型有其他相依性，factory 可以：</span><span class="sxs-lookup"><span data-stu-id="775db-568">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="775db-569"><xref:System.IServiceProvider>在其函式中接收。</span><span class="sxs-lookup"><span data-stu-id="775db-569">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="775db-570">使用 <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> 可在容器外部具現化實例，同時使用容器作為其相依性。</span><span class="sxs-lookup"><span data-stu-id="775db-570">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="775db-571">共用實例，有限存留期</span><span class="sxs-lookup"><span data-stu-id="775db-571">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="775db-572">**案例**</span><span class="sxs-lookup"><span data-stu-id="775db-572">**Scenario**</span></span>

<span data-ttu-id="775db-573">應用程式需要 <xref:System.IDisposable> 跨多個服務的共用實例，但是 <xref:System.IDisposable> 存留期應受限。</span><span class="sxs-lookup"><span data-stu-id="775db-573">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="775db-574">**方案**</span><span class="sxs-lookup"><span data-stu-id="775db-574">**Solution**</span></span>

<span data-ttu-id="775db-575">註冊具有範圍存留期的實例。</span><span class="sxs-lookup"><span data-stu-id="775db-575">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="775db-576">使用 <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> 啟動並建立新的 <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> 。</span><span class="sxs-lookup"><span data-stu-id="775db-576">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="775db-577">使用範圍 <xref:System.IServiceProvider> 來取得所需的服務。</span><span class="sxs-lookup"><span data-stu-id="775db-577">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="775db-578">在存留期結束時處置範圍。</span><span class="sxs-lookup"><span data-stu-id="775db-578">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="775db-579">一般準則</span><span class="sxs-lookup"><span data-stu-id="775db-579">General Guidelines</span></span>

* <span data-ttu-id="775db-580">請勿向 <xref:System.IDisposable> 暫時性範圍登錄實例。</span><span class="sxs-lookup"><span data-stu-id="775db-580">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="775db-581">請改用 factory 模式。</span><span class="sxs-lookup"><span data-stu-id="775db-581">Use the factory pattern instead.</span></span>
* <span data-ttu-id="775db-582">請勿在根範圍中解析暫時性或限定範圍 <xref:System.IDisposable> 的實例。</span><span class="sxs-lookup"><span data-stu-id="775db-582">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="775db-583">唯一的一般例外狀況是當應用程式建立/重新建立並處置時 <xref:System.IServiceProvider> ，這不是理想的模式。</span><span class="sxs-lookup"><span data-stu-id="775db-583">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="775db-584">透過 DI 接收相依性 <xref:System.IDisposable> 不需要接收者 <xref:System.IDisposable> 自行執行。</span><span class="sxs-lookup"><span data-stu-id="775db-584">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="775db-585">相依性的接收者不 <xref:System.IDisposable> 應呼叫 <xref:System.IDisposable.Dispose%2A> 該相依性。</span><span class="sxs-lookup"><span data-stu-id="775db-585">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="775db-586">範圍應該用來控制服務的存留期。</span><span class="sxs-lookup"><span data-stu-id="775db-586">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="775db-587">範圍不是階層式，而且範圍之間沒有特殊連接。</span><span class="sxs-lookup"><span data-stu-id="775db-587">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="775db-588">預設服務容器取代</span><span class="sxs-lookup"><span data-stu-id="775db-588">Default service container replacement</span></span>

<span data-ttu-id="775db-589">內建的服務容器是設計來滿足架構和大部分消費者應用程式的需求。</span><span class="sxs-lookup"><span data-stu-id="775db-589">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="775db-590">除非您需要內建容器不支援的特定功能，否則建議使用內建容器，例如：</span><span class="sxs-lookup"><span data-stu-id="775db-590">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="775db-591">屬性插入</span><span class="sxs-lookup"><span data-stu-id="775db-591">Property injection</span></span>
* <span data-ttu-id="775db-592">根據名稱插入</span><span class="sxs-lookup"><span data-stu-id="775db-592">Injection based on name</span></span>
* <span data-ttu-id="775db-593">子容器</span><span class="sxs-lookup"><span data-stu-id="775db-593">Child containers</span></span>
* <span data-ttu-id="775db-594">自訂生命週期管理</span><span class="sxs-lookup"><span data-stu-id="775db-594">Custom lifetime management</span></span>
* <span data-ttu-id="775db-595">`Func<T>` 支援延遲初始設定</span><span class="sxs-lookup"><span data-stu-id="775db-595">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="775db-596">以慣例為基礎的註冊</span><span class="sxs-lookup"><span data-stu-id="775db-596">Convention-based registration</span></span>

<span data-ttu-id="775db-597">下列協力廠商容器可與 ASP.NET Core 應用程式搭配使用：</span><span class="sxs-lookup"><span data-stu-id="775db-597">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="775db-598">Autofac</span><span class="sxs-lookup"><span data-stu-id="775db-598">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="775db-599">DryIoc</span><span class="sxs-lookup"><span data-stu-id="775db-599">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="775db-600">Grace</span><span class="sxs-lookup"><span data-stu-id="775db-600">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="775db-601">LightInject</span><span class="sxs-lookup"><span data-stu-id="775db-601">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="775db-602">拉馬爾</span><span class="sxs-lookup"><span data-stu-id="775db-602">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="775db-603">Stashbox</span><span class="sxs-lookup"><span data-stu-id="775db-603">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="775db-604">Unity</span><span class="sxs-lookup"><span data-stu-id="775db-604">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="775db-605">執行緒安全</span><span class="sxs-lookup"><span data-stu-id="775db-605">Thread safety</span></span>

<span data-ttu-id="775db-606">建立具備執行緒安全性的 singleton 服務。</span><span class="sxs-lookup"><span data-stu-id="775db-606">Create thread-safe singleton services.</span></span> <span data-ttu-id="775db-607">如果 singleton 服務相依於暫時性服務，暫時性服務可能也需要具備執行緒安全性，取決於 singleton 如何使用它。</span><span class="sxs-lookup"><span data-stu-id="775db-607">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="775db-608">單一服務的 factory 方法（例如 >addsingleton 的第二個自 [變數 \<TService> (IServiceCollection、Func \<IServiceProvider,TService>) ](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*)）不需要是安全線程。</span><span class="sxs-lookup"><span data-stu-id="775db-608">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="775db-609">就像型別 (`static`) 建構函式一樣，它一定會被單一執行緒呼叫一次。</span><span class="sxs-lookup"><span data-stu-id="775db-609">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="775db-610">建議</span><span class="sxs-lookup"><span data-stu-id="775db-610">Recommendations</span></span>

* <span data-ttu-id="775db-611">不支援以 `async/await` 與 `Task` 為基礎的服務解析。</span><span class="sxs-lookup"><span data-stu-id="775db-611">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="775db-612">C# 不支援非同步建構函式，因此建議的模式是以同步方式解析服務後使用非同步方法。</span><span class="sxs-lookup"><span data-stu-id="775db-612">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="775db-613">避免直接在服務容器中儲存資料與設定。</span><span class="sxs-lookup"><span data-stu-id="775db-613">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="775db-614">例如，使用者的購物車通常不應該新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="775db-614">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="775db-615">組態應該使用[選項模式](xref:fundamentals/configuration/options)。</span><span class="sxs-lookup"><span data-stu-id="775db-615">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="775db-616">同樣地，請避免只存在以允許存取某個其他物件的「資料持有者」物件。</span><span class="sxs-lookup"><span data-stu-id="775db-616">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="775db-617">最好是透過 DI 要求實際項目。</span><span class="sxs-lookup"><span data-stu-id="775db-617">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="775db-618">避免靜態存取服務。</span><span class="sxs-lookup"><span data-stu-id="775db-618">Avoid static access to services.</span></span> <span data-ttu-id="775db-619">例如，請避免以靜態方式輸入 [IApplicationBuilder >iapplicationbuilder.applicationservices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) ，以便在其他地方使用。</span><span class="sxs-lookup"><span data-stu-id="775db-619">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="775db-620">請避免使用 *服務定位器模式*，其混合 [了控制策略的反轉](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 。</span><span class="sxs-lookup"><span data-stu-id="775db-620">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="775db-621"><xref:System.IServiceProvider.GetService*>當您可以改用 DI 時，請勿叫用來取得服務實例：</span><span class="sxs-lookup"><span data-stu-id="775db-621">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="775db-622">**不正確：**</span><span class="sxs-lookup"><span data-stu-id="775db-622">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="775db-623">**正確**：</span><span class="sxs-lookup"><span data-stu-id="775db-623">**Correct**:</span></span>

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

* <span data-ttu-id="775db-624">避免插入在執行時間使用來解析相依性的 factory <xref:System.IServiceProvider.GetService*> 。</span><span class="sxs-lookup"><span data-stu-id="775db-624">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="775db-625">避免以靜態方式存取 `HttpContext` (例如 [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext))。</span><span class="sxs-lookup"><span data-stu-id="775db-625">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="775db-626">就像所有的建議集，您可能會遇到需要忽略建議的情況。</span><span class="sxs-lookup"><span data-stu-id="775db-626">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="775db-627">例外狀況很罕見，大多是架構本身內的特殊案例。</span><span class="sxs-lookup"><span data-stu-id="775db-627">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="775db-628">DI 是靜態/全域物件存取模式的「替代」選項。</span><span class="sxs-lookup"><span data-stu-id="775db-628">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="775db-629">如果您將 DI 與靜態物件存取混合，則可能無法實現 DI 的優點。</span><span class="sxs-lookup"><span data-stu-id="775db-629">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="775db-630">其他資源</span><span class="sxs-lookup"><span data-stu-id="775db-630">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="775db-631">在 ASP.NET Core 中處置 IDisposables 的四種方式</span><span class="sxs-lookup"><span data-stu-id="775db-631">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="775db-632">在 ASP.NET Core 使用 Dependency Injection 撰寫簡潔的程式碼 (MSDN)</span><span class="sxs-lookup"><span data-stu-id="775db-632">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="775db-633">明確相依性準則</span><span class="sxs-lookup"><span data-stu-id="775db-633">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="775db-634">[逆轉控制容器和相依性插入模式 (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="775db-634">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* <span data-ttu-id="775db-635">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/) (如何使用 ASP.NET Core DI 中的多個介面註冊服務)</span><span class="sxs-lookup"><span data-stu-id="775db-635">[How to register a service with multiple interfaces in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)</span></span>

::: moniker-end
