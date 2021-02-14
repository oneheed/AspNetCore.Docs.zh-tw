---
title: ASP.NET Core 的工具 Blazor
author: guardrex
description: 瞭解可用來建立 Blazor 應用程式的工具。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: a17b16563ac12d634e6bdc32638991f45e2a66d5
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280685"
---
# <a name="tooling-for-aspnet-core-blazor"></a>ASP.NET Core 的工具 Blazor

::: zone pivot="windows"

1. 使用 **ASP.NET 和網頁程式開發** 工作負載，安裝最新版本的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 。

1. 建立新專案。

1. 選取 [ **Blazor 應用程式**]。 選取 [下一步] 。

1. 在 [專案名稱] 欄位中提供專案名稱，或接受預設專案名稱。 確認 **位置** 專案是正確的，或提供專案的位置。 選取 [建立]  。

1. 如需 Blazor WebAssembly 體驗，請選擇 **Blazor WebAssembly 應用程式** 範本。 如需 Blazor Server 體驗，請選擇 **Blazor Server 應用程式** 範本。 選取 [建立]  。

   針對裝載 Blazor WebAssembly 體驗，請選取 [裝載 **ASP.NET Core** ] 核取方塊。

   如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。

1. 按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。

如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。

::: zone-end

::: zone pivot="linux"

1. 安裝最新版本的 [.NET Core SDK](https://dotnet.microsoft.com/download)。 如果您先前已安裝 SDK，您可以在命令 shell 中執行下列命令來判斷已安裝的版本：

   ```dotnetcli
   dotnet --version
   ```

1. 安裝最新版本的 [Visual Studio Code](https://code.visualstudio.com)。

1. 安裝 Visual Studio Code 擴充功能的最新 [c #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

1. 如需 Blazor WebAssembly 體驗，請在命令介面中執行下列命令：

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   針對託管 Blazor WebAssembly 體驗，請在命令中加入 hosted 選項 (`-ho` 或 `--hosted`) 選項：
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   如需 Blazor Server 體驗，請在命令介面中執行下列命令：

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。

1. 在 Visual Studio Code 中開啟 `WebApplication1` 資料夾。

1. IDE 要求您新增資產來建立和偵測專案。 選取 [是]  。

1. 按下<kbd>Ctrl</kbd> + <kbd>F5</kbd>以執行應用程式。

## <a name="trust-a-development-certificate"></a>信任開發憑證

沒有任何集中式方法可以信任 Linux 上的憑證。 通常會採用下列其中一種方法：

* 在瀏覽器的排除清單中排除應用程式的 URL。
* 信任的所有自我簽署憑證 `localhost` 。
* 將憑證新增至瀏覽器中受信任的憑證清單。

如需詳細資訊，請參閱瀏覽器製造商和 Linux 發行版本所提供的指引。

::: zone-end

::: zone pivot="macos"

1. 安裝 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。

1. 選取  >  [檔案 **新方案**] 或 [**開始] 視窗** 中的 [建立 **新** 專案]。

1. 在側邊欄中，選取 [ **Web] 和 [主控台**  >  **應用程式**]。

   如需 Blazor WebAssembly 體驗，請選擇 **Blazor WebAssembly 應用程式** 範本。 如需 Blazor Server 體驗，請選擇 **Blazor Server 應用程式** 範本。 選取 [下一步] 。

   如需這兩個 Blazor 裝載模型的相關資訊， *Blazor WebAssembly* (獨立和裝載) 和 *Blazor Server* ，請參閱 <xref:blazor/hosting-models> 。

1. 確認 [ **驗證** ] 設定為 [ **無驗證**]。 選取 [下一步] 。

1. 針對裝載 Blazor WebAssembly 體驗，請選取 [裝載 **ASP.NET Core** ] 核取方塊。

1. 在 [ **專案名稱** ] 欄位中，為應用程式命名 `WebApplication1` 。 選取 [建立]  。

1. 選取  >  [在不進行偵錯工具的 **情況下** 執行] 以執行應用程式 *而不需偵錯工具* 使用 [**執行**  >  **開始調試** 程式] 或 [執行 (&#9654;]) 按鈕來執行應用程式，以 *使用調試* 程式來執行應用程式。

如果出現提示信任開發憑證，請信任憑證並繼續。 需要使用者和 keychain 密碼才能信任憑證。 如需信任 ASP.NET Core HTTPS 開發憑證的詳細資訊，請參閱 <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos> 。

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a>使用 Visual Studio Code 進行跨平臺 Blazor 開發

[Visual Studio Code](https://code.visualstudio.com/) 是一種開放原始碼、跨平臺的整合式開發環境， (可用來開發應用程式的 IDE) Blazor 。 使用 .NET CLI 來建立新的 Blazor 應用程式，以 Visual Studio Code 的開發。 如需詳細資訊，請參閱本文的 [Linux 版本](?pivots=linux)。

## <a name="blazor-template-options"></a>Blazor 範本選項

Blazor架構會提供範本，以針對兩個裝載模型建立新的應用程式 Blazor 。 範本可用來建立新的 Blazor 專案和方案，不論您為 Blazor 開發 (Visual Studio、Visual Studio for Mac、Visual Studio Code 或 .net CLI) 選取的工具為何：

* Blazor WebAssembly 專案範本： `blazorwasm`
* Blazor Server 專案範本： `blazorserver`

如需裝載模型的詳細資訊 Blazor ，請參閱 <xref:blazor/hosting-models> 。

您可以藉由將 [說明] 選項 (`-h` 或 `--help`) 傳送至 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令 shell 中的 CLI 命令，來取得範本選項：

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```