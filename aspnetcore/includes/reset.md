Reset 可讓伺服器重設具有指定錯誤碼的 HTTP/2 要求。 重設要求會視為已中止。

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` 在上述程式碼範例中，指定 `INTERNAL_ERROR` 錯誤碼。 如需有關 HTTP/2 錯誤碼的詳細資訊，請造訪 [HTTP/2 規格錯誤碼一節](https://tools.ietf.org/html/rfc7540#page-50)。