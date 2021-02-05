---
title: Swashbuckle 與 ASP.NET Core 使用者入門
author: zuckerthoben
description: 了解如何將 Swashbuckle 新增至 ASP.NET Core Web API 專案，以整合 Swagger UI。
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
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
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: df9c78770f3494b1ae9e0d3604926d77cb1e67bb
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2021
ms.locfileid: "99578317"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="bf639-103">Swashbuckle 與 ASP.NET Core 使用者入門</span><span class="sxs-lookup"><span data-stu-id="bf639-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="bf639-104">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="bf639-104">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bf639-105">Swashbuckle 有三個主要元件：</span><span class="sxs-lookup"><span data-stu-id="bf639-105">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="bf639-106">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/)：Swagger 物件模型和中介軟體，用來公開 `SwaggerDocument` 物件作為 JSON 端點。</span><span class="sxs-lookup"><span data-stu-id="bf639-106">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="bf639-107">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/)：Swagger 產生器，可直接從您的路由、控制器和模型建置 `SwaggerDocument` 物件。</span><span class="sxs-lookup"><span data-stu-id="bf639-107">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="bf639-108">它通常會結合 Swagger 端點中介軟體，以便自動公開 Swagger JSON。</span><span class="sxs-lookup"><span data-stu-id="bf639-108">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="bf639-109">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/)：Swagger UI 工具的內嵌版本。</span><span class="sxs-lookup"><span data-stu-id="bf639-109">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="bf639-110">它可以解譯 Swagger JSON，建置豐富、可自訂的 Web API 功能描述體驗。</span><span class="sxs-lookup"><span data-stu-id="bf639-110">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="bf639-111">其中包括公用方法的內建測試載入器。</span><span class="sxs-lookup"><span data-stu-id="bf639-111">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="bf639-112">套件安裝</span><span class="sxs-lookup"><span data-stu-id="bf639-112">Package installation</span></span>

