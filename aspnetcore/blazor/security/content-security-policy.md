---
title: '針對 ASP.NET Core 強制執行內容安全性原則 :::no-loc(Blazor):::'
author: guardrex
description: '瞭解如何搭配 ASP.NET Core apps 使用 (CSP) 的內容安全性原則 :::no-loc(Blazor)::: ，以協助防範跨網站腳本 (XSS) 攻擊。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/content-security-policy
ms.openlocfilehash: 66fd41abe4f85071797bacc0a5531bbab35bd227
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055590"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a><span data-ttu-id="37c15-103">針對 ASP.NET Core 強制執行內容安全性原則 :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="37c15-103">Enforce a Content Security Policy for ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="37c15-104">由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="37c15-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="37c15-105">[跨網站腳本 (XSS) ](xref:security/cross-site-scripting) 是一種安全性弱點，攻擊者會將一或多個惡意的用戶端腳本放入應用程式的轉譯內容中。</span><span class="sxs-lookup"><span data-stu-id="37c15-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="37c15-106"> (CSP) 的內容安全性原則，可通知瀏覽器有效，以防止 XSS 攻擊：</span><span class="sxs-lookup"><span data-stu-id="37c15-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="37c15-107">載入之內容的來源，包括腳本、樣式表單和影像。</span><span class="sxs-lookup"><span data-stu-id="37c15-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="37c15-108">頁面所採取的動作，指定表單允許的 URL 目標。</span><span class="sxs-lookup"><span data-stu-id="37c15-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="37c15-109">可以載入的外掛程式。</span><span class="sxs-lookup"><span data-stu-id="37c15-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="37c15-110">若要將 CSP 套用至應用程式，開發人員會在一或多個標頭或標記中指定數個 CSP *內容安全性指示* 詞 `Content-Security-Policy` `<meta>` 。</span><span class="sxs-lookup"><span data-stu-id="37c15-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="37c15-111">當頁面載入時，瀏覽器會評估原則。</span><span class="sxs-lookup"><span data-stu-id="37c15-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="37c15-112">瀏覽器會檢查頁面的來源，並判斷它們是否符合內容安全性指示詞的需求。</span><span class="sxs-lookup"><span data-stu-id="37c15-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="37c15-113">當資源的原則指示詞不符合時，瀏覽器不會載入資源。</span><span class="sxs-lookup"><span data-stu-id="37c15-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="37c15-114">例如，請考慮不允許協力廠商腳本的原則。</span><span class="sxs-lookup"><span data-stu-id="37c15-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="37c15-115">當頁面包含 `<script>` 屬性中有協力廠商來源的標記時 `src` ，瀏覽器會防止載入腳本。</span><span class="sxs-lookup"><span data-stu-id="37c15-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="37c15-116">大部分新式桌面和行動瀏覽器都支援 CSP，包括 Chrome、Edge、Firefox、Opera 和 Safari。</span><span class="sxs-lookup"><span data-stu-id="37c15-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="37c15-117">建議使用 CSP 作為 :::no-loc(Blazor)::: 應用程式。</span><span class="sxs-lookup"><span data-stu-id="37c15-117">CSP is recommended for :::no-loc(Blazor)::: apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="37c15-118">原則指示詞</span><span class="sxs-lookup"><span data-stu-id="37c15-118">Policy directives</span></span>

<span data-ttu-id="37c15-119">至少指定下列指示詞和 :::no-loc(Blazor)::: 應用程式的來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-119">Minimally, specify the following directives and sources for :::no-loc(Blazor)::: apps.</span></span> <span data-ttu-id="37c15-120">視需要新增其他指示詞和來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="37c15-121">下列指示詞適用于本文的「套用 [原則](#apply-the-policy) 」一節，其中提供和的範例安全 :::no-loc(Blazor WebAssembly)::: 策略 :::no-loc(Blazor Server)::: ：</span><span class="sxs-lookup"><span data-stu-id="37c15-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server)::: are provided:</span></span>

