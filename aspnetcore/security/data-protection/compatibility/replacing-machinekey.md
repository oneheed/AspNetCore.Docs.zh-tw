---
title: 取代 ASP.NET Core 中的 ASP.NET machineKey
author: rick-anderson
description: 探索如何取代 ASP.NET 中的 machineKey，以允許使用新的、更安全的資料保護系統。
ms.author: riande
ms.date: 04/06/2019
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
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 7c6766fa20b0df021013da77b5d88fecefd40c62
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053055"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="8ad1d-103">取代 ASP.NET Core 中的 ASP.NET machineKey</span><span class="sxs-lookup"><span data-stu-id="8ad1d-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="8ad1d-104">`<machineKey>`ASP.NET 中的元素實作為[可取代](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)的專案。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="8ad1d-105">這可讓大部分的呼叫 ASP.NET 密碼編譯常式，以透過取代的資料保護機制（包括新的資料保護系統）來路由傳送。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="8ad1d-106">套件安裝</span><span class="sxs-lookup"><span data-stu-id="8ad1d-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="8ad1d-107">新的資料保護系統只能安裝到以 .NET 4.5.1 或更新版本為目標的現有 ASP.NET 應用程式。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="8ad1d-108">如果應用程式是以 .NET 4.5 或更低版本為目標，則安裝會失敗。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="8ad1d-109">若要將新的資料保護系統安裝到現有的 ASP.NET 4.5.1 + 專案，請 Microsoft.AspNetCore.DataProtection.SystemWeb 安裝套件。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="8ad1d-110">這將會使用 [預設](xref:security/data-protection/configuration/default-settings) 的設定來具現化資料保護系統。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="8ad1d-111">當您安裝封裝時，它會在 *Web.config* 中插入一行，告知 ASP.NET 將它用於 [大部分的密碼編譯作業](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/)，包括表單驗證、view state 和 MachineKey 的呼叫。保護。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="8ad1d-112">插入的行如下所示。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="8ad1d-113">您可以檢查欄位（如下列範例所示），以判斷新的資料保護系統是否為作用 `__VIEWSTATE` 中。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="8ad1d-114">"CfDJ8" 是魔術 "09 F0 C9 F0" 標頭的 base64 標記法，用來識別受資料保護系統保護的承載。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="8ad1d-115">封裝設定</span><span class="sxs-lookup"><span data-stu-id="8ad1d-115">Package configuration</span></span>

<span data-ttu-id="8ad1d-116">資料保護系統會使用預設的零安裝設定來具現化。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="8ad1d-117">不過，因為依預設，索引鍵會保存到本機檔案系統，所以這不適用於部署在伺服器陣列中的應用程式。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="8ad1d-118">若要解決此問題，您可以建立子類別 DataProtectionStartup 和覆寫其 ConfigureServices 方法的類型，以提供設定。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="8ad1d-119">以下是自訂資料保護啟動類型的範例，其會設定金鑰的保存位置和待用加密方式。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="8ad1d-120">它也會藉由提供自己的應用程式名稱，來覆寫預設的應用程式隔離原則。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="8ad1d-121">您也可以使用 `<machineKey applicationName="my-app" ... />` 來取代明確呼叫 SetApplicationName。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="8ad1d-122">這是一種便利的機制，可避免強制開發人員建立 DataProtectionStartup 衍生型別（如果他們想要設定應用程式名稱的話）。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="8ad1d-123">若要啟用此自訂設定，請返回 Web.config，並尋找 `<appSettings>` 套件安裝新增至設定檔的元素。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="8ad1d-124">它看起來會像下列標記：</span><span class="sxs-lookup"><span data-stu-id="8ad1d-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="8ad1d-125">使用您剛才建立之 DataProtectionStartup 衍生類型的元件限定名稱填入空白值。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="8ad1d-126">如果應用程式的名稱是 DataProtectionDemo，這看起來會如下所示。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="8ad1d-127">新設定的資料保護系統現在已準備好在應用程式內使用。</span><span class="sxs-lookup"><span data-stu-id="8ad1d-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
