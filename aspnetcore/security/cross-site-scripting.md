---
title: '防止 ASP.NET Core 中的跨網站腳本 (XSS) '
author: rick-anderson
description: 瞭解跨網站腳本 (XSS) 以及在 ASP.NET Core 應用程式中解決此弱點的技巧。
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: a7a0c0ff44de5b04d7fa9a8f2f16f7c9f786f64b
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057066"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="e58f3-103">防止 ASP.NET Core 中的跨網站腳本 (XSS) </span><span class="sxs-lookup"><span data-stu-id="e58f3-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="e58f3-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e58f3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e58f3-105">跨網站腳本 (XSS) 是一種安全性弱點，可讓攻擊者將用戶端腳本放 (通常是 JavaScript) 網頁中。</span><span class="sxs-lookup"><span data-stu-id="e58f3-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="e58f3-106">當其他使用者載入受影響的頁面時，攻擊者的腳本將會執行，讓攻擊者竊取 cookie 和會話權杖、透過 DOM 操作變更網頁的內容，或將瀏覽器重新導向至另一個頁面。</span><span class="sxs-lookup"><span data-stu-id="e58f3-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="e58f3-107">XSS 弱點通常會在應用程式接受使用者輸入並將其輸出至頁面，而不進行驗證、編碼或將其進行編碼時發生。</span><span class="sxs-lookup"><span data-stu-id="e58f3-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="e58f3-108">針對 XSS 保護您的應用程式</span><span class="sxs-lookup"><span data-stu-id="e58f3-108">Protecting your application against XSS</span></span>

