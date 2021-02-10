---
title: ASP.NET Core 簡介 Blazor
author: guardrex
description: 探索 ASP.NET Core Blazor ，這是在 ASP.NET Core 應用程式中使用 .net 建立互動式用戶端 WEB UI 的方式。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: d42136de00ccc9b7565c5ae088e1ecf4560a63c5
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106878"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="9404a-103">ASP.NET Core 簡介 Blazor</span><span class="sxs-lookup"><span data-stu-id="9404a-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="9404a-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9404a-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9404a-105">*歡迎使用 Blazor ！*</span><span class="sxs-lookup"><span data-stu-id="9404a-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="9404a-106">Blazor 是使用 [.net](/dotnet/standard/tour)建立互動式用戶端 web UI 的架構：</span><span class="sxs-lookup"><span data-stu-id="9404a-106">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="9404a-107">使用 [c #](/dotnet/csharp/) 而非 [JavaScript](https://www.javascript.com)來建立豐富的互動式 ui。</span><span class="sxs-lookup"><span data-stu-id="9404a-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="9404a-108">共用以 .NET 撰寫的伺服器端與用戶端應用程式邏輯。</span><span class="sxs-lookup"><span data-stu-id="9404a-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="9404a-109">將 UI 轉譯為 HTML 和 CSS 以支援寬瀏覽器，包括行動裝置瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="9404a-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="9404a-110">與新式裝載平臺（例如 [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)）整合。</span><span class="sxs-lookup"><span data-stu-id="9404a-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="9404a-111">使用 .NET 進行用戶端網頁程式開發可提供下列優點：</span><span class="sxs-lookup"><span data-stu-id="9404a-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="9404a-112">以 C# 撰寫而不是 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="9404a-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="9404a-113">利用 [.net 程式庫](/dotnet/standard/class-libraries)的現有 .net 生態系統。</span><span class="sxs-lookup"><span data-stu-id="9404a-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="9404a-114">跨伺服器和用戶端共用應用程式邏輯。</span><span class="sxs-lookup"><span data-stu-id="9404a-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="9404a-115">從 .NET 的效能、可靠性和安全性中獲益。</span><span class="sxs-lookup"><span data-stu-id="9404a-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="9404a-116">利用 Windows、Linux 和 macOS 上的 [Visual Studio](https://visualstudio.microsoft.com) 保持生產力。</span><span class="sxs-lookup"><span data-stu-id="9404a-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="9404a-117">以常用的語言、架構和工具建置，不僅穩定、功能豐富，而且容易使用。</span><span class="sxs-lookup"><span data-stu-id="9404a-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="9404a-118">單元</span><span class="sxs-lookup"><span data-stu-id="9404a-118">Components</span></span>