* <span data-ttu-id="37c15-122">[基底 uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri)：限制頁面標記的 url `<base>` 。</span><span class="sxs-lookup"><span data-stu-id="37c15-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="37c15-123">指定 `self` 以表示應用程式的來源（包括配置和埠號碼）是有效的來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="37c15-124">[全部封鎖-混合-內容](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content)：防止載入混合的 HTTP 和 HTTPS 內容。</span><span class="sxs-lookup"><span data-stu-id="37c15-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="37c15-125">[預設值-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src)：表示原則未明確指定的來源指示詞的回復。</span><span class="sxs-lookup"><span data-stu-id="37c15-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="37c15-126">指定 `self` 以表示應用程式的來源（包括配置和埠號碼）是有效的來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="37c15-127">[img： src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src)：表示影像的有效來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="37c15-128">指定 `data:` 允許從 url 載入影像 `data:` 。</span><span class="sxs-lookup"><span data-stu-id="37c15-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="37c15-129">指定 `https:` 允許從 HTTPS 端點載入映射。</span><span class="sxs-lookup"><span data-stu-id="37c15-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="37c15-130">[物件-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src)：指出 `<object>` 、 `<embed>` 和標記的有效來源 `<applet>` 。</span><span class="sxs-lookup"><span data-stu-id="37c15-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="37c15-131">指定 `none` 以防止所有 URL 來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="37c15-132">[腳本-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)：表示腳本的有效來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="37c15-133">指定 `https://stackpath.bootstrapcdn.com/` 啟動程式腳本的主機來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="37c15-134">指定 `self` 以表示應用程式的來源（包括配置和埠號碼）是有效的來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="37c15-135">在 :::no-loc(Blazor WebAssembly)::: 應用程式中：</span><span class="sxs-lookup"><span data-stu-id="37c15-135">In a :::no-loc(Blazor WebAssembly)::: app:</span></span>
    * <span data-ttu-id="37c15-136">指定下列雜湊以允許載入必要的 :::no-loc(Blazor WebAssembly)::: 內嵌腳本：</span><span class="sxs-lookup"><span data-stu-id="37c15-136">Specify the following hashes to permit the required :::no-loc(Blazor WebAssembly)::: inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="37c15-137">指定 `unsafe-eval` 要使用 `eval()` 的和方法，以從字串建立程式碼。</span><span class="sxs-lookup"><span data-stu-id="37c15-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="37c15-138">在 :::no-loc(Blazor Server)::: 應用程式中，指定 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` 執行樣式表單之回溯偵測的內嵌腳本雜湊。</span><span class="sxs-lookup"><span data-stu-id="37c15-138">In a :::no-loc(Blazor Server)::: app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="37c15-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src)：表示樣式表單的有效來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="37c15-140">指定啟動載入器樣式表單的 `https://stackpath.bootstrapcdn.com/` 主機來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="37c15-141">指定 `self` 以表示應用程式的來源（包括配置和埠號碼）是有效的來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="37c15-142">指定 `unsafe-inline` 以允許使用內嵌樣式。</span><span class="sxs-lookup"><span data-stu-id="37c15-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="37c15-143">應用程式中的 UI 需要內嵌宣告，才能在 :::no-loc(Blazor Server)::: 初始要求之後重新連接用戶端和伺服器。</span><span class="sxs-lookup"><span data-stu-id="37c15-143">The inline declaration is required for the UI in :::no-loc(Blazor Server)::: apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="37c15-144">在未來的版本中，可能會移除內嵌樣式，因此 `unsafe-inline` 不再需要。</span><span class="sxs-lookup"><span data-stu-id="37c15-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="37c15-145">[升級-不安全-要求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests)：指出來自不安全 (HTTP) 來源的內容 url 應該透過 HTTPS 安全取得。</span><span class="sxs-lookup"><span data-stu-id="37c15-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="37c15-146">除了 Microsoft Internet Explorer 之外，所有瀏覽器都支援上述指示詞。</span><span class="sxs-lookup"><span data-stu-id="37c15-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="37c15-147">若要取得其他內嵌腳本的 SHA 雜湊：</span><span class="sxs-lookup"><span data-stu-id="37c15-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="37c15-148">套用 [套用 [原則](#apply-the-policy) ] 區段中顯示的 CSP。</span><span class="sxs-lookup"><span data-stu-id="37c15-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="37c15-149">在本機執行應用程式時，存取瀏覽器的開發人員工具主控台。</span><span class="sxs-lookup"><span data-stu-id="37c15-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="37c15-150">當 CSP 標頭或標記存在時，瀏覽器會計算並顯示已封鎖腳本的雜湊 `meta` 。</span><span class="sxs-lookup"><span data-stu-id="37c15-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="37c15-151">將瀏覽器提供的雜湊複製到 `script-src` 來源。</span><span class="sxs-lookup"><span data-stu-id="37c15-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="37c15-152">使用單引號括住每個雜湊。</span><span class="sxs-lookup"><span data-stu-id="37c15-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="37c15-153">如需內容安全性原則層級2瀏覽器支援矩陣，請參閱 [我可以使用：內容安全性原則層級 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)。</span><span class="sxs-lookup"><span data-stu-id="37c15-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="37c15-154">套用原則</span><span class="sxs-lookup"><span data-stu-id="37c15-154">Apply the policy</span></span>

