<span data-ttu-id="d3f49-101">HTTP 結尾與 HTTP 標頭類似，不同之處在于它們會在傳送回應主體之後傳送。</span><span class="sxs-lookup"><span data-stu-id="d3f49-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="d3f49-102">針對 IIS 和 HTTP.SYS，只支援 HTTP/2 回應尾端。</span><span class="sxs-lookup"><span data-stu-id="d3f49-102">For IIS and HTTP.SYS, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="d3f49-103">在上述的範例程式碼中：</span><span class="sxs-lookup"><span data-stu-id="d3f49-103">In the preceding example code:</span></span>

* <span data-ttu-id="d3f49-104">`SupportsTrailers` 確保回應支援尾端。</span><span class="sxs-lookup"><span data-stu-id="d3f49-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="d3f49-105">`DeclareTrailer` 將指定的尾端名稱加入至 `Trailer` 回應標頭。</span><span class="sxs-lookup"><span data-stu-id="d3f49-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="d3f49-106">宣告回應的尾端是選擇性的，但建議使用。</span><span class="sxs-lookup"><span data-stu-id="d3f49-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="d3f49-107">如果 `DeclareTrailer` 呼叫，則必須在傳送回應標頭之前。</span><span class="sxs-lookup"><span data-stu-id="d3f49-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="d3f49-108">`AppendTrailer` 附加尾端。</span><span class="sxs-lookup"><span data-stu-id="d3f49-108">`AppendTrailer` appends the trailer.</span></span>
