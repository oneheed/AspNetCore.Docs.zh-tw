---
title: ASP.NET Core 和 Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 和更新版本適用于 ASP.NET Core 3.1 和更新版本。
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/entity-framework-6
ms.openlocfilehash: 086418c161677f585b08ed360555c93d8575e701
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059451"
---
# <a name="aspnet-core-and-entity-framework-6"></a><span data-ttu-id="2abf7-103">ASP.NET Core 和 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="2abf7-103">ASP.NET Core and Entity Framework 6</span></span>
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2abf7-104">依 [派翠克 Goode](https://github.com/attrib75)</span><span class="sxs-lookup"><span data-stu-id="2abf7-104">By [Patrick Goode](https://github.com/attrib75)</span></span>

## <a name="using-entity-framework-6-with-aspnet-core"></a><span data-ttu-id="2abf7-105">搭配使用 Entity Framework 6 與 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2abf7-105">Using Entity Framework 6 with ASP.NET Core</span></span>

<span data-ttu-id="2abf7-106">[Entity Framework Core](/ef/) 應該用於新的開發。</span><span class="sxs-lookup"><span data-stu-id="2abf7-106">[Entity Framework Core](/ef/) should be used for new development.</span></span> <span data-ttu-id="2abf7-107">[下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample)使用[ENTITY FRAMEWORK 6 (EF6) ](/ef/ef6)，可用來將現有的應用程式遷移至 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="2abf7-107">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) uses [Entity Framework 6 (EF6)](/ef/ef6), which can be used to migrate exiting apps to ASP.NET Core.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2abf7-108">其他資源</span><span class="sxs-lookup"><span data-stu-id="2abf7-108">Additional resources</span></span>

* <span data-ttu-id="2abf7-109">[Entity Framework - Code-Based Configuration](/ef/ef6/fundamentals/configuring/code-based) (Entity Framework - 以程式碼為基礎的組態)</span><span class="sxs-lookup"><span data-stu-id="2abf7-109">[Entity Framework - Code-Based Configuration](/ef/ef6/fundamentals/configuring/code-based)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2abf7-110">作者：[Paweł Grudzień](https://github.com/pgrudzien12)、[Damien Pontifex](https://github.com/DamienPontifex) 和 [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="2abf7-110">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="2abf7-111">本文示範如何在 ASP.NET Core 應用程式中使用 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="2abf7-111">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>    

## <a name="overview"></a><span data-ttu-id="2abf7-112">概觀</span><span class="sxs-lookup"><span data-stu-id="2abf7-112">Overview</span></span> 

<span data-ttu-id="2abf7-113">若要使用 Entity Framework 6，您的專案必須針對 .NET Framework 進行編譯，因為 Entity Framework 6 不支援 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="2abf7-113">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="2abf7-114">如果您需要跨平台功能，則必須升級至 [Entity Framework Core](/ef/)。</span><span class="sxs-lookup"><span data-stu-id="2abf7-114">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>  

<span data-ttu-id="2abf7-115">在 ASP.NET Core 應用程式中使用 Entity Framework 6 的建議方式，是將 EF6 內容和模型類別放在以 .NET Framework 為目標的類別庫專案中。</span><span class="sxs-lookup"><span data-stu-id="2abf7-115">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="2abf7-116">從 ASP.NET Core 專案新增類別庫的參考。</span><span class="sxs-lookup"><span data-stu-id="2abf7-116">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="2abf7-117">請參閱[使用 EF6 和 ASP.NET Core 專案的 Visual Studio 方案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)範例。</span><span class="sxs-lookup"><span data-stu-id="2abf7-117">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>  

<span data-ttu-id="2abf7-118">您無法將 EF6 內容置於 ASP.NET Core 專案中，因為 .NET Core 專案不支援 EF6 命令 (例如 *Enable-Migrations* ) 需要的所有功能。</span><span class="sxs-lookup"><span data-stu-id="2abf7-118">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>    

