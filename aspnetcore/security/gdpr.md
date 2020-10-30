---
title: ASP.NET Core 中的一般資料保護規定 (GDPR) 支援
author: rick-anderson
description: 瞭解如何存取 ASP.NET Core web 應用程式中的 GDPR 擴充點。
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
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
uid: security/gdpr
ms.openlocfilehash: ec65a2c8362c15716bebd6b22f5639785ba74c98
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051001"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a><span data-ttu-id="9759f-103">EU 一般資料保護規定 (GDPR) 支援 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9759f-103">EU General Data Protection Regulation (GDPR) support in ASP.NET Core</span></span>

<span data-ttu-id="9759f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9759f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9759f-105">ASP.NET Core 提供 Api 和範本，以協助符合某些 [EU 一般資料保護規定 (GDPR) ](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) 需求：</span><span class="sxs-lookup"><span data-stu-id="9759f-105">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) requirements:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="9759f-106">專案範本包含擴充點和 stub 標記，您可以將其取代為隱私權並 :::no-loc(cookie)::: 使用原則。</span><span class="sxs-lookup"><span data-stu-id="9759f-106">The project templates include extension points and stubbed markup that you can replace with your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="9759f-107">[ *頁面/隱私權* ] 頁面或 [ *視圖/首頁/隱私權* ] 會提供頁面來詳細說明您的網站隱私權原則。</span><span class="sxs-lookup"><span data-stu-id="9759f-107">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span>

<span data-ttu-id="9759f-108">若要啟用預設 :::no-loc(cookie)::: 同意功能，例如在 ASP.NET Core 3.0 範本產生的應用程式中于 ASP.NET Core 2.2 範本中找到的功能：</span><span class="sxs-lookup"><span data-stu-id="9759f-108">To enable the default :::no-loc(cookie)::: consent feature like that found in the ASP.NET Core 2.2 templates in an ASP.NET Core 3.0 template generated app:</span></span>

* <span data-ttu-id="9759f-109">加入 `using Microsoft.AspNetCore.Http` using 指示詞的清單。</span><span class="sxs-lookup"><span data-stu-id="9759f-109">Add `using Microsoft.AspNetCore.Http` to the list of using directives.</span></span>
* <span data-ttu-id="9759f-110">將[ :::no-loc(Cookie)::: PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions)新增至 `Startup.ConfigureServices` ，並[使用 :::no-loc(Cookie)::: 原則](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="9759f-110">Add [:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) to `Startup.ConfigureServices` and [Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) to `Startup.Configure`:</span></span>

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* <span data-ttu-id="9759f-111">將 :::no-loc(cookie)::: 同意部分新增至 *_Layout 的 cshtml* 檔案：</span><span class="sxs-lookup"><span data-stu-id="9759f-111">Add the :::no-loc(cookie)::: consent partial to the *_Layout.cshtml* file:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* <span data-ttu-id="9759f-112">將 *\_ :::no-loc(Cookie)::: ConsentPartial* 檔加入至專案：</span><span class="sxs-lookup"><span data-stu-id="9759f-112">Add the *\_:::no-loc(Cookie):::ConsentPartial.cshtml* file to the project:</span></span>

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_:::no-loc(Cookie):::ConsentPartial.cshtml)]

