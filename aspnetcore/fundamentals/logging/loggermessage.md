---
title: 在 ASP.NET Core 中使用 LoggerMessage 進行高效能記錄
author: rick-anderson
description: 了解如何使用 LoggerMessage 來建立可快取的委派，其對於高效能記錄案例需要較少的物件配置。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
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
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: d6fae8d2e056c0a578b64322bb82027523904254
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587394"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="b5dc8-103">在 ASP.NET Core 中使用 LoggerMessage 進行高效能記錄</span><span class="sxs-lookup"><span data-stu-id="b5dc8-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b5dc8-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> 功能會建立可快取的委派，比起[記錄器擴充方法](xref:Microsoft.Extensions.Logging.LoggerExtensions) (例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>)，其需要的物件配置較少且計算額外負荷較小。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="b5dc8-105">對於高效能記錄的案例，請使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="b5dc8-106">相較於記錄器擴充方法，<xref:Microsoft.Extensions.Logging.LoggerMessage> 提供下列效能優勢：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="b5dc8-107">記錄器擴充方法需要 "boxing" (轉換) 實值型別，例如將 `int` 轉換為 `object`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="b5dc8-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> 模式可使用靜態 <xref:System.Action> 欄位和擴充方法搭配強型別參數來避免 boxing。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="b5dc8-109">記錄器擴充方法在每次寫入記錄訊息時，都必須剖析訊息範本 (具名格式字串)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="b5dc8-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> 只需在定義訊息時剖析範本一次。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="b5dc8-111">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/logging/loggermessage/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="b5dc8-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b5dc8-112">範例應用程式以一個基本引述追蹤系統示範 <xref:Microsoft.Extensions.Logging.LoggerMessage> 功能。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="b5dc8-113">該應用程式使用記憶體內部資料庫來新增和刪除引述。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="b5dc8-114">在進行這些作業時，將會使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式來產生記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="b5dc8-115">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="b5dc8-115">LoggerMessage.Define</span></span>

