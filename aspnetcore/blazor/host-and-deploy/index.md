---
title: 裝載和部署 ASP.NET Core Blazor
author: guardrex
description: 探索如何裝載和部署 Blazor 應用程式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: e7bc44b396b46e2ac3e0279520c7cc8ea6679f5a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279774"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="76142-103">裝載和部署 ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="76142-103">Host and deploy ASP.NET Core Blazor</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="76142-104">發佈應用程式</span><span class="sxs-lookup"><span data-stu-id="76142-104">Publish the app</span></span>

<span data-ttu-id="76142-105">應用程式會在發行組態中發佈以供部署。</span><span class="sxs-lookup"><span data-stu-id="76142-105">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76142-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76142-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="76142-107">  >  從巡覽列選取 [組建 **發行 {應用程式**]。</span><span class="sxs-lookup"><span data-stu-id="76142-107">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="76142-108">選取「發佈目標」。</span><span class="sxs-lookup"><span data-stu-id="76142-108">Select the *publish target*.</span></span> <span data-ttu-id="76142-109">若要在本機發佈，請選取 [資料夾]。</span><span class="sxs-lookup"><span data-stu-id="76142-109">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="76142-110">接受 [選擇資料夾] 欄位中的預設位置，或指定不同的位置。</span><span class="sxs-lookup"><span data-stu-id="76142-110">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="76142-111">選取 [`Publish`] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="76142-111">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76142-112">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="76142-112">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="76142-113">選取 [**組建**  >  **發行至資料夾**]。</span><span class="sxs-lookup"><span data-stu-id="76142-113">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="76142-114">確認要接收已發佈資產的資料夾，然後選取 [] **`Publish`** 。</span><span class="sxs-lookup"><span data-stu-id="76142-114">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="76142-115">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="76142-115">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="76142-116">使用 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令來發佈具有發行設定的應用程式：</span><span class="sxs-lookup"><span data-stu-id="76142-116">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="76142-117">發佈應用程式會觸發專案相依性的[還原](/dotnet/core/tools/dotnet-restore)並[建置](/dotnet/core/tools/dotnet-build)專案，然後才會建立資產以供部署。</span><span class="sxs-lookup"><span data-stu-id="76142-117">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="76142-118">在建置程序中，會移除未使用的方法和組件，減少應用程式的下載大小和載入時間。</span><span class="sxs-lookup"><span data-stu-id="76142-118">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="76142-119">發佈位置：</span><span class="sxs-lookup"><span data-stu-id="76142-119">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="76142-120">獨立：將應用程式發佈到 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="76142-120">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="76142-121">若要將應用程式部署為靜態網站，請將該資料夾的內容複寫 `wwwroot` 到靜態網站主機。</span><span class="sxs-lookup"><span data-stu-id="76142-121">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="76142-122">Hosted：用戶端 Blazor WebAssembly 應用程式會 `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` 與伺服器應用程式的任何其他靜態 web 資產一起發行至伺服器應用程式的資料夾中。</span><span class="sxs-lookup"><span data-stu-id="76142-122">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="76142-123">將資料夾的內容部署 `publish` 至主機。</span><span class="sxs-lookup"><span data-stu-id="76142-123">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="76142-124">Blazor Server：應用程式會發佈到 `/bin/Release/{TARGET FRAMEWORK}/publish` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="76142-124">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="76142-125">將資料夾的內容部署 `publish` 至主機。</span><span class="sxs-lookup"><span data-stu-id="76142-125">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="76142-126">該資料夾中的資產均會部署至 Web 伺服器。</span><span class="sxs-lookup"><span data-stu-id="76142-126">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="76142-127">部署可能是手動或自動的程序，視使用中的開發工具而定。</span><span class="sxs-lookup"><span data-stu-id="76142-127">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="76142-128">應用程式基底路徑</span><span class="sxs-lookup"><span data-stu-id="76142-128">App base path</span></span>

<span data-ttu-id="76142-129">*應用程式基底路徑* 是應用程式的根 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="76142-129">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="76142-130">請考慮下列 ASP.NET Core 應用程式和 Blazor 子應用程式：</span><span class="sxs-lookup"><span data-stu-id="76142-130">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="76142-131">ASP.NET Core 應用程式的名稱 `MyApp` 如下：</span><span class="sxs-lookup"><span data-stu-id="76142-131">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="76142-132">應用程式實際上位於 `d:/MyApp` 。</span><span class="sxs-lookup"><span data-stu-id="76142-132">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="76142-133">收到的要求 `https://www.contoso.com/{MYAPP RESOURCE}` 。</span><span class="sxs-lookup"><span data-stu-id="76142-133">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="76142-134">名為的 Blazor 應用程式 `CoolApp` 是下列子應用程式 `MyApp` ：</span><span class="sxs-lookup"><span data-stu-id="76142-134">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="76142-135">子應用程式實際上位於 `d:/MyApp/CoolApp` 。</span><span class="sxs-lookup"><span data-stu-id="76142-135">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="76142-136">收到的要求 `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` 。</span><span class="sxs-lookup"><span data-stu-id="76142-136">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="76142-137">如果未指定的其他設定 `CoolApp` ，此案例中的子應用程式就不會知道它在伺服器上的位置。</span><span class="sxs-lookup"><span data-stu-id="76142-137">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="76142-138">例如，應用程式無法對其資源建立正確的相對 url，而不知道它是否位於相對 URL 路徑 `/CoolApp/` 。</span><span class="sxs-lookup"><span data-stu-id="76142-138">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="76142-139">為了提供 Blazor 應用程式基底路徑的 `https://www.contoso.com/CoolApp/` `<base>` 設定，標記的 `href` 屬性會設定為檔案中的相對根路徑 `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` (Blazor Server) 。</span><span class="sxs-lookup"><span data-stu-id="76142-139">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="76142-140">Blazor WebAssembly (`wwwroot/index.html`):</span><span class="sxs-lookup"><span data-stu-id="76142-140">Blazor WebAssembly (`wwwroot/index.html`):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="76142-141">Blazor Server (`Pages/_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="76142-141">Blazor Server (`Pages/_Host.cshtml`):</span></span>

```html
<base href="~/CoolApp/">
```

<span data-ttu-id="76142-142">Blazor Server 應用程式會另外設定伺服器端基底路徑，方法是 <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> 在應用程式的要求管線中呼叫 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="76142-142">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="76142-143">藉由提供相對 URL 路徑，不在根目錄中的元件可以針對應用程式的根路徑來建立 Url。</span><span class="sxs-lookup"><span data-stu-id="76142-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="76142-144">目錄結構不同層級的元件可以在應用程式中的位置建立其他資源的連結。</span><span class="sxs-lookup"><span data-stu-id="76142-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="76142-145">應用程式基底路徑也會用來攔截選取的超連結，其中 `href` 連結的目標位於應用程式基底路徑 URI 空間內。</span><span class="sxs-lookup"><span data-stu-id="76142-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="76142-146">Blazor路由器會處理內部導覽。</span><span class="sxs-lookup"><span data-stu-id="76142-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="76142-147">在許多裝載案例中，應用程式的相對 URL 路徑為應用程式的根目錄。</span><span class="sxs-lookup"><span data-stu-id="76142-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="76142-148">在這些情況下，應用程式的相對 URL 基底路徑是正斜線 (`<base href="/" />`) ，這是應用程式的預設設定 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="76142-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="76142-149">在其他裝載案例中（例如 GitHub 頁面和 IIS 子應用程式），應用程式基底路徑必須設定為該應用程式的伺服器相對 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="76142-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="76142-150">若要設定應用程式的基底路徑，請在檔案 `<base>` `<head>` `Pages/_Host.cshtml` (Blazor Server) 或檔案 `wwwroot/index.html` () 的標記元素內更新標記 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="76142-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="76142-151">將 `href` 屬性值設定為 `/{RELATIVE URL PATH}/` (Blazor WebAssembly) 或 `~/{RELATIVE URL PATH}/` (Blazor Server) 。</span><span class="sxs-lookup"><span data-stu-id="76142-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (Blazor WebAssembly) or `~/{RELATIVE URL PATH}/` (Blazor Server).</span></span> <span data-ttu-id="76142-152">**需要結尾的斜線。**</span><span class="sxs-lookup"><span data-stu-id="76142-152">**The trailing slash is required.**</span></span> <span data-ttu-id="76142-153">預留位置 `{RELATIVE URL PATH}` 是應用程式的完整相對 URL 路徑。</span><span class="sxs-lookup"><span data-stu-id="76142-153">The placeholder `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="76142-154">針對 Blazor WebAssembly 具有非根相對 URL 路徑的應用程式 (例如 `<base href="/CoolApp/">`) ，應用程式 *在本機執行時*，會無法找到其資源。</span><span class="sxs-lookup"><span data-stu-id="76142-154">For a Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="76142-155">若要在本機開發和測試期間解決這個問題，您可以提供「基底路徑」引數，讓它在執行時符合 `<base>` 標籤的 `href` 值。</span><span class="sxs-lookup"><span data-stu-id="76142-155">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="76142-156">**請勿包含結尾的斜線。**</span><span class="sxs-lookup"><span data-stu-id="76142-156">**Don't include a trailing slash.**</span></span> <span data-ttu-id="76142-157">若要在本機執行應用程式時傳遞路徑基底引數，請 `dotnet run` 使用下列選項，從應用程式的目錄執行命令 `--pathbase` ：</span><span class="sxs-lookup"><span data-stu-id="76142-157">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="76142-158">若為 Blazor WebAssembly 具有 () 之相對 URL 路徑的應用程式 `/CoolApp/` `<base href="/CoolApp/">` ，則命令為：</span><span class="sxs-lookup"><span data-stu-id="76142-158">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="76142-159">Blazor WebAssembly應用程式會在本機回應 `http://localhost:port/CoolApp` 。</span><span class="sxs-lookup"><span data-stu-id="76142-159">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="76142-160">**Blazor Server設定 `MapFallbackToPage`**</span><span class="sxs-lookup"><span data-stu-id="76142-160">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="76142-161">將下列路徑傳遞至 <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> 中 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="76142-161">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="76142-162">預留位置 `{RELATIVE PATH}` 是伺服器上的非根路徑。</span><span class="sxs-lookup"><span data-stu-id="76142-162">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="76142-163">例如， `CoolApp` 如果應用程式的非根 URL) ，則為預留位置區段 `https://{HOST}:{PORT}/CoolApp/` ：</span><span class="sxs-lookup"><span data-stu-id="76142-163">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="76142-164">**裝載多個 Blazor WebAssembly 應用程式**</span><span class="sxs-lookup"><span data-stu-id="76142-164">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="76142-165">如需在託管解決方案中裝載多個應用程式的詳細資訊 Blazor WebAssembly Blazor ，請參閱 <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps> 。</span><span class="sxs-lookup"><span data-stu-id="76142-165">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="76142-166">部署</span><span class="sxs-lookup"><span data-stu-id="76142-166">Deployment</span></span>

<span data-ttu-id="76142-167">如需部署指導方針，請參閱下列主題：</span><span class="sxs-lookup"><span data-stu-id="76142-167">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
