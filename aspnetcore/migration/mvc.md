---
title: 瞭解如何從 ASP.NET MVC 遷移至 ASP.NET Core MVC
author: wadepickett
description: 瞭解如何開始將 ASP.NET MVC 專案遷移至 ASP.NET Core MVC。
ms.author: wpickett
ms.date: 06/18/2020
no-loc:
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
uid: migration/mvc
ms.openlocfilehash: cd1a7ff57d911f96f0adfe4b548fa80ec844886d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632235"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="7a372-103">從 ASP.NET MVC 移轉至 ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="7a372-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a372-104">本文說明如何開始將 ASP.NET MVC 專案遷移至 [ASP.NET CORE mvc](xref:mvc/overview)。</span><span class="sxs-lookup"><span data-stu-id="7a372-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="7a372-105">在此程式中，它會反白顯示 ASP.NET MVC 的相關變更。</span><span class="sxs-lookup"><span data-stu-id="7a372-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="7a372-106">從 ASP.NET MVC 進行遷移是一個多步驟的程式。</span><span class="sxs-lookup"><span data-stu-id="7a372-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="7a372-107">本文將說明：</span><span class="sxs-lookup"><span data-stu-id="7a372-107">This article covers:</span></span>

* <span data-ttu-id="7a372-108">初始設定。</span><span class="sxs-lookup"><span data-stu-id="7a372-108">Initial setup.</span></span>
* <span data-ttu-id="7a372-109">基本控制器和 views。</span><span class="sxs-lookup"><span data-stu-id="7a372-109">Basic controllers and views.</span></span>
* <span data-ttu-id="7a372-110">靜態內容。</span><span class="sxs-lookup"><span data-stu-id="7a372-110">Static content.</span></span>
* <span data-ttu-id="7a372-111">用戶端相依性。</span><span class="sxs-lookup"><span data-stu-id="7a372-111">Client-side dependencies.</span></span>

