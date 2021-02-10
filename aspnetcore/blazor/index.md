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
# <a name="introduction-to-aspnet-core-blazor"></a>ASP.NET Core 簡介 Blazor

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

*歡迎使用 Blazor ！*

Blazor 是使用 [.net](/dotnet/standard/tour)建立互動式用戶端 web UI 的架構：

* 使用 [c #](/dotnet/csharp/) 而非 [JavaScript](https://www.javascript.com)來建立豐富的互動式 ui。
* 共用以 .NET 撰寫的伺服器端與用戶端應用程式邏輯。
* 將 UI 轉譯為 HTML 和 CSS 以支援寬瀏覽器，包括行動裝置瀏覽器。
* 與新式裝載平臺（例如 [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)）整合。

使用 .NET 進行用戶端網頁程式開發可提供下列優點：

* 以 C# 撰寫而不是 JavaScript。
* 利用 [.net 程式庫](/dotnet/standard/class-libraries)的現有 .net 生態系統。
* 跨伺服器和用戶端共用應用程式邏輯。
* 從 .NET 的效能、可靠性和安全性中獲益。
* 利用 Windows、Linux 和 macOS 上的 [Visual Studio](https://visualstudio.microsoft.com) 保持生產力。
* 以常用的語言、架構和工具建置，不僅穩定、功能豐富，而且容易使用。

## <a name="components"></a>單元

Blazor 應用程式是以 *元件* 為基礎。 中的元件 Blazor 是 UI 的元素，例如頁面、對話方塊或資料輸入表單。

元件是內建在 [.net 元件](/dotnet/standard/assembly/) 中的 .Net c # 類別：

* 定義彈性 UI 轉譯邏輯。
* 處理使用者動作。
* 可以為巢狀結構，且可重複使用。
* 可以共用和散發為[ Razor 類別庫](xref:razor-pages/ui-class)或[NuGet 套件](/nuget/what-is-nuget)。

元件類別通常是以 [Razor](xref:mvc/views/razor) 具有副檔名的標記頁面形式來撰寫 `.razor` 。 中的元件 Blazor 統稱為 *Razor 元件*。 Razor 是一種語法，可將 HTML 標籤與設計為開發人員生產力的 c # 程式碼結合。 Razor 可讓您使用 Visual Studio 中的 [IntelliSense](/visualstudio/ide/using-intellisense) 程式設計支援，在相同檔案中的 HTML 標籤與 c # 之間切換。 Razor Pages 和 MVC 也會使用 Razor 。 不同 Razor 于建立在要求/回應模型周圍的頁面和 MVC，元件專門用於用戶端 UI 邏輯和組合。

Blazor 使用自然 HTML 標籤進行 UI 組合。 下列 Razor 標記示範的元件 (`Dialog.razor`) 會顯示對話方塊，並在使用者選取按鈕時處理事件：

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

在上述範例中， `OnYes` 是按鈕事件所觸發的 c # 方法 `onclick` 。 對話方塊的 text (`ChildContent`) 和 title (`Title`) 是由在其 UI 中使用此元件的下列元件所提供。

`Dialog`元件會使用 HTML 標籤，以內嵌在另一個元件中。 在下列範例中， `Index` 元件 (`Pages/Index.razor`) 使用上述 `Dialog` 元件。 標記的屬性會將 `Title` 標題的值傳遞給 `Dialog` 元件的 `Title` 屬性。  `Dialog`元件的文字 (`ChildContent`) 是由元素的內容設定 `<Dialog>` 。 將 `Dialog` 元件新增至 `Index` 元件時， [Visual Studio 中的 IntelliSense](/visualstudio/ide/using-intellisense) 會使用語法和參數完成來加速開發。

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

當您 `Index` 在瀏覽器中存取元件時，就會呈現此對話方塊。 當使用者選取該按鈕時，瀏覽器的開發人員工具主控台會顯示方法所撰寫的訊息 `OnYes` ：

![在瀏覽器中以內嵌在索引元件內的對話方塊元件。 當使用者選取 [是] 時，瀏覽器開發人員工具主控台會顯示 c # 程式碼所寫入的訊息！ 按鈕。](index/_static/dialog.png)

元件會轉譯成瀏覽器 [檔物件模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 的記憶體中標記法，稱為轉譯 *樹狀結構*，可用來以彈性且有效率的方式更新 UI。

## Blazor WebAssembly

Blazor WebAssembly 是 [單一頁面應用程式， (SPA) 架構](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) ，可使用 .net 建立互動式用戶端 web 應用程式。 Blazor WebAssembly 使用開放式 web 標準，而不使用外掛程式或將程式碼重新編譯成其他語言。 Blazor WebAssembly 適用于所有新式網頁瀏覽器，包括行動瀏覽器。

[WebAssembly](https://webassembly.org) (縮寫) 可讓您在網頁瀏覽器內執行 .net 程式碼 `wasm` 。 WebAssembly 是一種精簡的位元組程式碼格式，針對快速下載和最快執行速度而最佳化。 WebAssembly 是開放式的 Web 標準，在不含外掛程式的網頁瀏覽器中支援。

WebAssembly 程式碼可以透過 JavaScript 存取瀏覽器的完整功能，稱為 *javascript 互通性*，通常會縮短為 *JAVAscript interop* 或 *JS interop*。 在瀏覽器中透過 WebAssembly 執行的 .NET 程式碼會在瀏覽器的 JavaScript 沙箱執行，且受沙箱所提供對用戶端電腦之惡意動作的保護。

![：：：非 loc (Blazor WebAssembly) ：：：在瀏覽器中使用 WebAssembly 執行 .NET 程式碼。](index/_static/blazor-webassembly.png)

當 Blazor WebAssembly 應用程式在瀏覽器中建立並執行時：

* C # 程式碼檔案和檔案 Razor 會編譯成 .net 元件。
* 元件和 [.net 運行](/dotnet/framework/get-started/overview) 時間會下載至瀏覽器。
* Blazor WebAssembly 啟動 .NET 執行時間，並設定執行時間以載入應用程式的元件。 執行時間會 Blazor WebAssembly 使用 JavaScript interop 來處理 DOM 操作和瀏覽器 API 呼叫。

已發佈應用程式的大小（其承載 *大小*）是應用程式可用性的重要效能因素。 大型應用程式需要相對較長的時間才能下載至瀏覽器，這點會對使用者體驗造成傷害。 Blazor WebAssembly 優化承載大小以縮短下載時間：

::: moniker range=">= aspnetcore-5.0"

* 當應用程式由 [中繼語言 (IL) 修剪器](xref:blazor/host-and-deploy/configure-trimmer)發行時，未使用的程式碼會從應用程式中移除。
* HTTP 回應會進行壓縮。
* .NET 執行階段與組件會在瀏覽器中進行快取。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* 若應用程式是透過[中繼語言 (IL) 連接器](xref:blazor/host-and-deploy/configure-linker)所發佈的，則會移除未使用的程式碼。
* HTTP 回應會進行壓縮。
* .NET 執行階段與組件會在瀏覽器中進行快取。

::: moniker-end

## Blazor Server

Blazor 將元件轉譯邏輯與應用程式 UI 更新的套用方式分離出來。 *Blazor Server* 提供在 Razor ASP.NET Core 應用程式的伺服器上裝載元件的支援。 UI 更新會透過連接來處理 [SignalR](xref:signalr/introduction) 。

執行時間會保留在伺服器上，並處理：

* 執行應用程式的 c # 程式碼。
* 從瀏覽器傳送 UI 事件至伺服器。
* 將 UI 更新套用至已轉譯的元件，該元件會由伺服器傳回。

用 Blazor Server 來與瀏覽器通訊的連接也會用來處理 JavaScript interop 呼叫。

![：：：非 loc (Blazor Server) ：：：在伺服器上執行 .NET 程式碼，並透過：：：無 loc (SignalR) ：：： connection 與用戶端上的檔物件模型互動](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>JavaScript Interop

對於需要協力廠商 JavaScript 程式庫和瀏覽器 API 存取的應用程式，元件能夠和 JavaScript 交互操作。 元件可以使用 JavaScript 可以使用的任何程式庫或 API。 C # 程式碼可以 [呼叫 javascript 程式碼](xref:blazor/call-javascript-from-dotnet)，而 javascript 程式碼可以 [呼叫 c #](xref:blazor/call-dotnet-from-javascript)程式碼。

## <a name="code-sharing-and-net-standard"></a>程式碼共用和 .NET Standard

Blazor 會執行 [.NET Standard](/dotnet/standard/net-standard)，讓 Blazor 專案參考符合 .NET Standard 規格的程式庫。 .NET Standard 是所有 .NET 實作都具備的 .NET API 正式規格。 .NET Standard 類別庫可以跨不同的 .NET 平臺共用，例如 Blazor .NET Framework、.Net Core、Xamarin、Mono 和 Unity。

網頁瀏覽器內不適用的 API (例如，存取檔案系統、開啟通訊端與執行緒) 均會擲回 <xref:System.PlatformNotSupportedException>。

## <a name="additional-resources"></a>其他資源

* [WebAssembly](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [C# 指南](/dotnet/csharp/)
* [Razor ASP.NET Core 的語法參考](xref:mvc/views/razor)
* [HTML](https://www.w3.org/html/)
* [很 Blazor 棒](https://github.com/AdrienTorris/awesome-blazor)社區連結