<span data-ttu-id="e58f3-109">在基本層級中，XSS 的運作方式是引誘您的應用程式將標記插入轉譯的 `<script>` 頁面，或是將事件插入專案 `On*` 中。</span><span class="sxs-lookup"><span data-stu-id="e58f3-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="e58f3-110">開發人員應該使用下列預防步驟，以避免在應用程式中引進 XSS。</span><span class="sxs-lookup"><span data-stu-id="e58f3-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="e58f3-111">除非您遵循下列步驟的其餘步驟，否則請勿將不受信任的資料放入您的 HTML 輸入中。</span><span class="sxs-lookup"><span data-stu-id="e58f3-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="e58f3-112">不受信任的資料是指攻擊者、HTML 表單輸入、查詢字串、HTTP 標頭，甚至是來自資料庫的資料（即使是源自資料庫的資料）可能會受到攻擊，即使它們無法入侵您的應用程式也是一樣。</span><span class="sxs-lookup"><span data-stu-id="e58f3-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="e58f3-113">在 HTML 元素內放置不受信任的資料之前，請確定它是 HTML 編碼的。</span><span class="sxs-lookup"><span data-stu-id="e58f3-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="e58f3-114">HTML 編碼會採用字元（例如） &lt; ，並將它們變更為安全的格式，例如 &amp; lt;</span><span class="sxs-lookup"><span data-stu-id="e58f3-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="e58f3-115">將不受信任的資料放入 HTML 屬性之前，請確定其已進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e58f3-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="e58f3-116">HTML 屬性編碼是 HTML 編碼的超集合，並會編碼額外的字元，例如 "和 '。</span><span class="sxs-lookup"><span data-stu-id="e58f3-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="e58f3-117">將不受信任的資料放入 JavaScript 之前，請先將資料放在您于執行時間取得其內容的 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="e58f3-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="e58f3-118">如果無法這麼做，請確定資料是以 JavaScript 編碼。</span><span class="sxs-lookup"><span data-stu-id="e58f3-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="e58f3-119">JavaScript 編碼會針對 JavaScript 採用危險的字元，並以其十六進位取代，例如 &lt; 編碼為 `\u003C` 。</span><span class="sxs-lookup"><span data-stu-id="e58f3-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="e58f3-120">將不受信任的資料放入 URL 查詢字串之前，請確定它是以 URL 編碼。</span><span class="sxs-lookup"><span data-stu-id="e58f3-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="e58f3-121">使用 HTML 編碼 Razor</span><span class="sxs-lookup"><span data-stu-id="e58f3-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="e58f3-122">Razor在 MVC 中使用的引擎會自動將所有源自于變數的輸出編碼，除非您真的很難防止它執行這項作業。</span><span class="sxs-lookup"><span data-stu-id="e58f3-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="e58f3-123">當您使用指示詞時，它會使用 HTML 屬性編碼規則 *@* 。</span><span class="sxs-lookup"><span data-stu-id="e58f3-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="e58f3-124">Html 屬性編碼是 HTML 編碼的超集合，這表示您不需要擔心您是否應該使用 HTML 編碼或 HTML 屬性編碼。</span><span class="sxs-lookup"><span data-stu-id="e58f3-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="e58f3-125">您必須確保在 HTML 內容中只使用 @，而不是嘗試將不受信任的輸入直接插入 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="e58f3-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="e58f3-126">標籤協助程式也會編碼您用於標記參數的輸入。</span><span class="sxs-lookup"><span data-stu-id="e58f3-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="e58f3-127">請參閱下列各項 Razor ：</span><span class="sxs-lookup"><span data-stu-id="e58f3-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="e58f3-128">此視圖會輸出 *untrustedInput* 變數的內容。</span><span class="sxs-lookup"><span data-stu-id="e58f3-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="e58f3-129">此變數包含一些在 XSS 攻擊中使用的字元，也就是「 &lt; 和」 &gt; 。</span><span class="sxs-lookup"><span data-stu-id="e58f3-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="e58f3-130">檢查來源會顯示編碼為的轉譯輸出：</span><span class="sxs-lookup"><span data-stu-id="e58f3-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="e58f3-131">ASP.NET Core MVC 提供的 `HtmlString` 類別不會在輸出時自動編碼。</span><span class="sxs-lookup"><span data-stu-id="e58f3-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="e58f3-132">這不應該與不受信任的輸入結合使用，因為這會公開 XSS 弱點。</span><span class="sxs-lookup"><span data-stu-id="e58f3-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="e58f3-133">使用 JavaScript 編碼 Razor</span><span class="sxs-lookup"><span data-stu-id="e58f3-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="e58f3-134">有時候您可能會想要在 JavaScript 中插入值，以便在您的視圖中處理。</span><span class="sxs-lookup"><span data-stu-id="e58f3-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="e58f3-135">做法有二種。</span><span class="sxs-lookup"><span data-stu-id="e58f3-135">There are two ways to do this.</span></span> <span data-ttu-id="e58f3-136">插入值最安全的方式是將值放在標記的資料屬性中，然後在您的 JavaScript 中加以取出。</span><span class="sxs-lookup"><span data-stu-id="e58f3-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="e58f3-137">例如：</span><span class="sxs-lookup"><span data-stu-id="e58f3-137">For example:</span></span>

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

<span data-ttu-id="e58f3-138">上述標記會產生下列 HTML：</span><span class="sxs-lookup"><span data-stu-id="e58f3-138">The preceding markup generates the following HTML:</span></span>

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

<span data-ttu-id="e58f3-139">上述程式碼會產生下列輸出：</span><span class="sxs-lookup"><span data-stu-id="e58f3-139">The preceding code generates the following output:</span></span>

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> <span data-ttu-id="e58f3-140">請勿在 JavaScript 中串連 **未** 受信任的輸入，以建立 DOM 元素或使用 `document.write()` 于動態產生的內容。</span><span class="sxs-lookup"><span data-stu-id="e58f3-140">Do \***NOT** _ concatenate untrusted input in JavaScript to create DOM elements or use `document.write()` on dynamically generated content.</span></span>
>
> <span data-ttu-id="e58f3-141">您可以使用下列其中一種方法，防止程式碼公開至 DOM 型 XSS： _ `createElement()` ，並使用適當的方法或屬性（例如或）指派屬性值 `node.textContent=` `node.InnerText=` 。</span><span class="sxs-lookup"><span data-stu-id="e58f3-141">Use one of the following approaches to prevent code from being exposed to DOM-based XSS: _ `createElement()` and assign property values with appropriate methods or properties such as `node.textContent=` or `node.InnerText=`.</span></span>
> * <span data-ttu-id="e58f3-142">`document.CreateTextNode()` 並將其附加至適當的 DOM 位置。</span><span class="sxs-lookup"><span data-stu-id="e58f3-142">`document.CreateTextNode()` and append it in the appropriate DOM location.</span></span>
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="e58f3-143">存取程式碼中的編碼器</span><span class="sxs-lookup"><span data-stu-id="e58f3-143">Accessing encoders in code</span></span>