<span data-ttu-id="9404a-119">Blazor 應用程式是以 *元件* 為基礎。</span><span class="sxs-lookup"><span data-stu-id="9404a-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="9404a-120">中的元件 Blazor 是 UI 的元素，例如頁面、對話方塊或資料輸入表單。</span><span class="sxs-lookup"><span data-stu-id="9404a-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="9404a-121">元件是內建在 [.net 元件](/dotnet/standard/assembly/) 中的 .Net c # 類別：</span><span class="sxs-lookup"><span data-stu-id="9404a-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="9404a-122">定義彈性 UI 轉譯邏輯。</span><span class="sxs-lookup"><span data-stu-id="9404a-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="9404a-123">處理使用者動作。</span><span class="sxs-lookup"><span data-stu-id="9404a-123">Handle user events.</span></span>
* <span data-ttu-id="9404a-124">可以為巢狀結構，且可重複使用。</span><span class="sxs-lookup"><span data-stu-id="9404a-124">Can be nested and reused.</span></span>
* <span data-ttu-id="9404a-125">可以共用和散發為[ Razor 類別庫](xref:razor-pages/ui-class)或[NuGet 套件](/nuget/what-is-nuget)。</span><span class="sxs-lookup"><span data-stu-id="9404a-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="9404a-126">元件類別通常是以 [Razor](xref:mvc/views/razor) 具有副檔名的標記頁面形式來撰寫 `.razor` 。</span><span class="sxs-lookup"><span data-stu-id="9404a-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="9404a-127">中的元件 Blazor 統稱為 *Razor 元件*。</span><span class="sxs-lookup"><span data-stu-id="9404a-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="9404a-128">Razor 是一種語法，可將 HTML 標籤與設計為開發人員生產力的 c # 程式碼結合。</span><span class="sxs-lookup"><span data-stu-id="9404a-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="9404a-129">Razor 可讓您使用 Visual Studio 中的 [IntelliSense](/visualstudio/ide/using-intellisense) 程式設計支援，在相同檔案中的 HTML 標籤與 c # 之間切換。</span><span class="sxs-lookup"><span data-stu-id="9404a-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="9404a-130">Razor Pages 和 MVC 也會使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9404a-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="9404a-131">不同 Razor 于建立在要求/回應模型周圍的頁面和 MVC，元件專門用於用戶端 UI 邏輯和組合。</span><span class="sxs-lookup"><span data-stu-id="9404a-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="9404a-132">Blazor 使用自然 HTML 標籤進行 UI 組合。</span><span class="sxs-lookup"><span data-stu-id="9404a-132">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="9404a-133">下列 Razor 標記示範的元件 (`Dialog.razor`) 會顯示對話方塊，並在使用者選取按鈕時處理事件：</span><span class="sxs-lookup"><span data-stu-id="9404a-133">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="9404a-134">在上述範例中， `OnYes` 是按鈕事件所觸發的 c # 方法 `onclick` 。</span><span class="sxs-lookup"><span data-stu-id="9404a-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="9404a-135">對話方塊的 text (`ChildContent`) 和 title (`Title`) 是由在其 UI 中使用此元件的下列元件所提供。</span><span class="sxs-lookup"><span data-stu-id="9404a-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="9404a-136">`Dialog`元件會使用 HTML 標籤，以內嵌在另一個元件中。</span><span class="sxs-lookup"><span data-stu-id="9404a-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="9404a-137">在下列範例中， `Index` 元件 (`Pages/Index.razor`) 使用上述 `Dialog` 元件。</span><span class="sxs-lookup"><span data-stu-id="9404a-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="9404a-138">標記的屬性會將 `Title` 標題的值傳遞給 `Dialog` 元件的 `Title` 屬性。</span><span class="sxs-lookup"><span data-stu-id="9404a-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="9404a-139">`Dialog`元件的文字 (`ChildContent`) 是由元素的內容設定 `<Dialog>` 。</span><span class="sxs-lookup"><span data-stu-id="9404a-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="9404a-140">將 `Dialog` 元件新增至 `Index` 元件時， [Visual Studio 中的 IntelliSense](/visualstudio/ide/using-intellisense) 會使用語法和參數完成來加速開發。</span><span class="sxs-lookup"><span data-stu-id="9404a-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="9404a-141">當您 `Index` 在瀏覽器中存取元件時，就會呈現此對話方塊。</span><span class="sxs-lookup"><span data-stu-id="9404a-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="9404a-142">當使用者選取該按鈕時，瀏覽器的開發人員工具主控台會顯示方法所撰寫的訊息 `OnYes` ：</span><span class="sxs-lookup"><span data-stu-id="9404a-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![在瀏覽器中以內嵌在索引元件內的對話方塊元件。](index/_static/dialog.png)