<span data-ttu-id="2abf7-119">不論您放置 EF6 內容的專案類型為何，只有 EF6 命令列工具才適用於 EF6 內容。</span><span class="sxs-lookup"><span data-stu-id="2abf7-119">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="2abf7-120">例如，`Scaffold-DbContext` 僅可用於 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="2abf7-120">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="2abf7-121">如果您需要對資料庫進行反向工程以 EF6 模型，請參閱 <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database> 。</span><span class="sxs-lookup"><span data-stu-id="2abf7-121">If you need to do reverse engineering of a database into an EF6 model, see <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database>.</span></span>    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="2abf7-122">在 ASP.NET Core 專案中參考完整 Framework 和 EF6</span><span class="sxs-lookup"><span data-stu-id="2abf7-122">Reference full framework and EF6 in the ASP.NET Core project</span></span> 

<span data-ttu-id="2abf7-123">您的 ASP.NET Core 專案必須以 .NET Framework 和參考 EF6 為目標。</span><span class="sxs-lookup"><span data-stu-id="2abf7-123">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="2abf7-124">例如，ASP.NET Core 專案的 *.csproj* 檔案看起來類似下列範例 (只顯示檔案的相關部分)。</span><span class="sxs-lookup"><span data-stu-id="2abf7-124">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

<span data-ttu-id="2abf7-125">建立新專案時，請使用 **ASP.NET Core Web 應用程式 (.NET Framework)** 範本。</span><span class="sxs-lookup"><span data-stu-id="2abf7-125">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>    

## <a name="handle-connection-strings"></a><span data-ttu-id="2abf7-126">處理連接字串</span><span class="sxs-lookup"><span data-stu-id="2abf7-126">Handle connection strings</span></span>    

<span data-ttu-id="2abf7-127">您將在 EF6 類別庫專案中使用的 EF6 命令列工具需要預設建構函式，因此它們可以具現化內容。</span><span class="sxs-lookup"><span data-stu-id="2abf7-127">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="2abf7-128">但是，您可能想要指定連接字串以用於 ASP.NET Core 專案；在此情況下，內容建構函式必須要有可讓您以連接字串傳遞的參數。</span><span class="sxs-lookup"><span data-stu-id="2abf7-128">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="2abf7-129">以下為範例。</span><span class="sxs-lookup"><span data-stu-id="2abf7-129">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

<span data-ttu-id="2abf7-130">由於您的 EF6 內容沒有無參數的函式，因此您的 EF6 專案必須提供的實作為 <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> 。</span><span class="sxs-lookup"><span data-stu-id="2abf7-130">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>.</span></span> <span data-ttu-id="2abf7-131">EF6 命令列工具將尋找並使用該實作，因此它們可以具現化內容。</span><span class="sxs-lookup"><span data-stu-id="2abf7-131">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="2abf7-132">以下為範例。</span><span class="sxs-lookup"><span data-stu-id="2abf7-132">Here's an example.</span></span>   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

<span data-ttu-id="2abf7-133">在此範例程式碼中，`IDbContextFactory` 實作將以硬式編碼的連接字串傳遞。</span><span class="sxs-lookup"><span data-stu-id="2abf7-133">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="2abf7-134">這是命令列工具將使用的連接字串。</span><span class="sxs-lookup"><span data-stu-id="2abf7-134">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="2abf7-135">您需要實作策略，以確保類別庫使用的連接字串與呼叫端應用程式所使用的連接字串相同。</span><span class="sxs-lookup"><span data-stu-id="2abf7-135">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="2abf7-136">例如，您可以在這兩個專案中從環境變數取得值。</span><span class="sxs-lookup"><span data-stu-id="2abf7-136">For example, you could get the value from an environment variable in both projects.</span></span>   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="2abf7-137">設定 ASP.NET Core 專案中的相依性插入</span><span class="sxs-lookup"><span data-stu-id="2abf7-137">Set up dependency injection in the ASP.NET Core project</span></span>  

<span data-ttu-id="2abf7-138">在 Core 專案的 *Startup.cs* 檔案中，在 `ConfigureServices` 內設定相依性插入 (DI) 的 EF6 內容。</span><span class="sxs-lookup"><span data-stu-id="2abf7-138">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="2abf7-139">EF 內容物件的範圍應該設為每個要求的存留期。</span><span class="sxs-lookup"><span data-stu-id="2abf7-139">EF context objects should be scoped for a per-request lifetime.</span></span>   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

