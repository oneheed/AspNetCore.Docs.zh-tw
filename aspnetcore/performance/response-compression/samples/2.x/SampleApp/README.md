# <a name="response-compression-sample-application-aspnet-core-2x"></a>回應壓縮範例應用程式 (ASP.NET Core 2.x) 

此範例說明如何使用 ASP.NET Core 2.x 回應壓縮中介軟體來壓縮 HTTP 回應。 此範例會示範適用于文字和影像回應的 Gzip、Brotli 和自訂壓縮提供者，並示範如何加入 MIME 類型以進行壓縮。 如需 ASP.NET Core 1.x 範例，請參閱 [回應壓縮範例應用程式 (ASP.NET Core 1.x) ](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/response-compression/samples/1.x)。

## <a name="examples-in-this-sample"></a>這個範例中的範例

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum 文字檔回應，2044位元組會壓縮為 ~ 979 位元組。
    * **/testfile1kb.txt** -將壓縮為 ~ 36 位元組的1033個位元組的文字檔回應。
    * **/trickle** -以單一字元形式發出的回應（1秒間隔）。
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum 文字檔回應，2044位元組會壓縮為 ~ 927 位元組。
    * **/testfile1kb.txt** -將壓縮為 ~ 47 位元組的1033個位元組的文字檔回應。
    * **/trickle** -以單一字元形式發出的回應（1秒間隔）。
  * `image/svg+xml`
    * **/banner.svg** -可調整的向量圖形 (svg) image 回應（9707個位元組），壓縮為 ~ 4459 個位元組。
* `CustomCompressionProvider`<br>示範如何執行自訂壓縮提供者以搭配中介軟體使用。

當要求包含 `Accept-Encoding` 標頭，且回應壓縮成功時，中介軟體會自動將 `Vary: Accept-Encoding` 標頭新增至回應。 `Vary`標頭會指示快取根據的替代值維護回應的多個複本 `Accept-Encoding` ，因此壓縮的 (Gzip 或 Brotli) 和未壓縮的版本都會儲存在可接受壓縮或未壓縮回應之系統的快取中。

## <a name="use-the-sample"></a>使用範例

1. 使用 [Fiddler](https://www.telerik.com/fiddler)、 [Firebug](https://getfirebug.com/)或 [Postman](https://www.getpostman.com/) 將要求發出至不含標頭的應用程式， `Accept-Encoding` 並記下回應承載、回應大小和回應標頭。
1. 新增 `Accept-Encoding: br` 或 `Accept-Encoding: gzip` 標頭，並記下壓縮的回應大小和回應標頭。 回應大小會下降， `Content-Encoding` 中介軟體會包含回應標頭，指出發生了 Gzip 或 Brotli 的壓縮。 當您查看 Lorem Ipsum 或 **testfile1kb.txt** 回應的回應主體時，您會看到文字已壓縮且無法讀取。
1. 新增 `Accept-Encoding: mycustomcompression` 標頭，並記下回應標頭。 `CustomCompressionProvider`是空的執行，不會實際壓縮回應，但您可以為方法建立自訂壓縮資料流程包裝函式 `CreateStream()` 。