<span data-ttu-id="bf639-113">可使用下列方法新增 Swashbuckle：</span><span class="sxs-lookup"><span data-stu-id="bf639-113">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="bf639-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf639-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bf639-115">從 [套件管理員主控台] 視窗中：</span><span class="sxs-lookup"><span data-stu-id="bf639-115">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="bf639-116">移至 [**查看**  >  **其他 Windows**  >  **封裝管理員主控台**]</span><span class="sxs-lookup"><span data-stu-id="bf639-116">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="bf639-117">巡覽至 *TodoApi.csproj* 檔案所在目錄</span><span class="sxs-lookup"><span data-stu-id="bf639-117">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="bf639-118">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bf639-118">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.6.3
    ```

* <span data-ttu-id="bf639-119">從 [管理 NuGet 套件] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="bf639-119">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="bf639-120">在 **方案總管**  >  **管理 NuGet 套件**] 中的專案上按一下滑鼠右鍵</span><span class="sxs-lookup"><span data-stu-id="bf639-120">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="bf639-121">將 [套件來源] 設定為 "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="bf639-121">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="bf639-122">請確定已啟用 [包含發行前版本] 選項</span><span class="sxs-lookup"><span data-stu-id="bf639-122">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="bf639-123">在搜尋方塊中輸入 "Swashbuckle.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="bf639-123">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="bf639-124">從 [瀏覽] 索引標籤中選取最新的 "Swashbuckle.AspNetCore" 套件，並按一下 [安裝]</span><span class="sxs-lookup"><span data-stu-id="bf639-124">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf639-125">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="bf639-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="bf639-126">在 [Solution Pad] > [新增套件...] 中，以滑鼠右鍵按一下 *Packages* 資料夾</span><span class="sxs-lookup"><span data-stu-id="bf639-126">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="bf639-127">將 [新增套件] 視窗的 [來源] 下拉式清單設定為 "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="bf639-127">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="bf639-128">請確定已啟用 [選定發行前版本的套件] 選項</span><span class="sxs-lookup"><span data-stu-id="bf639-128">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="bf639-129">在搜尋方塊中輸入 "Swashbuckle.AspNetCore"</span><span class="sxs-lookup"><span data-stu-id="bf639-129">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="bf639-130">從結果窗格中選取最新的 "Swashbuckle.AspNetCore" 套件，並按一下 [新增套件]</span><span class="sxs-lookup"><span data-stu-id="bf639-130">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="bf639-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf639-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bf639-132">從 [整合式終端機] 執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="bf639-132">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

### <a name="net-core-cli"></a>[<span data-ttu-id="bf639-133">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bf639-133">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bf639-134">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="bf639-134">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="bf639-135">新增和設定 Swagger 中介軟體</span><span class="sxs-lookup"><span data-stu-id="bf639-135">Add and configure Swagger middleware</span></span>

<span data-ttu-id="bf639-136">將 Swagger 產生器新增至 `Startup.ConfigureServices` 方法中的服務集合：</span><span class="sxs-lookup"><span data-stu-id="bf639-136">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

<span data-ttu-id="bf639-137">在 `Startup.Configure` 方法中，啟用中介軟體為產生的 JSON 文件和 Swagger UI 提供服務：</span><span class="sxs-lookup"><span data-stu-id="bf639-137">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="bf639-138">Swashbuckle 依賴 MVC <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> 來探索路由和端點。</span><span class="sxs-lookup"><span data-stu-id="bf639-138">Swashbuckle relies on MVC's <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> to discover the routes and endpoints.</span></span> <span data-ttu-id="bf639-139">如果專案呼叫 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A> ，就會自動探索路由和端點。</span><span class="sxs-lookup"><span data-stu-id="bf639-139">If the project calls <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A>, routes and endpoints are discovered automatically.</span></span> <span data-ttu-id="bf639-140">呼叫時 <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> ， <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> 必須明確呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="bf639-140">When calling <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A>, the <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> method must be explicitly called.</span></span> <span data-ttu-id="bf639-141">如需詳細資訊，請參閱 [Swashbuckle、ApiExplorer 和 Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing)。</span><span class="sxs-lookup"><span data-stu-id="bf639-141">For more information, see [Swashbuckle, ApiExplorer, and Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).</span></span>

<span data-ttu-id="bf639-142">上述 `UseSwaggerUI` 方法呼叫會啟用[靜態檔案中介軟體](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="bf639-142">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="bf639-143">如果以 .NET Framework 或 .NET Core 1.x 為目標，請將 [AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 套件新增至專案。</span><span class="sxs-lookup"><span data-stu-id="bf639-143">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="bf639-144">啟動應用程式，並巡覽至 `http://localhost:<port>/swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="bf639-144">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="bf639-145">描述端點的已產生檔隨即出現，如 [OpenAPI 規格中所示 ( # B0) ](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson)。</span><span class="sxs-lookup"><span data-stu-id="bf639-145">The generated document describing the endpoints appears as shown in [OpenAPI specification (openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson).</span></span>

<span data-ttu-id="bf639-146">您可以在 `http://localhost:<port>/swagger` 找到 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="bf639-146">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="bf639-147">透過 Swagger UI 探索 API，並將其併入其他程式。</span><span class="sxs-lookup"><span data-stu-id="bf639-147">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="bf639-148">若要在應用程式的根目錄 (`http://localhost:<port>/`) 上提供 Swagger UI，請將 `RoutePrefix` 屬性設為空字串：</span><span class="sxs-lookup"><span data-stu-id="bf639-148">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="bf639-149">若使用目錄搭配 IIS 或 反向 Proxy，請將 Swagger 端點設定為使用 `./` 前置詞的相對路徑。</span><span class="sxs-lookup"><span data-stu-id="bf639-149">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="bf639-150">例如： `./swagger/v1/swagger.json` 。</span><span class="sxs-lookup"><span data-stu-id="bf639-150">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="bf639-151">使用 `/swagger/v1/swagger.json` 指示應用程式在 URL 的真實根目錄 (若已使用，請加上路由前置詞) 尋找 JSON 檔案。</span><span class="sxs-lookup"><span data-stu-id="bf639-151">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="bf639-152">例如，請使用 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json`，而不要使用 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`。</span><span class="sxs-lookup"><span data-stu-id="bf639-152">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

