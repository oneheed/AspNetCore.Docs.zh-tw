---
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
ms.openlocfilehash: f58da78475d65cbb70b0b177e1b0443535e97d55
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102402301"
---
## <a name="troubleshoot"></a>疑難排解

### <a name="common-errors"></a>常見錯誤

* 應用程式或 Identity 提供者 (IP) 的設定不正確

  最常見的錯誤是因為設定不正確所造成。 以下是一些範例：
  
  * 視案例的需求而定，遺失或不正確的授權單位、實例、租使用者識別碼、租使用者網域、用戶端識別碼或重新導向 URI 會防止應用程式驗證用戶端。
  * 不正確的存取權杖範圍會防止用戶端存取伺服器 web API 端點。
  * 不正確或遺失的伺服器 API 許可權會防止用戶端存取伺服器 web API 端點。
  * 在與提供者應用程式註冊的重新導向 URI 中設定的不同埠上執行應用程式 Identity 。
  
  本文指導方針的設定章節會顯示正確設定的範例。 請仔細檢查文章的每一節，尋找應用程式和 IP 設定錯誤。
  
  如果設定顯示為正確：
  
  * 分析應用程式記錄檔。
  * 使用瀏覽器的開發人員工具，檢查用戶端應用程式和 IP 或伺服器應用程式之間的網路流量。 通常，在提出要求之後，IP 或伺服器應用程式會將確切的錯誤訊息或訊息與造成問題之原因的線索傳回給用戶端。 您可以在下列文章中找到開發人員工具指導方針：

    * Google [Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (google 檔) 
    * [Microsoft Edge](/microsoft-edge/devtools-guide-chromium/network/)
    * [Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (mozilla 檔) 

  * 根據發生問題的位置，將 JSON Web 權杖的內容解碼 (JWT) 用來驗證用戶端或存取伺服器 Web API。 如需詳細資訊，請參閱 [檢查 JSON Web 權杖的內容 (JWT) ](#inspect-the-content-of-a-json-web-token-jwt)。
  
  檔團隊會在文章中回應檔的意見反應和 bug (**從此頁面** 的意見反應一節中提出問題) 但無法提供產品支援。 有數個公開支援論壇可協助您針對應用程式進行疑難排解。 我們的建議如下：
  
  * [堆疊溢位 (標記： `blazor`) ](https://stackoverflow.com/questions/tagged/blazor)
  * [ASP.NET 核心時差小組](http://tattoocoder.com/aspnet-slack-sign-up/)
  * [Blazor Gitter](https://gitter.im/aspnet/Blazor)
  
  若為非安全性、非機密及非機密的可重現架構 bug 報告，請 [開啟 ASP.NET 核心產品單元的問題](https://github.com/dotnet/aspnetcore/issues)。 除非您徹底調查問題的原因，而且無法自行解決問題，並透過「公開支援論壇」上的「社區協助」來解決問題，否則請不要在產品單位開立問題。 產品單位無法針對因簡單設定錯誤或涉及協力廠商服務的使用案例而中斷的個別應用程式進行疑難排解。 如果報表本質上是敏感性或機密，或描述攻擊者可能會利用的產品潛在安全性瑕疵，請參閱 [ (dotnet/Aspnetcore GitHub 存放庫) 報告安全性問題和 bug ](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs)。

::: moniker range=">= aspnetcore-5.0"

* 未授權的 AAD 用戶端

  > 資訊： AspNetCore。 DefaultAuthorizationService [2] 授權失敗。 不符合這些需求： DenyAnonymousAuthorizationRequirement：需要經過驗證的使用者。

  來自 AAD 的登入回呼錯誤：

  * 錯誤：`unauthorized_client`
  * 說明：`AADB2C90058: The provided application is not configured to allow public clients.`

  若要解決此錯誤：

  1. 在 Azure 入口網站中，存取 [應用程式的資訊清單](/azure/active-directory/develop/reference-app-manifest)。
  1. 將[ `allowPublicClient` 屬性](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute)設為 `null` 或 `true` 。

::: moniker-end

### <a name="cookies-and-site-data"></a>Cookies 和網站資料

Cookie和網站資料可以跨應用程式更新保存，並干擾測試和疑難排解。 進行應用程式程式碼變更、與提供者的使用者帳戶變更或提供者應用程式設定變更時，請清除下列各項：

* 使用者 cookie 登入
* 應用 cookie 程式
* 快取和儲存的網站資料

防止延遲 cookie 和網站資料干擾測試和疑難排解的其中一種方法是：

* 設定瀏覽器
  * 使用瀏覽器進行測試，您可以設定在 cookie 每次關閉瀏覽器時刪除所有和網站資料。
  * 請確定已手動關閉瀏覽器，或透過 IDE 來變更應用程式、測試使用者或提供者設定。
* 使用自訂命令，在 Visual Studio 中以 incognito 或私用模式開啟瀏覽器：
  * 從 Visual Studio 的 [**執行**] 按鈕開啟 **[流覽方式**] 對話方塊。
  * 選取 [新增] 按鈕。
  * 在 [ **程式** ] 欄位中提供瀏覽器的路徑。 下列可執行檔路徑是適用于 Windows 10 的一般安裝位置。 如果您的瀏覽器安裝在不同的位置，或您未使用 Windows 10，請提供瀏覽器可執行檔的路徑。
    * Microsoft Edge： `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`
    * Google Chrome： `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`
    * Mozilla Firefox： `C:\Program Files\Mozilla Firefox\firefox.exe`
  * 在 [ **引數** ] 欄位中，提供瀏覽器用來在 incognito 或私用模式中開啟的命令列選項。 某些瀏覽器需要應用程式的 URL。
    * Microsoft Edge：使用 `-inprivate` 。
    * Google Chrome：使用 `--incognito --new-window {URL}` ，其中預留位置 `{URL}` 是要開啟的 URL (例如 `https://localhost:5001`) 。
    * Mozilla Firefox：使用 `-private -url {URL}` ，其中預留位置 `{URL}` 是要開啟的 URL (例如 `https://localhost:5001`) 。
  * 在 [ **易記名稱** ] 欄位中提供名稱。 例如： `Firefox Auth Testing` 。
  * 選取 [ **確定]** 按鈕。
  * 若要避免需要針對使用應用程式測試的每個反復專案選取瀏覽器設定檔，請將設定檔設定為預設值，並將 [ **設定為預設值** ] 按鈕。
  * 確定 IDE 已關閉瀏覽器，以進行應用程式、測試使用者或提供者設定的任何變更。

### <a name="app-upgrades"></a>應用程式升級

在開發電腦上升級 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。 在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。 大多數這些問題都可依照下列指示來進行修正：

1. 從命令列介面執行，以清除本機系統的 NuGet 套件快取 [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) 。
1. 刪除專案的 `bin` 和 `obj` 資料夾。
1. 還原並重建專案。
1. 重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。

> [!NOTE]
> 不支援使用與應用程式的目標架構不相容的套件版本。 如需封裝的相關資訊，請使用 [NuGet 資源庫](https://www.nuget.org) 或 [FuGet 套件瀏覽器](https://www.fuget.org)。

### <a name="run-the-server-app"></a>執行伺服器應用程式

測試及疑難排解託管解決方案時 Blazor ，請確定您是從專案執行應用程式 **`Server`** 。 例如，在 Visual Studio 中，使用下列任何方法來啟動應用程式之前，請先確認已在 [ **方案 Explorer** ] 中反白顯示伺服器專案：

* 選取 [執行] 按鈕。
* 從功能表使用 **Debug**  >  **開始調試**。
* 按 <kbd>F5</kbd>。

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>檢查 JSON Web 權杖的內容 (JWT) 

若要解碼 (JWT) 的 JSON Web 權杖，請使用 Microsoft 的 [jwt.ms](https://jwt.ms/) 工具。 UI 中的值永遠不會離開您的瀏覽器。

顯示) 的範例編碼 JWT (縮寫：

> eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ...bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q

針對 Azure AAD B2C 進行驗證之應用程式的工具所解碼的範例 JWT：

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1610059429,
  "nbf": 1610055829,
  "ver": "1.0",
  "iss": "https://mysiteb2c.b2clogin.com/5cc15ea8-a296-4aa3-97e4-226dcc9ad298/v2.0/",
  "sub": "5ee963fb-24d6-4d72-a1b6-889c6e2c7438",
  "aud": "70bde375-fce3-4b82-984a-b247d823a03f",
  "nonce": "b2641f54-8dc4-42ca-97ea-7f12ff4af871",
  "iat": 1610055829,
  "auth_time": 1610055822,
  "idp": "idp.com",
  "tfp": "B2C_1_signupsignin"
}.[Signature]
```