<span data-ttu-id="2abf7-140">然後，您就可以使用 DI，在控制器中取得內容的執行個體。</span><span class="sxs-lookup"><span data-stu-id="2abf7-140">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="2abf7-141">此程式碼類似於您將針對 EF Core 內容撰寫的程式碼：</span><span class="sxs-lookup"><span data-stu-id="2abf7-141">The code is similar to what you'd write for an EF Core context:</span></span>    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a><span data-ttu-id="2abf7-142">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="2abf7-142">Sample application</span></span>   

<span data-ttu-id="2abf7-143">如需可用的範例應用程式，請參閱本文所隨附的 [Visual Studio 方案範例 ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/)。</span><span class="sxs-lookup"><span data-stu-id="2abf7-143">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>  

<span data-ttu-id="2abf7-144">此範例可以透過 Visual Studio 中的下列步驟從頭開始建立：</span><span class="sxs-lookup"><span data-stu-id="2abf7-144">This sample can be created from scratch by the following steps in Visual Studio:</span></span>    

* <span data-ttu-id="2abf7-145">建立方案。</span><span class="sxs-lookup"><span data-stu-id="2abf7-145">Create a solution.</span></span>    

* <span data-ttu-id="2abf7-146">[新增] </span><span class="sxs-lookup"><span data-stu-id="2abf7-146">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>    
  * <span data-ttu-id="2abf7-147">在專案範本選取項目對話方塊中，選取下拉式清單中的 API 和.NET Framework</span><span class="sxs-lookup"><span data-stu-id="2abf7-147">In project template selection dialog, select API and .NET Framework in dropdown</span></span> 

* <span data-ttu-id="2abf7-148">[新增] </span><span class="sxs-lookup"><span data-stu-id="2abf7-148">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>  

* <span data-ttu-id="2abf7-149">在這兩個專案的 [套件管理員主控台]  (PMC) 中，執行 `Install-Package Entityframework` 命令。</span><span class="sxs-lookup"><span data-stu-id="2abf7-149">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>    

* <span data-ttu-id="2abf7-150">在類別庫專案中，建立資料模型類別和內容類別，以及 `IDbContextFactory` 的實作。</span><span class="sxs-lookup"><span data-stu-id="2abf7-150">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>    

* <span data-ttu-id="2abf7-151">在類別庫專案的 PMC 中，執行 `Enable-Migrations` 和 `Add-Migration Initial` 命令。</span><span class="sxs-lookup"><span data-stu-id="2abf7-151">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="2abf7-152">如果您已設定 ASP.NET Core 專案作為啟始專案，請將 `-StartupProjectName EF6` 新增至這些命令。</span><span class="sxs-lookup"><span data-stu-id="2abf7-152">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span> 

* <span data-ttu-id="2abf7-153">在 Core 專案中，將專案參考新增至類別庫專案中。</span><span class="sxs-lookup"><span data-stu-id="2abf7-153">In the Core project, add a project reference to the class library project.</span></span>    

* <span data-ttu-id="2abf7-154">在 Core 專案的 *Startup.cs* 中，登錄 DI 的內容。</span><span class="sxs-lookup"><span data-stu-id="2abf7-154">In the Core project, in *Startup.cs* , register the context for DI.</span></span>    

* <span data-ttu-id="2abf7-155">在 Core 專案的中， *:::no-loc(appsettings.json):::* 加入連接字串。</span><span class="sxs-lookup"><span data-stu-id="2abf7-155">In the Core project, in *:::no-loc(appsettings.json):::* , add the connection string.</span></span>  

* <span data-ttu-id="2abf7-156">在 Core 專案中，新增控制器和檢視，以確認您可以讀取和寫入資料。</span><span class="sxs-lookup"><span data-stu-id="2abf7-156">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="2abf7-157">(請注意，ASP.NET Core MVC Scaffolding 不會使用參考自類別庫的 EF6 內容。)</span><span class="sxs-lookup"><span data-stu-id="2abf7-157">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

::: moniker-end