> [!NOTE]
> <span data-ttu-id="bf639-153">根據預設，Swashbuckle 會在 &mdash; 正式稱為 OpenAPI 規格的規格3.0 版中產生並公開 SWAGGER JSON。</span><span class="sxs-lookup"><span data-stu-id="bf639-153">By default, Swashbuckle generates and exposes Swagger JSON in version 3.0 of the specification&mdash;officially called the OpenAPI Specification.</span></span> <span data-ttu-id="bf639-154">若要支援回溯相容性，您可以改為選擇改為以2.0 格式公開 JSON。</span><span class="sxs-lookup"><span data-stu-id="bf639-154">To support backwards compatibility, you can opt into exposing JSON in the 2.0 format instead.</span></span> <span data-ttu-id="bf639-155">此2.0 格式對於目前支援 OpenAPI 2.0 版的 Microsoft Power Apps 和 Microsoft Flow 等整合而言很重要。</span><span class="sxs-lookup"><span data-stu-id="bf639-155">This 2.0 format is important for integrations such as Microsoft Power Apps and Microsoft Flow that currently support OpenAPI version 2.0.</span></span> <span data-ttu-id="bf639-156">若要加入宣告2.0 格式，請 `SerializeAsV2` 在中設定 `Startup.Configure` 下列屬性：</span><span class="sxs-lookup"><span data-stu-id="bf639-156">To opt into the 2.0 format, set the `SerializeAsV2` property in `Startup.Configure`:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a><span data-ttu-id="bf639-157">自訂與擴充</span><span class="sxs-lookup"><span data-stu-id="bf639-157">Customize and extend</span></span>

<span data-ttu-id="bf639-158">Swagger 提供用來記載物件模型和自訂 UI 以符合佈景主題的選項。</span><span class="sxs-lookup"><span data-stu-id="bf639-158">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="bf639-159">在 `Startup` 類別中，加入下列命名空間：</span><span class="sxs-lookup"><span data-stu-id="bf639-159">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="bf639-160">API 資訊與描述</span><span class="sxs-lookup"><span data-stu-id="bf639-160">API info and description</span></span>

<span data-ttu-id="bf639-161">傳遞至 `AddSwaggerGen` 方法的組態動作會新增作者、授權和描述等資訊：</span><span class="sxs-lookup"><span data-stu-id="bf639-161">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

<span data-ttu-id="bf639-162">在 `Startup` 類別中，匯入下列命名空間以使用 `OpenApiInfo` 類別：</span><span class="sxs-lookup"><span data-stu-id="bf639-162">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="bf639-163">使用 `OpenApiInfo` 類別，修改顯示在 UI 中的資訊：</span><span class="sxs-lookup"><span data-stu-id="bf639-163">Using the `OpenApiInfo` class, modify the information displayed in the UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="bf639-164">Swagger UI 會顯示版本資訊：</span><span class="sxs-lookup"><span data-stu-id="bf639-164">The Swagger UI displays the version's information:</span></span>