<span data-ttu-id="e58f3-144">HTML、JavaScript 和 URL 編碼器以兩種方式提供給您的程式碼，您可以透過相依性 [插入](xref:fundamentals/dependency-injection) 將它們插入，也可以使用命名空間中包含的預設編碼器 `System.Text.Encodings.Web` 。</span><span class="sxs-lookup"><span data-stu-id="e58f3-144">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="e58f3-145">如果您使用預設編碼器，則任何套用至字元範圍以視為安全的系統都不會生效，預設編碼器會使用最安全的編碼規則。</span><span class="sxs-lookup"><span data-stu-id="e58f3-145">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="e58f3-146">若要透過 DI 使用可設定的編碼器，您的函式應適當地採用 *HtmlEncoder*、 *JavaScriptEncoder* 和 *UrlEncoder* 參數。</span><span class="sxs-lookup"><span data-stu-id="e58f3-146">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="e58f3-147">例如：</span><span class="sxs-lookup"><span data-stu-id="e58f3-147">For example;</span></span>

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

## <a name="encoding-url-parameters"></a><span data-ttu-id="e58f3-148">編碼 URL 參數</span><span class="sxs-lookup"><span data-stu-id="e58f3-148">Encoding URL Parameters</span></span>

<span data-ttu-id="e58f3-149">如果您想要使用不受信任的輸入來建立 URL 查詢字串作為值，請使用 `UrlEncoder` 來編碼值。</span><span class="sxs-lookup"><span data-stu-id="e58f3-149">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="e58f3-150">例如，</span><span class="sxs-lookup"><span data-stu-id="e58f3-150">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="e58f3-151">編碼之後，Url-encodedvalue 變數將會包含 `%22Quoted%20Value%20with%20spaces%20and%20%26%22` 。</span><span class="sxs-lookup"><span data-stu-id="e58f3-151">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="e58f3-152">空格、引號、標點符號和其他 unsafe 字元將以百分比編碼為其十六進位值，例如空白字元將會變成 %20。</span><span class="sxs-lookup"><span data-stu-id="e58f3-152">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="e58f3-153">請勿使用不受信任的輸入作為 URL 路徑的一部分。</span><span class="sxs-lookup"><span data-stu-id="e58f3-153">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="e58f3-154">一律傳遞不受信任的輸入作為查詢字串值。</span><span class="sxs-lookup"><span data-stu-id="e58f3-154">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="e58f3-155">自訂編碼器</span><span class="sxs-lookup"><span data-stu-id="e58f3-155">Customizing the Encoders</span></span>

