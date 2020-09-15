---
title: '防止 ASP.NET Core 中的跨網站腳本 (XSS) '
author: rick-anderson
description: 瞭解跨網站腳本 (XSS) 以及在 ASP.NET Core 應用程式中解決此弱點的技巧。
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: 03bdfe9260ef6433456ba53d0cab8c7bf9f86377
ms.sourcegitcommit: 422e02bad384775bfe19a90910737340ad106c5b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90083462"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>防止 ASP.NET Core 中的跨網站腳本 (XSS) 

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

跨網站腳本 (XSS) 是一種安全性弱點，可讓攻擊者將用戶端腳本放 (通常是 JavaScript) 網頁中。 當其他使用者載入受影響的頁面時，攻擊者的腳本將會執行，讓攻擊者竊取 cookie 和會話權杖、透過 DOM 操作變更網頁的內容，或將瀏覽器重新導向至另一個頁面。 XSS 弱點通常會在應用程式接受使用者輸入並將其輸出至頁面，而不進行驗證、編碼或將其進行編碼時發生。

## <a name="protecting-your-application-against-xss"></a>針對 XSS 保護您的應用程式

在基本層級中，XSS 的運作方式是引誘您的應用程式將標記插入轉譯的 `<script>` 頁面，或是將事件插入專案 `On*` 中。 開發人員應該使用下列預防步驟，以避免在應用程式中引進 XSS。

1. 除非您遵循下列步驟的其餘步驟，否則請勿將不受信任的資料放入您的 HTML 輸入中。 不受信任的資料是指攻擊者、HTML 表單輸入、查詢字串、HTTP 標頭，甚至是來自資料庫的資料（即使是源自資料庫的資料）可能會受到攻擊，即使它們無法入侵您的應用程式也是一樣。

2. 在 HTML 元素內放置不受信任的資料之前，請確定它是 HTML 編碼的。 HTML 編碼會採用字元（例如） &lt; ，並將它們變更為安全的格式，例如 &amp; lt;

3. 將不受信任的資料放入 HTML 屬性之前，請確定其已進行 HTML 編碼。 HTML 屬性編碼是 HTML 編碼的超集合，並會編碼額外的字元，例如 "和 '。

4. 將不受信任的資料放入 JavaScript 之前，請先將資料放在您于執行時間取得其內容的 HTML 元素中。 如果無法這麼做，請確定資料是以 JavaScript 編碼。 JavaScript 編碼會針對 JavaScript 採用危險的字元，並以其十六進位取代，例如 &lt; 編碼為 `\u003C` 。

5. 將不受信任的資料放入 URL 查詢字串之前，請確定它是以 URL 編碼。

## <a name="html-encoding-using-no-locrazor"></a>使用 HTML 編碼 Razor

Razor在 MVC 中使用的引擎會自動將所有源自于變數的輸出編碼，除非您真的很難防止它執行這項作業。 當您使用指示詞時，它會使用 HTML 屬性編碼規則 *@* 。 Html 屬性編碼是 HTML 編碼的超集合，這表示您不需要擔心您是否應該使用 HTML 編碼或 HTML 屬性編碼。 您必須確保在 HTML 內容中只使用 @，而不是嘗試將不受信任的輸入直接插入 JavaScript。 標籤協助程式也會編碼您用於標記參數的輸入。

請參閱下列各項 Razor ：

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

此視圖會輸出 *untrustedInput* 變數的內容。 此變數包含一些在 XSS 攻擊中使用的字元，也就是「 &lt; 和」 &gt; 。 檢查來源會顯示編碼為的轉譯輸出：

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC 提供的 `HtmlString` 類別不會在輸出時自動編碼。 這不應該與不受信任的輸入結合使用，因為這會公開 XSS 弱點。

## <a name="javascript-encoding-using-no-locrazor"></a>使用 JavaScript 編碼 Razor

有時候您可能會想要在 JavaScript 中插入值，以便在您的視圖中處理。 做法有二種。 插入值最安全的方式是將值放在標記的資料屬性中，然後在您的 JavaScript 中加以取出。 例如：

```cshtml
@{
    var untrustedInput = "<script>alert(1)</script>";
}

<div id="injectedData"
     data-untrustedinput="@untrustedInput" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it
    // can lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

上述標記會產生下列 HTML：

```html
<div id="injectedData"
     data-untrustedinput="&lt;script&gt;alert(1)&lt;/script&gt;" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
// Do NOT use document.write() on dynamically generated data as it can
// lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

