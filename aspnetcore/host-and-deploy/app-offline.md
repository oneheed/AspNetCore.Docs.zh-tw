---
title: '應用程式離線檔 ( # A0) '
author: rick-anderson
description: 瞭解應用程式離線檔 (`app_offline.htm`) 如何與 ASP.NET Core 模組搭配運作。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 4d71b95680a9b160ebb25116e35096495a2eaf93
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058645"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="386b4-103">應用程式離線檔 (`app_offline.htm`) </span><span class="sxs-lookup"><span data-stu-id="386b4-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="386b4-104">ASP.NET Core 模組會使用應用程式離線檔 (`app_offline.htm`) 來關閉應用程式。</span><span class="sxs-lookup"><span data-stu-id="386b4-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="386b4-105">如果 `app_offline.htm` 在應用程式的根目錄中偵測到名稱為的檔案，ASP.NET Core 模組會嘗試以正常方式關閉應用程式，並停止處理傳入的要求。</span><span class="sxs-lookup"><span data-stu-id="386b4-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="386b4-106">如果應用程式在中定義的秒數之後仍在執行中 `shutdownTimeLimit` ，ASP.NET Core 模組會停止執行中的進程。</span><span class="sxs-lookup"><span data-stu-id="386b4-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="386b4-107">當檔案 `app_offline.htm` 存在時，ASP.NET Core 模組會藉由傳回檔案的內容來回應要求 `app_offline.htm` 。</span><span class="sxs-lookup"><span data-stu-id="386b4-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="386b4-108">移除檔案時 `app_offline.htm` ，下一個要求會啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="386b4-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="386b4-109">使用跨處理序裝載模型時，若未開啟連線，應用程式可能無法立即關閉。</span><span class="sxs-lookup"><span data-stu-id="386b4-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="386b4-110">例如，WebSocket 連線可能會延遲應用程式關機。</span><span class="sxs-lookup"><span data-stu-id="386b4-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="386b4-111">已鎖定的部署檔案</span><span class="sxs-lookup"><span data-stu-id="386b4-111">Locked deployment files</span></span>

<span data-ttu-id="386b4-112">當應用程式執行時，會鎖定部署資料夾中的檔案。</span><span class="sxs-lookup"><span data-stu-id="386b4-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="386b4-113">無法於部署期間覆寫已鎖定的檔案。</span><span class="sxs-lookup"><span data-stu-id="386b4-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="386b4-114">`app_offline.htm` 是釋放鎖定檔案的主要機制。</span><span class="sxs-lookup"><span data-stu-id="386b4-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="386b4-115">`app_offline.htm` Web Deploy 用來適當地停止和啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="386b4-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="386b4-116">`app_offline.htm` 您可以手動用來啟動和停止應用程式 (需要 PowerShell 5 或更新版本) ：</span><span class="sxs-lookup"><span data-stu-id="386b4-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

<span data-ttu-id="386b4-117">在先前的 PowerShell 腳本中：</span><span class="sxs-lookup"><span data-stu-id="386b4-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="386b4-118">預留位置 `{PATH TO APP}` 是應用程式的路徑。</span><span class="sxs-lookup"><span data-stu-id="386b4-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="386b4-119">此 `New-Item` 命令會停止應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="386b4-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="386b4-120">此 `Remove-Item` 命令會啟動應用程式集區。</span><span class="sxs-lookup"><span data-stu-id="386b4-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="386b4-121">`New-Item` `Remove-Item` 開發人員會提供命令與命令之間的命令來部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="386b4-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="386b4-122">您也可以手動停止伺服器上 IIS 管理員中的應用程式集區，以解除鎖定檔案。</span><span class="sxs-lookup"><span data-stu-id="386b4-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="386b4-123">`app_offine.htm`使用 IIS 管理員停止並重新啟動應用程式集區時，請勿使用此檔案。</span><span class="sxs-lookup"><span data-stu-id="386b4-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>