<span data-ttu-id="e58f3-156">根據預設，編碼器會使用限制為基本拉丁 Unicode 範圍的安全清單，並將該範圍之外的所有字元編碼為其對等字元碼。</span><span class="sxs-lookup"><span data-stu-id="e58f3-156">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="e58f3-157">這個行為也會影響 Razor TagHelper 和 HtmlHelper 轉譯，因為它會使用編碼器來輸出您的字串。</span><span class="sxs-lookup"><span data-stu-id="e58f3-157">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="e58f3-158">這種情況的原因是為了防範未知或未來的瀏覽器錯誤 (先前的瀏覽器錯誤會根據非英文) 字元的處理，而使剖析無法進行。</span><span class="sxs-lookup"><span data-stu-id="e58f3-158">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="e58f3-159">如果您的網站大量使用非拉丁字元，例如中文、斯拉夫文或其他專案，則可能不是您想要的行為。</span><span class="sxs-lookup"><span data-stu-id="e58f3-159">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="e58f3-160">您可以自訂編碼器安全清單，以在啟動期間包含適合您應用程式的 Unicode 範圍 `ConfigureServices()` 。</span><span class="sxs-lookup"><span data-stu-id="e58f3-160">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="e58f3-161">例如，使用預設設定時，您可能會使用 Razor HtmlHelper，如下所示：</span><span class="sxs-lookup"><span data-stu-id="e58f3-161">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="e58f3-162">當您查看網頁的來源時，您會看到它的轉譯方式如下，並以中文文字編碼;</span><span class="sxs-lookup"><span data-stu-id="e58f3-162">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="e58f3-163">若要將編碼程式所視為安全的字元加寬，您可以在的方法中插入下列程式程式碼 `ConfigureServices()` `startup.cs` 。</span><span class="sxs-lookup"><span data-stu-id="e58f3-163">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="e58f3-164">此範例會擴大安全清單，以包含 Unicode 範圍 CjkUnifiedIdeographs。</span><span class="sxs-lookup"><span data-stu-id="e58f3-164">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="e58f3-165">轉譯的輸出現在會變成</span><span class="sxs-lookup"><span data-stu-id="e58f3-165">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="e58f3-166">安全清單範圍會指定為 Unicode 程式碼圖表，而不是語言。</span><span class="sxs-lookup"><span data-stu-id="e58f3-166">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="e58f3-167">[Unicode 標準](https://unicode.org/)有一份程式[代碼圖表](https://www.unicode.org/charts/index.html)清單，您可以用來尋找包含您字元的圖表。</span><span class="sxs-lookup"><span data-stu-id="e58f3-167">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="e58f3-168">每個編碼器、Html、JavaScript 和 Url 都必須分別設定。</span><span class="sxs-lookup"><span data-stu-id="e58f3-168">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="e58f3-169">安全清單的自訂只會影響透過 DI 來源的編碼器。</span><span class="sxs-lookup"><span data-stu-id="e58f3-169">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="e58f3-170">如果您透過預設值直接存取編碼器，則會 `System.Text.Encodings.Web.*Encoder.Default` 使用僅限基本拉丁的安全的安全。</span><span class="sxs-lookup"><span data-stu-id="e58f3-170">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="e58f3-171">應該在哪裡進行編碼？</span><span class="sxs-lookup"><span data-stu-id="e58f3-171">Where should encoding take place?</span></span>

<span data-ttu-id="e58f3-172">一般已接受的作法是在輸出的時間點進行編碼，而編碼的值絕對不應該儲存在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="e58f3-172">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="e58f3-173">在輸出點進行編碼可讓您變更資料的使用，例如，從 HTML 變更為查詢字串值。</span><span class="sxs-lookup"><span data-stu-id="e58f3-173">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="e58f3-174">它也可讓您輕鬆地搜尋資料，而不需要在搜尋前編碼值，並可讓您利用對編碼器所做的任何變更或錯誤修正。</span><span class="sxs-lookup"><span data-stu-id="e58f3-174">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="e58f3-175">作為 XSS 防護技術的驗證</span><span class="sxs-lookup"><span data-stu-id="e58f3-175">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="e58f3-176">驗證可能是限制 XSS 攻擊的有用工具。</span><span class="sxs-lookup"><span data-stu-id="e58f3-176">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="e58f3-177">例如，只包含字元0-9 的數值字串將不會觸發 XSS 攻擊。</span><span class="sxs-lookup"><span data-stu-id="e58f3-177">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="e58f3-178">接受使用者輸入中的 HTML 時，驗證變得更複雜。</span><span class="sxs-lookup"><span data-stu-id="e58f3-178">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="e58f3-179">如果不可能，剖析 HTML 輸入是很困難的。</span><span class="sxs-lookup"><span data-stu-id="e58f3-179">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="e58f3-180">Markdown 結合了剖析內嵌 HTML 的剖析器，是接受豐富輸入的更安全選擇。</span><span class="sxs-lookup"><span data-stu-id="e58f3-180">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="e58f3-181">切勿單獨依賴驗證。</span><span class="sxs-lookup"><span data-stu-id="e58f3-181">Never rely on validation alone.</span></span> <span data-ttu-id="e58f3-182">一律在輸出之前編碼未受信任的輸入，無論已執行何種驗證或清理。</span><span class="sxs-lookup"><span data-stu-id="e58f3-182">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
