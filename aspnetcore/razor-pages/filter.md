---
title: RazorASP.NET Core 中的頁面篩選方法
author: Rick-Anderson
description: 瞭解如何 Razor 在 ASP.NET Core 中建立頁面的篩選方法。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
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
uid: razor-pages/filter
ms.openlocfilehash: 178e6348d2d50dae34feea6a0ed261de01037136
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586133"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a>RazorASP.NET Core 中的頁面篩選方法

::: moniker range=">= aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

Razor 頁面篩選 [>ipagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) 和 [>iasyncpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) 允許頁面在 Razor Razor 頁面處理常式執行之前和之後執行程式碼。 Razor 頁面篩選器類似于 [ASP.NET 核心 MVC 動作篩選器](xref:mvc/controllers/filters#action-filters)，但它們無法套用至個別的頁面處理常式方法。

Razor 頁面篩選：

* 在選取處理常式方法之後，但在進行模型繫結之前執行程式碼。
* 在執行處理常式方法之前，並在完成模型繫結之後執行程式碼。
* 在執行處理常式方法之後執行程式碼。
* 可以在某個頁面或全域實作。
* 無法套用至特定頁面處理常式方法。
* 可以有以相依性 [插入](xref:fundamentals/dependency-injection) (DI) 填入的函式相依性。 如需詳細資訊，請參閱 [ServiceFilterAttribute](../mvc/controllers/filters.md#servicefilterattribute) 和 [TypeFilterAttribute](../mvc/controllers/filters.md#typefilterattribute)。

雖然頁面自訂程式和中介軟體可讓您在處理常式方法執行之前執行自訂程式碼，但是只有 Razor 頁面篩選可以存取 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> 和頁面。 中介軟體可存取 `HttpContext` ，但不能存取「頁面內容」。 篩選具有可 <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> 提供存取的衍生參數 `HttpContext` 。 以下是頁面篩選的範例： [執行篩選屬性](#ifa) ，將標頭新增至回應，這是無法使用函式或中介軟體完成的動作。 只有在執行篩選、處理常式或頁面的主體時，才可存取頁面內容（包括存取頁面的實例和其模型） Razor 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/3.1sample) ([如何下載](xref:index#how-to-download-a-sample)) 

Razor 頁面篩選器提供下列方法，可在全域或在頁面層級套用：

* 同步方法：

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0)：在選取處理常式方法之後，但在進行模型繫結之前呼叫。
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0)：在執行處理常式方法之前，並在完成模型繫結之後呼叫。
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0)：在執行處理常式方法之後，並在動作結果之前呼叫。

* 非同步方法：

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0)：在選取處理常式方法之後，但在進行模型繫結之前以非同步方式呼叫。
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0)：在叫用處理常式方法之前，並在完成模型繫結之後以非同步方式呼叫。

請實作同步 **或** 非同步版本的篩選條件介面，而 **不要** 同時實作這兩者。 架構會先檢查以查看篩選條件是否實作非同步介面，如果是的話，便呼叫該介面。 如果沒有，它會呼叫同步介面的方法。 如果這兩個介面都已執行，則只會呼叫非同步方法。 相同的規則會套用至頁面中的覆寫，實作覆寫的同步或非同步版本，但不能同時實作。

## <a name="implement-razor-page-filters-globally"></a>Razor全域執行頁面篩選

下列程式碼會實作 `IAsyncPageFilter`：

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

在上述程式碼中， `ProcessUserAgent.Write` 是使用者提供的程式碼，可搭配使用者代理字串使用。

下列程式碼會啟用 `Startup` 類別中的 `SampleAsyncPageFilter`：

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

下列程式碼會呼叫，將套用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> `SampleAsyncPageFilter` 至 */Movies* 中的頁面：

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

下例程式碼會實作同步的 `IPageFilter`：

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

下列程式碼會啟用 `SamplePageFilter`：

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>藉 Razor 由覆寫篩選方法來執行頁面篩選

下列程式碼會覆寫非同步 Razor 頁面篩選：

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>實作篩選條件屬性

內建的屬性型篩選 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> 篩選器可以子類別化。 下列篩選條件會將標頭新增至回應：

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

下列程式碼會套用 `AddHeader` 屬性：

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

