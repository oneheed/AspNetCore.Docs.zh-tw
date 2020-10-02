<span data-ttu-id="69fdf-101">Reset 可讓伺服器重設具有指定錯誤碼的 HTTP/2 要求。</span><span class="sxs-lookup"><span data-stu-id="69fdf-101">Reset allows for the server to reset a HTTP/2 request with a specified error code.</span></span> <span data-ttu-id="69fdf-102">重設要求會視為已中止。</span><span class="sxs-lookup"><span data-stu-id="69fdf-102">A reset request is considered aborted.</span></span>

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

<span data-ttu-id="69fdf-103">`Reset` 在上述程式碼範例中，指定 `INTERNAL_ERROR` 錯誤碼。</span><span class="sxs-lookup"><span data-stu-id="69fdf-103">`Reset` in the preceding code example specifies the `INTERNAL_ERROR` error code.</span></span> <span data-ttu-id="69fdf-104">如需有關 HTTP/2 錯誤碼的詳細資訊，請造訪 [HTTP/2 規格錯誤碼一節](https://tools.ietf.org/html/rfc7540#page-50)。</span><span class="sxs-lookup"><span data-stu-id="69fdf-104">For more information about HTTP/2 error codes, visit the [HTTP/2 specification error code section](https://tools.ietf.org/html/rfc7540#page-50).</span></span>