<span data-ttu-id="37c15-155">使用 `<meta>` 標記套用原則：</span><span class="sxs-lookup"><span data-stu-id="37c15-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="37c15-156">將屬性的值設定 `http-equiv` 為 `Content-Security-Policy` 。</span><span class="sxs-lookup"><span data-stu-id="37c15-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="37c15-157">將指示詞放在 `content` 屬性值中。</span><span class="sxs-lookup"><span data-stu-id="37c15-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="37c15-158">以分號 (分隔指示詞 `;`) 。</span><span class="sxs-lookup"><span data-stu-id="37c15-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="37c15-159">一律將 `meta` 標記放在 `<head>` 內容中。</span><span class="sxs-lookup"><span data-stu-id="37c15-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="37c15-160">下列各節顯示和的範例 :::no-loc(Blazor WebAssembly)::: 原則 :::no-loc(Blazor Server)::: 。</span><span class="sxs-lookup"><span data-stu-id="37c15-160">The following sections show example policies for :::no-loc(Blazor WebAssembly)::: and :::no-loc(Blazor Server):::.</span></span> <span data-ttu-id="37c15-161">這些範例會針對的每個版本，使用此文章來建立版本 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="37c15-161">These examples are versioned with this article for each release of :::no-loc(Blazor):::.</span></span> <span data-ttu-id="37c15-162">若要使用適用于您的版本的版本，請選取此網頁上具有 [ **版本** ] 下拉式清單選取器的檔版本。</span><span class="sxs-lookup"><span data-stu-id="37c15-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="37c15-163">在 [主機] 頁面的 [內容] 中，套用 [原則指示詞] `<head>` `wwwroot/index.html` 區段中所述的指示詞： [Policy directives](#policy-directives)</span><span class="sxs-lookup"><span data-stu-id="37c15-163">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### :::no-loc(Blazor Server):::

<span data-ttu-id="37c15-164">在 [主機] 頁面的 [內容] 中，套用 [原則指示詞] `<head>` `Pages/_Host.cshtml` 區段中所述的指示詞： [Policy directives](#policy-directives)</span><span class="sxs-lookup"><span data-stu-id="37c15-164">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a><span data-ttu-id="37c15-165">中繼標記限制</span><span class="sxs-lookup"><span data-stu-id="37c15-165">Meta tag limitations</span></span>

<span data-ttu-id="37c15-166">`<meta>`標記原則不支援下列指示詞：</span><span class="sxs-lookup"><span data-stu-id="37c15-166">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="37c15-167">框架-祖系</span><span class="sxs-lookup"><span data-stu-id="37c15-167">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="37c15-168">報表至</span><span class="sxs-lookup"><span data-stu-id="37c15-168">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="37c15-169">報告-uri</span><span class="sxs-lookup"><span data-stu-id="37c15-169">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="37c15-170">沙 箱</span><span class="sxs-lookup"><span data-stu-id="37c15-170">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="37c15-171">若要支援上述指示詞，請使用名為的標頭 `Content-Security-Policy` 。</span><span class="sxs-lookup"><span data-stu-id="37c15-171">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="37c15-172">指示詞字串是標頭的值。</span><span class="sxs-lookup"><span data-stu-id="37c15-172">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="37c15-173">測試原則並接收違規報告</span><span class="sxs-lookup"><span data-stu-id="37c15-173">Test a policy and receive violation reports</span></span>

<span data-ttu-id="37c15-174">測試有助於確認在建立初始原則時，不會不慎封鎖協力廠商腳本。</span><span class="sxs-lookup"><span data-stu-id="37c15-174">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="37c15-175">若要在一段時間內測試原則，而不強制執行原則指示詞，請將 `<meta>` `http-equiv` 標頭型原則的標記屬性或標頭名稱設定為 `Content-Security-Policy-Report-Only` 。</span><span class="sxs-lookup"><span data-stu-id="37c15-175">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="37c15-176">失敗報告會以 JSON 檔的形式傳送至指定的 URL。</span><span class="sxs-lookup"><span data-stu-id="37c15-176">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="37c15-177">如需詳細資訊，請參閱 [MDN web 檔：內容-安全性-原則-僅報告](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)。</span><span class="sxs-lookup"><span data-stu-id="37c15-177">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="37c15-178">若要在原則啟用時報告違規，請參閱下列文章：</span><span class="sxs-lookup"><span data-stu-id="37c15-178">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="37c15-179">報表至</span><span class="sxs-lookup"><span data-stu-id="37c15-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="37c15-180">報告-uri</span><span class="sxs-lookup"><span data-stu-id="37c15-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="37c15-181">雖然不再 `report-uri` 建議使用，但 `report-to` 在所有主要瀏覽器都支援之前，都應該使用這兩個指示詞。</span><span class="sxs-lookup"><span data-stu-id="37c15-181">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="37c15-182">請勿單獨使用 `report-uri` ，因為的支援隨時 `report-uri` 都可從 *at any time* 瀏覽器卸載。</span><span class="sxs-lookup"><span data-stu-id="37c15-182">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="37c15-183">`report-uri`當完全受支援時，請移除原則中的支援 `report-to` 。</span><span class="sxs-lookup"><span data-stu-id="37c15-183">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="37c15-184">若要追蹤的採用 `report-to` ，請參閱 [我可以使用： report to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)。</span><span class="sxs-lookup"><span data-stu-id="37c15-184">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="37c15-185">每次發行時測試並更新應用程式的原則。</span><span class="sxs-lookup"><span data-stu-id="37c15-185">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="37c15-186">疑難排解</span><span class="sxs-lookup"><span data-stu-id="37c15-186">Troubleshoot</span></span>

* <span data-ttu-id="37c15-187">錯誤會出現在瀏覽器的開發人員工具主控台中。</span><span class="sxs-lookup"><span data-stu-id="37c15-187">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="37c15-188">瀏覽器提供下列資訊：</span><span class="sxs-lookup"><span data-stu-id="37c15-188">Browsers provide information about:</span></span>
  * <span data-ttu-id="37c15-189">不符合原則的元素。</span><span class="sxs-lookup"><span data-stu-id="37c15-189">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="37c15-190">如何修改原則以允許封鎖的專案。</span><span class="sxs-lookup"><span data-stu-id="37c15-190">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="37c15-191">只有當用戶端的瀏覽器支援所有包含的指示詞時，原則才會完全有效。</span><span class="sxs-lookup"><span data-stu-id="37c15-191">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="37c15-192">如需目前的瀏覽器支援矩陣，請參閱 [我可以使用：內容安全性原則](https://caniuse.com/#search=Content-Security-Policy)。</span><span class="sxs-lookup"><span data-stu-id="37c15-192">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="37c15-193">其他資源</span><span class="sxs-lookup"><span data-stu-id="37c15-193">Additional resources</span></span>

* [<span data-ttu-id="37c15-194">MDN web 檔：內容-安全性-原則</span><span class="sxs-lookup"><span data-stu-id="37c15-194">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="37c15-195">內容安全性原則層級2</span><span class="sxs-lookup"><span data-stu-id="37c15-195">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="37c15-196">Google CSP 評估工具</span><span class="sxs-lookup"><span data-stu-id="37c15-196">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
