## <a name="troubleshoot"></a>疑難排解

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a>常見錯誤

* 未授權的 AAD 用戶端

  > 資訊： AspNetCore。 DefaultAuthorizationService [2] 授權失敗。 不符合這些需求： DenyAnonymousAuthorizationRequirement：需要經過驗證的使用者。

  來自 AAD 的登入回呼錯誤：

  * 錯誤：`unauthorized_client`
  * 說明：`AADB2C90058: The provided application is not configured to allow public clients.`

  若要解決此錯誤：

  1. 在 Azure 入口網站中，存取 [應用程式的資訊清單](/azure/active-directory/develop/reference-app-manifest)。
  1. 將[ `allowPublicClient` 屬性](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute)設為 `null` 或 `true` 。

::: moniker-end

### <a name="cookies-and-site-data"></a>Cookie 和網站資料

Cookie 和網站資料可以跨應用程式更新保存，並干擾測試和疑難排解。 進行應用程式程式碼變更、與提供者的使用者帳戶變更或提供者應用程式設定變更時，請清除下列各項：

* 使用者登入 cookie
* 應用程式 cookie
* 快取和儲存的網站資料

防止延遲 cookie 和網站資料干擾測試和疑難排解的其中一種方法是：

* 設定瀏覽器
  * 使用瀏覽器進行測試，您可以設定在每次關閉瀏覽器時刪除所有 cookie 和網站資料。
  * 請確定已手動關閉瀏覽器，或透過 IDE 來變更應用程式、測試使用者或提供者設定。
* 在 Visual Studio 中使用自訂命令以 incognito 或私用模式開啟瀏覽器：
  * 從 Visual Studio 的 [**執行**] 按鈕開啟 **[流覽方式**] 對話方塊。
  * 選取 [新增] 按鈕。
  * 在 [ **程式** ] 欄位中提供瀏覽器的路徑。 下列可執行檔路徑是 Windows 10 的一般安裝位置。 如果您的瀏覽器安裝在不同的位置，或您未使用 Windows 10，請提供瀏覽器可執行檔的路徑。
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

### <a name="run-the-server-app"></a>執行伺服器應用程式

測試及疑難排解託管 Blazor 應用程式時，請確定您是從專案執行應用程式 **`Server`** 。 例如，在 Visual Studio 中，請在使用下列任何一種方法啟動應用程式之前，先確認 **方案總管** 中的伺服器專案已反白顯示：

* 選取 [執行] 按鈕。
* 從功能表使用 **Debug**  >  **開始調試**。
* 按 <kbd>F5</kbd>。

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>檢查 JSON Web 權杖的內容 (JWT) 

若要解碼 (JWT) 的 JSON Web 權杖，請使用 Microsoft 的 [jwt.ms](https://jwt.ms/) 工具。 UI 中的值永遠不會離開您的瀏覽器。