<span data-ttu-id="9404a-146">元件會轉譯成瀏覽器 [檔物件模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 的記憶體中標記法，稱為轉譯 *樹狀結構*，可用來以彈性且有效率的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="9404a-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="9404a-147">Blazor WebAssembly 是 [單一頁面應用程式， (SPA) 架構](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) ，可使用 .net 建立互動式用戶端 web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="9404a-147">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="9404a-148">Blazor WebAssembly 使用開放式 web 標準，而不使用外掛程式或將程式碼重新編譯成其他語言。</span><span class="sxs-lookup"><span data-stu-id="9404a-148">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="9404a-149">Blazor WebAssembly 適用于所有新式網頁瀏覽器，包括行動瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="9404a-149">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="9404a-150">[WebAssembly](https://webassembly.org) (縮寫) 可讓您在網頁瀏覽器內執行 .net 程式碼 `wasm` 。</span><span class="sxs-lookup"><span data-stu-id="9404a-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="9404a-151">WebAssembly 是一種精簡的位元組程式碼格式，針對快速下載和最快執行速度而最佳化。</span><span class="sxs-lookup"><span data-stu-id="9404a-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="9404a-152">WebAssembly 是開放式的 Web 標準，在不含外掛程式的網頁瀏覽器中支援。</span><span class="sxs-lookup"><span data-stu-id="9404a-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="9404a-153">WebAssembly 程式碼可以透過 JavaScript 存取瀏覽器的完整功能，稱為 *javascript 互通性*，通常會縮短為 *JAVAscript interop* 或 *JS interop*。</span><span class="sxs-lookup"><span data-stu-id="9404a-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="9404a-154">在瀏覽器中透過 WebAssembly 執行的 .NET 程式碼會在瀏覽器的 JavaScript 沙箱執行，且受沙箱所提供對用戶端電腦之惡意動作的保護。</span><span class="sxs-lookup"><span data-stu-id="9404a-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![：：：非 loc (Blazor WebAssembly) ：：：在瀏覽器中使用 WebAssembly 執行 .NET 程式碼。](index/_static/blazor-webassembly.png)

<span data-ttu-id="9404a-156">當 Blazor WebAssembly 應用程式在瀏覽器中建立並執行時：</span><span class="sxs-lookup"><span data-stu-id="9404a-156">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="9404a-157">C # 程式碼檔案和檔案 Razor 會編譯成 .net 元件。</span><span class="sxs-lookup"><span data-stu-id="9404a-157">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="9404a-158">元件和 [.net 運行](/dotnet/framework/get-started/overview) 時間會下載至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="9404a-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="9404a-159">Blazor WebAssembly 啟動 .NET 執行時間，並設定執行時間以載入應用程式的元件。</span><span class="sxs-lookup"><span data-stu-id="9404a-159">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="9404a-160">執行時間會 Blazor WebAssembly 使用 JavaScript interop 來處理 DOM 操作和瀏覽器 API 呼叫。</span><span class="sxs-lookup"><span data-stu-id="9404a-160">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="9404a-161">已發佈應用程式的大小（其承載 *大小*）是應用程式可用性的重要效能因素。</span><span class="sxs-lookup"><span data-stu-id="9404a-161">The size of the published app, its *payload size*, is a critical performance factor for an app's usability.</span></span> <span data-ttu-id="9404a-162">大型應用程式需要相對較長的時間才能下載至瀏覽器，這點會對使用者體驗造成傷害。</span><span class="sxs-lookup"><span data-stu-id="9404a-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="9404a-163">Blazor WebAssembly 優化承載大小以縮短下載時間：</span><span class="sxs-lookup"><span data-stu-id="9404a-163">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="9404a-164">當應用程式由 [中繼語言 (IL) 修剪器](xref:blazor/host-and-deploy/configure-trimmer)發行時，未使用的程式碼會從應用程式中移除。</span><span class="sxs-lookup"><span data-stu-id="9404a-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="9404a-165">HTTP 回應會進行壓縮。</span><span class="sxs-lookup"><span data-stu-id="9404a-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="9404a-166">.NET 執行階段與組件會在瀏覽器中進行快取。</span><span class="sxs-lookup"><span data-stu-id="9404a-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="9404a-167">若應用程式是透過[中繼語言 (IL) 連接器](xref:blazor/host-and-deploy/configure-linker)所發佈的，則會移除未使用的程式碼。</span><span class="sxs-lookup"><span data-stu-id="9404a-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="9404a-168">HTTP 回應會進行壓縮。</span><span class="sxs-lookup"><span data-stu-id="9404a-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="9404a-169">.NET 執行階段與組件會在瀏覽器中進行快取。</span><span class="sxs-lookup"><span data-stu-id="9404a-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="9404a-170">Blazor 將元件轉譯邏輯與應用程式 UI 更新的套用方式分離出來。</span><span class="sxs-lookup"><span data-stu-id="9404a-170">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="9404a-171">*Blazor Server* 提供在 Razor ASP.NET Core 應用程式的伺服器上裝載元件的支援。</span><span class="sxs-lookup"><span data-stu-id="9404a-171">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="9404a-172">UI 更新會透過連接來處理 [SignalR](xref:signalr/introduction) 。</span><span class="sxs-lookup"><span data-stu-id="9404a-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="9404a-173">執行時間會保留在伺服器上，並處理：</span><span class="sxs-lookup"><span data-stu-id="9404a-173">The runtime stays on the server and handles:</span></span>

* <span data-ttu-id="9404a-174">執行應用程式的 c # 程式碼。</span><span class="sxs-lookup"><span data-stu-id="9404a-174">Executing the app's C# code.</span></span>
* <span data-ttu-id="9404a-175">從瀏覽器傳送 UI 事件至伺服器。</span><span class="sxs-lookup"><span data-stu-id="9404a-175">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="9404a-176">將 UI 更新套用至已轉譯的元件，該元件會由伺服器傳回。</span><span class="sxs-lookup"><span data-stu-id="9404a-176">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="9404a-177">用 Blazor Server 來與瀏覽器通訊的連接也會用來處理 JavaScript interop 呼叫。</span><span class="sxs-lookup"><span data-stu-id="9404a-177">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![：：：非 loc (Blazor Server) ：：：在伺服器上執行 .NET 程式碼，並透過：：：無 loc (SignalR) ：：： connection 與用戶端上的檔物件模型互動](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="9404a-179">JavaScript Interop</span><span class="sxs-lookup"><span data-stu-id="9404a-179">JavaScript interop</span></span>

<span data-ttu-id="9404a-180">對於需要協力廠商 JavaScript 程式庫和瀏覽器 API 存取的應用程式，元件能夠和 JavaScript 交互操作。</span><span class="sxs-lookup"><span data-stu-id="9404a-180">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="9404a-181">元件可以使用 JavaScript 可以使用的任何程式庫或 API。</span><span class="sxs-lookup"><span data-stu-id="9404a-181">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="9404a-182">C # 程式碼可以 [呼叫 javascript 程式碼](xref:blazor/call-javascript-from-dotnet)，而 javascript 程式碼可以 [呼叫 c #](xref:blazor/call-dotnet-from-javascript)程式碼。</span><span class="sxs-lookup"><span data-stu-id="9404a-182">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="9404a-183">程式碼共用和 .NET Standard</span><span class="sxs-lookup"><span data-stu-id="9404a-183">Code sharing and .NET Standard</span></span>

<span data-ttu-id="9404a-184">Blazor 會執行 [.NET Standard](/dotnet/standard/net-standard)，讓 Blazor 專案參考符合 .NET Standard 規格的程式庫。</span><span class="sxs-lookup"><span data-stu-id="9404a-184">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="9404a-185">.NET Standard 是所有 .NET 實作都具備的 .NET API 正式規格。</span><span class="sxs-lookup"><span data-stu-id="9404a-185">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="9404a-186">.NET Standard 類別庫可以跨不同的 .NET 平臺共用，例如 Blazor .NET Framework、.Net Core、Xamarin、Mono 和 Unity。</span><span class="sxs-lookup"><span data-stu-id="9404a-186">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="9404a-187">網頁瀏覽器內不適用的 API (例如，存取檔案系統、開啟通訊端與執行緒) 均會擲回 <xref:System.PlatformNotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="9404a-187">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9404a-188">其他資源</span><span class="sxs-lookup"><span data-stu-id="9404a-188">Additional resources</span></span>

* [<span data-ttu-id="9404a-189">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="9404a-189">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="9404a-190">C# 指南</span><span class="sxs-lookup"><span data-stu-id="9404a-190">C# Guide</span></span>](/dotnet/csharp/)
* [<span data-ttu-id="9404a-191">Razor ASP.NET Core 的語法參考</span><span class="sxs-lookup"><span data-stu-id="9404a-191">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* [<span data-ttu-id="9404a-192">HTML</span><span class="sxs-lookup"><span data-stu-id="9404a-192">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="9404a-193">[很 Blazor 棒](https://github.com/AdrienTorris/awesome-blazor)社區連結</span><span class="sxs-lookup"><span data-stu-id="9404a-193">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
