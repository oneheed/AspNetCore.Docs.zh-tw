---
title: ASP.NET Core MVC 中的快取標籤協助程式
author: pkellner
description: 了解如何使用快取標籤協助程式。
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/cache-tag-helper
ms.openlocfilehash: a87f91255bd1f280b1567f522423a6f4e88a6dd8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060881"
---
# <a name="cache-tag-helper-in-aspnet-core-mvc"></a><span data-ttu-id="f3c46-103">ASP.NET Core MVC 中的快取標籤協助程式</span><span class="sxs-lookup"><span data-stu-id="f3c46-103">Cache Tag Helper in ASP.NET Core MVC</span></span>

<span data-ttu-id="f3c46-104">由 [Peter Kellner](https://peterkellner.net) 提供</span><span class="sxs-lookup"><span data-stu-id="f3c46-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="f3c46-105">快取標籤協助程式可將 ASP.NET Core 應用程式內容快取至內部 ASP.NET Core 快取提供者，以提升應用程式的效能。</span><span class="sxs-lookup"><span data-stu-id="f3c46-105">The Cache Tag Helper provides the ability to improve the performance of your ASP.NET Core app by caching its content to the internal ASP.NET Core cache provider.</span></span>

<span data-ttu-id="f3c46-106">如需標籤協助程式的概觀，請參閱 <xref:mvc/views/tag-helpers/intro>。</span><span class="sxs-lookup"><span data-stu-id="f3c46-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="f3c46-107">下列標記會快取 :::no-loc(Razor)::: 目前的日期：</span><span class="sxs-lookup"><span data-stu-id="f3c46-107">The following :::no-loc(Razor)::: markup caches the current date:</span></span>

```cshtml
<cache>@DateTime.Now</cache>
```

<span data-ttu-id="f3c46-108">第一個對包含標籤協助程式之頁面的要求會顯示目前的日期/時間。</span><span class="sxs-lookup"><span data-stu-id="f3c46-108">The first request to the page that contains the Tag Helper displays the current date.</span></span> <span data-ttu-id="f3c46-109">其他要求則會顯示在快取過期 (預設為 20 分鐘) 之前或快取日期從快取撤銷之前的快取值。</span><span class="sxs-lookup"><span data-stu-id="f3c46-109">Additional requests show the cached value until the cache expires (default 20 minutes) or until the cached date is evicted from the cache.</span></span>

## <a name="cache-tag-helper-attributes"></a><span data-ttu-id="f3c46-110">快取標籤協助程式屬性</span><span class="sxs-lookup"><span data-stu-id="f3c46-110">Cache Tag Helper Attributes</span></span>

### <a name="enabled"></a><span data-ttu-id="f3c46-111">已啟用</span><span class="sxs-lookup"><span data-stu-id="f3c46-111">enabled</span></span>

| <span data-ttu-id="f3c46-112">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-112">Attribute Type</span></span>  | <span data-ttu-id="f3c46-113">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-113">Examples</span></span>        | <span data-ttu-id="f3c46-114">預設</span><span class="sxs-lookup"><span data-stu-id="f3c46-114">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="f3c46-115">Boolean</span><span class="sxs-lookup"><span data-stu-id="f3c46-115">Boolean</span></span>         | <span data-ttu-id="f3c46-116">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="f3c46-116">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="f3c46-117">`enabled` 會決定是否快取以快取標籤協助程式括住的內容。</span><span class="sxs-lookup"><span data-stu-id="f3c46-117">`enabled` determines if the content enclosed by the Cache Tag Helper is cached.</span></span> <span data-ttu-id="f3c46-118">預設為 `true`。</span><span class="sxs-lookup"><span data-stu-id="f3c46-118">The default is `true`.</span></span> <span data-ttu-id="f3c46-119">如果設定為 `false`，則轉譯輸出 **不會** 被快取。</span><span class="sxs-lookup"><span data-stu-id="f3c46-119">If set to `false`, the rendered output is **not** cached.</span></span>

<span data-ttu-id="f3c46-120">範例：</span><span class="sxs-lookup"><span data-stu-id="f3c46-120">Example:</span></span>

```cshtml
<cache enabled="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-on"></a><span data-ttu-id="f3c46-121">expires-on</span><span class="sxs-lookup"><span data-stu-id="f3c46-121">expires-on</span></span>

| <span data-ttu-id="f3c46-122">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-122">Attribute Type</span></span>   | <span data-ttu-id="f3c46-123">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-123">Example</span></span>                            |
| ---------------- | ---------------------------------- |
| `DateTimeOffset` | `@new DateTime(2025,1,29,17,02,0)` |

<span data-ttu-id="f3c46-124">`expires-on` 可設定快取項目的絕對到期日。</span><span class="sxs-lookup"><span data-stu-id="f3c46-124">`expires-on` sets an absolute expiration date for the cached item.</span></span>

<span data-ttu-id="f3c46-125">下列範例會快取 2025 年 1 月 29 日下午 5:02 以前的快取標籤協助程式內容：</span><span class="sxs-lookup"><span data-stu-id="f3c46-125">The following example caches the contents of the Cache Tag Helper until 5:02 PM on January 29, 2025:</span></span>

```cshtml
<cache expires-on="@new DateTime(2025,1,29,17,02,0)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="expires-after"></a><span data-ttu-id="f3c46-126">expires-after</span><span class="sxs-lookup"><span data-stu-id="f3c46-126">expires-after</span></span>

| <span data-ttu-id="f3c46-127">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-127">Attribute Type</span></span> | <span data-ttu-id="f3c46-128">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-128">Example</span></span>                      | <span data-ttu-id="f3c46-129">預設</span><span class="sxs-lookup"><span data-stu-id="f3c46-129">Default</span></span>    |
| -------------- | ---------------------------- | ---------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(120)` | <span data-ttu-id="f3c46-130">20 分鐘</span><span class="sxs-lookup"><span data-stu-id="f3c46-130">20 minutes</span></span> |

<span data-ttu-id="f3c46-131">`expires-after` 可設定從第一個要求時間到快取內容的時間長度。</span><span class="sxs-lookup"><span data-stu-id="f3c46-131">`expires-after` sets the length of time from the first request time to cache the contents.</span></span>

<span data-ttu-id="f3c46-132">範例：</span><span class="sxs-lookup"><span data-stu-id="f3c46-132">Example:</span></span>

```cshtml
<cache expires-after="@TimeSpan.FromSeconds(120)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="f3c46-133">:::no-loc(Razor):::視圖引擎會將預設 `expires-after` 值設定為20分鐘。</span><span class="sxs-lookup"><span data-stu-id="f3c46-133">The :::no-loc(Razor)::: View Engine sets the default `expires-after` value to twenty minutes.</span></span>

### <a name="expires-sliding"></a><span data-ttu-id="f3c46-134">expires-sliding</span><span class="sxs-lookup"><span data-stu-id="f3c46-134">expires-sliding</span></span>

| <span data-ttu-id="f3c46-135">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-135">Attribute Type</span></span> | <span data-ttu-id="f3c46-136">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-136">Example</span></span>                     |
| -------------- | --------------------------- |
| `TimeSpan`     | `@TimeSpan.FromSeconds(60)` |

<span data-ttu-id="f3c46-137">設定快取項目的值多久未被存取時應該撤出。</span><span class="sxs-lookup"><span data-stu-id="f3c46-137">Sets the time that a cache entry should be evicted if its value hasn't been accessed.</span></span>

<span data-ttu-id="f3c46-138">範例：</span><span class="sxs-lookup"><span data-stu-id="f3c46-138">Example:</span></span>

```cshtml
<cache expires-sliding="@TimeSpan.FromSeconds(60)">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-header"></a><span data-ttu-id="f3c46-139">vary-by-header</span><span class="sxs-lookup"><span data-stu-id="f3c46-139">vary-by-header</span></span>

| <span data-ttu-id="f3c46-140">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-140">Attribute Type</span></span> | <span data-ttu-id="f3c46-141">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-141">Examples</span></span>                                    |
| -------------- | ------------------------------------------- |
| <span data-ttu-id="f3c46-142">String</span><span class="sxs-lookup"><span data-stu-id="f3c46-142">String</span></span>         | <span data-ttu-id="f3c46-143">`User-Agent`, `User-Agent,content-encoding`</span><span class="sxs-lookup"><span data-stu-id="f3c46-143">`User-Agent`, `User-Agent,content-encoding`</span></span> |

<span data-ttu-id="f3c46-144">`vary-by-header` 接受標頭值的逗號分隔清單，在標頭值變更時，會觸發快取重新整理。</span><span class="sxs-lookup"><span data-stu-id="f3c46-144">`vary-by-header` accepts a comma-delimited list of header values that trigger a cache refresh when they change.</span></span>

<span data-ttu-id="f3c46-145">下列範例會監視標頭值 `User-Agent`。</span><span class="sxs-lookup"><span data-stu-id="f3c46-145">The following example monitors the header value `User-Agent`.</span></span> <span data-ttu-id="f3c46-146">此範例會快取展示給網頁伺服器之每個不同 `User-Agent` 的內容：</span><span class="sxs-lookup"><span data-stu-id="f3c46-146">The example caches the content for every different `User-Agent` presented to the web server:</span></span>

```cshtml
<cache vary-by-header="User-Agent">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-query"></a><span data-ttu-id="f3c46-147">vary-by-query</span><span class="sxs-lookup"><span data-stu-id="f3c46-147">vary-by-query</span></span>

| <span data-ttu-id="f3c46-148">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-148">Attribute Type</span></span> | <span data-ttu-id="f3c46-149">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-149">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="f3c46-150">String</span><span class="sxs-lookup"><span data-stu-id="f3c46-150">String</span></span>         | <span data-ttu-id="f3c46-151">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="f3c46-151">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="f3c46-152">`vary-by-query` 接受查詢字串 (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) 中 <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> 的逗點分隔清單，當任何所列索引碼的值變更時，會觸發快取重新整理。</span><span class="sxs-lookup"><span data-stu-id="f3c46-152">`vary-by-query` accepts a comma-delimited list of <xref:Microsoft.AspNetCore.Http.IQueryCollection.Keys*> in a query string (<xref:Microsoft.AspNetCore.Http.HttpRequest.Query*>) that trigger a cache refresh when the value of any listed key changes.</span></span>

<span data-ttu-id="f3c46-153">下列範例會監視 `Make` 與 `Model` 的值。</span><span class="sxs-lookup"><span data-stu-id="f3c46-153">The following example monitors the values of `Make` and `Model`.</span></span> <span data-ttu-id="f3c46-154">此範例會快取展示給網頁伺服器之每個不同 `Make` 與 `Model` 的內容：</span><span class="sxs-lookup"><span data-stu-id="f3c46-154">The example caches the content for every different `Make` and `Model` presented to the web server:</span></span>

```cshtml
<cache vary-by-query="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-route"></a><span data-ttu-id="f3c46-155">vary-by-route</span><span class="sxs-lookup"><span data-stu-id="f3c46-155">vary-by-route</span></span>

| <span data-ttu-id="f3c46-156">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-156">Attribute Type</span></span> | <span data-ttu-id="f3c46-157">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-157">Examples</span></span>             |
| -------------- | -------------------- |
| <span data-ttu-id="f3c46-158">String</span><span class="sxs-lookup"><span data-stu-id="f3c46-158">String</span></span>         | <span data-ttu-id="f3c46-159">`Make`, `Make,Model`</span><span class="sxs-lookup"><span data-stu-id="f3c46-159">`Make`, `Make,Model`</span></span> |

<span data-ttu-id="f3c46-160">`vary-by-route` 接受路由參數名稱的逗點分隔清單，當路由資料參數值變更時，這些路由參數名稱會觸發快取重新整理。</span><span class="sxs-lookup"><span data-stu-id="f3c46-160">`vary-by-route` accepts a comma-delimited list of route parameter names that trigger a cache refresh when the route data parameter value changes.</span></span>

<span data-ttu-id="f3c46-161">範例：</span><span class="sxs-lookup"><span data-stu-id="f3c46-161">Example:</span></span>

<span data-ttu-id="f3c46-162">*Startup.cs* ：</span><span class="sxs-lookup"><span data-stu-id="f3c46-162">*Startup.cs* :</span></span>

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{Make?}/{Model?}");
```

<span data-ttu-id="f3c46-163">*Index. cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="f3c46-163">*Index.cshtml* :</span></span>

```cshtml
<cache vary-by-route="Make,Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-no-loccookie"></a><span data-ttu-id="f3c46-164">依:::no-loc(cookie):::</span><span class="sxs-lookup"><span data-stu-id="f3c46-164">vary-by-:::no-loc(cookie):::</span></span>

| <span data-ttu-id="f3c46-165">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-165">Attribute Type</span></span> | <span data-ttu-id="f3c46-166">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-166">Examples</span></span>                                                                         |
| -------------- | -------------------------------------------------------------------------------- |
| <span data-ttu-id="f3c46-167">String</span><span class="sxs-lookup"><span data-stu-id="f3c46-167">String</span></span>         | <span data-ttu-id="f3c46-168">`.AspNetCore.:::no-loc(Identity):::.Application`, `.AspNetCore.:::no-loc(Identity):::.Application,HairColor`</span><span class="sxs-lookup"><span data-stu-id="f3c46-168">`.AspNetCore.:::no-loc(Identity):::.Application`, `.AspNetCore.:::no-loc(Identity):::.Application,HairColor`</span></span> |

<span data-ttu-id="f3c46-169">`vary-by-:::no-loc(cookie):::` 接受以逗號分隔的名稱清單 :::no-loc(cookie)::: ，這些名稱會在值變更時觸發快取重新整理 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="f3c46-169">`vary-by-:::no-loc(cookie):::` accepts a comma-delimited list of :::no-loc(cookie)::: names that trigger a cache refresh when the :::no-loc(cookie)::: values change.</span></span>

<span data-ttu-id="f3c46-170">下列範例會監視 :::no-loc(cookie)::: 與相關聯的 :::no-loc(ASP.NET Core Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="f3c46-170">The following example monitors the :::no-loc(cookie)::: associated with :::no-loc(ASP.NET Core Identity):::.</span></span> <span data-ttu-id="f3c46-171">當使用者經過驗證時，中的變更會觸發快取重新整理 :::no-loc(Identity)::: :::no-loc(cookie)::: ：</span><span class="sxs-lookup"><span data-stu-id="f3c46-171">When a user is authenticated, a change in the :::no-loc(Identity)::: :::no-loc(cookie)::: triggers a cache refresh:</span></span>

```cshtml
<cache vary-by-:::no-loc(cookie):::=".AspNetCore.:::no-loc(Identity):::.Application">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="vary-by-user"></a><span data-ttu-id="f3c46-172">vary-by-user</span><span class="sxs-lookup"><span data-stu-id="f3c46-172">vary-by-user</span></span>

| <span data-ttu-id="f3c46-173">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-173">Attribute Type</span></span>  | <span data-ttu-id="f3c46-174">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-174">Examples</span></span>        | <span data-ttu-id="f3c46-175">預設</span><span class="sxs-lookup"><span data-stu-id="f3c46-175">Default</span></span> |
| --------------- | --------------- | ------- |
| <span data-ttu-id="f3c46-176">Boolean</span><span class="sxs-lookup"><span data-stu-id="f3c46-176">Boolean</span></span>         | <span data-ttu-id="f3c46-177">`true`, `false`</span><span class="sxs-lookup"><span data-stu-id="f3c46-177">`true`, `false`</span></span> | `true`  |

<span data-ttu-id="f3c46-178">`vary-by-user` 可指定當登入的使用者 (或內容主體) 變更時，是否重設快取。</span><span class="sxs-lookup"><span data-stu-id="f3c46-178">`vary-by-user` specifies whether or not the cache resets when the signed-in user (or Context Principal) changes.</span></span> <span data-ttu-id="f3c46-179">目前的使用者也稱為「要求內容主體」，您可以 :::no-loc(Razor)::: 參考來在視圖中查看 `@User.:::no-loc(Identity):::.Name` 。</span><span class="sxs-lookup"><span data-stu-id="f3c46-179">The current user is also known as the Request Context Principal and can be viewed in a :::no-loc(Razor)::: view by referencing `@User.:::no-loc(Identity):::.Name`.</span></span>

<span data-ttu-id="f3c46-180">下列範例會監視目前登入的使用者，以觸發快取重新整理：</span><span class="sxs-lookup"><span data-stu-id="f3c46-180">The following example monitors the current logged in user to trigger a cache refresh:</span></span>

```cshtml
<cache vary-by-user="true">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="f3c46-181">使用此屬性可保留登入與登出週期中的快取內容。</span><span class="sxs-lookup"><span data-stu-id="f3c46-181">Using this attribute maintains the contents in cache through a sign-in and sign-out cycle.</span></span> <span data-ttu-id="f3c46-182">當此值設定為 `true` 時，驗證週期將使已驗證之使用者的快取無效。</span><span class="sxs-lookup"><span data-stu-id="f3c46-182">When the value is set to `true`, an authentication cycle invalidates the cache for the authenticated user.</span></span> <span data-ttu-id="f3c46-183">快取無效，因為 :::no-loc(cookie)::: 當使用者經過驗證時，會產生新的唯一值。</span><span class="sxs-lookup"><span data-stu-id="f3c46-183">The cache is invalidated because a new unique :::no-loc(cookie)::: value is generated when a user is authenticated.</span></span> <span data-ttu-id="f3c46-184">如果不 :::no-loc(cookie)::: 存在或已過期，則會為匿名狀態維護快取 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="f3c46-184">Cache is maintained for the anonymous state when no :::no-loc(cookie)::: is present or the :::no-loc(cookie)::: has expired.</span></span> <span data-ttu-id="f3c46-185">如果使用者 **未** 通過驗證，則會維護快取。</span><span class="sxs-lookup"><span data-stu-id="f3c46-185">If the user is **not** authenticated, the cache is maintained.</span></span>

### <a name="vary-by"></a><span data-ttu-id="f3c46-186">vary-by</span><span class="sxs-lookup"><span data-stu-id="f3c46-186">vary-by</span></span>

| <span data-ttu-id="f3c46-187">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-187">Attribute Type</span></span> | <span data-ttu-id="f3c46-188">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-188">Example</span></span>  |
| -------------- | -------- |
| <span data-ttu-id="f3c46-189">String</span><span class="sxs-lookup"><span data-stu-id="f3c46-189">String</span></span>         | `@Model` |

<span data-ttu-id="f3c46-190">`vary-by` 可自訂要快取哪些資料。</span><span class="sxs-lookup"><span data-stu-id="f3c46-190">`vary-by` allows for customization of what data is cached.</span></span> <span data-ttu-id="f3c46-191">當屬性字串值所參考的物件變更時，就會更新快取標籤協助程式的內容。</span><span class="sxs-lookup"><span data-stu-id="f3c46-191">When the object referenced by the attribute's string value changes, the content of the Cache Tag Helper is updated.</span></span> <span data-ttu-id="f3c46-192">通常會對此屬性指派模型值的字串串連。</span><span class="sxs-lookup"><span data-stu-id="f3c46-192">Often, a string-concatenation of model values are assigned to this attribute.</span></span> <span data-ttu-id="f3c46-193">實際上，這會導致更新任何串連值都會使快取失效的情節。</span><span class="sxs-lookup"><span data-stu-id="f3c46-193">Effectively, this results in a scenario where an update to any of the concatenated values invalidates the cache.</span></span>

<span data-ttu-id="f3c46-194">下列範例假設轉譯檢視的控制器方法加總兩個路由參數 (`myParam1` 與 `myParam2`) 的整數值，並以單一模型屬性傳回加總。</span><span class="sxs-lookup"><span data-stu-id="f3c46-194">The following example assumes the controller method rendering the view sums the integer value of the two route parameters, `myParam1` and `myParam2`, and returns the sum as the single model property.</span></span> <span data-ttu-id="f3c46-195">當此總和變更時，快取標籤協助程式的內容會重新轉譯和快取。</span><span class="sxs-lookup"><span data-stu-id="f3c46-195">When this sum changes, the content of the Cache Tag Helper is rendered and cached again.</span></span>  

<span data-ttu-id="f3c46-196">動作：</span><span class="sxs-lookup"><span data-stu-id="f3c46-196">Action:</span></span>

```csharp
public IActionResult Index(string myParam1, string myParam2, string myParam3)
{
    int num1;
    int num2;
    int.TryParse(myParam1, out num1);
    int.TryParse(myParam2, out num2);
    return View(viewName, num1 + num2);
}
```

<span data-ttu-id="f3c46-197">*Index. cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="f3c46-197">*Index.cshtml* :</span></span>

```cshtml
<cache vary-by="@Model">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

### <a name="priority"></a><span data-ttu-id="f3c46-198">priority</span><span class="sxs-lookup"><span data-stu-id="f3c46-198">priority</span></span>

| <span data-ttu-id="f3c46-199">屬性類型</span><span class="sxs-lookup"><span data-stu-id="f3c46-199">Attribute Type</span></span>      | <span data-ttu-id="f3c46-200">範例</span><span class="sxs-lookup"><span data-stu-id="f3c46-200">Examples</span></span>                               | <span data-ttu-id="f3c46-201">預設</span><span class="sxs-lookup"><span data-stu-id="f3c46-201">Default</span></span>  |
| ------------------- | -------------------------------------- | -------- |
| `CacheItemPriority` | <span data-ttu-id="f3c46-202">`High`, `Low`, `NeverRemove`, `Normal`</span><span class="sxs-lookup"><span data-stu-id="f3c46-202">`High`, `Low`, `NeverRemove`, `Normal`</span></span> | `Normal` |

<span data-ttu-id="f3c46-203">`priority` 可提供快取撤銷指導方針給內建快取提供者。</span><span class="sxs-lookup"><span data-stu-id="f3c46-203">`priority` provides cache eviction guidance to the built-in cache provider.</span></span> <span data-ttu-id="f3c46-204">當記憶體不足時，網頁伺服器會先撤出 `Low` 快取項目。</span><span class="sxs-lookup"><span data-stu-id="f3c46-204">The web server evicts `Low` cache entries first when it's under memory pressure.</span></span>

<span data-ttu-id="f3c46-205">範例：</span><span class="sxs-lookup"><span data-stu-id="f3c46-205">Example:</span></span>

```cshtml
<cache priority="High">
    Current Time Inside Cache Tag Helper: @DateTime.Now
</cache>
```

<span data-ttu-id="f3c46-206">`priority` 屬性並不保證特定快取保留層級。</span><span class="sxs-lookup"><span data-stu-id="f3c46-206">The `priority` attribute doesn't guarantee a specific level of cache retention.</span></span> <span data-ttu-id="f3c46-207">`CacheItemPriority` 僅供建議。</span><span class="sxs-lookup"><span data-stu-id="f3c46-207">`CacheItemPriority` is only a suggestion.</span></span> <span data-ttu-id="f3c46-208">將此屬性設定為 `NeverRemove` 並不保證一定會保留快取項目。</span><span class="sxs-lookup"><span data-stu-id="f3c46-208">Setting this attribute to `NeverRemove` doesn't guarantee that cached items are always retained.</span></span> <span data-ttu-id="f3c46-209">請參閱[其他資源](#additional-resources)一節中的主題，以取得詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="f3c46-209">See the topics in the [Additional Resources](#additional-resources) section for more information.</span></span>

<span data-ttu-id="f3c46-210">快取標籤協助程式相依於[記憶體快取服務](xref:performance/caching/memory)。</span><span class="sxs-lookup"><span data-stu-id="f3c46-210">The Cache Tag Helper is dependent on the [memory cache service](xref:performance/caching/memory).</span></span> <span data-ttu-id="f3c46-211">如果尚未新增此服務，快取標籤協助程式會予以新增。</span><span class="sxs-lookup"><span data-stu-id="f3c46-211">The Cache Tag Helper adds the service if it hasn't been added.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f3c46-212">其他資源</span><span class="sxs-lookup"><span data-stu-id="f3c46-212">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
