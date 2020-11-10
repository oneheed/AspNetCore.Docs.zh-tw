---
title: '應用程式離線檔 ( # A0) '
author: rick-anderson
description: 瞭解應用程式離線檔 (`app_offline.htm`) 如何與 ASP.NET Core 模組搭配運作。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 29f3fc5ecd18196d914a46629bc9eb50b183bf61
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431026"
---
# <a name="app-offline-file-app_offlinehtm"></a>應用程式離線檔 (`app_offline.htm`) 

ASP.NET Core 模組會使用應用程式離線檔 (`app_offline.htm`) 來關閉應用程式。

如果 `app_offline.htm` 在應用程式的根目錄中偵測到名稱為的檔案，ASP.NET Core 模組會嘗試以正常方式關閉應用程式，並停止處理傳入的要求。 如果應用程式在中定義的秒數之後仍在執行中 `shutdownTimeLimit` ，ASP.NET Core 模組會停止執行中的進程。

當檔案 `app_offline.htm` 存在時，ASP.NET Core 模組會藉由傳回檔案的內容來回應要求 `app_offline.htm` 。 移除檔案時 `app_offline.htm` ，下一個要求會啟動應用程式。

使用跨處理序裝載模型時，若未開啟連線，應用程式可能無法立即關閉。 例如，WebSocket 連線可能會延遲應用程式關機。

## <a name="locked-deployment-files"></a>已鎖定的部署檔案

當應用程式執行時，會鎖定部署資料夾中的檔案。 無法於部署期間覆寫已鎖定的檔案。

`app_offline.htm` 是釋放鎖定檔案的主要機制。 `app_offline.htm` Web Deploy 用來適當地停止和啟動應用程式。

`app_offline.htm` 您可以手動用來啟動和停止應用程式 (需要 PowerShell 5 或更新版本) ：

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

在先前的 PowerShell 腳本中：

* 預留位置 `{PATH TO APP}` 是應用程式的路徑。
* 此 `New-Item` 命令會停止應用程式集區。
* 此 `Remove-Item` 命令會啟動應用程式集區。
* `New-Item` `Remove-Item` 開發人員會提供命令與命令之間的命令來部署應用程式。

您也可以手動停止伺服器上 IIS 管理員中的應用程式集區，以解除鎖定檔案。 `app_offine.htm`使用 IIS 管理員停止並重新啟動應用程式集區時，請勿使用此檔案。