使用瀏覽器開發人員工具之類的工具來檢查標頭。 在 [ **回應標頭**] 底下 `author: Rick` 顯示。

如需覆寫順序的指示，請參閱[覆寫預設順序](xref:mvc/controllers/filters#overriding-the-default-order)。

如需從篩選條件縮短篩選管線的指示，請參閱[取消和縮短](xref:mvc/controllers/filters#cancellation-and-short-circuiting)。

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>授權篩選條件屬性

[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 屬性可以套用至 `PageModel`：

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

Razor 頁面篩選 [>ipagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) 和 [>iasyncpagefilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) 允許頁面在 Razor Razor 頁面處理常式執行之前和之後執行程式碼。 Razor 頁面篩選器類似于 [ASP.NET 核心 MVC 動作篩選器](xref:mvc/controllers/filters#action-filters)，但它們無法套用至個別的頁面處理常式方法。

Razor 頁面篩選：

* 在選取處理常式方法之後，但在進行模型繫結之前執行程式碼。
* 在執行處理常式方法之前，並在完成模型繫結之後執行程式碼。
* 在執行處理常式方法之後執行程式碼。
* 可以在某個頁面或全域實作。
* 無法套用至特定頁面處理常式方法。

程式碼可以在處理常式方法使用頁面的函式或中介軟體執行之前執行，但只有 Razor 頁面篩選可以存取 [HttpCoNtext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)。 篩選條件具有 [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) 衍生參數，可提供對 `HttpContext` 的存取。 例如，[實作篩選條件屬性](#ifa)範例會將標頭新增至回應，這是無法使用建構函式或中介軟體完成的作業。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages/filter/sample/PageFilter) ([如何下載](xref:index#how-to-download-a-sample)) 

Razor 頁面篩選器提供下列方法，可在全域或在頁面層級套用：

* 同步方法：

  * [OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0)：在選取處理常式方法之後，但在進行模型繫結之前呼叫。
  * [OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0)：在執行處理常式方法之前，並在完成模型繫結之後呼叫。
  * [OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0)：在執行處理常式方法之後，並在動作結果之前呼叫。

* 非同步方法：

  * [OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0)：在選取處理常式方法之後，但在進行模型繫結之前以非同步方式呼叫。
  * [OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0)：在叫用處理常式方法之前，並在完成模型繫結之後以非同步方式呼叫。

> [!NOTE]
> 請實作同步 **或** 非同步版本的篩選條件介面，而不要同時實作這兩者。 架構會先檢查以查看篩選條件是否實作非同步介面，如果是的話，便呼叫該介面。 如果沒有，它會呼叫同步介面的方法。 如果這兩個介面都已執行，則只會呼叫非同步方法。 相同的規則會套用至頁面中的覆寫，實作覆寫的同步或非同步版本，但不能同時實作。

## <a name="implement-razor-page-filters-globally"></a>Razor全域執行頁面篩選

下列程式碼會實作 `IAsyncPageFilter`：

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

在上述程式碼中，[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) 並非必要。 它在範例中用來提供應用程式的追蹤資訊。

下列程式碼會啟用 `Startup` 類別中的 `SampleAsyncPageFilter`：

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

下列程式碼顯示完整的 `Startup` 類別：

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

下列程式碼會呼叫 `AddFolderApplicationModelConvention`，只將 `SampleAsyncPageFilter` 套用至 */subFolder* 中的頁面：

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

下例程式碼會實作同步的 `IPageFilter`：

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

下列程式碼會啟用 `SamplePageFilter`：

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a>藉 Razor 由覆寫篩選方法來執行頁面篩選

下列程式碼會覆寫同步 Razor 頁面篩選：

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a>實作篩選條件屬性

以內建屬性為基礎的篩選條件 [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) 可以進行子類別化。 下列篩選條件會將標頭新增至回應：

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

下列程式碼會套用 `AddHeader` 屬性：

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

如需覆寫順序的指示，請參閱[覆寫預設順序](xref:mvc/controllers/filters#overriding-the-default-order)。

如需從篩選條件縮短篩選管線的指示，請參閱[取消和縮短](xref:mvc/controllers/filters#cancellation-and-short-circuiting)。 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a>授權篩選條件屬性

[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 屬性可以套用至 `PageModel`：

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end