<span data-ttu-id="b5dc8-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 建立記錄訊息的 <xref:System.Action> 委派。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="b5dc8-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 多載允許最多將六個型別參數傳遞至具名格式字串 (範本)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="b5dc8-118">提供給 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的字串是範本，而不是內插字串。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="b5dc8-119">預留位置會依照指定類型的順序填入。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="b5dc8-120">範本中的預留位置名稱應該是描述性名稱，而且在範本之間應該保持一致。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="b5dc8-121">它們將作為結構化記錄資料內的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="b5dc8-122">建議您針對預留位置名稱使用 [Pascal 大小寫](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="b5dc8-123">例如，`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="b5dc8-124">每個記錄訊息都是 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 所建立之靜態欄位中保存的 <xref:System.Action>。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="b5dc8-125">例如，範例應用程式會建立一個欄位來描述 Index 頁面之 GET 要求的記錄訊息 (*Internal/LoggerExtensions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="b5dc8-126">針對 <xref:System.Action>，請指定：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="b5dc8-127">記錄層級。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-127">The log level.</span></span>
* <span data-ttu-id="b5dc8-128">含有靜態擴充方法名稱的唯一事件識別碼 (<xref:Microsoft.Extensions.Logging.EventId>)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="b5dc8-129">訊息範本 (具名格式字串)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-129">The message template (named format string).</span></span> 

<span data-ttu-id="b5dc8-130">範例應用程式之 Index 頁面的要求會：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="b5dc8-131">將記錄層級設定為 `Information`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-131">Log level to `Information`.</span></span>
* <span data-ttu-id="b5dc8-132">將事件識別碼設定為含有 `IndexPageRequested` 方法名稱的 `1`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="b5dc8-133">將訊息範本 (具名格式字串) 設定為字串。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="b5dc8-134">結構化的記錄存放區提供事件識別碼來加強記錄時，可以使用事件名稱。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="b5dc8-135">例如，[Serilog](https://github.com/serilog/serilog-extensions-logging) 會使用事件名稱。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="b5dc8-136"><xref:System.Action> 是透過強型別擴充方法叫用。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="b5dc8-137">`IndexPageRequested` 方法會在範例應用程式中針對 Index 頁面的 GET 要求記錄一則訊息：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="b5dc8-138">`IndexPageRequested` 會在 *Pages/Index.cshtml.cs* 中 `OnGetAsync` 方法的記錄器上呼叫：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="b5dc8-139">檢查應用程式的主控台輸出：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="b5dc8-140">若要將參數傳遞至記錄訊息，請在建立靜態欄位時定義最多六個型別。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="b5dc8-141">透過為 <xref:System.Action> 欄位定義 `string` 類型來新增引述時，範例應用程式會記錄字串：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="b5dc8-142">委派的記錄訊息範本會從所提供的類型接收其預留位置值。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="b5dc8-143">範例應用程式會定義新增引述的委派，其中的引述參數是 `string`：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="b5dc8-144">新增引述的靜態擴充方法 `QuoteAdded` 會接引述引數值，並將其傳遞至 <xref:System.Action> 委派：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="b5dc8-145">在索引頁面的頁面模型中 (會呼叫 *Pages/Index. cshtml*) ， `QuoteAdded` 以記錄訊息：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="b5dc8-146">檢查應用程式的主控台輸出：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="b5dc8-147">範例應用程式會針對報價刪除來實行 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-147">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="b5dc8-148">成功的刪除作業會記錄告知性訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="b5dc8-149">如果擲回例外狀況，則會針對刪除作業記錄一則錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="b5dc8-150">失敗刪除作業的記錄訊息包含例外狀況堆疊追蹤 (*Internal/LoggerExtensions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="b5dc8-151">請注意例外狀況如何傳遞至 `QuoteDeleteFailed` 中的委派：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="b5dc8-152">在 Index 頁面的頁面模型中，成功的引述刪除會在記錄器上呼叫 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="b5dc8-153">找不到要刪除的引述時，就會擲回 <xref:System.ArgumentNullException>。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="b5dc8-154">[Try-catch 語句會](/dotnet/csharp/language-reference/keywords/try-catch)攔截例外狀況，並藉由在 `QuoteDeleteFailed` [catch](/dotnet/csharp/language-reference/keywords/try-catch)區塊中的記錄器上呼叫方法來記錄， (*Pages/Index. .cs*) ：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-154">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="b5dc8-155">成功刪除引述時，請檢查應用程式的主控台輸出：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="b5dc8-156">引述刪除失敗時，請檢查應用程式的主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="b5dc8-157">請注意，例外狀況會包含在記錄訊息中：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-157">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="b5dc8-158">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="b5dc8-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="b5dc8-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) 建立定義[記錄範圍](xref:fundamentals/logging/index#log-scopes)的 <xref:System.Func%601> 委派。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="b5dc8-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 多載允許最多將三個型別參數傳遞至具名格式字串 (範本)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="b5dc8-161">就像 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的情況一樣，提供給 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法的字串是範本，而不是內插字串。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="b5dc8-162">預留位置會依照指定類型的順序填入。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="b5dc8-163">範本中的預留位置名稱應該是描述性名稱，而且在範本之間應該保持一致。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="b5dc8-164">它們將作為結構化記錄資料內的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="b5dc8-165">建議您針對預留位置名稱使用 [Pascal 大小寫](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="b5dc8-166">例如，`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="b5dc8-167">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法，定義要套用至一系列記錄訊息的[記錄範圍](xref:fundamentals/logging/index#log-scopes)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="b5dc8-168">範例應用程式具有 [全部清除] 按鈕，可用來刪除資料庫中的所有引述。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="b5dc8-169">也可透過逐一移除引述來刪除它們。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="b5dc8-170">每次刪除引述時，就會在記錄器上呼叫 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="b5dc8-171">記錄範圍會新增至這些記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="b5dc8-172">`IncludeScopes`在的主控台記錄器區段中啟用 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="b5dc8-173">若要建立記錄範圍，請新增欄位以保留該範圍的 <xref:System.Func%601> 委派。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="b5dc8-174">範例應用程式會建立稱為 `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*) 的欄位：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="b5dc8-175">請使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 來建立委派。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="b5dc8-176">叫用委派時，最多可以指定三種類型作為範本引數。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="b5dc8-177">範例應用程式會使用包含已刪除引述數目 (`int` 類型) 的訊息範本：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="b5dc8-178">提供記錄訊息的靜態擴充方法。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="b5dc8-179">包含訊息範本中出現之具名屬性的任何型別參數。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="b5dc8-180">範例應用程式會帶入要刪除之引述的 `count`，然後傳回 `_allQuotesDeletedScope`：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="b5dc8-181">此範圍會將記錄擴充呼叫包裝在 [using](/dotnet/csharp/language-reference/keywords/using-statement) 區塊中：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="b5dc8-182">檢查應用程式主控台輸出中的記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="b5dc8-183">下列結果顯示包含記錄範圍訊息的三個刪除引述：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-183">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b5dc8-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> 功能會建立可快取的委派，比起[記錄器擴充方法](xref:Microsoft.Extensions.Logging.LoggerExtensions) (例如 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 和 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>)，其需要的物件配置較少且計算額外負荷較小。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="b5dc8-185">對於高效能記錄的案例，請使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="b5dc8-186">相較於記錄器擴充方法，<xref:Microsoft.Extensions.Logging.LoggerMessage> 提供下列效能優勢：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="b5dc8-187">記錄器擴充方法需要 "boxing" (轉換) 實值型別，例如將 `int` 轉換為 `object`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="b5dc8-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> 模式可使用靜態 <xref:System.Action> 欄位和擴充方法搭配強型別參數來避免 boxing。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="b5dc8-189">記錄器擴充方法在每次寫入記錄訊息時，都必須剖析訊息範本 (具名格式字串)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="b5dc8-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> 只需在定義訊息時剖析範本一次。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="b5dc8-191">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/logging/loggermessage/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="b5dc8-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b5dc8-192">範例應用程式以一個基本引述追蹤系統示範 <xref:Microsoft.Extensions.Logging.LoggerMessage> 功能。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="b5dc8-193">該應用程式使用記憶體內部資料庫來新增和刪除引述。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="b5dc8-194">在進行這些作業時，將會使用 <xref:Microsoft.Extensions.Logging.LoggerMessage> 模式來產生記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="b5dc8-195">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="b5dc8-195">LoggerMessage.Define</span></span>

<span data-ttu-id="b5dc8-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 建立記錄訊息的 <xref:System.Action> 委派。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="b5dc8-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 多載允許最多將六個型別參數傳遞至具名格式字串 (範本)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="b5dc8-198">提供給 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的字串是範本，而不是內插字串。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="b5dc8-199">預留位置會依照指定類型的順序填入。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="b5dc8-200">範本中的預留位置名稱應該是描述性名稱，而且在範本之間應該保持一致。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="b5dc8-201">它們將作為結構化記錄資料內的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="b5dc8-202">建議您針對預留位置名稱使用 [Pascal 大小寫](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="b5dc8-203">例如，`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="b5dc8-204">每個記錄訊息都是 [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) 所建立之靜態欄位中保存的 <xref:System.Action>。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="b5dc8-205">例如，範例應用程式會建立一個欄位來描述 Index 頁面之 GET 要求的記錄訊息 (*Internal/LoggerExtensions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="b5dc8-206">針對 <xref:System.Action>，請指定：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="b5dc8-207">記錄層級。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-207">The log level.</span></span>
* <span data-ttu-id="b5dc8-208">含有靜態擴充方法名稱的唯一事件識別碼 (<xref:Microsoft.Extensions.Logging.EventId>)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="b5dc8-209">訊息範本 (具名格式字串)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-209">The message template (named format string).</span></span> 

<span data-ttu-id="b5dc8-210">範例應用程式之 Index 頁面的要求會：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="b5dc8-211">將記錄層級設定為 `Information`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-211">Log level to `Information`.</span></span>
* <span data-ttu-id="b5dc8-212">將事件識別碼設定為含有 `IndexPageRequested` 方法名稱的 `1`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="b5dc8-213">將訊息範本 (具名格式字串) 設定為字串。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="b5dc8-214">結構化的記錄存放區提供事件識別碼來加強記錄時，可以使用事件名稱。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="b5dc8-215">例如，[Serilog](https://github.com/serilog/serilog-extensions-logging) 會使用事件名稱。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="b5dc8-216"><xref:System.Action> 是透過強型別擴充方法叫用。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="b5dc8-217">`IndexPageRequested` 方法會在範例應用程式中針對 Index 頁面的 GET 要求記錄一則訊息：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="b5dc8-218">`IndexPageRequested` 會在 *Pages/Index.cshtml.cs* 中 `OnGetAsync` 方法的記錄器上呼叫：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="b5dc8-219">檢查應用程式的主控台輸出：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="b5dc8-220">若要將參數傳遞至記錄訊息，請在建立靜態欄位時定義最多六個型別。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="b5dc8-221">透過為 <xref:System.Action> 欄位定義 `string` 類型來新增引述時，範例應用程式會記錄字串：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="b5dc8-222">委派的記錄訊息範本會從所提供的類型接收其預留位置值。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="b5dc8-223">範例應用程式會定義新增引述的委派，其中的引述參數是 `string`：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="b5dc8-224">新增引述的靜態擴充方法 `QuoteAdded` 會接引述引數值，並將其傳遞至 <xref:System.Action> 委派：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="b5dc8-225">在索引頁面的頁面模型中 (會呼叫 *Pages/Index. cshtml*) ， `QuoteAdded` 以記錄訊息：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="b5dc8-226">檢查應用程式的主控台輸出：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="b5dc8-227">範例應用程式會針對報價刪除來實行 [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) 模式。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-227">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="b5dc8-228">成功的刪除作業會記錄告知性訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="b5dc8-229">如果擲回例外狀況，則會針對刪除作業記錄一則錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="b5dc8-230">失敗刪除作業的記錄訊息包含例外狀況堆疊追蹤 (*Internal/LoggerExtensions.cs*)：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="b5dc8-231">請注意例外狀況如何傳遞至 `QuoteDeleteFailed` 中的委派：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="b5dc8-232">在 Index 頁面的頁面模型中，成功的引述刪除會在記錄器上呼叫 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="b5dc8-233">找不到要刪除的引述時，就會擲回 <xref:System.ArgumentNullException>。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="b5dc8-234">[Try-catch 語句會](/dotnet/csharp/language-reference/keywords/try-catch)攔截例外狀況，並藉由在 `QuoteDeleteFailed` [catch](/dotnet/csharp/language-reference/keywords/try-catch)區塊中的記錄器上呼叫方法來記錄， (*Pages/Index. .cs*) ：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-234">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="b5dc8-235">成功刪除引述時，請檢查應用程式的主控台輸出：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="b5dc8-236">引述刪除失敗時，請檢查應用程式的主控台輸出。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="b5dc8-237">請注意，例外狀況會包含在記錄訊息中：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-237">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="b5dc8-238">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="b5dc8-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="b5dc8-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) 建立定義[記錄範圍](xref:fundamentals/logging/index#log-scopes)的 <xref:System.Func%601> 委派。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="b5dc8-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 多載允許最多將三個型別參數傳遞至具名格式字串 (範本)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="b5dc8-241">就像 <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> 方法的情況一樣，提供給 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法的字串是範本，而不是內插字串。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="b5dc8-242">預留位置會依照指定類型的順序填入。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="b5dc8-243">範本中的預留位置名稱應該是描述性名稱，而且在範本之間應該保持一致。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="b5dc8-244">它們將作為結構化記錄資料內的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="b5dc8-245">建議您針對預留位置名稱使用 [Pascal 大小寫](/dotnet/standard/design-guidelines/capitalization-conventions)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="b5dc8-246">例如，`{Count}`、`{FirstName}`。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="b5dc8-247">使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 方法，定義要套用至一系列記錄訊息的[記錄範圍](xref:fundamentals/logging/index#log-scopes)。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="b5dc8-248">範例應用程式具有 [全部清除] 按鈕，可用來刪除資料庫中的所有引述。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="b5dc8-249">也可透過逐一移除引述來刪除它們。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="b5dc8-250">每次刪除引述時，就會在記錄器上呼叫 `QuoteDeleted` 方法。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="b5dc8-251">記錄範圍會新增至這些記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="b5dc8-252">`IncludeScopes`在的主控台記錄器區段中啟用 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-json[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="b5dc8-253">若要建立記錄範圍，請新增欄位以保留該範圍的 <xref:System.Func%601> 委派。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="b5dc8-254">範例應用程式會建立稱為 `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*) 的欄位：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="b5dc8-255">請使用 <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> 來建立委派。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="b5dc8-256">叫用委派時，最多可以指定三種類型作為範本引數。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="b5dc8-257">範例應用程式會使用包含已刪除引述數目 (`int` 類型) 的訊息範本：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="b5dc8-258">提供記錄訊息的靜態擴充方法。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="b5dc8-259">包含訊息範本中出現之具名屬性的任何型別參數。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="b5dc8-260">範例應用程式會帶入要刪除之引述的 `count`，然後傳回 `_allQuotesDeletedScope`：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="b5dc8-261">此範圍會將記錄擴充呼叫包裝在 [using](/dotnet/csharp/language-reference/keywords/using-statement) 區塊中：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="b5dc8-262">檢查應用程式主控台輸出中的記錄訊息。</span><span class="sxs-lookup"><span data-stu-id="b5dc8-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="b5dc8-263">下列結果顯示包含記錄範圍訊息的三個刪除引述：</span><span class="sxs-lookup"><span data-stu-id="b5dc8-263">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b5dc8-264">其他資源</span><span class="sxs-lookup"><span data-stu-id="b5dc8-264">Additional resources</span></span>

* [<span data-ttu-id="b5dc8-265">Logging</span><span class="sxs-lookup"><span data-stu-id="b5dc8-265">Logging</span></span>](xref:fundamentals/logging/index)
