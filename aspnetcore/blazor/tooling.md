---
title: ASP.NET Core 的工具 Blazor
author: guardrex
description: 瞭解可用來建立 Blazor 應用程式的工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/07/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: d7e3743d12c235c20cc27f6a3263e2994a9e160a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625826"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a>ASP.NET Core 的工具 Blazor

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

::: zone pivot="windows"

1. 使用**ASP.NET 和網頁程式開發**工作負載，安裝最新版本的[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 。

1. 建立新專案。

1. 選取 [ ** Blazor 應用程式**]。 選取 [下一步]  。

1. 在 [專案名稱]**** 欄位中提供專案名稱，或接受預設專案名稱。 確認 **位置** 專案是正確的，或提供專案的位置。 選取 [建立]。

1. 如需 Blazor WebAssembly 體驗，請選擇** Blazor WebAssembly 應用程式**範本。 如需 Blazor Server 體驗，請選擇** Blazor Server 應用程式**範本。 選取 [建立]。

   如需這兩個裝載模型和的詳細資訊，請 Blazor *Blazor WebAssembly* *Blazor Server* 參閱 <xref:blazor/hosting-models> 。

1. 按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。

如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。

::: zone-end

::: zone pivot="linux"

1. 安裝最新版本的 [.Net Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。 如果您先前已安裝 SDK，您可以在命令 shell 中執行下列命令來判斷已安裝的版本：

   ```dotnetcli
   dotnet --version
   ```

1. 安裝最新版本的 [Visual Studio Code](https://code.visualstudio.com/)。

1. 安裝 Visual Studio Code 擴充功能的最新 [c #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

1. 如需 Blazor WebAssembly 體驗，請在命令介面中執行下列命令：

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   如需 Blazor Server 體驗，請在命令介面中執行下列命令：

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   如需這兩個裝載模型和的詳細資訊，請 Blazor *Blazor WebAssembly* *Blazor Server* 參閱 <xref:blazor/hosting-models> 。

1. 在 Visual Studio Code 中開啟 `WebApplication1` 資料夾。

1. IDE 要求您新增資產來建立和偵測專案。 選取 [是]。

1. 按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。

## <a name="trust-a-development-certificate"></a>信任開發憑證

沒有任何集中式方法可以信任 Linux 上的憑證。 通常會採用下列其中一種方法：

* 在瀏覽器的排除清單中排除應用程式的 URL。
* 信任的所有自我簽署憑證 `localhost` 。
* 將憑證新增至瀏覽器中受信任的憑證清單。

如需詳細資訊，請參閱瀏覽器和 Linux 發行版本所提供的指引。

::: zone-end

::: zone pivot="macos"

1. 安裝 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。

1. 選取**File**  >  [檔案**新方案**] 或 [**開始] 視窗**中的 [建立**新**專案]。

1. 在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。

   如需 Blazor WebAssembly 體驗，請選擇** Blazor WebAssembly 應用程式**範本。 如需 Blazor Server 體驗，請選擇** Blazor Server 應用程式**範本。 選取 [下一步]  。

   如需這兩個裝載模型和的詳細資訊，請 Blazor *Blazor WebAssembly* *Blazor Server* 參閱 <xref:blazor/hosting-models> 。

1. 確認 [ **驗證** ] 設定為 [ **無驗證**]。 選取 [下一步]  。

1. 在 [ **專案名稱** ] 欄位中，為應用程式命名 `WebApplication1` 。 選取 [建立]。

1. 選取**Run**  >  [在不進行偵錯工具的**情況下**執行] 以執行應用程式*而不需偵錯工具* 使用 [**執行**  >  **開始調試**程式] 或 [執行 ( # A0) ] 按鈕執行應用程式，以*使用調試*程式來執行應用程式。

如果出現提示信任開發憑證，請信任憑證並繼續。 需要使用者和 keychain 密碼才能信任憑證。 如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。

::: zone-end
