---
title: 將設定遷移至 ASP.NET Core
author: ardalis
description: 瞭解如何將 configuration 從 ASP.NET MVC 專案遷移至 ASP.NET Core MVC 專案。
ms.author: riande
ms.date: 10/14/2016
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
uid: migration/configuration
ms.openlocfilehash: c3957bf45dddcead24f7bb0f2702bf1a08950bdd
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587940"
---
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="7327e-103">將設定遷移至 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7327e-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="7327e-104">作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="7327e-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="7327e-105">在前一篇文章中，我們開始將 [ASP.NET mvc 專案遷移至 ASP.NET CORE mvc](xref:migration/mvc)。</span><span class="sxs-lookup"><span data-stu-id="7327e-105">In the previous article, we began to [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="7327e-106">在本文中，我們會遷移設定。</span><span class="sxs-lookup"><span data-stu-id="7327e-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="7327e-107">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="7327e-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="7327e-108">設定組態</span><span class="sxs-lookup"><span data-stu-id="7327e-108">Setup configuration</span></span>

<span data-ttu-id="7327e-109">ASP.NET Core 不會再使用舊版 ASP.NET 所使用的 *global.asax* 和 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="7327e-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="7327e-110">在舊版的 ASP.NET 中，應用程式啟動邏輯會置於 `Application_StartUp` *global.asax* 內的方法中。</span><span class="sxs-lookup"><span data-stu-id="7327e-110">In the earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="7327e-111">之後，在 ASP.NET MVC 中， *Startup.cs* 檔案會包含在專案的根目錄中;而且，它會在應用程式啟動時呼叫。</span><span class="sxs-lookup"><span data-stu-id="7327e-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="7327e-112">ASP.NET Core 已完全採用此方法，方法是將所有啟動邏輯放在 *Startup.cs* 檔案中。</span><span class="sxs-lookup"><span data-stu-id="7327e-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="7327e-113">ASP.NET Core 中也已取代 *web.config* 檔案。</span><span class="sxs-lookup"><span data-stu-id="7327e-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="7327e-114">現在設定本身可以設定為 *Startup.cs* 中所述的應用程式啟動程式的一部分。</span><span class="sxs-lookup"><span data-stu-id="7327e-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="7327e-115">設定仍可以利用 XML 檔案，但 ASP.NET 核心專案通常會將設定值放在 JSON 格式的檔案中，例如 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="7327e-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="7327e-116">ASP.NET Core 的設定系統也可以輕鬆地存取環境變數，以便為環境特有的值提供 [更安全且更穩固的位置](xref:security/app-secrets) 。</span><span class="sxs-lookup"><span data-stu-id="7327e-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="7327e-117">這特別適用于不應簽入原始檔控制的秘密，例如連接字串和 API 金鑰。</span><span class="sxs-lookup"><span data-stu-id="7327e-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="7327e-118">若要深入瞭解 ASP.NET Core [中的設定](xref:fundamentals/configuration/index) ，請參閱設定。</span><span class="sxs-lookup"><span data-stu-id="7327e-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="7327e-119">在本文中，我們會從 [先前的文章](xref:migration/mvc)中開始部分遷移的 ASP.NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="7327e-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="7327e-120">若要設定設定，請將下列函式和屬性新增至位於專案根目錄中的 *Startup.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="7327e-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="7327e-121">請注意，此時 *Startup.cs* 檔案不會進行編譯，因為我們仍然需要加入下列 `using` 語句：</span><span class="sxs-lookup"><span data-stu-id="7327e-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="7327e-122">*appsettings.json* 使用適當的專案範本，將檔案加入至專案的根目錄：</span><span class="sxs-lookup"><span data-stu-id="7327e-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![新增 AppSettings JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="7327e-124">從 web.config 遷移設定設定</span><span class="sxs-lookup"><span data-stu-id="7327e-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="7327e-125">我們的 ASP.NET MVC 專案在 *web.config* 的元素中包含必要的資料庫連接字串 `<connectionStrings>` 。</span><span class="sxs-lookup"><span data-stu-id="7327e-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="7327e-126">在我們的 ASP.NET Core 專案中，我們會將此資訊儲存在檔案中 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="7327e-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="7327e-127">開啟 *appsettings.json* ，並請注意它已包含下列內容：</span><span class="sxs-lookup"><span data-stu-id="7327e-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="7327e-128">在上面所述的醒目提示行中，將資料庫的名稱從 **_CHANGE_ME** 變更為您的資料庫名稱。</span><span class="sxs-lookup"><span data-stu-id="7327e-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="7327e-129">摘要</span><span class="sxs-lookup"><span data-stu-id="7327e-129">Summary</span></span>

<span data-ttu-id="7327e-130">ASP.NET Core 會將應用程式的所有啟動邏輯放入單一檔案中，而您可以在其中定義和設定必要的服務和相依性。</span><span class="sxs-lookup"><span data-stu-id="7327e-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="7327e-131">它會以彈性的設定功能取代 *web.config* 檔案，該功能可利用各種不同的檔案格式，例如 JSON 和環境變數。</span><span class="sxs-lookup"><span data-stu-id="7327e-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
