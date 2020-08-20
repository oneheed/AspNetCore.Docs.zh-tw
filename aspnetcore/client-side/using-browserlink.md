---
title: ASP.NET Core 中的瀏覽器連結
author: ncarandini
description: 說明瀏覽器連結如何將開發環境與一或多個網頁瀏覽器連結在一起的 Visual Studio 功能。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: ab4ca78fa50768ff66536608a7cf03e73aecf73a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628816"
---
# <a name="browser-link-in-aspnet-core"></a>ASP.NET Core 中的瀏覽器連結

[Nicolò Carandini](https://github.com/ncarandini)、 [Mike Wasson](https://github.com/MikeWasson)和[Tom Dykstra](https://github.com/tdykstra)

瀏覽器連結是 Visual Studio 功能。 它會在開發環境和一或多個網頁瀏覽器之間建立通道。 您可以使用瀏覽器連結，一次在數個瀏覽器中重新整理 web 應用程式，這對跨瀏覽器測試很有用。

## <a name="browser-link-setup"></a>瀏覽器連結設定

::: moniker range=">= aspnetcore-3.0"

將 [VisualStudio BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 套件新增至您的專案。 針對 ASP.NET Core Razor 頁面或 MVC 專案，也可以依照中的 Razor 說明，啟用 (*cshtml*) 檔案的執行時間編譯 <xref:mvc/views/view-compilation> 。 Razor 只有在已啟用執行時間編譯時，才會套用語法變更。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

將 ASP.NET Core 2.0 專案轉換成 ASP.NET Core 2.1 並轉換至 [AspNetCore 應用程式中繼套件](xref:fundamentals/metapackage-app)時，請安裝瀏覽器連結功能的 [VisualStudio. BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 套件。 ASP.NET Core 2.1 專案範本預設會使用 `Microsoft.AspNetCore.App` 中繼套件。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

ASP.NET Core 2.0 **Web 應用程式**、 **空白**和 **Web API** 專案範本會使用 [AspNetCore. All 中繼套件](xref:fundamentals/metapackage)，其中包含 [BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/)的套件參考。 因此，使用 `Microsoft.AspNetCore.All` 中繼套件不需要進一步的動作，就能讓瀏覽器連結可供使用。

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

ASP.NET Core 1.x **Web 應用程式** 專案範本中有 [BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) 封裝的套件參考。 其他專案類型則需要您加入的封裝參考 `Microsoft.VisualStudio.Web.BrowserLink` 。

::: moniker-end

### <a name="configuration"></a>組態

呼叫 `Startup.Configure` 方法中的 `UseBrowserLink`：

```csharp
app.UseBrowserLink();
```

`UseBrowserLink`呼叫通常會放在只在 `if` 開發環境中啟用瀏覽器連結的區塊內。 例如：

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

如需詳細資訊，請參閱<xref:fundamentals/environments>。

## <a name="how-to-use-browser-link"></a>如何使用瀏覽器連結

當您開啟 ASP.NET Core 專案時，Visual Studio 會在 [偵錯工具] **目標** 工具列控制項旁顯示瀏覽器連結工具列控制項：

![瀏覽器連結下拉式功能表](using-browserlink/_static/browserLink-dropdown-menu.png)

您可以從瀏覽器連結的工具列控制項，：

* 一次在數個瀏覽器中重新整理 web 應用程式。
* 開啟 **瀏覽器連結儀表板**。
* 啟用或停用 **瀏覽器連結**。 注意：在 Visual Studio 中，預設會停用瀏覽器連結。
* 啟用或停用 [CSS 自動同步](#enable-or-disable-css-auto-sync)處理。

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>一次在數個瀏覽器中重新整理 web 應用程式

若要在啟動專案時選擇要啟動的單一網頁瀏覽器，請使用 **Debug 目標** 工具列控制項中的下拉式功能表：

![F5 下拉式功能表](using-browserlink/_static/debug-target-dropdown-menu.png)

若要一次開啟多個瀏覽器，請從相同的下拉式清單中選擇 **[流覽方式]** 。 按住 <kbd>Ctrl</kbd> 鍵以選取您想要的瀏覽器，然後按一下 **[流覽]**：

![一次開啟許多瀏覽器](using-browserlink/_static/open-many-browsers-at-once.png)

下列螢幕擷取畫面顯示已開啟索引視圖的 Visual Studio，以及兩個開啟的瀏覽器：

![與兩個瀏覽器範例同步](using-browserlink/_static/sync-with-two-browsers-example.png)

將滑鼠停留在瀏覽器連結工具列控制項上，以查看連接至專案的瀏覽器：

![停留提示](using-browserlink/_static/hoover-tip.png)

當您按一下瀏覽器連結的 [重新整理] 按鈕時，變更索引視圖和所有連接的瀏覽器都會更新：

![瀏覽器-同步至變更](using-browserlink/_static/browsers-sync-to-changes.png)

瀏覽器連結也適用于您從外部 Visual Studio 啟動的瀏覽器，並流覽至應用程式 URL。

### <a name="the-browser-link-dashboard"></a>瀏覽器連結儀表板

從 [瀏覽器連結] 下拉式功能表開啟 **瀏覽器連結儀表板** 視窗，以管理與開啟的瀏覽器的連接：

![開啟-browserslink-儀表板](using-browserlink/_static/open-browserlink-dashboard.png)

如果沒有連線的瀏覽器，您可以選取 [ **在瀏覽器中查看** ] 連結來啟動非偵錯工具：

![browserlink-儀表板-無連接](using-browserlink/_static/browserlink-dashboard-no-connections.png)

否則，連接的瀏覽器會顯示每個瀏覽器所顯示頁面的路徑：

![browserlink-儀表板-雙連接](using-browserlink/_static/browserlink-dashboard-two-connections.png)

您也可以按一下個別的瀏覽器名稱，只重新整理該瀏覽器。

### <a name="enable-or-disable-browser-link"></a>啟用或停用瀏覽器連結

停用瀏覽器連結之後，您必須重新整理瀏覽器以重新連接。

### <a name="enable-or-disable-css-auto-sync"></a>啟用或停用 CSS 自動同步處理

當 CSS 自動同步已啟用時，當您對 CSS 檔案進行任何變更時，會自動重新整理連接的瀏覽器。

## <a name="how-it-works"></a>運作方式

瀏覽器連結使用 [SignalR](xref:signalr/introduction) 來建立 Visual Studio 與瀏覽器之間的通道。 啟用瀏覽器連結時，Visual Studio SignalR 會當做多個用戶端 (瀏覽器) 可連接的伺服器。 瀏覽器連結也會在 ASP.NET Core 要求管線中註冊中介軟體元件。 此元件會在 `<script>` 伺服器的每個頁面要求中插入特殊參考。 您可以在瀏覽器中選取 [ **視圖來源** ]，並將其滾動至標記內容的結尾，以查看腳本參考 `<body>` ：

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

不會修改您的原始程式檔。 中介軟體元件會動態插入腳本參考。

因為瀏覽器端程式碼是所有 JavaScript，所以它適用于所有支援的瀏覽器， SignalR 而不需要瀏覽器外掛程式。
