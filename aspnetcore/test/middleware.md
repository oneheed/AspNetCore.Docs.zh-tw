---
title: 測試 ASP.NET Core 中介軟體
author: tratcher
description: 瞭解如何使用 TestServer 測試 ASP.NET Core 中介軟體。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
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
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051430"
---
# <a name="test-aspnet-core-middleware"></a>測試 ASP.NET Core 中介軟體

由 [Chris Ross](https://github.com/Tratcher)

您可以使用隔離中介軟體來測試中介軟體 <xref:Microsoft.AspNetCore.TestHost.TestServer> 。 它可讓您：

* 具現化應用程式管線，其中只包含您需要測試的元件。
* 傳送自訂要求以驗證中介軟體行為。

優點：

* 要求會以記憶體傳送，而不是透過網路進行序列化。
* 這可避免其他問題，例如埠管理和 HTTPS 憑證。
* 中介軟體中的例外狀況可以直接流回呼叫的測試。
* 您可以直接在測試中自訂伺服器資料結構（例如 <xref:Microsoft.AspNetCore.Http.HttpContext> ）。

## <a name="set-up-the-testserver"></a>設定 TestServer

在測試專案中，建立測試：

* 建立並啟動使用的主機 <xref:Microsoft.AspNetCore.TestHost.TestServer> 。
* 新增中介軟體所使用的任何必要服務。
* 將 [AspNetCore TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet 套件新增至專案：
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* 將處理管線設定為使用中介軟體進行測試。

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>使用 HttpClient 傳送要求

使用 <xref:System.Net.Http.HttpClient> 下列內容傳送要求：

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

判斷提示結果。 首先，讓判斷提示的結果與您預期的結果相反。 具有 false 正數判斷提示的初始回合，可確認當中間件正常執行時，測試會失敗。 執行測試，並確認測試失敗。

在下列範例中，中介軟體應該會傳回404狀態碼 (當要求根端點時， *找不到* ) 。 執行第一個測試 `Assert.NotEqual( ... );` ，這應該會失敗：

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

變更判斷提示，以在正常操作條件下測試中介軟體。 最終的測試會使用 `Assert.Equal( ... );` 。 再次執行測試，以確認它通過。

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>使用 HttpCoNtext 傳送要求

測試應用程式也可以使用 [SendAsync (Action \<HttpContext> ，CancellationToken) ](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A)來傳送要求。 在下列範例中，中介軟體處理時，會進行幾項檢查 `https://example.com/A/Path/?and=query` ：

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> 允許直接設定物件， <xref:Microsoft.AspNetCore.Http.HttpContext> 而不是使用 <xref:System.Net.Http.HttpClient> 抽象概念。 您 <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> 可以使用來操作伺服器上僅提供的結構，例如 [HttpcoNtext. Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) 或 [HTTPcoNtext 功能](xref:Microsoft.AspNetCore.Http.HttpContext.Features)。

如同先前測試過 *404-找不* 到回應的範例，請檢查 `Assert` 先前測試中每個語句的相反。 這項檢查會確認當中間件正常運作時測試是否正確。 確認錯誤的正面測試正常運作之後，請 `Assert` 針對預期的條件和測試值設定最後的語句。 再執行一次，以確認測試通過。

## <a name="testserver-limitations"></a>TestServer 限制

TestServer

* 是為了將伺服器行為複寫到測試中介軟體所建立。
* * **Not** _ 會嘗試複寫所有 <xref:System.Net.Http.HttpClient> 行為。
_ 會盡可能讓用戶端存取伺服器的存取權限，而且可以盡可能看到伺服器上發生的狀況。 例如，它可能會擲回通常未擲回的例外 `HttpClient` 狀況，以便直接傳達伺服器狀態。
* 預設不會設定某些傳輸特定標頭，因為這些標頭通常與中介軟體無關。 如需詳細資訊，請參閱下一節。

### <a name="content-length-and-transfer-encoding-headers"></a>Content-type 長度和 Transfer-Encoding 標頭

TestServer **不會** 設定傳輸相關的要求或回應標頭，例如 [內容長度](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) 或 [傳輸編碼](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding)。 應用程式應該避免根據這些標頭，因為其使用方式會因用戶端、案例和通訊協定而異。 如果 `Content-Length` 和 `Transfer-Encoding` 是測試特定案例的必要項，則可以在撰寫或時于測試中指定 <xref:System.Net.Http.HttpRequestMessage> 它們 <xref:Microsoft.AspNetCore.Http.HttpContext> 。 如需詳細資訊，請參閱下列 GitHub 問題：

_ [dotnet/aspnetcore # 21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [dotnet/aspnetcore # 18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [dotnet/aspnetcore # 13273](https://github.com/dotnet/aspnetcore/issues/13273)