<span data-ttu-id="7a372-112">如需遷移設定和程式 Identity 代碼，請參閱 [將設定遷移至 ASP.NET Core](xref:migration/configuration) 以及 [將驗證遷移至 Identity ASP.NET Core](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="7a372-112">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7a372-113">Prerequisites</span><span class="sxs-lookup"><span data-stu-id="7a372-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="7a372-114">建立 starter ASP.NET MVC 專案</span><span class="sxs-lookup"><span data-stu-id="7a372-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="7a372-115">在 Visual Studio 中建立 MVC 專案的範例以進行遷移：</span><span class="sxs-lookup"><span data-stu-id="7a372-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="7a372-116">從 [檔案]\*\*\*\* 功能表選取 [新增]**[專案]** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="7a372-116">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="7a372-117">選取 [ \*\*ASP.NET Web 應用程式 (] .NET Framework) \*\* 然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="7a372-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next**.</span></span>
1. <span data-ttu-id="7a372-118">將專案命名為 *WebApp1* ，讓命名空間符合下一個步驟中所建立的 ASP.NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="7a372-119">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="7a372-119">Select **Create**.</span></span>
1. <span data-ttu-id="7a372-120">選取 [ **MVC**]，然後選取 [ **建立**]。</span><span class="sxs-lookup"><span data-stu-id="7a372-120">Select **MVC**, and then select **Create**.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="7a372-121">建立 ASP.NET Core 專案</span><span class="sxs-lookup"><span data-stu-id="7a372-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="7a372-122">使用新的 ASP.NET Core 專案來建立新的方案，以遷移至：</span><span class="sxs-lookup"><span data-stu-id="7a372-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="7a372-123">啟動 Visual Studio 的第二個實例。</span><span class="sxs-lookup"><span data-stu-id="7a372-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="7a372-124">從 [檔案]\*\*\*\* 功能表選取 [新增]**[專案]** > \*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="7a372-124">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="7a372-125">選取 [ **ASP.NET Web Core Web 應用程式** ]，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="7a372-125">Select **ASP.NET Web Core Web Application** and then select **Next**.</span></span>
1. <span data-ttu-id="7a372-126">在 [ **設定您的新專案** ] 對話方塊中，將專案命名為 *WebApp1*。</span><span class="sxs-lookup"><span data-stu-id="7a372-126">In the **Configure your new project** dialog, Name the project *WebApp1*.</span></span>
1. <span data-ttu-id="7a372-127">將位置設定為與上一個專案不同的目錄，以使用相同的專案名稱。</span><span class="sxs-lookup"><span data-stu-id="7a372-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="7a372-128">使用相同的命名空間，可讓您更輕鬆地在兩個專案之間複製程式碼。</span><span class="sxs-lookup"><span data-stu-id="7a372-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="7a372-129">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="7a372-129">Select **Create**.</span></span>
1. <span data-ttu-id="7a372-130">在 [ **建立新的 ASP.NET Core Web 應用程式** ] 對話方塊中，確認已選取 [ **.net Core** ] 和 [ **ASP.NET Core 3.1** ]。</span><span class="sxs-lookup"><span data-stu-id="7a372-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="7a372-131">選取 \*\*Web 應用程式 (模型-視圖控制器) \*\* 專案範本，然後選取 [ **建立**]。</span><span class="sxs-lookup"><span data-stu-id="7a372-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create**.</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="7a372-132">將 ASP.NET Core 網站設定為使用 MVC</span><span class="sxs-lookup"><span data-stu-id="7a372-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="7a372-133">在 ASP.NET Core 3.0 和更新版本的專案中，.NET Framework 不再是支援的目標 Framework。</span><span class="sxs-lookup"><span data-stu-id="7a372-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="7a372-134">您的專案必須以 .NET Core 為目標。</span><span class="sxs-lookup"><span data-stu-id="7a372-134">Your project must target .NET Core.</span></span> <span data-ttu-id="7a372-135">ASP.NET Core 共用架構（包括 MVC）是 .NET Core 執行時間安裝的一部分。</span><span class="sxs-lookup"><span data-stu-id="7a372-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="7a372-136">在專案檔中使用 SDK 時，會自動參考共用架構 `Microsoft.NET.Sdk.Web` ：</span><span class="sxs-lookup"><span data-stu-id="7a372-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="7a372-137">如需詳細資訊，請參閱 [架構參考](xref:migration/22-to-30#framework-reference)。</span><span class="sxs-lookup"><span data-stu-id="7a372-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="7a372-138">在 ASP.NET Core 中， `Startup` 類別：</span><span class="sxs-lookup"><span data-stu-id="7a372-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="7a372-139">取代 *global.asax*。</span><span class="sxs-lookup"><span data-stu-id="7a372-139">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="7a372-140">處理所有應用程式啟動工作。</span><span class="sxs-lookup"><span data-stu-id="7a372-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="7a372-141">如需詳細資訊，請參閱<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="7a372-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="7a372-142">在 ASP.NET Core 專案中，開啟 *Startup.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="7a372-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="7a372-143">ASP.NET Core apps 必須選擇使用中介軟體的架構功能。</span><span class="sxs-lookup"><span data-stu-id="7a372-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="7a372-144">先前範本產生的程式碼會新增下列服務和中介軟體：</span><span class="sxs-lookup"><span data-stu-id="7a372-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="7a372-145"><xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>擴充方法會註冊控制器、API 相關功能和 views 的 MVC 服務支援。</span><span class="sxs-lookup"><span data-stu-id="7a372-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="7a372-146">如需 MVC 服務註冊選項的詳細資訊，請參閱 [mvc 服務註冊](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="7a372-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="7a372-147"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>擴充方法會新增靜態檔案處理常式 `Microsoft.AspNetCore.StaticFiles` 。</span><span class="sxs-lookup"><span data-stu-id="7a372-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="7a372-148">`UseStaticFiles`必須先呼叫擴充方法 `UseRouting` 。</span><span class="sxs-lookup"><span data-stu-id="7a372-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="7a372-149">如需詳細資訊，請參閱<xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="7a372-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="7a372-150"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>擴充方法會新增路由。</span><span class="sxs-lookup"><span data-stu-id="7a372-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="7a372-151">如需詳細資訊，請參閱<xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="7a372-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="7a372-152">此現有設定包含遷移範例 ASP.NET MVC 專案所需的專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="7a372-153">如需 ASP.NET Core 中介軟體選項的詳細資訊，請參閱 <xref:fundamentals/startup> 。</span><span class="sxs-lookup"><span data-stu-id="7a372-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="7a372-154">遷移控制器和視圖</span><span class="sxs-lookup"><span data-stu-id="7a372-154">Migrate controllers and views</span></span>

<span data-ttu-id="7a372-155">在 ASP.NET Core 專案中，會加入新的空白控制器類別和 view 類別做為預留位置，其使用的名稱與 ASP.NET MVC 專案中的控制器和 view 類別相同，可從中進行遷移。</span><span class="sxs-lookup"><span data-stu-id="7a372-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="7a372-156">ASP.NET Core 的 *WebApp1* 專案已經包含最基本的範例控制器，並以與 ASP.NET MVC 專案相同的名稱來查看。</span><span class="sxs-lookup"><span data-stu-id="7a372-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="7a372-157">因此，這些專案將作為 ASP.NET MVC 控制器的預留位置，以及要從 ASP.NET MVC *WebApp1* 專案中遷移的視圖。</span><span class="sxs-lookup"><span data-stu-id="7a372-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="7a372-158">從 ASP.NET MVC 複製方法 `HomeController` ，以取代新的 ASP.NET Core `HomeController` 方法。</span><span class="sxs-lookup"><span data-stu-id="7a372-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="7a372-159">不需要變更動作方法的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="7a372-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="7a372-160">ASP.NET MVC 內建範本的控制器動作方法傳回類型為 [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);在 ASP.NET Core MVC 中，動作方法會 `IActionResult` 改為傳回。</span><span class="sxs-lookup"><span data-stu-id="7a372-160">The ASP.NET MVC built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="7a372-161">`ActionResult` 會實作 `IActionResult`。</span><span class="sxs-lookup"><span data-stu-id="7a372-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="7a372-162">在 ASP.NET Core 專案中，以滑鼠右鍵按一下 *Views/Home* 目錄，然後選取 [ **加入** > **現有專案**]。</span><span class="sxs-lookup"><span data-stu-id="7a372-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="7a372-163">在 [ **加入現有專案** ] 對話方塊中，流覽至 ASP.NET MVC *WebApp1* 專案的 *Views/Home* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="7a372-164">選取 [ *About*]、[ *Contact*] 和 [ *Index* ] Razor view 檔案，然後選取 [ **加入**]，取代現有的檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-164">Select the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files, then select **Add**, replacing the existing files.</span></span>

<span data-ttu-id="7a372-165">如需詳細資訊，請參閱 <xref:mvc/controllers/actions> 和 <xref:mvc/views/overview>。</span><span class="sxs-lookup"><span data-stu-id="7a372-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="7a372-166">測試每個方法</span><span class="sxs-lookup"><span data-stu-id="7a372-166">Test each method</span></span>

<span data-ttu-id="7a372-167">您可以測試每個控制器端點，不過，配置和樣式稍後會在檔中涵蓋。</span><span class="sxs-lookup"><span data-stu-id="7a372-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="7a372-168">執行 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7a372-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="7a372-169">將目前的埠號碼取代為 ASP.NET Core 專案中使用的埠號碼，以在執行 ASP.NET Core 應用程式的瀏覽器中叫用轉譯的視圖。</span><span class="sxs-lookup"><span data-stu-id="7a372-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="7a372-170">例如： `https://localhost:44375/home/about` 。</span><span class="sxs-lookup"><span data-stu-id="7a372-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="7a372-171">遷移靜態內容</span><span class="sxs-lookup"><span data-stu-id="7a372-171">Migrate static content</span></span>

<span data-ttu-id="7a372-172">在 ASP.NET MVC 5 及更早版本中，靜態內容是由 Web 專案的根目錄所裝載，並與伺服器端檔案混合。</span><span class="sxs-lookup"><span data-stu-id="7a372-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="7a372-173">在 ASP.NET Core 中，靜態檔案會儲存在專案的 [web 根目錄](xref:fundamentals/index#web-root) 中。</span><span class="sxs-lookup"><span data-stu-id="7a372-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="7a372-174">預設目錄是 *{content root}/wwwroot*，但可以變更。</span><span class="sxs-lookup"><span data-stu-id="7a372-174">The default directory is *{content root}/wwwroot*, but it can be changed.</span></span> <span data-ttu-id="7a372-175">如需詳細資訊，請參閱 [ASP.NET Core 中的靜態檔案](xref:fundamentals/static-files#serve-static-files)。</span><span class="sxs-lookup"><span data-stu-id="7a372-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="7a372-176">將 ASP.NET MVC *WebApp1*專案中的靜態內容複寫到 ASP.NET Core *WebApp1*專案中的*wwwroot*目錄：</span><span class="sxs-lookup"><span data-stu-id="7a372-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="7a372-177">在 ASP.NET Core 專案中，以滑鼠右鍵按一下 [ *wwwroot* ] 目錄，然後選取 [ **加入** > **現有專案**]。</span><span class="sxs-lookup"><span data-stu-id="7a372-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="7a372-178">在 [ **加入現有專案** ] 對話方塊中，流覽至 [ASP.NET MVC *WebApp1* ] 專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="7a372-179">選取 *favicon .ico* 檔案，然後選取 [ **加入**]，取代現有的檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-179">Select the *favicon.ico* file, then select **Add**, replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="7a372-180">遷移版面配置檔案</span><span class="sxs-lookup"><span data-stu-id="7a372-180">Migrate the layout files</span></span>

<span data-ttu-id="7a372-181">將 ASP.NET MVC 專案版面配置檔案複製到 ASP.NET Core 專案：</span><span class="sxs-lookup"><span data-stu-id="7a372-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="7a372-182">在 ASP.NET Core 專案中，以滑鼠右鍵按一下 *Views* 目錄，然後選取 [ **加入** > **現有專案**]。</span><span class="sxs-lookup"><span data-stu-id="7a372-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="7a372-183">在 [ **加入現有專案** ] 對話方塊中，流覽至 ASP.NET MVC *WebApp1* 專案的 *Views* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="7a372-184">選取 *_ViewStart 的 cshtml* 檔案，然後選取 [ **新增**]。</span><span class="sxs-lookup"><span data-stu-id="7a372-184">Select the *_ViewStart.cshtml* file then select **Add**.</span></span>

<span data-ttu-id="7a372-185">將 ASP.NET MVC 專案共用設定檔案複製到 ASP.NET Core 專案：</span><span class="sxs-lookup"><span data-stu-id="7a372-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="7a372-186">在 ASP.NET Core 專案中，以滑鼠右鍵按一下 *Views/Shared* 目錄，然後選取 [ **加入** > **現有專案**]。</span><span class="sxs-lookup"><span data-stu-id="7a372-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item**.</span></span>
1. <span data-ttu-id="7a372-187">在 [ **加入現有專案** ] 對話方塊中，流覽至 ASP.NET MVC *WebApp1* 專案的 *Views/Shared* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="7a372-188">選取 *_Layout 的 cshtml* 檔案，然後選取 [ **新增**]，取代現有的檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-188">Select the *_Layout.cshtml* file, then select **Add**, replacing the existing file.</span></span>

<span data-ttu-id="7a372-189">在 ASP.NET Core 專案中，開啟 *_Layout 的 cshtml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="7a372-190">進行下列變更，以符合如下所示的完整程式碼：</span><span class="sxs-lookup"><span data-stu-id="7a372-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="7a372-191">更新啟動載入 CSS 包含的程式碼，以符合下列完成的程式碼：</span><span class="sxs-lookup"><span data-stu-id="7a372-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="7a372-192">取代為 `@Styles.Render("~/Content/css")` 用 `<link>` 來載入開機載入程式的元素 *。 css* (請參閱下面) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="7a372-193">移除 `@Scripts.Render("~/bundles/modernizr")`。</span><span class="sxs-lookup"><span data-stu-id="7a372-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="7a372-194">啟動載入 CSS 的完整取代標記包含：</span><span class="sxs-lookup"><span data-stu-id="7a372-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="7a372-195">更新 jQuery 和啟動程式 JavaScript 包含，以符合下列完成的程式碼：</span><span class="sxs-lookup"><span data-stu-id="7a372-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="7a372-196">`@Scripts.Render("~/bundles/jquery")`以元素取代 `<script>` (請參閱以下) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="7a372-197">`@Scripts.Render("~/bundles/bootstrap")`以元素取代 `<script>` (請參閱以下) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="7a372-198">JQuery 和啟動程式 JavaScript 的完整取代標記包含：</span><span class="sxs-lookup"><span data-stu-id="7a372-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="7a372-199">更新的 *_Layout cshtml* 檔案如下所示：</span><span class="sxs-lookup"><span data-stu-id="7a372-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="7a372-200">在瀏覽器中查看網站。</span><span class="sxs-lookup"><span data-stu-id="7a372-200">View the site in the browser.</span></span> <span data-ttu-id="7a372-201">它應該會以預期的樣式來呈現。</span><span class="sxs-lookup"><span data-stu-id="7a372-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="7a372-202">設定捆綁和縮制</span><span class="sxs-lookup"><span data-stu-id="7a372-202">Configure bundling and minification</span></span>

<span data-ttu-id="7a372-203">ASP.NET Core 與數個開放原始碼的包裝和縮制解決方案（例如 [WebOptimizer](https://github.com/ligershark/WebOptimizer) 和其他類似的程式庫）相容。</span><span class="sxs-lookup"><span data-stu-id="7a372-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="7a372-204">ASP.NET Core 不提供原生包裝和縮制解決方案。</span><span class="sxs-lookup"><span data-stu-id="7a372-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="7a372-205">如需設定包裝和縮制的相關資訊，請參閱 [捆綁和縮制](xref:client-side/bundling-and-minification)。</span><span class="sxs-lookup"><span data-stu-id="7a372-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="7a372-206">解決 HTTP 500 錯誤</span><span class="sxs-lookup"><span data-stu-id="7a372-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="7a372-207">有許多問題可能會導致 HTTP 500 錯誤訊息，而不包含問題來源的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="7a372-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="7a372-208">例如，如果 *Views/_ViewImports cshtml* 檔案包含不存在於專案中的命名空間，則會產生 HTTP 500 錯誤。</span><span class="sxs-lookup"><span data-stu-id="7a372-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="7a372-209">根據預設，在 ASP.NET Core apps 中， `UseDeveloperExceptionPage` 擴充功能會新增至， `IApplicationBuilder` 並在環境為 *開發*環境時執行。</span><span class="sxs-lookup"><span data-stu-id="7a372-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development*.</span></span> <span data-ttu-id="7a372-210">下列程式碼會詳細說明這一點：</span><span class="sxs-lookup"><span data-stu-id="7a372-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="7a372-211">ASP.NET Core 將未處理的例外狀況轉換成 HTTP 500 錯誤回應。</span><span class="sxs-lookup"><span data-stu-id="7a372-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="7a372-212">一般來說，這些回應中不會包含錯誤詳細資料，以防止洩漏有關伺服器的潛在敏感性資訊。</span><span class="sxs-lookup"><span data-stu-id="7a372-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="7a372-213">如需詳細資訊，請參閱 [開發人員例外狀況頁面](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="7a372-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7a372-214">後續步驟</span><span class="sxs-lookup"><span data-stu-id="7a372-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="7a372-215">其他資源</span><span class="sxs-lookup"><span data-stu-id="7a372-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="7a372-216">本文說明如何開始將 ASP.NET MVC 專案遷移至 [ASP.NET CORE mvc](xref:mvc/overview) 2.2。</span><span class="sxs-lookup"><span data-stu-id="7a372-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="7a372-217">在此程式中，它會反白顯示已從 ASP.NET MVC 變更的許多專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="7a372-218">從 ASP.NET MVC 進行遷移是一個多步驟的程式。</span><span class="sxs-lookup"><span data-stu-id="7a372-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="7a372-219">本文將說明：</span><span class="sxs-lookup"><span data-stu-id="7a372-219">This article covers:</span></span>

* <span data-ttu-id="7a372-220">初始設定</span><span class="sxs-lookup"><span data-stu-id="7a372-220">Initial setup</span></span>
* <span data-ttu-id="7a372-221">基本控制器與視圖</span><span class="sxs-lookup"><span data-stu-id="7a372-221">Basic controllers and views</span></span>
* <span data-ttu-id="7a372-222">靜態內容</span><span class="sxs-lookup"><span data-stu-id="7a372-222">Static content</span></span>
* <span data-ttu-id="7a372-223">用戶端相依性。</span><span class="sxs-lookup"><span data-stu-id="7a372-223">Client-side dependencies.</span></span>

<span data-ttu-id="7a372-224">如需遷移設定和程式 Identity 代碼，請參閱 <xref:migration/configuration> 和 <xref:migration/identity> 。</span><span class="sxs-lookup"><span data-stu-id="7a372-224">For migrating configuration and Identity code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="7a372-225">範例中的版本號碼可能不是最新版本，請據以更新專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="7a372-226">建立 starter ASP.NET MVC 專案</span><span class="sxs-lookup"><span data-stu-id="7a372-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="7a372-227">為了示範升級，我們將從建立 ASP.NET MVC 應用程式開始。</span><span class="sxs-lookup"><span data-stu-id="7a372-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="7a372-228">使用名稱 *WebApp1* 加以建立，讓命名空間符合下一個步驟中所建立的 ASP.NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio 的 [新增專案] 對話方塊](mvc/_static/new-project.png)

![新增 Web 應用程式對話方塊： ASP.NET 範本面板中選取的 MVC 專案範本](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="7a372-231">*選用：* 將方案名稱從 *WebApp1* 變更為 *Mvc5*。</span><span class="sxs-lookup"><span data-stu-id="7a372-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="7a372-232">Visual Studio 會顯示新的方案名稱 (*Mvc5*) ，讓您更輕鬆地從下一個專案告訴這個專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-232">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="7a372-233">建立 ASP.NET Core 專案</span><span class="sxs-lookup"><span data-stu-id="7a372-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="7a372-234">使用與上一個專案相同的名稱來建立新的 *空白* ASP.NET Core web 應用程式 (*WebApp1*) 讓兩個專案中的命名空間相符。</span><span class="sxs-lookup"><span data-stu-id="7a372-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="7a372-235">擁有相同的命名空間可讓您更輕鬆地在兩個專案之間複製程式碼。</span><span class="sxs-lookup"><span data-stu-id="7a372-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="7a372-236">在與前一個專案不同的目錄中建立此專案，以使用相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="7a372-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![[新增專案] 對話方塊](mvc/_static/new_core.png)

![新的 ASP.NET Web 應用程式對話方塊： ASP.NET Core 範本面板中選取了空白專案範本](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="7a372-239">*選用：* 使用 *Web 應用程式* 專案範本建立新的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7a372-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="7a372-240">將專案命名為 *WebApp1*，然後選取 **個別使用者帳戶**的驗證選項。</span><span class="sxs-lookup"><span data-stu-id="7a372-240">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="7a372-241">將此應用程式重新命名為 *FullAspNetCore*。</span><span class="sxs-lookup"><span data-stu-id="7a372-241">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="7a372-242">建立此專案可節省轉換的時間。</span><span class="sxs-lookup"><span data-stu-id="7a372-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="7a372-243">您可以在範本產生的程式碼中查看最終結果、將程式碼複製到轉換專案，或與範本產生的專案比較。</span><span class="sxs-lookup"><span data-stu-id="7a372-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="7a372-244">將網站設定為使用 MVC</span><span class="sxs-lookup"><span data-stu-id="7a372-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="7a372-245">以 .NET Core 為目標時，預設會參考 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="7a372-246">此套件包含 MVC 應用程式經常使用的套件。</span><span class="sxs-lookup"><span data-stu-id="7a372-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="7a372-247">如果目標為 .NET Framework，則套件參考必須個別列在專案檔中。</span><span class="sxs-lookup"><span data-stu-id="7a372-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="7a372-248">`Microsoft.AspNetCore.Mvc` 是 ASP.NET Core 的 MVC 架構。</span><span class="sxs-lookup"><span data-stu-id="7a372-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="7a372-249">`Microsoft.AspNetCore.StaticFiles` 這是靜態檔案處理常式。</span><span class="sxs-lookup"><span data-stu-id="7a372-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="7a372-250">ASP.NET Core apps 明確加入宣告中介軟體，例如提供靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="7a372-251">如需詳細資訊，請參閱[靜態檔案](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="7a372-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="7a372-252">開啟 *Startup.cs* 檔案，並變更程式碼以符合下列各項：</span><span class="sxs-lookup"><span data-stu-id="7a372-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="7a372-253"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>擴充方法會新增靜態檔案處理常式。</span><span class="sxs-lookup"><span data-stu-id="7a372-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="7a372-254">如需詳細資訊，請參閱 [應用程式啟動](xref:fundamentals/startup) 和 [路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="7a372-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="7a372-255">新增控制器和查看</span><span class="sxs-lookup"><span data-stu-id="7a372-255">Add a controller and view</span></span>

<span data-ttu-id="7a372-256">在本節中，會新增最基本的控制器和視圖，作為 ASP.NET MVC 控制器的預留位置，以及下一節中所遷移的視圖。</span><span class="sxs-lookup"><span data-stu-id="7a372-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="7a372-257">新增 *控制器* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="7a372-258">將名為*HomeController.cs*的**控制器類別**新增至*控制器*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![[新增項目] 對話方塊](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="7a372-260">新增 *Views* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="7a372-261">新增 *Views/Home* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="7a372-262">將名為*Index. cshtml*的\*\* Razor View\**新增至*Views/Home\*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-262">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![[新增項目] 對話方塊](mvc/_static/view.png)

<span data-ttu-id="7a372-264">專案結構如下所示：</span><span class="sxs-lookup"><span data-stu-id="7a372-264">The project structure is shown below:</span></span>

![方案總管顯示 WebApp1 的檔案和目錄](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="7a372-266">將 *Views/Home/Index.cshtml* 檔案的內容取代為下列標記：</span><span class="sxs-lookup"><span data-stu-id="7a372-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="7a372-267">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="7a372-267">Run the app.</span></span>

![Web 應用程式在 Microsoft Edge 中開啟](mvc/_static/hello-world.png)

<span data-ttu-id="7a372-269">如需詳細資訊，請參閱 [控制器](xref:mvc/controllers/actions) 和 [Views](xref:mvc/views/overview)。</span><span class="sxs-lookup"><span data-stu-id="7a372-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="7a372-270">下列功能需要從範例 ASP.NET MVC 專案遷移至 ASP.NET Core 專案：</span><span class="sxs-lookup"><span data-stu-id="7a372-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="7a372-271">用戶端內容 (CSS、字型和腳本) </span><span class="sxs-lookup"><span data-stu-id="7a372-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="7a372-272">controllers</span><span class="sxs-lookup"><span data-stu-id="7a372-272">controllers</span></span>

* <span data-ttu-id="7a372-273">檢視</span><span class="sxs-lookup"><span data-stu-id="7a372-273">views</span></span>

* <span data-ttu-id="7a372-274">模型</span><span class="sxs-lookup"><span data-stu-id="7a372-274">models</span></span>

* <span data-ttu-id="7a372-275">捆綁</span><span class="sxs-lookup"><span data-stu-id="7a372-275">bundling</span></span>

* <span data-ttu-id="7a372-276">filters</span><span class="sxs-lookup"><span data-stu-id="7a372-276">filters</span></span>

* <span data-ttu-id="7a372-277">登入/登出， Identity (在下一個教學課程中完成這項操作。 ) </span><span class="sxs-lookup"><span data-stu-id="7a372-277">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="7a372-278">控制器與視圖</span><span class="sxs-lookup"><span data-stu-id="7a372-278">Controllers and views</span></span>

* <span data-ttu-id="7a372-279">將 ASP.NET MVC 中的每個方法複製 `HomeController` 到新的 `HomeController` 。</span><span class="sxs-lookup"><span data-stu-id="7a372-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="7a372-280">在 ASP.NET MVC 中，內建範本的控制器動作方法傳回型別為 [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);在 ASP.NET Core MVC 中，動作方法會 `IActionResult` 改為傳回。</span><span class="sxs-lookup"><span data-stu-id="7a372-280">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="7a372-281">`ActionResult` implements `IActionResult` ，因此不需要變更動作方法的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="7a372-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="7a372-282">將 ASP.NET MVC 專案中的 *About*、 *Contact*和 *Index.* cshtml view 檔案複製 Razor 到 ASP.NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-282">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="7a372-283">測試每個方法</span><span class="sxs-lookup"><span data-stu-id="7a372-283">Test each method</span></span>

<span data-ttu-id="7a372-284">版面配置檔案和樣式尚未遷移，所以轉譯的視圖只會包含視圖檔案中的內容。</span><span class="sxs-lookup"><span data-stu-id="7a372-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="7a372-285">和 views 的版面配置檔案產生的連結 `About` `Contact` 將無法使用。</span><span class="sxs-lookup"><span data-stu-id="7a372-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="7a372-286">將目前的埠號碼取代為 ASP.NET core 專案中使用的埠號碼，以在執行 ASP.NET core 應用程式的瀏覽器中叫用轉譯的視圖。</span><span class="sxs-lookup"><span data-stu-id="7a372-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="7a372-287">例如： `https://localhost:44375/home/about` 。</span><span class="sxs-lookup"><span data-stu-id="7a372-287">For example: `https://localhost:44375/home/about`.</span></span>

![連絡人頁面](mvc/_static/contact-page.png)

<span data-ttu-id="7a372-289">請注意缺少樣式和功能表項目。</span><span class="sxs-lookup"><span data-stu-id="7a372-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="7a372-290">下一節將會修正樣式。</span><span class="sxs-lookup"><span data-stu-id="7a372-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="7a372-291">靜態內容</span><span class="sxs-lookup"><span data-stu-id="7a372-291">Static content</span></span>

<span data-ttu-id="7a372-292">在 ASP.NET MVC 5 及更早版本中，靜態內容是由 Web 專案的根目錄所裝載，並與伺服器端檔案混合。</span><span class="sxs-lookup"><span data-stu-id="7a372-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="7a372-293">在 ASP.NET Core 中，靜態內容裝載于 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="7a372-294">將靜態內容從 ASP.NET MVC 應用程式複製到 ASP.NET Core 專案中的 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="7a372-295">在此範例轉換中：</span><span class="sxs-lookup"><span data-stu-id="7a372-295">In this sample conversion:</span></span>

* <span data-ttu-id="7a372-296">將 *favicon .ico* 檔案從 ASP.NET MVC 專案複製到 ASP.NET Core 專案中的 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="7a372-297">ASP.NET MVC 專案會使用 [啟動](https://getbootstrap.com/) 程式進行其樣式，並將啟動程式檔案儲存在 *內容* 和 *腳本* 目錄中。</span><span class="sxs-lookup"><span data-stu-id="7a372-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="7a372-298">產生 ASP.NET MVC 專案的範本會參考設定檔案中的啟動程式 (*Views/Shared/_Layout. cshtml*) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="7a372-299">*bootstrap.js*和*啟動程式 .css*檔案可以從 ASP.NET MVC 專案複製到新專案中的*wwwroot*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="7a372-300">相反地，本檔會在下一節中使用 Cdn 來新增啟動載入 (和其他用戶端程式庫的支援) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="7a372-301">遷移版面配置檔案</span><span class="sxs-lookup"><span data-stu-id="7a372-301">Migrate the layout file</span></span>

* <span data-ttu-id="7a372-302">將 ASP.NET MVC 專案*views*目錄中的 *_ViewStart cshtml*檔案複製到 ASP.NET Core 專案的*views*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="7a372-303">在 ASP.NET Core MVC 中， *_ViewStart 的 cshtml* 檔案尚未變更。</span><span class="sxs-lookup"><span data-stu-id="7a372-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="7a372-304">建立 *Views/Shared* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="7a372-305">*選用：* 從*FullAspNetCore* MVC 專案的*views*目錄將 *_ViewImports. cshtml*複製到 ASP.NET Core 專案的*views*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="7a372-306">移除 *_ViewImports cshtml* 檔案中的任何命名空間宣告。</span><span class="sxs-lookup"><span data-stu-id="7a372-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="7a372-307">*_ViewImports cshtml*檔案提供所有 view 檔案的命名空間，並引進標籤協助[程式。](xref:mvc/views/tag-helpers/intro)</span><span class="sxs-lookup"><span data-stu-id="7a372-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="7a372-308">標籤協助程式會在新的版面配置檔案中使用。</span><span class="sxs-lookup"><span data-stu-id="7a372-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="7a372-309">*_ViewImports 的 cshtml*檔案是 ASP.NET Core 的新功能。</span><span class="sxs-lookup"><span data-stu-id="7a372-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="7a372-310">將 ASP.NET MVC 專案的*views/shared*目錄中的 *_Layout cshtml*檔案複製到 ASP.NET Core 專案的*views/shared*目錄中。</span><span class="sxs-lookup"><span data-stu-id="7a372-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="7a372-311">開啟 *_Layout cshtml* 檔案，並進行下列變更 (完成的程式碼如下所示) ：</span><span class="sxs-lookup"><span data-stu-id="7a372-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="7a372-312">取代為 `@Styles.Render("~/Content/css")` 用 `<link>` 來載入開機載入程式的元素 *。 css* (請參閱下面) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="7a372-313">移除 `@Scripts.Render("~/bundles/modernizr")`。</span><span class="sxs-lookup"><span data-stu-id="7a372-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="7a372-314">`@Html.Partial("_LoginPartial")`使用) 將行批註 (程式程式碼 `@*...*@` 。</span><span class="sxs-lookup"><span data-stu-id="7a372-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="7a372-315">如需詳細資訊，請參閱 [遷移驗證和 Identity ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="7a372-315">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="7a372-316">`@Scripts.Render("~/bundles/jquery")`以元素取代 `<script>` (請參閱以下) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="7a372-317">`@Scripts.Render("~/bundles/bootstrap")`以元素取代 `<script>` (請參閱以下) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="7a372-318">啟動載入 CSS 的取代標記包含：</span><span class="sxs-lookup"><span data-stu-id="7a372-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="7a372-319">JQuery 和啟動程式 JavaScript 的取代標記包含：</span><span class="sxs-lookup"><span data-stu-id="7a372-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="7a372-320">更新的 *_Layout cshtml* 檔案如下所示：</span><span class="sxs-lookup"><span data-stu-id="7a372-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="7a372-321">在瀏覽器中查看網站。</span><span class="sxs-lookup"><span data-stu-id="7a372-321">View the site in the browser.</span></span> <span data-ttu-id="7a372-322">它現在應該會正確載入，且需要適當的樣式。</span><span class="sxs-lookup"><span data-stu-id="7a372-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="7a372-323">*選用：* 請嘗試使用新的版面配置檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="7a372-324">從 *FullAspNetCore* 專案複製版面配置檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="7a372-325">新的版面配置檔案 [使用卷](xref:mvc/views/tag-helpers/intro) 標協助程式，並有其他改良功能。</span><span class="sxs-lookup"><span data-stu-id="7a372-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="7a372-326">設定捆綁和縮制</span><span class="sxs-lookup"><span data-stu-id="7a372-326">Configure bundling and minification</span></span>

<span data-ttu-id="7a372-327">如需有關如何設定包裝和縮制的詳細資訊，請參閱 [捆綁和縮制](xref:client-side/bundling-and-minification)。</span><span class="sxs-lookup"><span data-stu-id="7a372-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="7a372-328">解決 HTTP 500 錯誤</span><span class="sxs-lookup"><span data-stu-id="7a372-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="7a372-329">有許多問題可能會導致 HTTP 500 錯誤訊息，而不包含問題來源的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="7a372-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="7a372-330">例如，如果 *Views/_ViewImports cshtml* 檔案包含不存在於專案中的命名空間，則會產生 HTTP 500 錯誤。</span><span class="sxs-lookup"><span data-stu-id="7a372-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="7a372-331">根據預設，在 ASP.NET Core apps 中， `UseDeveloperExceptionPage` 擴充功能會新增至，並在設定 `IApplicationBuilder` 為 *開發*時執行。</span><span class="sxs-lookup"><span data-stu-id="7a372-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="7a372-332">請參閱下列程式碼中的範例：</span><span class="sxs-lookup"><span data-stu-id="7a372-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="7a372-333">ASP.NET Core 將未處理的例外狀況轉換成 HTTP 500 錯誤回應。</span><span class="sxs-lookup"><span data-stu-id="7a372-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="7a372-334">一般來說，這些回應中不會包含錯誤詳細資料，以防止洩漏有關伺服器的潛在敏感性資訊。</span><span class="sxs-lookup"><span data-stu-id="7a372-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="7a372-335">如需詳細資訊，請參閱 [開發人員例外狀況頁面](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="7a372-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a372-336">其他資源</span><span class="sxs-lookup"><span data-stu-id="7a372-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="7a372-337">本文說明如何開始將 ASP.NET MVC 專案遷移至 [ASP.NET CORE mvc](xref:mvc/overview) 2.1。</span><span class="sxs-lookup"><span data-stu-id="7a372-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="7a372-338">在此程式中，它會反白顯示已從 ASP.NET MVC 變更的許多專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="7a372-339">從 ASP.NET MVC 進行遷移是一個多步驟的程式。</span><span class="sxs-lookup"><span data-stu-id="7a372-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="7a372-340">本文將說明：</span><span class="sxs-lookup"><span data-stu-id="7a372-340">This article covers:</span></span>

* <span data-ttu-id="7a372-341">初始設定</span><span class="sxs-lookup"><span data-stu-id="7a372-341">Initial setup</span></span>
* <span data-ttu-id="7a372-342">基本控制器與視圖</span><span class="sxs-lookup"><span data-stu-id="7a372-342">Basic controllers and views</span></span>
* <span data-ttu-id="7a372-343">靜態內容</span><span class="sxs-lookup"><span data-stu-id="7a372-343">Static content</span></span>
* <span data-ttu-id="7a372-344">用戶端相依性。</span><span class="sxs-lookup"><span data-stu-id="7a372-344">Client-side dependencies.</span></span>

<span data-ttu-id="7a372-345">如需遷移設定和程式 Identity 代碼，請參閱 [將設定遷移至 ASP.NET Core](xref:migration/configuration) 以及 [將驗證遷移至 Identity ASP.NET Core](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="7a372-345">For migrating configuration and Identity code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="7a372-346">範例中的版本號碼可能不是最新版本，請據以更新專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="7a372-347">建立 starter ASP.NET MVC 專案</span><span class="sxs-lookup"><span data-stu-id="7a372-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="7a372-348">為了示範升級，我們將從建立 ASP.NET MVC 應用程式開始。</span><span class="sxs-lookup"><span data-stu-id="7a372-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="7a372-349">使用名稱 *WebApp1* 加以建立，讓命名空間符合下一個步驟中所建立的 ASP.NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio 的 [新增專案] 對話方塊](mvc/_static/new-project.png)

![新增 Web 應用程式對話方塊： ASP.NET 範本面板中選取的 MVC 專案範本](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="7a372-352">*選用：* 將方案名稱從 *WebApp1* 變更為 *Mvc5*。</span><span class="sxs-lookup"><span data-stu-id="7a372-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="7a372-353">Visual Studio 會顯示新的方案名稱 (*Mvc5*) ，讓您更輕鬆地從下一個專案告訴這個專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-353">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="7a372-354">建立 ASP.NET Core 專案</span><span class="sxs-lookup"><span data-stu-id="7a372-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="7a372-355">使用與上一個專案相同的名稱來建立新的 *空白* ASP.NET Core web 應用程式 (*WebApp1*) 讓兩個專案中的命名空間相符。</span><span class="sxs-lookup"><span data-stu-id="7a372-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="7a372-356">擁有相同的命名空間可讓您更輕鬆地在兩個專案之間複製程式碼。</span><span class="sxs-lookup"><span data-stu-id="7a372-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="7a372-357">在與前一個專案不同的目錄中建立此專案，以使用相同的名稱。</span><span class="sxs-lookup"><span data-stu-id="7a372-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![[新增專案] 對話方塊](mvc/_static/new_core.png)

![新的 ASP.NET Web 應用程式對話方塊： ASP.NET Core 範本面板中選取了空白專案範本](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="7a372-360">*選用：* 使用 *Web 應用程式* 專案範本建立新的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="7a372-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="7a372-361">將專案命名為 *WebApp1*，然後選取 **個別使用者帳戶**的驗證選項。</span><span class="sxs-lookup"><span data-stu-id="7a372-361">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="7a372-362">將此應用程式重新命名為 *FullAspNetCore*。</span><span class="sxs-lookup"><span data-stu-id="7a372-362">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="7a372-363">建立此專案可節省轉換的時間。</span><span class="sxs-lookup"><span data-stu-id="7a372-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="7a372-364">您可以在範本產生的程式碼中查看最終結果、將程式碼複製到轉換專案，或與範本產生的專案比較。</span><span class="sxs-lookup"><span data-stu-id="7a372-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="7a372-365">將網站設定為使用 MVC</span><span class="sxs-lookup"><span data-stu-id="7a372-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="7a372-366">以 .NET Core 為目標時，預設會參考 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="7a372-367">此套件包含 MVC 應用程式經常使用的套件。</span><span class="sxs-lookup"><span data-stu-id="7a372-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="7a372-368">如果目標為 .NET Framework，則套件參考必須個別列在專案檔中。</span><span class="sxs-lookup"><span data-stu-id="7a372-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="7a372-369">`Microsoft.AspNetCore.Mvc` 是 ASP.NET Core 的 MVC 架構。</span><span class="sxs-lookup"><span data-stu-id="7a372-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="7a372-370">`Microsoft.AspNetCore.StaticFiles` 這是靜態檔案處理常式。</span><span class="sxs-lookup"><span data-stu-id="7a372-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="7a372-371">ASP.NET Core apps 明確加入宣告中介軟體，例如提供靜態檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="7a372-372">如需詳細資訊，請參閱[靜態檔案](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="7a372-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="7a372-373">開啟 *Startup.cs* 檔案，並變更程式碼以符合下列各項：</span><span class="sxs-lookup"><span data-stu-id="7a372-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="7a372-374"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>擴充方法會新增靜態檔案處理常式。</span><span class="sxs-lookup"><span data-stu-id="7a372-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="7a372-375">`UseMvc`擴充方法會新增路由。</span><span class="sxs-lookup"><span data-stu-id="7a372-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="7a372-376">如需詳細資訊，請參閱 [應用程式啟動](xref:fundamentals/startup) 和 [路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="7a372-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="7a372-377">新增控制器和查看</span><span class="sxs-lookup"><span data-stu-id="7a372-377">Add a controller and view</span></span>

<span data-ttu-id="7a372-378">在本節中，會新增最基本的控制器和視圖，作為 ASP.NET MVC 控制器的預留位置，以及下一節中所遷移的視圖。</span><span class="sxs-lookup"><span data-stu-id="7a372-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="7a372-379">新增 *控制器* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="7a372-380">將名為*HomeController.cs*的**控制器類別**新增至*控制器*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![[新增項目] 對話方塊](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="7a372-382">新增 *Views* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="7a372-383">新增 *Views/Home* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="7a372-384">將名為*Index. cshtml*的\*\* Razor View\**新增至*Views/Home\*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-384">Add a **Razor View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![[新增項目] 對話方塊](mvc/_static/view.png)

<span data-ttu-id="7a372-386">專案結構如下所示：</span><span class="sxs-lookup"><span data-stu-id="7a372-386">The project structure is shown below:</span></span>

![方案總管顯示 WebApp1 的檔案和目錄](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="7a372-388">將 *Views/Home/Index.cshtml* 檔案的內容取代為下列標記：</span><span class="sxs-lookup"><span data-stu-id="7a372-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="7a372-389">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="7a372-389">Run the app.</span></span>

![Web 應用程式在 Microsoft Edge 中開啟](mvc/_static/hello-world.png)

<span data-ttu-id="7a372-391">如需詳細資訊，請參閱 [控制器](xref:mvc/controllers/actions) 和 [Views](xref:mvc/views/overview)。</span><span class="sxs-lookup"><span data-stu-id="7a372-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="7a372-392">下列功能需要從範例 ASP.NET MVC 專案遷移至 ASP.NET Core 專案：</span><span class="sxs-lookup"><span data-stu-id="7a372-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="7a372-393">用戶端內容 (CSS、字型和腳本) </span><span class="sxs-lookup"><span data-stu-id="7a372-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="7a372-394">controllers</span><span class="sxs-lookup"><span data-stu-id="7a372-394">controllers</span></span>

* <span data-ttu-id="7a372-395">檢視</span><span class="sxs-lookup"><span data-stu-id="7a372-395">views</span></span>

* <span data-ttu-id="7a372-396">模型</span><span class="sxs-lookup"><span data-stu-id="7a372-396">models</span></span>

* <span data-ttu-id="7a372-397">捆綁</span><span class="sxs-lookup"><span data-stu-id="7a372-397">bundling</span></span>

* <span data-ttu-id="7a372-398">filters</span><span class="sxs-lookup"><span data-stu-id="7a372-398">filters</span></span>

* <span data-ttu-id="7a372-399">登入/登出， Identity (在下一個教學課程中完成這項操作。 ) </span><span class="sxs-lookup"><span data-stu-id="7a372-399">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="7a372-400">控制器與視圖</span><span class="sxs-lookup"><span data-stu-id="7a372-400">Controllers and views</span></span>

* <span data-ttu-id="7a372-401">將 ASP.NET MVC 中的每個方法複製 `HomeController` 到新的 `HomeController` 。</span><span class="sxs-lookup"><span data-stu-id="7a372-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="7a372-402">在 ASP.NET MVC 中，內建範本的控制器動作方法傳回型別為 [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);在 ASP.NET Core MVC 中，動作方法會 `IActionResult` 改為傳回。</span><span class="sxs-lookup"><span data-stu-id="7a372-402">In ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="7a372-403">`ActionResult` implements `IActionResult` ，因此不需要變更動作方法的傳回型別。</span><span class="sxs-lookup"><span data-stu-id="7a372-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="7a372-404">將 ASP.NET MVC 專案中的 *About*、 *Contact*和 *Index.* cshtml view 檔案複製 Razor 到 ASP.NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="7a372-404">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="7a372-405">測試每個方法</span><span class="sxs-lookup"><span data-stu-id="7a372-405">Test each method</span></span>

<span data-ttu-id="7a372-406">版面配置檔案和樣式尚未遷移，所以轉譯的視圖只會包含視圖檔案中的內容。</span><span class="sxs-lookup"><span data-stu-id="7a372-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="7a372-407">和 views 的版面配置檔案產生的連結 `About` `Contact` 將無法使用。</span><span class="sxs-lookup"><span data-stu-id="7a372-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="7a372-408">將目前的埠號碼取代為 ASP.NET core 專案中使用的埠號碼，以在執行 ASP.NET core 應用程式的瀏覽器中叫用轉譯的視圖。</span><span class="sxs-lookup"><span data-stu-id="7a372-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="7a372-409">例如： `https://localhost:44375/home/about` 。</span><span class="sxs-lookup"><span data-stu-id="7a372-409">For example: `https://localhost:44375/home/about`.</span></span>

![連絡人頁面](mvc/_static/contact-page.png)

<span data-ttu-id="7a372-411">請注意缺少樣式和功能表項目。</span><span class="sxs-lookup"><span data-stu-id="7a372-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="7a372-412">下一節將會修正樣式。</span><span class="sxs-lookup"><span data-stu-id="7a372-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="7a372-413">靜態內容</span><span class="sxs-lookup"><span data-stu-id="7a372-413">Static content</span></span>

<span data-ttu-id="7a372-414">在 ASP.NET MVC 5 及更早版本中，靜態內容是由 Web 專案的根目錄所裝載，並與伺服器端檔案混合。</span><span class="sxs-lookup"><span data-stu-id="7a372-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="7a372-415">在 ASP.NET Core 中，靜態內容裝載于 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="7a372-416">將靜態內容從 ASP.NET MVC 應用程式複製到 ASP.NET Core 專案中的 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="7a372-417">在此範例轉換中：</span><span class="sxs-lookup"><span data-stu-id="7a372-417">In this sample conversion:</span></span>

* <span data-ttu-id="7a372-418">將 *favicon .ico* 檔案從 ASP.NET MVC 專案複製到 ASP.NET Core 專案中的 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="7a372-419">ASP.NET MVC 專案會使用 [啟動](https://getbootstrap.com/) 程式進行其樣式，並將啟動程式檔案儲存在 *內容* 和 *腳本* 目錄中。</span><span class="sxs-lookup"><span data-stu-id="7a372-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="7a372-420">產生 ASP.NET MVC 專案的範本會參考設定檔案中的啟動程式 (*Views/Shared/_Layout. cshtml*) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="7a372-421">*bootstrap.js*和*啟動程式 .css*檔案可以從 ASP.NET MVC 專案複製到新專案中的*wwwroot*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="7a372-422">相反地，本檔會在下一節中使用 Cdn 來新增啟動載入 (和其他用戶端程式庫的支援) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="7a372-423">遷移版面配置檔案</span><span class="sxs-lookup"><span data-stu-id="7a372-423">Migrate the layout file</span></span>

* <span data-ttu-id="7a372-424">將 ASP.NET MVC 專案*views*目錄中的 *_ViewStart cshtml*檔案複製到 ASP.NET Core 專案的*views*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="7a372-425">在 ASP.NET Core MVC 中， *_ViewStart 的 cshtml* 檔案尚未變更。</span><span class="sxs-lookup"><span data-stu-id="7a372-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="7a372-426">建立 *Views/Shared* 目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="7a372-427">*選用：* 從*FullAspNetCore* MVC 專案的*views*目錄將 *_ViewImports. cshtml*複製到 ASP.NET Core 專案的*views*目錄。</span><span class="sxs-lookup"><span data-stu-id="7a372-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="7a372-428">移除 *_ViewImports cshtml* 檔案中的任何命名空間宣告。</span><span class="sxs-lookup"><span data-stu-id="7a372-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="7a372-429">*_ViewImports cshtml*檔案提供所有 view 檔案的命名空間，並引進標籤協助[程式。](xref:mvc/views/tag-helpers/intro)</span><span class="sxs-lookup"><span data-stu-id="7a372-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="7a372-430">標籤協助程式會在新的版面配置檔案中使用。</span><span class="sxs-lookup"><span data-stu-id="7a372-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="7a372-431">*_ViewImports 的 cshtml*檔案是 ASP.NET Core 的新功能。</span><span class="sxs-lookup"><span data-stu-id="7a372-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="7a372-432">將 ASP.NET MVC 專案的*views/shared*目錄中的 *_Layout cshtml*檔案複製到 ASP.NET Core 專案的*views/shared*目錄中。</span><span class="sxs-lookup"><span data-stu-id="7a372-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="7a372-433">開啟 *_Layout cshtml* 檔案，並進行下列變更 (完成的程式碼如下所示) ：</span><span class="sxs-lookup"><span data-stu-id="7a372-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="7a372-434">取代為 `@Styles.Render("~/Content/css")` 用 `<link>` 來載入開機載入程式的元素 *。 css* (請參閱下面) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="7a372-435">移除 `@Scripts.Render("~/bundles/modernizr")`。</span><span class="sxs-lookup"><span data-stu-id="7a372-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="7a372-436">`@Html.Partial("_LoginPartial")`使用) 將行批註 (程式程式碼 `@*...*@` 。</span><span class="sxs-lookup"><span data-stu-id="7a372-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="7a372-437">如需詳細資訊，請參閱 [遷移驗證和 Identity ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="7a372-437">For more information, see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="7a372-438">`@Scripts.Render("~/bundles/jquery")`以元素取代 `<script>` (請參閱以下) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="7a372-439">`@Scripts.Render("~/bundles/bootstrap")`以元素取代 `<script>` (請參閱以下) 。</span><span class="sxs-lookup"><span data-stu-id="7a372-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="7a372-440">啟動載入 CSS 的取代標記包含：</span><span class="sxs-lookup"><span data-stu-id="7a372-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="7a372-441">JQuery 和啟動程式 JavaScript 的取代標記包含：</span><span class="sxs-lookup"><span data-stu-id="7a372-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="7a372-442">更新的 *_Layout cshtml* 檔案如下所示：</span><span class="sxs-lookup"><span data-stu-id="7a372-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="7a372-443">在瀏覽器中查看網站。</span><span class="sxs-lookup"><span data-stu-id="7a372-443">View the site in the browser.</span></span> <span data-ttu-id="7a372-444">它現在應該會正確載入，且需要適當的樣式。</span><span class="sxs-lookup"><span data-stu-id="7a372-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="7a372-445">*選用：* 請嘗試使用新的版面配置檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="7a372-446">從 *FullAspNetCore* 專案複製版面配置檔案。</span><span class="sxs-lookup"><span data-stu-id="7a372-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="7a372-447">新的版面配置檔案 [使用卷](xref:mvc/views/tag-helpers/intro) 標協助程式，並有其他改良功能。</span><span class="sxs-lookup"><span data-stu-id="7a372-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="7a372-448">設定捆綁和縮制</span><span class="sxs-lookup"><span data-stu-id="7a372-448">Configure bundling and minification</span></span>

<span data-ttu-id="7a372-449">如需有關如何設定包裝和縮制的詳細資訊，請參閱 [捆綁和縮制](xref:client-side/bundling-and-minification)。</span><span class="sxs-lookup"><span data-stu-id="7a372-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="7a372-450">解決 HTTP 500 錯誤</span><span class="sxs-lookup"><span data-stu-id="7a372-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="7a372-451">有許多問題可能會導致 HTTP 500 錯誤訊息，而不包含問題來源的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="7a372-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="7a372-452">例如，如果 *Views/_ViewImports cshtml* 檔案包含不存在於專案中的命名空間，則會產生 HTTP 500 錯誤。</span><span class="sxs-lookup"><span data-stu-id="7a372-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="7a372-453">根據預設，在 ASP.NET Core apps 中， `UseDeveloperExceptionPage` 擴充功能會新增至，並在設定 `IApplicationBuilder` 為 *開發*時執行。</span><span class="sxs-lookup"><span data-stu-id="7a372-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="7a372-454">請參閱下列程式碼中的範例：</span><span class="sxs-lookup"><span data-stu-id="7a372-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="7a372-455">ASP.NET Core 將未處理的例外狀況轉換成 HTTP 500 錯誤回應。</span><span class="sxs-lookup"><span data-stu-id="7a372-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="7a372-456">一般來說，這些回應中不會包含錯誤詳細資料，以防止洩漏有關伺服器的潛在敏感性資訊。</span><span class="sxs-lookup"><span data-stu-id="7a372-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="7a372-457">如需詳細資訊，請參閱 [開發人員例外狀況頁面](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="7a372-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7a372-458">其他資源</span><span class="sxs-lookup"><span data-stu-id="7a372-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
