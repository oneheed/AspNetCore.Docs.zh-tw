---
title: 針對 ASP.NET Core 強制執行內容安全性原則 Blazor
author: guardrex
description: 瞭解如何搭配 ASP.NET Core apps 使用 (CSP) 的內容安全性原則 Blazor ，以協助防範跨網站腳本 (XSS) 攻擊。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/content-security-policy
ms.openlocfilehash: 744449240fabc3dae317d0d7bc9090311521c224
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94570116"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-no-locblazor"></a>針對 ASP.NET Core 強制執行內容安全性原則 Blazor

由 [Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

[跨網站腳本 (XSS) ](xref:security/cross-site-scripting) 是一種安全性弱點，攻擊者會將一或多個惡意的用戶端腳本放入應用程式的轉譯內容中。  (CSP) 的內容安全性原則，可通知瀏覽器有效，以防止 XSS 攻擊：

* 載入之內容的來源，包括腳本、樣式表單和影像。
* 頁面所採取的動作，指定表單允許的 URL 目標。
* 可以載入的外掛程式。

若要將 CSP 套用至應用程式，開發人員會在一或多個標頭或標記中指定數個 CSP *內容安全性指示* 詞 `Content-Security-Policy` `<meta>` 。

當頁面載入時，瀏覽器會評估原則。 瀏覽器會檢查頁面的來源，並判斷它們是否符合內容安全性指示詞的需求。 當資源的原則指示詞不符合時，瀏覽器不會載入資源。 例如，請考慮不允許協力廠商腳本的原則。 當頁面包含 `<script>` 屬性中有協力廠商來源的標記時 `src` ，瀏覽器會防止載入腳本。

大部分新式桌面和行動瀏覽器都支援 CSP，包括 Chrome、Edge、Firefox、Opera 和 Safari。 建議使用 CSP 作為 Blazor 應用程式。

## <a name="policy-directives"></a>原則指示詞

至少指定下列指示詞和 Blazor 應用程式的來源。 視需要新增其他指示詞和來源。 下列指示詞適用于本文的「套用 [原則](#apply-the-policy) 」一節，其中提供和的範例安全 Blazor WebAssembly 策略 Blazor Server ：

* [基底 uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri)：限制頁面標記的 url `<base>` 。 指定 `self` 以表示應用程式的來源（包括配置和埠號碼）是有效的來源。
* [全部封鎖-混合-內容](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content)：防止載入混合的 HTTP 和 HTTPS 內容。
* [預設值-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src)：表示原則未明確指定的來源指示詞的回復。 指定 `self` 以表示應用程式的來源（包括配置和埠號碼）是有效的來源。
* [img： src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src)：表示影像的有效來源。
  * 指定 `data:` 允許從 url 載入影像 `data:` 。
  * 指定 `https:` 允許從 HTTPS 端點載入映射。
* [物件-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src)：指出 `<object>` 、 `<embed>` 和標記的有效來源 `<applet>` 。 指定 `none` 以防止所有 URL 來源。
* [腳本-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)：表示腳本的有效來源。
  * 指定 `https://stackpath.bootstrapcdn.com/` 啟動程式腳本的主機來源。
  * 指定 `self` 以表示應用程式的來源（包括配置和埠號碼）是有效的來源。
  * 在 Blazor WebAssembly 應用程式中：
    * 指定雜湊以允許載入必要的腳本。
    * 指定 `unsafe-eval` 要使用 `eval()` 的和方法，以從字串建立程式碼。
  * 在 Blazor Server 應用程式中，指定雜湊以允許載入必要的腳本。
* [style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src)：表示樣式表單的有效來源。
  * 指定啟動載入器樣式表單的 `https://stackpath.bootstrapcdn.com/` 主機來源。
  * 指定 `self` 以表示應用程式的來源（包括配置和埠號碼）是有效的來源。
  * 指定 `unsafe-inline` 以允許使用內嵌樣式。 應用程式中的 UI 需要內嵌宣告，才能在 Blazor Server 初始要求之後重新連接用戶端和伺服器。 在未來的版本中，可能會移除內嵌樣式，因此 `unsafe-inline` 不再需要。
* [升級-不安全-要求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests)：指出來自不安全 (HTTP) 來源的內容 url 應該透過 HTTPS 安全取得。

除了 Microsoft Internet Explorer 之外，所有瀏覽器都支援上述指示詞。

若要取得其他內嵌腳本的 SHA 雜湊：

* 套用 [套用 [原則](#apply-the-policy) ] 區段中顯示的 CSP。
* 在本機執行應用程式時，存取瀏覽器的開發人員工具主控台。 當 CSP 標頭或標記存在時，瀏覽器會計算並顯示已封鎖腳本的雜湊 `meta` 。
* 將瀏覽器提供的雜湊複製到 `script-src` 來源。 使用單引號括住每個雜湊。

如需內容安全性原則層級2瀏覽器支援矩陣，請參閱 [我可以使用：內容安全性原則層級 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)。

## <a name="apply-the-policy"></a>套用原則

使用 `<meta>` 標記套用原則：

* 將屬性的值設定 `http-equiv` 為 `Content-Security-Policy` 。
* 將指示詞放在 `content` 屬性值中。 以分號 (分隔指示詞 `;`) 。
* 一律將 `meta` 標記放在 `<head>` 內容中。

下列各節顯示和的範例 Blazor WebAssembly 原則 Blazor Server 。 這些範例會針對的每個版本，使用此文章來建立版本 Blazor 。 若要使用適用于您的版本的版本，請選取此網頁上具有 [ **版本** ] 下拉式清單選取器的檔版本。

### Blazor WebAssembly

在 [主機] 頁面的 [內容] 中，套用 [原則指示詞] `<head>` `wwwroot/index.html` 區段中所述的指示詞： [](#policy-directives)

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

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

::: moniker-end

新增 `script-src` `style-src` 應用程式所需的其他和雜湊。 在開發期間，使用線上工具或瀏覽器開發人員工具來為您計算雜湊。 例如，下列瀏覽器工具主控台錯誤會報告原則未涵蓋之必要腳本的雜湊：

> 拒絕執行內嵌腳本，因為它違反下列內容安全性原則指示詞： ".。。". 若要啟用內嵌執行，必須使用 ' unsafe-inline ' 關鍵字、雜湊 ( ' sha256 v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA = ' ) 或 nonce ( ' nonce-... ' ) 。

與錯誤相關聯的特定腳本會顯示在主控台的錯誤旁。

### Blazor Server

在 [主機] 頁面的 [內容] 中，套用 [原則指示詞] `<head>` `Pages/_Host.cshtml` 區段中所述的指示詞： [](#policy-directives)

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

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

::: moniker-end

新增 `script-src` `style-src` 應用程式所需的其他和雜湊。 在開發期間，使用線上工具或瀏覽器開發人員工具來為您計算雜湊。 例如，下列瀏覽器工具主控台錯誤會報告原則未涵蓋之必要腳本的雜湊：

> 拒絕執行內嵌腳本，因為它違反下列內容安全性原則指示詞： ".。。". 若要啟用內嵌執行，必須使用 ' unsafe-inline ' 關鍵字、雜湊 ( ' sha256 v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA = ' ) 或 nonce ( ' nonce-... ' ) 。

與錯誤相關聯的特定腳本會顯示在主控台的錯誤旁。

## <a name="meta-tag-limitations"></a>中繼標記限制

`<meta>`標記原則不支援下列指示詞：

* [框架-祖系](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [報表至](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [報告-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [沙 箱](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

若要支援上述指示詞，請使用名為的標頭 `Content-Security-Policy` 。 指示詞字串是標頭的值。

## <a name="test-a-policy-and-receive-violation-reports"></a>測試原則並接收違規報告

測試有助於確認在建立初始原則時，不會不慎封鎖協力廠商腳本。

若要在一段時間內測試原則，而不強制執行原則指示詞，請將 `<meta>` `http-equiv` 標頭型原則的標記屬性或標頭名稱設定為 `Content-Security-Policy-Report-Only` 。 失敗報告會以 JSON 檔的形式傳送至指定的 URL。 如需詳細資訊，請參閱 [MDN web 檔：內容-安全性-原則-僅報告](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)。

若要在原則啟用時報告違規，請參閱下列文章：

* [報表至](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [報告-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

雖然不再 `report-uri` 建議使用，但 `report-to` 在所有主要瀏覽器都支援之前，都應該使用這兩個指示詞。 請勿單獨使用 `report-uri` ，因為的支援隨時 `report-uri` 都可從瀏覽器卸載。 `report-uri`當完全受支援時，請移除原則中的支援 `report-to` 。 若要追蹤的採用 `report-to` ，請參閱 [我可以使用： report to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)。

每次發行時測試並更新應用程式的原則。

## <a name="troubleshoot"></a>疑難排解

* 錯誤會出現在瀏覽器的開發人員工具主控台中。 瀏覽器提供下列資訊：
  * 不符合原則的元素。
  * 如何修改原則以允許封鎖的專案。
* 只有當用戶端的瀏覽器支援所有包含的指示詞時，原則才會完全有效。 如需目前的瀏覽器支援矩陣，請參閱 [我可以使用：內容安全性原則](https://caniuse.com/#search=Content-Security-Policy)。

## <a name="additional-resources"></a>其他資源

* [MDN web 檔：內容-安全性-原則](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [內容安全性原則層級2](https://www.w3.org/TR/CSP2/)
* [Google CSP 評估工具](https://csp-evaluator.withgoogle.com/)