上述程式碼會產生下列輸出：

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> 請勿在 JavaScript 中串連 ***未*** 受信任的輸入，以建立 DOM 元素或使用 `document.write()` 于動態產生的內容。
>
> 您可以使用下列其中一種方法，防止將程式碼公開給以 DOM 為基礎的 XSS：
> * `createElement()` 並使用適當的方法或屬性（例如或 node）來指派屬性值 `node.textContent=` 。InnerText = '。
> * `document.CreateTextNode()` 並將其附加至適當的 DOM 位置。
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a>存取程式碼中的編碼器

HTML、JavaScript 和 URL 編碼器以兩種方式提供給您的程式碼，您可以透過相依性 [插入](xref:fundamentals/dependency-injection) 將它們插入，也可以使用命名空間中包含的預設編碼器 `System.Text.Encodings.Web` 。 如果您使用預設編碼器，則任何套用至字元範圍以視為安全的系統都不會生效，預設編碼器會使用最安全的編碼規則。

若要透過 DI 使用可設定的編碼器，您的函式應適當地採用 *HtmlEncoder*、 *JavaScriptEncoder* 和 *UrlEncoder* 參數。 例如：

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a>編碼 URL 參數

如果您想要使用不受信任的輸入來建立 URL 查詢字串作為值，請使用 `UrlEncoder` 來編碼值。 例如，

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

編碼之後，Url-encodedvalue 變數將會包含 `%22Quoted%20Value%20with%20spaces%20and%20%26%22` 。 空格、引號、標點符號和其他 unsafe 字元將以百分比編碼為其十六進位值，例如空白字元將會變成 %20。

>[!WARNING]
> 請勿使用不受信任的輸入作為 URL 路徑的一部分。 一律傳遞不受信任的輸入作為查詢字串值。

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>自訂編碼器

根據預設，編碼器會使用限制為基本拉丁 Unicode 範圍的安全清單，並將該範圍之外的所有字元編碼為其對等字元碼。 這個行為也會影響 Razor TagHelper 和 HtmlHelper 轉譯，因為它會使用編碼器來輸出您的字串。

這種情況的原因是為了防範未知或未來的瀏覽器錯誤 (先前的瀏覽器錯誤會根據非英文) 字元的處理，而使剖析無法進行。 如果您的網站大量使用非拉丁字元，例如中文、斯拉夫文或其他專案，則可能不是您想要的行為。

您可以自訂編碼器安全清單，以在啟動期間包含適合您應用程式的 Unicode 範圍 `ConfigureServices()` 。

例如，使用預設設定時，您可能會使用 Razor HtmlHelper，如下所示：

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

當您查看網頁的來源時，您會看到它的轉譯方式如下，並以中文文字編碼;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

若要將編碼程式所視為安全的字元加寬，您可以在的方法中插入下列程式程式碼 `ConfigureServices()` `startup.cs` 。

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

此範例會擴大安全清單，以包含 Unicode 範圍 CjkUnifiedIdeographs。 轉譯的輸出現在會變成

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

安全清單範圍會指定為 Unicode 程式碼圖表，而不是語言。 [Unicode 標準](https://unicode.org/)有一份程式[代碼圖表](https://www.unicode.org/charts/index.html)清單，您可以用來尋找包含您字元的圖表。 每個編碼器、Html、JavaScript 和 Url 都必須分別設定。

> [!NOTE]
> 安全清單的自訂只會影響透過 DI 來源的編碼器。 如果您透過預設值直接存取編碼器，則會 `System.Text.Encodings.Web.*Encoder.Default` 使用僅限基本拉丁的安全的安全。

## <a name="where-should-encoding-take-place"></a>應該在哪裡進行編碼？

一般已接受的作法是在輸出的時間點進行編碼，而編碼的值絕對不應該儲存在資料庫中。 在輸出點進行編碼可讓您變更資料的使用，例如，從 HTML 變更為查詢字串值。 它也可讓您輕鬆地搜尋資料，而不需要在搜尋前編碼值，並可讓您利用對編碼器所做的任何變更或錯誤修正。

## <a name="validation-as-an-xss-prevention-technique"></a>作為 XSS 防護技術的驗證

驗證可能是限制 XSS 攻擊的有用工具。 例如，只包含字元0-9 的數值字串將不會觸發 XSS 攻擊。 接受使用者輸入中的 HTML 時，驗證變得更複雜。 如果不可能，剖析 HTML 輸入是很困難的。 Markdown 結合了剖析內嵌 HTML 的剖析器，是接受豐富輸入的更安全選擇。 切勿單獨依賴驗證。 一律在輸出之前編碼未受信任的輸入，無論已執行何種驗證或清理。