![含有版本資訊的 Swagger UI：描述、作者，以及查看更多連結](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="bf639-166">XML 註解</span><span class="sxs-lookup"><span data-stu-id="bf639-166">XML comments</span></span>

<span data-ttu-id="bf639-167">XML 註解可以使用下列方式啟用：</span><span class="sxs-lookup"><span data-stu-id="bf639-167">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="bf639-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bf639-168">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="bf639-169">以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取 [編輯 <專案名稱>.csproj]。</span><span class="sxs-lookup"><span data-stu-id="bf639-169">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="bf639-170">將醒目提示的程式碼行手動新增至 *.csproj* 檔案：</span><span class="sxs-lookup"><span data-stu-id="bf639-170">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="bf639-171">以滑鼠右鍵按一下 **方案總管** 中的專案，然後選取 [ **屬性**]。</span><span class="sxs-lookup"><span data-stu-id="bf639-171">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="bf639-172">在 [**組建**] 索引標籤的 [**輸出**] 區段下，選取 [ **XML 檔** 檔案] 方塊。</span><span class="sxs-lookup"><span data-stu-id="bf639-172">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="bf639-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="bf639-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="bf639-174">從 [Solution Pad] 中，按下 [控制項]，然後按一下專案名稱。</span><span class="sxs-lookup"><span data-stu-id="bf639-174">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="bf639-175">流覽至 [**工具**  >  **編輯** 檔案]。</span><span class="sxs-lookup"><span data-stu-id="bf639-175">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="bf639-176">將醒目提示的程式碼行手動新增至 *.csproj* 檔案：</span><span class="sxs-lookup"><span data-stu-id="bf639-176">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="bf639-177">開啟 [專案選項] 對話方塊 > [組建]**[編譯器]** > </span><span class="sxs-lookup"><span data-stu-id="bf639-177">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="bf639-178">勾選 [**一般選項**] 區段底下的 [**產生 xml 檔**] 方塊</span><span class="sxs-lookup"><span data-stu-id="bf639-178">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="bf639-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="bf639-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="bf639-180">將醒目提示的程式碼行手動新增至 *.csproj* 檔案：</span><span class="sxs-lookup"><span data-stu-id="bf639-180">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="bf639-181">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="bf639-181">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bf639-182">將醒目提示的程式碼行手動新增至 *.csproj* 檔案：</span><span class="sxs-lookup"><span data-stu-id="bf639-182">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="bf639-183">啟用 XML 註解，可提供未記載之公用類型與成員的偵錯資訊。</span><span class="sxs-lookup"><span data-stu-id="bf639-183">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="bf639-184">未記載的類型和成員會以警告訊息表示。</span><span class="sxs-lookup"><span data-stu-id="bf639-184">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="bf639-185">例如，下列訊息指出警告碼 1591 的違規：</span><span class="sxs-lookup"><span data-stu-id="bf639-185">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="bf639-186">在專案檔中定義要忽略的警告碼清單 (以分號分隔)，即可隱藏警告。</span><span class="sxs-lookup"><span data-stu-id="bf639-186">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="bf639-187">將警告碼附加至 `$(NoWarn);` 也會套用 [C# 預設值](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)。</span><span class="sxs-lookup"><span data-stu-id="bf639-187">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="bf639-188">若只要針對特定成員隱藏，請將程式碼放在 [#pragma 警告](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning)前置處理器指示詞中。</span><span class="sxs-lookup"><span data-stu-id="bf639-188">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="bf639-189">這種方法適用于不應透過 API 檔公開的程式碼。在下列範例中，會忽略整個類別的警告程式碼 CS1591 `Program` 。</span><span class="sxs-lookup"><span data-stu-id="bf639-189">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="bf639-190">會在類別定義結尾處還原強制執行的警告碼。</span><span class="sxs-lookup"><span data-stu-id="bf639-190">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="bf639-191">以逗號分隔清單指定多個警告碼。</span><span class="sxs-lookup"><span data-stu-id="bf639-191">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="bf639-192">設定 Swagger 以使用先前指示所產生的 XML 檔案。</span><span class="sxs-lookup"><span data-stu-id="bf639-192">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="bf639-193">對於 Linux 或非 Windows 作業系統，檔案名稱和路徑可以區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="bf639-193">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="bf639-194">例如，*TodoApi.XML* 檔案在 Windows 上有效，但在 CentOS 上則無效。</span><span class="sxs-lookup"><span data-stu-id="bf639-194">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="bf639-195">在上述程式碼中，會使用 [反映](/dotnet/csharp/programming-guide/concepts/reflection) 來建立符合 web API 專案之 XML 檔案名的名稱。</span><span class="sxs-lookup"><span data-stu-id="bf639-195">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="bf639-196">[AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) 屬性用來建構 XML 檔案的路徑。</span><span class="sxs-lookup"><span data-stu-id="bf639-196">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="bf639-197">某些 Swagger 功能 (例如輸入參數的結構描述，或來自相對應屬性的 HTTP 方法和回應碼) 能在不使用 XML 文件檔案的情況下運作。</span><span class="sxs-lookup"><span data-stu-id="bf639-197">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="bf639-198">對於大部分的功能 (也就是方法摘要，以及參數和回應碼的描述) 而言，皆必須使用 XML 檔案。</span><span class="sxs-lookup"><span data-stu-id="bf639-198">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="bf639-199">透過在區段標題新增描述，對動作新增三斜線註解，可增強 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="bf639-199">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="bf639-200">[\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary)在動作上方新增元素 `Delete` ：</span><span class="sxs-lookup"><span data-stu-id="bf639-200">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="bf639-201">Swagger UI 會顯示上述程式碼的 `<summary>` 項目的內部文字：</span><span class="sxs-lookup"><span data-stu-id="bf639-201">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![顯示 XML 註解 'Deletes a specific TodoItem.' 的 Swagger UI](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="bf639-204">UI 是由產生的 JSON 結構描述所驅動：</span><span class="sxs-lookup"><span data-stu-id="bf639-204">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="bf639-205">將 [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 元素加入至 `Create` 動作方法檔。</span><span class="sxs-lookup"><span data-stu-id="bf639-205">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="bf639-206">它會補充 `<summary>` 項目中指定的資訊，並提供更強固的 Swagger UI。</span><span class="sxs-lookup"><span data-stu-id="bf639-206">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="bf639-207">`<remarks>` 項目內容可以包含文字、JSON 或 XML。</span><span class="sxs-lookup"><span data-stu-id="bf639-207">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="bf639-208">請注意使用這些額外註解的 UI 增強功能：</span><span class="sxs-lookup"><span data-stu-id="bf639-208">Notice the UI enhancements with these additional comments:</span></span>

![顯示額外註解的 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="bf639-210">資料註解</span><span class="sxs-lookup"><span data-stu-id="bf639-210">Data annotations</span></span>

<span data-ttu-id="bf639-211">使用 [ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 命名空間中的屬性標記模型，以協助驅動 Swagger UI 元件。</span><span class="sxs-lookup"><span data-stu-id="bf639-211">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="bf639-212">將 `[Required]` 屬性 (attribute) 新增至 `TodoItem` 類別的 `Name` 屬性 (property)：</span><span class="sxs-lookup"><span data-stu-id="bf639-212">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="bf639-213">這個屬性的存在會變更 UI 行為，並改變基礎的 JSON 結構描述：</span><span class="sxs-lookup"><span data-stu-id="bf639-213">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="bf639-214">將 `[Produces("application/json")]` 屬性新增至 API 控制器。</span><span class="sxs-lookup"><span data-stu-id="bf639-214">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="bf639-215">其目的是要宣告控制器的動作支援回應內容類型為 *application/json*：</span><span class="sxs-lookup"><span data-stu-id="bf639-215">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="bf639-216">[ **回應內容類型** ] 下拉式清單會選取此內容類型作為控制器的 [取得動作] 的預設值：</span><span class="sxs-lookup"><span data-stu-id="bf639-216">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![含有預設回應內容類型的 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="bf639-218">當 Web API 中的資料註釋使用量增加時，UI 和 API 說明頁面會變得更清楚且更有用。</span><span class="sxs-lookup"><span data-stu-id="bf639-218">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="bf639-219">描述回應類型</span><span class="sxs-lookup"><span data-stu-id="bf639-219">Describe response types</span></span>

<span data-ttu-id="bf639-220">取用 Web API 的開發人員最關心的是傳回的內容 &mdash; 特別是回應類型和錯誤碼 (如果不是標準的話)。</span><span class="sxs-lookup"><span data-stu-id="bf639-220">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="bf639-221">回應類型和錯誤碼會在 XML 註解及資料註解中表示。</span><span class="sxs-lookup"><span data-stu-id="bf639-221">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="bf639-222">`Create` 動作在成功時會傳回 HTTP 201 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="bf639-222">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="bf639-223">張貼的要求主體為 Null 時，會傳回 HTTP 400 狀態碼。</span><span class="sxs-lookup"><span data-stu-id="bf639-223">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="bf639-224">如果 Swagger UI 中沒有適當的文件，取用者便會缺乏對這些預期結果的了解。</span><span class="sxs-lookup"><span data-stu-id="bf639-224">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="bf639-225">在下列範例中新增強調顯示的那幾行來修正該問題：</span><span class="sxs-lookup"><span data-stu-id="bf639-225">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="bf639-226">現在，Swagger UI 清楚記載了預期的 HTTP 回應碼：</span><span class="sxs-lookup"><span data-stu-id="bf639-226">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![顯示 POST 回應類別描述 'Returns the newly created Todo item'，並針對回應訊息下方的狀態碼和原因顯示 '400 - If the item is null' 的 Swagger UI](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="bf639-228">在 ASP.NET Core 2.2 或更新版本中，慣例可作為使用 `[ProducesResponseType]` 明確裝飾個別動作的替代方案。</span><span class="sxs-lookup"><span data-stu-id="bf639-228">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="bf639-229">如需詳細資訊，請參閱<xref:web-api/advanced/conventions>。</span><span class="sxs-lookup"><span data-stu-id="bf639-229">For more information, see <xref:web-api/advanced/conventions>.</span></span>

<span data-ttu-id="bf639-230">為了支援 `[ProducesResponseType]` 裝飾， [Swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) 會提供延伸模組，以啟用及擴充回應、架構和參數中繼資料。</span><span class="sxs-lookup"><span data-stu-id="bf639-230">To support the `[ProducesResponseType]` decoration, the [Swashbuckle.AspNetCore.Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) package offers extensions to enable and enrich the response, schema, and parameter metadata.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="bf639-231">自訂 UI</span><span class="sxs-lookup"><span data-stu-id="bf639-231">Customize the UI</span></span>

<span data-ttu-id="bf639-232">預設的 UI 都可以正常運作。</span><span class="sxs-lookup"><span data-stu-id="bf639-232">The default UI is both functional and presentable.</span></span> <span data-ttu-id="bf639-233">不過，API 的文件頁面應該表示您的品牌或佈景主題。</span><span class="sxs-lookup"><span data-stu-id="bf639-233">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="bf639-234">設定 Swashbuckle 元件商標時，需要新增資源來處理靜態檔案，然後建置裝載這些檔案的資料夾結構。</span><span class="sxs-lookup"><span data-stu-id="bf639-234">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="bf639-235">如果以 .NET Framework 或 .NET Core 1.x 為目標，請將 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet 套件新增至您的專案：</span><span class="sxs-lookup"><span data-stu-id="bf639-235">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="bf639-236">如果以 .NET Core 2.x 為目標並使用[中繼套件](xref:fundamentals/metapackage)，則已安裝上述 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="bf639-236">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="bf639-237">啟用靜態檔案中介軟體：</span><span class="sxs-lookup"><span data-stu-id="bf639-237">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="bf639-238">若要插入其他 CSS 樣式表單，請將它們新增至專案的 [ *wwwroot* ] 資料夾，並在中介軟體選項中指定相對路徑：</span><span class="sxs-lookup"><span data-stu-id="bf639-238">To inject additional CSS stylesheets, add them to the project's *wwwroot* folder and specify the relative path in the middleware options:</span></span>

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```

## <a name="additional-resources"></a><span data-ttu-id="bf639-239">其他資源</span><span class="sxs-lookup"><span data-stu-id="bf639-239">Additional resources</span></span>

* [<span data-ttu-id="bf639-240">Microsoft Learn：使用 Swagger 檔改善 API 的開發人員體驗</span><span class="sxs-lookup"><span data-stu-id="bf639-240">Microsoft Learn: Improve the developer experience of an API with Swagger documentation</span></span>](/learn/modules/improve-api-developer-experience-with-swagger/)
