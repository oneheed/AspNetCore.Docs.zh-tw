---
title: 裝載套件組合
author: rick-anderson
description: 瞭解如何設定 .NET Core 裝載套件組合。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755244"
---
# <a name="the-net-core-hosting-bundle"></a>.NET Core 裝載套件組合

.NET Core 裝載套件組合是 .NET Core 執行時間和 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)的安裝程式。 套件組合可讓 ASP.NET Core 的應用程式與 IIS 搭配執行。

## <a name="install-the-net-core-hosting-bundle"></a>安裝 .NET Core 裝載套件組合

> [!IMPORTANT]
> 若裝載套件組合在 IIS 之前安裝，則必須對該套件組合安裝進行修復。 請在安裝 IIS 之後，再次執行裝載套件組合安裝程式。
>
> 如果在安裝 64 位元 (x64) 版本的 .NET Core 後才安裝裝載套件組合，那麼可能會遺漏 SDK ([未偵測到 .NET Core SDK](xref:test/troubleshoot#no-net-core-sdks-were-detected))。 若要解決此問題，請參閱 <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>。

### <a name="direct-download-current-version"></a>直接下載 (目前版本)

使用下列連結下載安裝程式：

[目前的 .NET Core 裝載套件組合安裝程式 (直接下載)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>安裝程式的先前版本

若要取得安裝程式的先前版本：

1. 流覽至 [ [下載 .Net Core](https://dotnet.microsoft.com/download/dotnet-core) ] 頁面。
1. 選取所需的 .NET Core 版本。
1. 在 [執行應用程式 - 執行階段]**** 欄中，尋找想要的 .NET Core 執行階段版本列。
1. 使用 **裝載** 套件組合連結來下載安裝程式。

> [!WARNING]
> 某些安裝程式包含已達到期生命週期結束 (EOL) 的發行版本，這些發行版本已不受 Microsoft 支援。 如需詳細資訊，請參閱[支援原則](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) \(英文 \)。

### <a name="install-the-hosting-bundle"></a>安裝裝載套件組合

1. 在伺服器上執行安裝程式。 從系統管理員命令殼層執行安裝程式時，有 下列參數可用：

   * `OPT_NO_ANCM=1`：略過安裝 ASP.NET Core 模組。
   * `OPT_NO_RUNTIME=1`：略過安裝 .NET Core 執行時間。 當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。
   * `OPT_NO_SHAREDFX=1`：略過安裝 ASP.NET 共用架構 (ASP.NET 執行時間) 。 當伺服器僅裝載 [獨立部署 (SCD) ](/dotnet/core/deploying/#self-contained-deployments-scd)時使用。
   * `OPT_NO_X86=1`：略過安裝 x86 執行時間。 當您確定不會裝載 32 位元應用程式時，請使用此參數。 如果將來有可能同時裝載 32 位元和 64 位元應用程式，請不要使用此參數並安裝這兩個執行階段。
   * `OPT_NO_SHARED_CONFIG_CHECK=1`：當共用設定 (`applicationHost.config`) 位於與 IIS 安裝相同的電腦上時，請停用 Iis 共用設定的檢查。 *只在 ASP.NET Core 2.2 或更新版本的裝載套件組合安裝程式上可用。* 如需詳細資訊，請參閱<xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>。
1. 重新開機系統，或在命令 shell 中執行下列命令：

   ```console
   net stop was /y
   net start w3svc
   ```
   重新啟動 IIS 將能偵測到由安裝程式對系統路徑 (此為環境變數) 所做出的變更。

ASP.NET Core 不會針對共用架構套件的修補程式版本採用向前復原行為。 藉由安裝新的裝載套件組合升級共用架構之後，請重新開機系統，或在命令 shell 中執行下列命令：

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> 如需 IIS 共用組態的資訊，請參閱[使用 IIS 共用組態的 ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration)。

## <a name="module-version-and-hosting-bundle-installer-logs"></a>模組版本和裝載套件組合安裝程式記錄檔

判斷已安裝的 ASP.NET Core 模組版本：

1. 在主控系統上，流覽至 `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` 。
1. 找出檔案 `aspnetcorev2.dll` 。
1. 在該檔案上按一下滑鼠右鍵，然後從關聯式功能表中選取 [內容]****。
1. 選取 [ **詳細資料** ] 索引標籤。檔案 **版本** 和 **產品版本** 代表已安裝的模組版本。

模組的裝載套件組合安裝程式記錄檔位於 `C:\Users\%UserName%\AppData\Local\Temp` 。 檔案的名稱 `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` ，其中預留位置是檔案的 `{TIMESTAMP}` 時間戳記。