* <span data-ttu-id="9759f-113">請選取此文章的 ASP.NET Core 2.2 版本，以閱讀同意功能的相關資訊 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="9759f-113">Select the ASP.NET Core 2.2 version of this article to read about the :::no-loc(cookie)::: consent feature.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="9759f-114">專案範本包含擴充點和 stub 標記，您可以將其取代為隱私權並 :::no-loc(cookie)::: 使用原則。</span><span class="sxs-lookup"><span data-stu-id="9759f-114">The project templates include extension points and stubbed markup that you can replace with your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="9759f-115">:::no-loc(cookie):::同意功能可讓您要求 (，並追蹤使用者的) 同意，以儲存個人資訊。</span><span class="sxs-lookup"><span data-stu-id="9759f-115">A :::no-loc(cookie)::: consent feature allows you to ask for (and track) consent from your users for storing personal information.</span></span> <span data-ttu-id="9759f-116">如果使用者尚未同意資料收集，而且應用程式已將 [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions.checkconsentneeded) 設定為，則不會將 `true` 非必要的設定 :::no-loc(cookie)::: 傳送至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="9759f-116">If a user hasn't consented to data collection and the app has [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions.checkconsentneeded) set to `true`, non-essential :::no-loc(cookie):::s aren't sent to the browser.</span></span>
* <span data-ttu-id="9759f-117">:::no-loc(Cookie):::可以標示為必要。</span><span class="sxs-lookup"><span data-stu-id="9759f-117">:::no-loc(Cookie):::s can be marked as essential.</span></span> <span data-ttu-id="9759f-118">:::no-loc(cookie):::即使使用者尚未同意且已停用追蹤，也會將重要的傳送至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="9759f-118">Essential :::no-loc(cookie):::s are sent to the browser even when the user hasn't consented and tracking is disabled.</span></span>
* <span data-ttu-id="9759f-119">停用追蹤時[，TempData 和會話 :::no-loc(cookie)::: s](#tempdata)無法正常運作。</span><span class="sxs-lookup"><span data-stu-id="9759f-119">[TempData and Session :::no-loc(cookie):::s](#tempdata) aren't functional when tracking is disabled.</span></span>
* <span data-ttu-id="9759f-120">[ [ :::no-loc(Identity)::: 管理](#pd)] 頁面會提供下載和刪除使用者資料的連結。</span><span class="sxs-lookup"><span data-stu-id="9759f-120">The [:::no-loc(Identity)::: manage](#pd) page provides a link to download and delete user data.</span></span>

<span data-ttu-id="9759f-121">[範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample)可讓您測試新增至 ASP.NET Core 2.1 範本的大部分 GDPR 擴充點和 api。</span><span class="sxs-lookup"><span data-stu-id="9759f-121">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) allows you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span> <span data-ttu-id="9759f-122">請參閱 [自述](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) 檔以取得測試指示。</span><span class="sxs-lookup"><span data-stu-id="9759f-122">See the [ReadMe](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) file for testing instructions.</span></span>

<span data-ttu-id="9759f-123">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="9759f-123">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a><span data-ttu-id="9759f-124">ASP.NET Core 範本產生的程式碼中的 GDPR 支援</span><span class="sxs-lookup"><span data-stu-id="9759f-124">ASP.NET Core GDPR support in template-generated code</span></span>

<span data-ttu-id="9759f-125">:::no-loc(Razor)::: 以專案範本建立的頁面和 MVC 專案包含下列 GDPR 支援：</span><span class="sxs-lookup"><span data-stu-id="9759f-125">:::no-loc(Razor)::: Pages and MVC projects created with the project templates include the following GDPR support:</span></span>

* <span data-ttu-id="9759f-126">[ :::no-loc(Cookie)::: PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions)和[使用 :::no-loc(Cookie)::: 原則](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy)是在類別中設定 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="9759f-126">[:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) and [Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) are set in the `Startup` class.</span></span>
* <span data-ttu-id="9759f-127">*\_ :::no-loc(Cookie)::: ConsentPartial* [部分視圖](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="9759f-127">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* [partial view](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="9759f-128">此檔案中包含 [ **接受** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="9759f-128">An **Accept** button is included in this file.</span></span> <span data-ttu-id="9759f-129">當使用者按一下 [ **接受** ] 按鈕時，會提供同意存放區 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="9759f-129">When the user clicks the **Accept** button, consent to store :::no-loc(cookie):::s is provided.</span></span>
* <span data-ttu-id="9759f-130">[ *頁面/隱私權* ] 頁面或 [ *視圖/首頁/隱私權* ] 會提供頁面來詳細說明您的網站隱私權原則。</span><span class="sxs-lookup"><span data-stu-id="9759f-130">The *Pages/Privacy.cshtml* page or *Views/Home/Privacy.cshtml* view provides a page to detail your site's privacy policy.</span></span> <span data-ttu-id="9759f-131">*\_ :::no-loc(Cookie)::: ConsentPartial. cshtml* 檔案會產生隱私權頁面的連結。</span><span class="sxs-lookup"><span data-stu-id="9759f-131">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* file generates a link to the Privacy page.</span></span>
* <span data-ttu-id="9759f-132">針對使用個別使用者帳戶所建立的應用程式，[管理] 頁面會提供下載和刪除 [個人使用者資料](#pd)的連結。</span><span class="sxs-lookup"><span data-stu-id="9759f-132">For apps created with individual user accounts, the Manage page provides links to download and delete [personal user data](#pd).</span></span>

### <a name="no-loccookiepolicyoptions-and-useno-loccookiepolicy"></a><span data-ttu-id="9759f-133">:::no-loc(Cookie):::PolicyOptions 和使用 :::no-loc(Cookie)::: 原則</span><span class="sxs-lookup"><span data-stu-id="9759f-133">:::no-loc(Cookie):::PolicyOptions and Use:::no-loc(Cookie):::Policy</span></span>

<span data-ttu-id="9759f-134">[ :::no-loc(Cookie)::: PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions)會在中初始化 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9759f-134">[:::no-loc(Cookie):::PolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyoptions) are initialized in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

<span data-ttu-id="9759f-135">[使用 :::no-loc(Cookie):::](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy)在下列情況中呼叫原則 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="9759f-135">[Use:::no-loc(Cookie):::Policy](/dotnet/api/microsoft.aspnetcore.builder.:::no-loc(cookie):::policyappbuilderextensions.use:::no-loc(cookie):::policy) is called in `Startup.Configure`:</span></span>

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_no-loccookieconsentpartialcshtml-partial-view"></a><span data-ttu-id="9759f-136">\_:::no-loc(Cookie):::ConsentPartial. cshtml 部分視圖</span><span class="sxs-lookup"><span data-stu-id="9759f-136">\_:::no-loc(Cookie):::ConsentPartial.cshtml partial view</span></span>

<span data-ttu-id="9759f-137">*\_ :::no-loc(Cookie)::: ConsentPartial* 的部分觀點：</span><span class="sxs-lookup"><span data-stu-id="9759f-137">The *\_:::no-loc(Cookie):::ConsentPartial.cshtml* partial view:</span></span>

[!code-cshtml[](gdpr/sample/RP2.2/Pages/Shared/_:::no-loc(Cookie):::ConsentPartial.cshtml)]

<span data-ttu-id="9759f-138">部分：</span><span class="sxs-lookup"><span data-stu-id="9759f-138">This partial:</span></span>

* <span data-ttu-id="9759f-139">取得使用者追蹤的狀態。</span><span class="sxs-lookup"><span data-stu-id="9759f-139">Obtains the state of tracking for the user.</span></span> <span data-ttu-id="9759f-140">如果應用程式設定為要求同意，則使用者必須先同意才能 :::no-loc(cookie)::: 追蹤。</span><span class="sxs-lookup"><span data-stu-id="9759f-140">If the app is configured to require consent, the user must consent before :::no-loc(cookie):::s can be tracked.</span></span> <span data-ttu-id="9759f-141">如果需要同意，則 :::no-loc(cookie)::: 會在配置 *\_ cshtml* 檔案所建立的導覽列頂端修正同意面板。</span><span class="sxs-lookup"><span data-stu-id="9759f-141">If consent is required, the :::no-loc(cookie)::: consent panel is fixed at top of the navigation bar created by the *\_Layout.cshtml* file.</span></span>
* <span data-ttu-id="9759f-142">提供 HTML `<p>` 元素，以摘要您的隱私權和 :::no-loc(cookie)::: 使用原則。</span><span class="sxs-lookup"><span data-stu-id="9759f-142">Provides an HTML `<p>` element to summarize your privacy and :::no-loc(cookie)::: use policy.</span></span>
* <span data-ttu-id="9759f-143">提供隱私權頁面的連結，或可讓您詳細瞭解網站隱私權原則的位置。</span><span class="sxs-lookup"><span data-stu-id="9759f-143">Provides a link to Privacy page or view where you can detail your site's privacy policy.</span></span>

## <a name="essential-no-loccookies"></a><span data-ttu-id="9759f-144">重要 :::no-loc(cookie)::: s</span><span class="sxs-lookup"><span data-stu-id="9759f-144">Essential :::no-loc(cookie):::s</span></span>

<span data-ttu-id="9759f-145">如果 :::no-loc(cookie)::: 未提供同意儲存，則只會將標示為「基本」 :::no-loc(cookie)::: 的標記傳送至瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="9759f-145">If consent to store :::no-loc(cookie):::s hasn't been provided, only :::no-loc(cookie):::s marked essential are sent to the browser.</span></span> <span data-ttu-id="9759f-146">下列程式碼會有 :::no-loc(cookie)::: 必要：</span><span class="sxs-lookup"><span data-stu-id="9759f-146">The following code makes a :::no-loc(cookie)::: essential:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/:::no-loc(Cookie):::.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-no-loccookies-arent-essential"></a><span data-ttu-id="9759f-147">TempData 提供者和會話狀態 :::no-loc(cookie)::: 不是必要的</span><span class="sxs-lookup"><span data-stu-id="9759f-147">TempData provider and session state :::no-loc(cookie):::s aren't essential</span></span>

<span data-ttu-id="9759f-148">[TempData 提供者](xref:fundamentals/app-state#tempdata) :::no-loc(cookie)::: 並不重要。</span><span class="sxs-lookup"><span data-stu-id="9759f-148">The [TempData provider](xref:fundamentals/app-state#tempdata) :::no-loc(cookie)::: isn't essential.</span></span> <span data-ttu-id="9759f-149">如果停用追蹤，TempData 提供者將無法運作。</span><span class="sxs-lookup"><span data-stu-id="9759f-149">If tracking is disabled, the TempData provider isn't functional.</span></span> <span data-ttu-id="9759f-150">若要在停用追蹤時啟用 TempData 提供者，請 :::no-loc(cookie)::: 在下列情況中將 TempData 標示為必要 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="9759f-150">To enable the TempData provider when tracking is disabled, mark the TempData :::no-loc(cookie)::: as essential in `Startup.ConfigureServices`:</span></span>

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

<span data-ttu-id="9759f-151">[會話狀態](xref:fundamentals/app-state) :::no-loc(cookie):::不是必要的。</span><span class="sxs-lookup"><span data-stu-id="9759f-151">[Session state](xref:fundamentals/app-state) :::no-loc(cookie):::s are not essential.</span></span> <span data-ttu-id="9759f-152">停用追蹤時，會話狀態無法運作。</span><span class="sxs-lookup"><span data-stu-id="9759f-152">Session state isn't functional when tracking is disabled.</span></span> <span data-ttu-id="9759f-153">下列程式碼會讓會話成為 :::no-loc(cookie)::: 必要的：</span><span class="sxs-lookup"><span data-stu-id="9759f-153">The following code makes session :::no-loc(cookie):::s essential:</span></span>

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a><span data-ttu-id="9759f-154">個人資料</span><span class="sxs-lookup"><span data-stu-id="9759f-154">Personal data</span></span>

<span data-ttu-id="9759f-155">使用個別使用者帳戶建立的 ASP.NET Core 應用程式包含下載及刪除個人資料的程式碼。</span><span class="sxs-lookup"><span data-stu-id="9759f-155">ASP.NET Core apps created with individual user accounts include code to download and delete personal data.</span></span>

<span data-ttu-id="9759f-156">選取使用者名稱，然後選取 [ **個人資料** ]：</span><span class="sxs-lookup"><span data-stu-id="9759f-156">Select the user name and then select **Personal data** :</span></span>

![管理個人資料頁面](gdpr/_static/pd.png)

<span data-ttu-id="9759f-158">注意：</span><span class="sxs-lookup"><span data-stu-id="9759f-158">Notes:</span></span>

* <span data-ttu-id="9759f-159">若要產生程式 `Account/Manage` 代碼，請參閱[Scaffold :::no-loc(Identity)::: ](xref:security/authentication/scaffold-identity)。</span><span class="sxs-lookup"><span data-stu-id="9759f-159">To generate the `Account/Manage` code, see [Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity).</span></span>
* <span data-ttu-id="9759f-160">**刪除** 和 **下載** 連結僅適用于預設的身分識別資料。</span><span class="sxs-lookup"><span data-stu-id="9759f-160">The **Delete** and **Download** links only act on the default identity data.</span></span> <span data-ttu-id="9759f-161">您必須擴充建立自訂使用者資料的應用程式，才能刪除/下載自訂的使用者資料。</span><span class="sxs-lookup"><span data-stu-id="9759f-161">Apps that create custom user data must be extended to delete/download the custom user data.</span></span> <span data-ttu-id="9759f-162">如需詳細資訊，請參閱[新增、下載及刪除自訂使用者 :::no-loc(Identity)::: 資料](xref:security/authentication/add-user-data)。</span><span class="sxs-lookup"><span data-stu-id="9759f-162">For more information, see [Add, download, and delete custom user data to :::no-loc(Identity):::](xref:security/authentication/add-user-data).</span></span>
* <span data-ttu-id="9759f-163">:::no-loc(Identity)::: `AspNetUserTokens` 當使用者因為[外鍵](https://github.com/aspnet/:::no-loc(Identity):::/blob/release/2.1/src/EF/:::no-loc(Identity):::UserContext.cs#L152)而透過串聯刪除行為刪除時，會刪除儲存在資料庫資料表中的使用者儲存的權杖。</span><span class="sxs-lookup"><span data-stu-id="9759f-163">Saved tokens for the user that are stored in the :::no-loc(Identity)::: database table `AspNetUserTokens` are deleted when the user is deleted via the cascading delete behavior due to the [foreign key](https://github.com/aspnet/:::no-loc(Identity):::/blob/release/2.1/src/EF/:::no-loc(Identity):::UserContext.cs#L152).</span></span>
* <span data-ttu-id="9759f-164">在接受原則之前，無法使用[外部提供者驗證](xref:security/authentication/social/index)，例如 Facebook 和 Google :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="9759f-164">[External provider authentication](xref:security/authentication/social/index), such as Facebook and Google, isn't available before the :::no-loc(cookie)::: policy is accepted.</span></span>

::: moniker-end

## <a name="encryption-at-rest"></a><span data-ttu-id="9759f-165">待用加密</span><span class="sxs-lookup"><span data-stu-id="9759f-165">Encryption at rest</span></span>

<span data-ttu-id="9759f-166">部分資料庫和儲存機制允許待用加密。</span><span class="sxs-lookup"><span data-stu-id="9759f-166">Some databases and storage mechanisms allow for encryption at rest.</span></span> <span data-ttu-id="9759f-167">待用加密：</span><span class="sxs-lookup"><span data-stu-id="9759f-167">Encryption at rest:</span></span>

* <span data-ttu-id="9759f-168">自動加密儲存的資料。</span><span class="sxs-lookup"><span data-stu-id="9759f-168">Encrypts stored data automatically.</span></span>
* <span data-ttu-id="9759f-169">加密存取資料的軟體，而不需要設定、程式設計或其他工作。</span><span class="sxs-lookup"><span data-stu-id="9759f-169">Encrypts without configuration, programming, or other work for the software that accesses the data.</span></span>
* <span data-ttu-id="9759f-170">是最簡單且最安全的選項。</span><span class="sxs-lookup"><span data-stu-id="9759f-170">Is the easiest and safest option.</span></span>
* <span data-ttu-id="9759f-171">允許資料庫管理金鑰和加密。</span><span class="sxs-lookup"><span data-stu-id="9759f-171">Allows the database to manage keys and encryption.</span></span>

<span data-ttu-id="9759f-172">例如：</span><span class="sxs-lookup"><span data-stu-id="9759f-172">For example:</span></span>

* <span data-ttu-id="9759f-173">Microsoft SQL 和 Azure SQL 提供 [透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) 。</span><span class="sxs-lookup"><span data-stu-id="9759f-173">Microsoft SQL and Azure SQL provide [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).</span></span>
* [<span data-ttu-id="9759f-174">SQL Azure 預設會加密資料庫</span><span class="sxs-lookup"><span data-stu-id="9759f-174">SQL Azure encrypts the database by default</span></span>](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* <span data-ttu-id="9759f-175">[依預設會加密 Azure blob、檔案、資料表和佇列儲存體](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)。</span><span class="sxs-lookup"><span data-stu-id="9759f-175">[Azure Blobs, Files, Table, and Queue Storage are encrypted by default](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).</span></span>

<span data-ttu-id="9759f-176">對於不提供待用內建加密的資料庫，您可以使用磁片加密來提供相同的保護。</span><span class="sxs-lookup"><span data-stu-id="9759f-176">For databases that don't provide built-in encryption at rest, you may be able to use disk encryption to provide the same protection.</span></span> <span data-ttu-id="9759f-177">例如：</span><span class="sxs-lookup"><span data-stu-id="9759f-177">For example:</span></span>

* [<span data-ttu-id="9759f-178">適用于 Windows Server 的 BitLocker</span><span class="sxs-lookup"><span data-stu-id="9759f-178">BitLocker for Windows Server</span></span>](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* <span data-ttu-id="9759f-179">Linux：</span><span class="sxs-lookup"><span data-stu-id="9759f-179">Linux:</span></span>
  * [<span data-ttu-id="9759f-180">eCryptfs</span><span class="sxs-lookup"><span data-stu-id="9759f-180">eCryptfs</span></span>](https://launchpad.net/ecryptfs)
  * <span data-ttu-id="9759f-181">[EncFS](https://github.com/vgough/encfs)。</span><span class="sxs-lookup"><span data-stu-id="9759f-181">[EncFS](https://github.com/vgough/encfs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9759f-182">其他資源</span><span class="sxs-lookup"><span data-stu-id="9759f-182">Additional resources</span></span>

* [<span data-ttu-id="9759f-183">Microsoft.com/GDPR</span><span class="sxs-lookup"><span data-stu-id="9759f-183">Microsoft.com/GDPR</span></span>](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* <span data-ttu-id="9759f-184">[GDPR-在 ASP.NET Core 中新增 [撤銷同意] 按鈕](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)</span><span class="sxs-lookup"><span data-stu-id="9759f-184">[GDPR - Adding a Revoke Consent Button in ASP.NET Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)</span></span>
