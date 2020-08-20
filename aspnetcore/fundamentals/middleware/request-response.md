---
title: ASP.NET Core 中的要求和回應作業
author: jkotalik
description: 了解如何在 ASP.NET Core 中讀取要求本文及寫入回應本文。
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
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
uid: fundamentals/middleware/request-response
ms.openlocfilehash: ce7357ccbb52736bfb44cd8e041c68a0992bf319
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627776"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="29c07-103">ASP.NET Core 中的要求和回應作業</span><span class="sxs-lookup"><span data-stu-id="29c07-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="29c07-104">作者：[Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="29c07-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="29c07-105">此文章說明如何讀取要求本文及寫入回應本文。</span><span class="sxs-lookup"><span data-stu-id="29c07-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="29c07-106">撰寫中介軟體時，可能需要這些作業的程式碼。</span><span class="sxs-lookup"><span data-stu-id="29c07-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="29c07-107">除了撰寫中介軟體之外，自訂程式碼通常不是必要的，因為這些作業是由 MVC 和 Razor 頁面處理。</span><span class="sxs-lookup"><span data-stu-id="29c07-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="29c07-108">要求和回應主體有兩個抽象概念： <xref:System.IO.Stream> 和 <xref:System.IO.Pipelines.Pipe> 。</span><span class="sxs-lookup"><span data-stu-id="29c07-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="29c07-109">若為要求讀取， <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> 則為 <xref:System.IO.Stream> ，而且 `HttpRequest.BodyReader` 為 <xref:System.IO.Pipelines.PipeReader> 。</span><span class="sxs-lookup"><span data-stu-id="29c07-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="29c07-110">針對寫入的回應， <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> 是 <xref:System.IO.Stream> ，而且 `HttpResponse.BodyWriter` 是 <xref:System.IO.Pipelines.PipeWriter> 。</span><span class="sxs-lookup"><span data-stu-id="29c07-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="29c07-111">建議將[管線](/dotnet/standard/io/pipelines)用於資料流程。</span><span class="sxs-lookup"><span data-stu-id="29c07-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="29c07-112">資料流可以更容易地用於一些簡單的作業，但管線具有效能優勢，並且更容易在大部分情況下使用。</span><span class="sxs-lookup"><span data-stu-id="29c07-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="29c07-113">ASP.NET Core 開始使用管線而非內部的資料流程。</span><span class="sxs-lookup"><span data-stu-id="29c07-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="29c07-114">範例包括：</span><span class="sxs-lookup"><span data-stu-id="29c07-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="29c07-115">資料流程不會從架構中移除。</span><span class="sxs-lookup"><span data-stu-id="29c07-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="29c07-116">資料流程會繼續在整個 .NET 中使用，而許多資料流程類型不會有對等的管道，例如 `FileStreams` 和 `ResponseCompression` 。</span><span class="sxs-lookup"><span data-stu-id="29c07-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="29c07-117">資料流範例</span><span class="sxs-lookup"><span data-stu-id="29c07-117">Stream examples</span></span>

<span data-ttu-id="29c07-118">假設目標是要建立一個中介軟體，將整個要求本文以字串清單的形式讀取，並在新行上進行分割。</span><span class="sxs-lookup"><span data-stu-id="29c07-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="29c07-119">簡單的資料流實作看起來可能像下列範例這樣：</span><span class="sxs-lookup"><span data-stu-id="29c07-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="29c07-120">下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="29c07-120">The following code:</span></span>
> * <span data-ttu-id="29c07-121">用來示範不使用管道來讀取要求主體的問題。</span><span class="sxs-lookup"><span data-stu-id="29c07-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="29c07-122">不適合用于生產應用程式。</span><span class="sxs-lookup"><span data-stu-id="29c07-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="29c07-123">此程式碼確實有用，但有一些問題：</span><span class="sxs-lookup"><span data-stu-id="29c07-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="29c07-124">附加到 `StringBuilder` 之前，此範例會建立另一個會立即棄置的字串 (`encodedString`)。</span><span class="sxs-lookup"><span data-stu-id="29c07-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="29c07-125">系統會對資料流中的所有位元組執行此處理序，因此整個要求本文會額外耗用記憶體。</span><span class="sxs-lookup"><span data-stu-id="29c07-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="29c07-126">此範例會在於新行上進行分割之前讀取整個字串。</span><span class="sxs-lookup"><span data-stu-id="29c07-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="29c07-127">在位元組陣列中檢查是否有新行是更有效率的作法。</span><span class="sxs-lookup"><span data-stu-id="29c07-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="29c07-128">以下是修正上述一些問題的範例：</span><span class="sxs-lookup"><span data-stu-id="29c07-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="29c07-129">下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="29c07-129">The following code:</span></span>
> * <span data-ttu-id="29c07-130">用來示範上述程式碼中某些問題的解決方案，同時不會解決所有問題。</span><span class="sxs-lookup"><span data-stu-id="29c07-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="29c07-131">不適合用于生產應用程式。</span><span class="sxs-lookup"><span data-stu-id="29c07-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="29c07-132">上述範例：</span><span class="sxs-lookup"><span data-stu-id="29c07-132">This preceding example:</span></span>

* <span data-ttu-id="29c07-133">不會針對 `StringBuilder` 中的整個要求本文進行緩衝處理，除非沒有任何新行字元。</span><span class="sxs-lookup"><span data-stu-id="29c07-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="29c07-134">不會在字串上呼叫 `Split`。</span><span class="sxs-lookup"><span data-stu-id="29c07-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="29c07-135">但是仍有幾個問題：</span><span class="sxs-lookup"><span data-stu-id="29c07-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="29c07-136">如果換行字元是稀疏的，則會在字串中緩衝處理大部分的要求主體。</span><span class="sxs-lookup"><span data-stu-id="29c07-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="29c07-137">程式碼會繼續建立 () 的字串， `remainingString` 並將它們新增至字串緩衝區，這會導致額外的配置。</span><span class="sxs-lookup"><span data-stu-id="29c07-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="29c07-138">這些問題都是可修復的，但程式碼逐漸變得更複雜，但卻沒有改進。</span><span class="sxs-lookup"><span data-stu-id="29c07-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="29c07-139">管線提供解決這些問題的方法，且具有最低的程式碼複雜度。</span><span class="sxs-lookup"><span data-stu-id="29c07-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="29c07-140">Pipelines</span><span class="sxs-lookup"><span data-stu-id="29c07-140">Pipelines</span></span>

<span data-ttu-id="29c07-141">下列範例顯示如何使用 [PipeReader](/dotnet/standard/io/pipelines#pipe)來處理相同案例：</span><span class="sxs-lookup"><span data-stu-id="29c07-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="29c07-142">此範例可修正資料流實作的許多問題：</span><span class="sxs-lookup"><span data-stu-id="29c07-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="29c07-143">不需要字串緩衝區，因為 `PipeReader` 會處理尚未使用的位元組。</span><span class="sxs-lookup"><span data-stu-id="29c07-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="29c07-144">編碼的字串會直接新增至所傳回字串的清單。</span><span class="sxs-lookup"><span data-stu-id="29c07-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="29c07-145">除了呼叫之外 `ToArray` ，以及字串所使用的記憶體之外，還可以免費配置建立字串。</span><span class="sxs-lookup"><span data-stu-id="29c07-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="29c07-146">配接器</span><span class="sxs-lookup"><span data-stu-id="29c07-146">Adapters</span></span>

<span data-ttu-id="29c07-147">`Body`、和 `BodyReader` `BodyWriter` 屬性適用于 `HttpRequest` 和 `HttpResponse` 。</span><span class="sxs-lookup"><span data-stu-id="29c07-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="29c07-148">當您將設定 `Body` 為不同的資料流程時，一組新的介面卡會自動調整每個類型。</span><span class="sxs-lookup"><span data-stu-id="29c07-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="29c07-149">如果您將設定 `HttpRequest.Body` 為新的資料流程，會 `HttpRequest.BodyReader` 自動設為包裝的新資料流程 `PipeReader` `HttpRequest.Body` 。</span><span class="sxs-lookup"><span data-stu-id="29c07-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="29c07-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="29c07-150">StartAsync</span></span>

<span data-ttu-id="29c07-151">`HttpResponse.StartAsync` 用來指出標頭是不可修改的，以及執行 `OnStarting` 回呼。</span><span class="sxs-lookup"><span data-stu-id="29c07-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="29c07-152">使用 Kestrel 做為伺服器時，在 `StartAsync` 使用之前呼叫 `PipeReader` 會保證所傳回的記憶體 `GetMemory` 屬於 Kestrel 的內部， <xref:System.IO.Pipelines.Pipe> 而不是外部緩衝區。</span><span class="sxs-lookup"><span data-stu-id="29c07-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="29c07-153">其他資源</span><span class="sxs-lookup"><span data-stu-id="29c07-153">Additional resources</span></span>

* [<span data-ttu-id="29c07-154">.NET 中的 system.object</span><span class="sxs-lookup"><span data-stu-id="29c07-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
