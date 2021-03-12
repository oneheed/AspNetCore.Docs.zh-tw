---
title: ASP.NET Core 中的會話
author: rick-anderson
description: 探索在要求之間保留會話的方法。
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
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
uid: fundamentals/app-state
ms.openlocfilehash: ab09867a1b1aa73e423babbe295036e292fc733f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586575"
---
# <a name="session-and-state-management-in-aspnet-core"></a>ASP.NET Core 中的工作階段和狀態管理 (機器翻譯)

::: moniker range=">= aspnetcore-3.0"

由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Kirk Larkin](https://twitter.com/serpent5)和 [Diana LaRose](https://github.com/DianaLaRose)

HTTP 是無狀態的通訊協定。 根據預設，HTTP 要求是獨立的訊息，不會保留使用者的值。 本文說明在要求之間保留使用者資料的數種方法。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/app-state/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="state-management"></a>狀態管理

可以使用數種方法來儲存狀態。 本主題稍後將提供每種方法的描述。

| 儲存方法 | 儲存機制 |
| ---------------- | ----------------- |
| [Cookie！](#cookies) | HTTP cookie s。 可能包括使用伺服器端應用程式程式碼儲存的資料。 |
| [會話狀態](#session-state) | HTTP cookie s 和伺服器端應用程式程式碼 |
| [TempData](#tempdata) | HTTP cookie s 或會話狀態 |
| [查詢字串](#query-strings) | HTTP 查詢字串 |
| [隱藏欄位](#hidden-fields) | HTTP 表單欄位 |
| [HttpContext.Items](#httpcontextitems) | 伺服器端應用程式程式碼 |
| [Cache](#cache) | 伺服器端應用程式程式碼 |

## <a name="cookies"></a>Cookie

Cookie會跨要求儲存資料。 因為 cookie 會隨每個要求一起傳送，所以其大小應保持最小值。 在理想情況下，只有識別碼應該儲存在中， cookie 並包含應用程式所儲存的資料。 大部分的瀏覽器會 cookie 將大小限制為4096個位元組。 cookie每個網域只能使用有限數目的。

因為 cookie 會受到篡改的制約，所以必須由應用程式進行驗證。 Cookie可以由使用者刪除，並在用戶端上過期。 不過， cookie 通常是用戶端上最持久的資料持續性形式。

Cookie通常用於個人化，其中內容是針對已知使用者自訂的。 在大部分情況下，只會識別使用者，而未加以驗證。 cookie可以儲存使用者的名稱、帳戶名稱或唯一使用者識別碼（例如 GUID）。 cookie可以用來存取使用者的個人化設定，例如其慣用的網站背景色彩。

請參閱 [歐盟一般資料保護法規 (GDPR) ](https://ec.europa.eu/info/law/law-topic/data-protection) 發出 cookie 和處理隱私權問題。 如需詳細資訊，請參閱 [ASP.NET Core 中的一般資料保護規定 (GDPR) 支援](xref:security/gdpr)。

## <a name="session-state"></a>工作階段狀態

工作階段狀態是用來在使用者瀏覽 Web 應用程式時存放使用者資料的 ASP.NET Core 情節。 工作階段狀態使用應用程式所維護的存放區，在用戶端的要求之間保存資料。 會話資料是由快取所支援，並被視為暫時資料。 網站應該會在沒有會話資料的情況下繼續運作。 重要應用程式資料應該儲存在使用者資料庫，並只在工作階段中快取以獲得效能最佳化。

應用程式不支援會話， [SignalR](xref:signalr/index) 因為[ SignalR 中樞](xref:signalr/hubs)可以獨立于 HTTP 內容執行。 例如，當長時間輪詢要求由中樞維持開啟，超過要求的 HTTP 內容存留期時，便可能發生此情況。

ASP.NET Core 會藉由提供 cookie 給包含會話識別碼的用戶端來維護會話狀態。 cookie會話識別碼：

* 會隨每個要求傳送至應用程式。
* 應用程式會使用來提取會話資料。

工作階段狀態表現下列行為：

* cookie這是瀏覽器專用的會話。 不會跨瀏覽器共用會話。
* cookie當瀏覽器會話結束時，會刪除會話 s。
* 如果 cookie 收到過期會話的，則會建立使用相同會話的新會話 cookie 。
* 不會保留空的會話。 會話至少必須設定一個值，才能跨要求保存會話。 不保留工作階段時，會為每個新的要求產生新的工作階段識別碼。
* 應用程式會在最後一個要求之後，保留工作階段一段有限的時間。 應用程式會設定工作階段逾時或使用預設值 20 分鐘。 會話狀態適合用來儲存使用者資料：
  * 這是特定會話特有的。
  * 資料不需要在會話之間永久儲存。
* 當 <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> 呼叫執行時或會話過期時，就會刪除會話資料。
* 沒有任何預設機制可通知應用程式程式碼用戶端瀏覽器已關閉，或是在 cookie 用戶端上的會話遭到刪除或過期。
* 依預設，會話狀態 cookie s 未標示為「基本」。 除非網站訪客允許追蹤，否則會話狀態無法運作。 如需詳細資訊，請參閱<xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>。

> [!WARNING]
> 請勿將敏感性資料存放在工作階段狀態。 使用者可能不會關閉瀏覽器並清除會話 cookie 。 某些流覽 cookie 器會跨瀏覽器視窗維護有效的會話。 會話可能無法限制為單一使用者。 下一使用者可能會繼續使用相同的會話流覽應用程式 cookie 。

記憶體中快取提供者會將工作階段資料存放在應用程式所在伺服器的記憶體中。 在伺服器陣列案例中：

* 使用「黏性工作階段」將每個工作階段繫結至個別伺服器上的特定應用程式執行個體。 [Azure App Service](https://azure.microsoft.com/services/app-service/) 預設會使用[應用程式要求路由 (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 來強制執行自黏工作階段。 不過，黏性工作階段可能會影響延展性，並使 Web 應用程式更新複雜化。 較好的方法是使用 Redis 或 SQL Server 分散式快取，這不需要黏性工作階段。 如需詳細資訊，請參閱<xref:performance/caching/distributed>。
* 此會話 cookie 會透過進行加密 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 。 必須正確設定資料保護，才能讀取每部 cookie 電腦上的會話。 如需詳細資訊，請參閱 <xref:security/data-protection/introduction>與[金鑰儲存提供者](xref:security/data-protection/implementation/key-storage-providers)。

### <a name="configure-session-state"></a>設定工作階段狀態

[AspNetCore 會話](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/)封裝：

* 由架構隱含包含。
* 提供用來管理會話狀態的中介軟體。

若要啟用工作階段中介軟體，`Startup` 必須包含：

* 任何記憶體快取 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。 `IDistributedCache` 實作會作為工作階段的支援存放區。 如需詳細資訊，請參閱<xref:performance/caching/distributed>。
* <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A>在中的呼叫 `ConfigureServices` 。
* <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>在中的呼叫 `Configure` 。

下列程式碼示範如何設定記憶體內部工作階段提供者，以及 `IDistributedCache` 的預設記憶體中實作。

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

上述程式碼會設定短暫的時間，以簡化測試。

中介軟體的順序很重要。  `UseSession`在前後呼叫 `UseRouting` `UseEndpoints` 。 請參閱 [中介軟體順序](xref:fundamentals/middleware/index#order)。

設定工作階段狀態之後便可以使用 [HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session)。

呼叫 `UseSession` 之前無法存取 `HttpContext.Session`。

cookie當應用程式開始寫入至回應資料流程之後，就無法建立新會話的新會話。 例外狀況會記錄在 Web 伺服器記錄檔中，而不會顯示在瀏覽器。

### <a name="load-session-state-asynchronously"></a>非同步載入工作階段狀態

ASP.NET Core 中的預設會話提供者 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，只有在 <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> 、或方法上明確呼叫方法時，才會以非同步方式從基礎備份存放區載入會話記錄 <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> 。 如果並未先呼叫 `LoadAsync`，則基礎工作階段記錄會同步載入，這可能大規模地為效能帶來負面影響。

若要讓應用程式強制執行此模式，請 <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> 使用擲回例外狀況的版本包裝和程式， <xref:Microsoft.AspNetCore.Session.DistributedSession> 如果在 `LoadAsync` 、或之前未呼叫方法，則會擲回例外狀況 `TryGetValue` `Set` `Remove` 。 請在服務容器中註冊已包裝的版本。

### <a name="session-options"></a>工作階段選項

若要覆寫會話的預設值，請使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions> 。

| 選項 | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | 決定用來建立的設定 cookie 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> 預設為 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`) 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> 預設為 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`) 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> 預設為 <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`) 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 預設為 `true` 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 預設為 `false` 。 |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` 指出工作階段可以閒置多久，之後才會放棄它的內容。 每個工作階段存取都會重設逾時。 這項設定只適用于會話的內容，而不會套用至 cookie 。 預設值是 20 分鐘。 |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | 從存放區載入工作階段，或將它認可回到存放區時，所允許的時間長度上限。 此設定只可能適用於非同步作業。 您可以使用來停用這個超時時間 <xref:System.Threading.Timeout.InfiniteTimeSpan> 。 預設值是 1 分鐘。 |

會話會使用 cookie 從單一瀏覽器追蹤和識別要求。 根據預設，這 cookie 會命名為 `.AspNetCore.Session` ，並使用的路徑 `/` 。 由於 cookie 預設值未指定網域，因此它不會提供給頁面上的用戶端腳本 (因為 <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 預設為 `true`) 。

若要覆寫 cookie 會話的預設值，請使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions> ：

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

應用程式會使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> 屬性來判斷會話可以閒置多久之後，才能放棄其在伺服器快取中的內容。 這個屬性與 cookie 到期無關。 透過[工作階段中介軟體](xref:Microsoft.AspNetCore.Session.SessionMiddleware)傳遞的每個要求都會重設逾時。

工作階段狀態為「非鎖定」。 如果兩個要求同時嘗試修改工作階段的內容，則最後一個要求會覆寫第一個要求。 `Session` 會實作為「一致性工作階段」，這表示所有內容會都儲存在一起。 當兩個要求試圖修改不同的工作階段值時，最後一個要求可能會覆寫第一個要求所做的工作階段變更。

### <a name="set-and-get-session-values"></a>設定和取得工作階段值

您可以 Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 使用頁面類別或 MVC 類別來存取會話狀態 <xref:Microsoft.AspNetCore.Mvc.Controller> <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> 。 這個屬性是 <xref:Microsoft.AspNetCore.Http.ISession> 實作為。

`ISession` 實作提供數個延伸模組來設定和擷取整數和字串值。 擴充方法位於 <xref:Microsoft.AspNetCore.Http> 命名空間中。

`ISession` 擴充方法：

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

下列範例會在 `IndexModel.SessionKeyName` `_Name` 頁面頁面) 的範例應用程式中，取得金鑰 (的會話值 Razor ：

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

下列範例示範如何設定及取得整數和字串：

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

若要啟用分散式快取案例，即使是使用記憶體中快取時，都必須序列化所有工作階段資料。 字串和整數序列化程式是由的擴充方法所提供 <xref:Microsoft.AspNetCore.Http.ISession> 。 複雜類型必須由使用者使用另一個機制加以序列化，例如 JSON。

使用下列範例程式碼來序列化物件：

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

下列範例示範如何使用類別來設定和取得可序列化物件 `SessionExtensions` ：

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core 會公開 Razor [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) 或控制器頁面 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> 。 這個屬性會儲存資料，直到讀取另一個要求為止。 [保留 (字串) ](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*)和[查看 (字串) ](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*)方法可以用來檢查資料，而不需要在要求結束時刪除。 [繼續](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 將字典中的所有專案標示為保留。 `TempData` 是：

* 適用于需要多個單一要求的資料時重新導向。
* 由 `TempData` 使用或會話狀態的提供者所執行 cookie 。

## <a name="tempdata-samples"></a>TempData 範例

請考慮下列建立客戶的頁面：

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

下列頁面會顯示 `TempData["Message"]` ：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

在上述標記中，在要求結束 `TempData["Message"]` 時， **不** 會刪除，因為 `Peek` 會使用。 重新整理頁面會顯示的內容 `TempData["Message"]` 。

下列標記類似于上述程式碼，但會使用在 `Keep` 要求結束時保留資料：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

在 *IndexPeek* 和 *IndexKeep* 頁面之間流覽並不會刪除 `TempData["Message"]` 。

下列 `TempData["Message"]` 程式碼會顯示，但在要求結束 `TempData["Message"]` 時，會刪除：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData 提供者

cookie依預設，會使用以 TempData 提供者將 TempData 儲存在 cookie s 中。

cookie資料會使用加密 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> ，然後使用來進行 <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> 區塊編碼。 cookie由於加密和區塊處理，大小上限小於[4096 個位元組](http://www.faqs.org/rfcs/rfc2965.html)。 cookie資料不會壓縮，因為壓縮加密資料可能會導致安全性問題，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[缺口](https://wikipedia.org/wiki/BREACH_(security_exploit))攻擊。 如需有關以 cookie TempData 提供者為基礎的詳細資訊，請參閱 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> 。

### <a name="choose-a-tempdata-provider"></a>選擇 TempData 提供者

選擇 TempData 提供者涉及數項考量，例如：

* 應用程式已經使用工作階段狀態了嗎？ 若是如此，使用會話狀態 TempData 提供者不會對超出資料大小的應用程式產生額外的成本。
* 應用程式是否只針對相對較少量的資料（最多500個位元組）使用 TempData？ 如果是， cookie TempData 提供者會為每個攜帶 TempData 的要求增加一個小成本。 如果不是的話，則工作階段狀態 TempData 提供者可能有助於避免在每個要求中來回傳送大量資料，直到取用 TempData 為止。
* 應用程式在伺服器陣列中的多部伺服器上執行？ 如果是，則不需要額外的設定，就能在 cookie 資料保護之外使用 TempData 提供者 (請參閱 <xref:security/data-protection/introduction> 和 [金鑰儲存提供者](xref:security/data-protection/implementation/key-storage-providers)) 。

大部分的 web 用戶端（例如網頁瀏覽器）都會強制執行每個大小上限 cookie 和總數的限制 cookie 。 使用 cookie TempData 提供者時，請確認應用程式不會超過 [這些限制](http://www.faqs.org/rfcs/rfc2965.html)。 請考慮資料的大小總計。 cookie由於加密和區塊處理而增加的大小。

### <a name="configure-the-tempdata-provider"></a>設定 TempData 提供者

cookie預設會啟用以 TempData 提供者為基礎的提供者。

若要啟用以會話為基礎的 TempData 提供者，請使用 <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> 擴充方法。 只需要一個呼叫 `AddSessionStateTempDataProvider` ：

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>查詢字串

可以將數量有限的資料從某個要求傳遞到另一個要求，方法是將其新增至新要求的查詢字串。 這對於以持續方式擷取狀態很有用，可讓內嵌狀態的連結透過電子郵件或社交網路共用。 因為 URL 查詢字串為公用，所以請絕對不要使用查詢字串來處理敏感性資料。

除了非預期的共用之外，在查詢字串中包含資料，也可以將應用程式公開給 [跨網站偽造要求 (CSRF) ](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 攻擊。 任何保留的會話狀態都必須防範 CSRF 攻擊。 如需詳細資訊，請參閱<xref:security/anti-request-forgery>。

## <a name="hidden-fields"></a>隱藏欄位

資料可以儲存在隱藏的表單欄位，並貼回下一個要求。 這在多頁表單中很常見。 因為用戶端可能會竄改資料，所以應用程式必須一律重新驗證存放在隱藏欄位中的資料。

## <a name="httpcontextitems"></a>HttpContext.Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType>集合是用來在處理單一要求時儲存資料。 集合的內容會在每個要求處理之後捨棄。 當元件或中介軟體在要求期間的不同時間點運作，而且沒有可傳遞參數的直接方式時，`Items` 集合經常用來允許元件或中介軟體進行通訊。

在下列範例中， [中介軟體](xref:fundamentals/middleware/index) 會新增 `isVerified` 至 `Items` 集合：

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

針對只在單一應用程式中使用的中介軟體， `string` 可以接受固定的金鑰。 應用程式之間共用的中介軟體應該使用唯一的物件金鑰來避免金鑰衝突。 下列範例示範如何使用中介軟體類別中定義的唯一物件索引鍵：

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

其他程式碼可以使用中介軟體類別所公開的索引鍵，來存取 `HttpContext.Items` 中儲存的值：

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

此方法也有在程式碼中消除使用索引鍵字串的優點。

## <a name="cache"></a>快取

快取是儲存和擷取資料的有效方式。 應用程式可以控制快取項目的存留期。 如需詳細資訊，請參閱<xref:performance/caching/response>。

快取的資料未與特定要求、使用者或工作階段建立關聯。 **請勿快取其他使用者要求可能抓取的使用者特定資料。**

若要快取整個應用程式的資料，請參閱 <xref:performance/caching/memory> 。

## <a name="common-errors"></a>常見錯誤

* 「嘗試啟動 'Microsoft.AspNetCore.Session.DistributedSessionStore' 時，無法解析類型 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' 。」

  這通常是因為無法設定至少一個執行所造成 `IDistributedCache` 。 如需詳細資訊，請參閱 <xref:performance/caching/distributed> 和 <xref:performance/caching/memory>。

如果會話中介軟體無法保存會話：

* 中介軟體會記錄例外狀況，而要求會繼續正常執行。
* 這會導致無法預期的行為。

如果備份存放區無法使用，會話中介軟體可能無法保存會話。 例如，使用者在工作階段中存放購物車。 使用者在購物車新增一個項目，但認可失敗。 應用程式未察覺到失敗，因此它向使用者報告項目已新增至購物車，但這並不正確。

檢查錯誤的建議方法是在 `await feature.Session.CommitAsync` 應用程式完成寫入會話時呼叫。 備份存放區無法使用時，<xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> 會擲回例外狀況。 如果 `CommitAsync` 失敗，應用程式可以處理例外狀況。 <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*> 當資料存放區無法使用時，會在相同的情況下擲回。
  
## <a name="signalr-and-session-state"></a>SignalR 和會話狀態

SignalR 應用程式不應使用會話狀態來儲存資訊。 SignalR 應用程式可以將每個連接狀態儲存在 `Context.Items` 中樞內。 <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>其他資源

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)、[Diana LaRose](https://github.com/DianaLaRose) 和 [Luke Latham](https://github.com/guardrex)

HTTP 是無狀態的通訊協定。 若不採取其他步驟，HTTP 要求是獨立的訊息，不會保留使用者的值或應用程式狀態。 本文描述數種方法來在要求之間保留使用者資料和應用程式狀態。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/app-state/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="state-management"></a>狀態管理

可以使用數種方法來儲存狀態。 本主題稍後將提供每種方法的描述。

| 儲存方法 | 儲存機制 |
| ---------------- | ----------------- |
| [Cookie！](#cookies) | HTTP cookie s (可能包括使用伺服器端應用程式程式碼儲存的資料)  |
| [會話狀態](#session-state) | HTTP cookie s 和伺服器端應用程式程式碼 |
| [TempData](#tempdata) | HTTP cookie s 或會話狀態 |
| [查詢字串](#query-strings) | HTTP 查詢字串 |
| [隱藏欄位](#hidden-fields) | HTTP 表單欄位 |
| [HttpContext.Items](#httpcontextitems) | 伺服器端應用程式程式碼 |
| [Cache](#cache) | 伺服器端應用程式程式碼 |
| [相依性插入](#dependency-injection) | 伺服器端應用程式程式碼 |

## <a name="cookies"></a>Cookie

Cookie會跨要求儲存資料。 因為 cookie 會隨每個要求一起傳送，所以其大小應保持最小值。 在理想情況下，只有識別碼應該儲存在中， cookie 並包含應用程式所儲存的資料。 大部分的瀏覽器會 cookie 將大小限制為4096個位元組。 cookie每個網域只能使用有限數目的。

因為 cookie 會受到篡改的制約，所以必須由應用程式進行驗證。 Cookie可以由使用者刪除，並在用戶端上過期。 不過， cookie 通常是用戶端上最持久的資料持續性形式。

Cookie通常用於個人化，其中內容是針對已知使用者自訂的。 在大部分情況下，只會識別使用者，而未加以驗證。 cookie可以儲存使用者的名稱、帳戶名稱或唯一使用者識別碼 (例如 GUID) 。 然後，您可以使用 cookie 來存取使用者的個人化設定，例如其慣用的網站背景色彩。

請注意 [歐盟一般資料保護法規 (](https://ec.europa.eu/info/law/law-topic/data-protection) 在發出 cookie 和處理隱私權考慮時，GDPR) 。 如需詳細資訊，請參閱 [ASP.NET Core 中的一般資料保護規定 (GDPR) 支援](xref:security/gdpr)。

## <a name="session-state"></a>工作階段狀態

工作階段狀態是用來在使用者瀏覽 Web 應用程式時存放使用者資料的 ASP.NET Core 情節。 工作階段狀態使用應用程式所維護的存放區，在用戶端的要求之間保存資料。 工作階段資料受到快取的支援，並被視為暫時資料&mdash;網站沒有工作階段資料應該也會繼續運作。 重要應用程式資料應該儲存在使用者資料庫，並只在工作階段中快取以獲得效能最佳化。

> [!NOTE]
> 應用程式不支援會話， [SignalR](xref:signalr/index) 因為[ SignalR 中樞](xref:signalr/hubs)可以獨立于 HTTP 內容執行。 例如，當長時間輪詢要求由中樞維持開啟，超過要求的 HTTP 內容存留期時，便可能發生此情況。

ASP.NET Core 會維護會話狀態，方法是提供 cookie 包含會話識別碼的用戶端，此識別碼會隨著每個要求傳送至應用程式。 應用程式則使用工作階段識別碼來擷取工作階段資料。

工作階段狀態表現下列行為：

* 由於會話 cookie 是瀏覽器的特定會話，因此不會跨瀏覽器共用會話。
* cookie當瀏覽器會話結束時，會刪除會話 s。
* 如果 cookie 收到過期會話的，則會建立使用相同會話的新會話 cookie 。
* 並不會保留空白工作階段&mdash;必須在工作階段中設定至少一個值，才能在要求之間保存工作階段。 不保留工作階段時，會為每個新的要求產生新的工作階段識別碼。
* 應用程式會在最後一個要求之後，保留工作階段一段有限的時間。 應用程式會設定工作階段逾時或使用預設值 20 分鐘。 工作階段狀態適合用來儲存特定工作階段特定的使用者資料，但資料不需要在工作階段之間永久儲存的情況。
* 當 <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> 呼叫執行時或會話過期時，就會刪除會話資料。
* 沒有任何預設機制可通知應用程式程式碼用戶端瀏覽器已關閉，或是在 cookie 用戶端上的會話遭到刪除或過期。
* ASP.NET Core MVC 和 Razor pages 範本包含 (GDPR) 的一般資料保護規定支援。 cookie依預設，會話狀態不會標示為「基本」，因此除非網站訪客允許追蹤，否則會話狀態無法運作。 如需詳細資訊，請參閱<xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>。

> [!WARNING]
> 請勿將敏感性資料存放在工作階段狀態。 使用者可能不會關閉瀏覽器並清除會話 cookie 。 某些流覽 cookie 器會跨瀏覽器視窗維護有效的會話。 會話可能無法限制為單一使用者 &mdash; ，下一使用者可能會繼續使用相同的會話流覽應用程式 cookie 。

記憶體中快取提供者會將工作階段資料存放在應用程式所在伺服器的記憶體中。 在伺服器陣列案例中：

* 使用「黏性工作階段」將每個工作階段繫結至個別伺服器上的特定應用程式執行個體。 [Azure App Service](https://azure.microsoft.com/services/app-service/) 預設會使用[應用程式要求路由 (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 來強制執行自黏工作階段。 不過，黏性工作階段可能會影響延展性，並使 Web 應用程式更新複雜化。 較好的方法是使用 Redis 或 SQL Server 分散式快取，這不需要黏性工作階段。 如需詳細資訊，請參閱<xref:performance/caching/distributed>。
* 此會話 cookie 會透過進行加密 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 。 必須正確設定資料保護，才能讀取每部 cookie 電腦上的會話。 如需詳細資訊，請參閱 <xref:security/data-protection/introduction>與[金鑰儲存提供者](xref:security/data-protection/implementation/key-storage-providers)。

### <a name="configure-session-state"></a>設定工作階段狀態

[Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 套件隨附於 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)，提供用來管理工作階段狀態的中介軟體。 若要啟用工作階段中介軟體，`Startup` 必須包含：

* 任何記憶體快取 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。 `IDistributedCache` 實作會作為工作階段的支援存放區。 如需詳細資訊，請參閱<xref:performance/caching/distributed>。
* <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A>在中的呼叫 `ConfigureServices` 。
* <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>在中的呼叫 `Configure` 。

下列程式碼示範如何設定記憶體內部工作階段提供者，以及 `IDistributedCache` 的預設記憶體中實作。

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

中介軟體的順序很重要。 在上述範例中，如果在 `UseMvc` 之後叫用 `UseSession`，則會發生 `InvalidOperationException`　例外狀況。 如需詳細資訊，請參閱[中介軟體順序](xref:fundamentals/middleware/index#order)。

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> 可以在會話狀態設定之後使用。

呼叫 `UseSession` 之前無法存取 `HttpContext.Session`。

cookie當應用程式開始寫入至回應資料流程之後，就無法建立新會話的新會話。 例外狀況會記錄在 Web 伺服器記錄檔中，而不會顯示在瀏覽器。

### <a name="load-session-state-asynchronously"></a>非同步載入工作階段狀態

ASP.NET Core 中的預設會話提供者 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，只有在 <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType> <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A> 、或方法上明確呼叫方法時，才會以非同步方式從基礎備份存放區載入會話記錄 <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> 。 如果並未先呼叫 `LoadAsync`，則基礎工作階段記錄會同步載入，這可能大規模地為效能帶來負面影響。

若要讓應用程式強制執行此模式，請 <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> 使用擲回例外狀況的版本包裝和程式， <xref:Microsoft.AspNetCore.Session.DistributedSession> 如果在 `LoadAsync` 、或之前未呼叫方法，則會擲回例外狀況 `TryGetValue` `Set` `Remove` 。 請在服務容器中註冊已包裝的版本。

### <a name="session-options"></a>工作階段選項

若要覆寫會話的預設值，請使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions> 。

| 選項 | Description |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | 決定用來建立的設定 cookie 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> 預設為 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`) 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> 預設為 <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`) 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> 預設為 <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`) 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 預設為 `true` 。 <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> 預設為 `false` 。 |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` 指出工作階段可以閒置多久，之後才會放棄它的內容。 每個工作階段存取都會重設逾時。 這項設定只適用于會話的內容，而不會套用至 cookie 。 預設值是 20 分鐘。 |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | 從存放區載入工作階段，或將它認可回到存放區時，所允許的時間長度上限。 此設定只可能適用於非同步作業。 您可以使用來停用這個超時時間 <xref:System.Threading.Timeout.InfiniteTimeSpan> 。 預設值是 1 分鐘。 |

會話會使用 cookie 從單一瀏覽器追蹤和識別要求。 根據預設，這 cookie 會命名為 `.AspNetCore.Session` ，並使用的路徑 `/` 。 由於 cookie 預設值未指定網域，因此它不會提供給頁面上的用戶端腳本 (因為 <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> 預設為 `true`) 。

若要覆寫 cookie 會話的預設值，請使用 `SessionOptions` ：

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

應用程式會使用 <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> 屬性來判斷會話可以閒置多久之後，才能放棄其在伺服器快取中的內容。 這個屬性與 cookie 到期無關。 透過[工作階段中介軟體](xref:Microsoft.AspNetCore.Session.SessionMiddleware)傳遞的每個要求都會重設逾時。

工作階段狀態為「非鎖定」。 如果兩個要求同時嘗試修改工作階段的內容，則最後一個要求會覆寫第一個要求。 `Session` 會實作為「一致性工作階段」，這表示所有內容會都儲存在一起。 當兩個要求試圖修改不同的工作階段值時，最後一個要求可能會覆寫第一個要求所做的工作階段變更。

### <a name="set-and-get-session-values"></a>設定和取得工作階段值

您可以 Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 使用頁面類別或 MVC 類別來存取會話狀態 <xref:Microsoft.AspNetCore.Mvc.Controller> <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> 。 這個屬性是 <xref:Microsoft.AspNetCore.Http.ISession> 實作為。

`ISession` 實作提供數個延伸模組來設定和擷取整數和字串值。 擴充方法位於 <xref:Microsoft.AspNetCore.Http> 命名空間 (加入 `using Microsoft.AspNetCore.Http;` 語句，以取得專案所參考的 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) 延伸模組方法) 的存取權。 [Microsoft.AspNetCore.App 中繼套件](xref:fundamentals/metapackage-app)包含兩個套件。

`ISession` 擴充方法：

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

下列範例會在 `IndexModel.SessionKeyName` `_Name` 頁面頁面) 的範例應用程式中，取得金鑰 (的會話值 Razor ：

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

下列範例示範如何設定及取得整數和字串：

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

若要啟用分散式快取案例，即使是使用記憶體中快取時，都必須序列化所有工作階段資料。 字串和整數序列化程式是由) 的擴充方法所提供 <xref:Microsoft.AspNetCore.Http.ISession> 。 複雜類型必須由使用者使用另一個機制加以序列化，例如 JSON。

新增下列擴充方法，即可設定和取得可序列化物件：

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

下列範例示範如何使用擴充方法來設定和取得可序列化物件：

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core 會公開 Razor [TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) 或控制器頁面 <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> 。 這個屬性會儲存資料，直到讀取另一個要求為止。 [保留 (字串) ](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 和 [查看 (字串) ](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) 方法可以用來檢查資料，而不需要在要求結束時刪除。 [保留 () ](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) 標示字典中的所有專案以進行保留。 `TempData` 在需要多個單一要求的資料時，特別適用于重新導向。 `TempData` 是由 `TempData` 使用或會話狀態的提供者所執行 cookie 。

## <a name="tempdata-samples"></a>TempData 範例

請考慮下列建立客戶的頁面：

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

下列頁面會顯示 `TempData["Message"]` ：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

在上述標記中，在要求結束 `TempData["Message"]` 時， **不** 會刪除，因為 `Peek` 會使用。 重新整理頁面隨即顯示 `TempData["Message"]` 。

下列標記類似于上述程式碼，但會使用在 `Keep` 要求結束時保留資料：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

在 *IndexPeek* 和 *IndexKeep* 頁面之間流覽並不會刪除 `TempData["Message"]` 。

下列 `TempData["Message"]` 程式碼會顯示，但在要求結束 `TempData["Message"]` 時，會刪除：

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData 提供者

cookie依預設，會使用以 TempData 提供者將 TempData 儲存在 cookie s 中。

cookie資料會使用加密 <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> ，然後使用來進行 <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> 區塊編碼。 因為 cookie 是區塊，所以 cookie 在 ASP.NET Core 1.x 中找到的單一大小限制不適用。 cookie資料不會壓縮，因為壓縮加密資料可能會導致安全性問題，例如[犯罪](https://wikipedia.org/wiki/CRIME_(security_exploit))和[缺口](https://wikipedia.org/wiki/BREACH_(security_exploit))攻擊。 如需有關以 cookie TempData 提供者為基礎的詳細資訊，請參閱 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider> 。

### <a name="choose-a-tempdata-provider"></a>選擇 TempData 提供者

選擇 TempData 提供者涉及數項考量，例如：

1. 應用程式已經使用工作階段狀態了嗎？ 如果是的話，使用工作階段狀態 TempData 提供者沒有額外的應用程式成本 (除了資料的大小之外)。
2. 應用程式是否盡量只將 TempData 用於相對少量的資料 (最多 500 個位元組)？ 如果是， cookie TempData 提供者會為每個攜帶 TempData 的要求增加一個小成本。 如果不是的話，則工作階段狀態 TempData 提供者可能有助於避免在每個要求中來回傳送大量資料，直到取用 TempData 為止。
3. 應用程式在伺服器陣列中的多部伺服器上執行？ 如果是，則不需要額外的設定，就能在 cookie 資料保護之外使用 TempData 提供者 (請參閱 <xref:security/data-protection/introduction> 和 [金鑰儲存提供者](xref:security/data-protection/implementation/key-storage-providers)) 。

> [!NOTE]
> 大部分的 web 客戶 (端（例如網頁瀏覽器）都) 在每個的大小上限 cookie 、總數或兩者上強制執行限制 cookie 。 使用 cookie TempData 提供者時，請確認應用程式不會超過這些限制。 請考慮資料的大小總計。 cookie由於加密和區塊處理而增加的大小。

### <a name="configure-the-tempdata-provider"></a>設定 TempData 提供者

cookie預設會啟用以 TempData 提供者為基礎的提供者。

若要啟用以會話為基礎的 TempData 提供者，請使用 <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A> 擴充方法：

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

中介軟體的順序很重要。 在上述範例中，如果在 `UseMvc` 之後叫用 `UseSession`，則會發生 `InvalidOperationException`　例外狀況。 如需詳細資訊，請參閱[中介軟體順序](xref:fundamentals/middleware/index#order)。

> [!IMPORTANT]
> 如果目標為 .NET Framework 且使用以工作階段為基礎的 TempData 提供者，請將 [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 套件新增至專案。

## <a name="query-strings"></a>查詢字串

可以將數量有限的資料從某個要求傳遞到另一個要求，方法是將其新增至新要求的查詢字串。 這對於以持續方式擷取狀態很有用，可讓內嵌狀態的連結透過電子郵件或社交網路共用。 因為 URL 查詢字串為公用，所以請絕對不要使用查詢字串來處理敏感性資料。

除了非預期的共用之外，在查詢字串中包括資料還可能會為[跨網站偽造要求 (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 攻擊創造機會，這些攻擊可能會誘騙使用者在驗證時瀏覽惡意網站。 然後，攻擊者可能會竊取應用程式中的使用者資料，或代表使用者採取惡意動作。 任何保留的應用程式或工作階段狀態必須防範 CSRF 攻擊。 如需詳細資訊，請參閱<xref:security/anti-request-forgery>。

## <a name="hidden-fields"></a>隱藏欄位

資料可以儲存在隱藏的表單欄位，並貼回下一個要求。 這在多頁表單中很常見。 因為用戶端可能會竄改資料，所以應用程式必須一律重新驗證存放在隱藏欄位中的資料。

## <a name="httpcontextitems"></a>HttpContext.Items

<xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType>集合是用來在處理單一要求時儲存資料。 集合的內容會在每個要求處理之後捨棄。 當元件或中介軟體在要求期間的不同時間點運作，而且沒有可傳遞參數的直接方式時，`Items` 集合經常用來允許元件或中介軟體進行通訊。

在下列範例中，[中介軟體](xref:fundamentals/middleware/index)會將 `isVerified` 新增至 `Items` 集合。

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

稍後在管線中，另一個中介軟體可以存取 `isVerified` 的值：

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

如果是只由單一應用程式使用的中介軟體，可接受 `string` 索引鍵。 應用程式執行個體之間共用的中介軟體，應該使用唯一的物件索引鍵，來避免索引鍵衝突。 下列範例示範如何使用中介軟體類別中定義的唯一物件索引鍵：

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

其他程式碼可以使用中介軟體類別所公開的索引鍵，來存取 `HttpContext.Items` 中儲存的值：

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

此方法也有在程式碼中消除使用索引鍵字串的優點。

## <a name="cache"></a>快取

快取是儲存和擷取資料的有效方式。 應用程式可以控制快取項目的存留期。

快取的資料未與特定要求、使用者或工作階段建立關聯。 **請小心不要快取可能由其他使用者要求所擷取的特定使用者資料。**

如需詳細資訊，請參閱<xref:performance/caching/response>。

## <a name="dependency-injection"></a>相依性插入

請使用[相依性插入](xref:fundamentals/dependency-injection)將資料提供給所有使用者：

1. 定義包含資料的服務。 例如，已定義名為 `MyAppData` 的類別：

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. 將服務類別新增至 `Startup.ConfigureServices`：

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. 取用資料服務類別：

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>常見錯誤

* 「嘗試啟動 'Microsoft.AspNetCore.Session.DistributedSessionStore' 時，無法解析類型 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' 。」

  這種情形通常是因為無法設定至少一個 `IDistributedCache` 實作。 如需詳細資訊，請參閱 <xref:performance/caching/distributed> 和 <xref:performance/caching/memory>。

* 如果工作階段中介軟體無法持續存在於工作階段 (例如，備份存放區無法使用)，中介軟體會記錄例外狀況，而要求會照常繼續進行。 這會導致無法預期的行為。

  例如，使用者在工作階段中存放購物車。 使用者在購物車新增一個項目，但認可失敗。 應用程式未察覺到失敗，因此它向使用者報告項目已新增至購物車，但這並不正確。

  檢查是否有錯誤的建議方法是，當應用程式完成寫入至工作階段後，從應用程式程式碼呼叫 `await feature.Session.CommitAsync();`。 備份存放區無法使用時，`CommitAsync` 會擲回例外狀況。 如果 `CommitAsync` 失敗，應用程式可以處理例外狀況。 `LoadAsync` 在無法使用資料存放區的相同情況下會擲回。
  
## <a name="signalr-and-session-state"></a>SignalR 和會話狀態

SignalR 應用程式不應使用會話狀態來儲存資訊。 SignalR 應用程式可以將每個連接狀態儲存在 `Context.Items` 中樞內。 <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>其他資源

<xref:host-and-deploy/web-farm>
::: moniker-end
