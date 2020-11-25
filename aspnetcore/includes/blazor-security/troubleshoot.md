## <a name="troubleshoot"></a><span data-ttu-id="d5575-101">疑難排解</span><span class="sxs-lookup"><span data-stu-id="d5575-101">Troubleshoot</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a><span data-ttu-id="d5575-102">常見錯誤</span><span class="sxs-lookup"><span data-stu-id="d5575-102">Common errors</span></span>

* <span data-ttu-id="d5575-103">未授權的 AAD 用戶端</span><span class="sxs-lookup"><span data-stu-id="d5575-103">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="d5575-104">資訊： AspNetCore。 DefaultAuthorizationService [2] 授權失敗。</span><span class="sxs-lookup"><span data-stu-id="d5575-104">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="d5575-105">不符合這些需求： DenyAnonymousAuthorizationRequirement：需要經過驗證的使用者。</span><span class="sxs-lookup"><span data-stu-id="d5575-105">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="d5575-106">來自 AAD 的登入回呼錯誤：</span><span class="sxs-lookup"><span data-stu-id="d5575-106">Login callback error from AAD:</span></span>

  * <span data-ttu-id="d5575-107">錯誤：`unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="d5575-107">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="d5575-108">說明：`AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="d5575-108">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="d5575-109">若要解決此錯誤：</span><span class="sxs-lookup"><span data-stu-id="d5575-109">To resolve the error:</span></span>

  1. <span data-ttu-id="d5575-110">在 Azure 入口網站中，存取 [應用程式的資訊清單](/azure/active-directory/develop/reference-app-manifest)。</span><span class="sxs-lookup"><span data-stu-id="d5575-110">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="d5575-111">將 [`allowPublicClient`](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) 屬性設為 `null` 或 `true` 。</span><span class="sxs-lookup"><span data-stu-id="d5575-111">Set the [`allowPublicClient`](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) attribute to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="d5575-112">Cookie 和網站資料</span><span class="sxs-lookup"><span data-stu-id="d5575-112">Cookies and site data</span></span>

<span data-ttu-id="d5575-113">Cookie 和網站資料可以跨應用程式更新保存，並干擾測試和疑難排解。</span><span class="sxs-lookup"><span data-stu-id="d5575-113">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="d5575-114">進行應用程式程式碼變更、與提供者的使用者帳戶變更或提供者應用程式設定變更時，請清除下列各項：</span><span class="sxs-lookup"><span data-stu-id="d5575-114">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="d5575-115">使用者登入 cookie</span><span class="sxs-lookup"><span data-stu-id="d5575-115">User sign-in cookies</span></span>
* <span data-ttu-id="d5575-116">應用程式 cookie</span><span class="sxs-lookup"><span data-stu-id="d5575-116">App cookies</span></span>
* <span data-ttu-id="d5575-117">快取和儲存的網站資料</span><span class="sxs-lookup"><span data-stu-id="d5575-117">Cached and stored site data</span></span>

<span data-ttu-id="d5575-118">防止延遲 cookie 和網站資料干擾測試和疑難排解的其中一種方法是：</span><span class="sxs-lookup"><span data-stu-id="d5575-118">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="d5575-119">設定瀏覽器</span><span class="sxs-lookup"><span data-stu-id="d5575-119">Configure a browser</span></span>
  * <span data-ttu-id="d5575-120">使用瀏覽器進行測試，您可以設定在每次關閉瀏覽器時刪除所有 cookie 和網站資料。</span><span class="sxs-lookup"><span data-stu-id="d5575-120">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="d5575-121">請確定已手動關閉瀏覽器，或透過 IDE 來變更應用程式、測試使用者或提供者設定。</span><span class="sxs-lookup"><span data-stu-id="d5575-121">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="d5575-122">在 Visual Studio 中使用自訂命令以 incognito 或私用模式開啟瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="d5575-122">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="d5575-123">從 Visual Studio 的 [**執行**] 按鈕開啟 **[流覽方式**] 對話方塊。</span><span class="sxs-lookup"><span data-stu-id="d5575-123">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="d5575-124">選取 [新增] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d5575-124">Select the **Add** button.</span></span>
  * <span data-ttu-id="d5575-125">在 [ **程式** ] 欄位中提供瀏覽器的路徑。</span><span class="sxs-lookup"><span data-stu-id="d5575-125">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="d5575-126">下列可執行檔路徑是 Windows 10 的一般安裝位置。</span><span class="sxs-lookup"><span data-stu-id="d5575-126">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="d5575-127">如果您的瀏覽器安裝在不同的位置，或您未使用 Windows 10，請提供瀏覽器可執行檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="d5575-127">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="d5575-128">Microsoft Edge： `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="d5575-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="d5575-129">Google Chrome： `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="d5575-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="d5575-130">Mozilla Firefox： `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="d5575-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="d5575-131">在 [ **引數** ] 欄位中，提供瀏覽器用來在 incognito 或私用模式中開啟的命令列選項。</span><span class="sxs-lookup"><span data-stu-id="d5575-131">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="d5575-132">某些瀏覽器需要應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="d5575-132">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="d5575-133">Microsoft Edge：使用 `-inprivate` 。</span><span class="sxs-lookup"><span data-stu-id="d5575-133">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="d5575-134">Google Chrome：使用 `--incognito --new-window {URL}` ，其中預留位置 `{URL}` 是要開啟的 URL (例如 `https://localhost:5001`) 。</span><span class="sxs-lookup"><span data-stu-id="d5575-134">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="d5575-135">Mozilla Firefox：使用 `-private -url {URL}` ，其中預留位置 `{URL}` 是要開啟的 URL (例如 `https://localhost:5001`) 。</span><span class="sxs-lookup"><span data-stu-id="d5575-135">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="d5575-136">在 [ **易記名稱** ] 欄位中提供名稱。</span><span class="sxs-lookup"><span data-stu-id="d5575-136">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="d5575-137">例如： `Firefox Auth Testing` 。</span><span class="sxs-lookup"><span data-stu-id="d5575-137">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="d5575-138">選取 [ **確定]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d5575-138">Select the **OK** button.</span></span>
  * <span data-ttu-id="d5575-139">若要避免需要針對使用應用程式測試的每個反復專案選取瀏覽器設定檔，請將設定檔設定為預設值，並將 [ **設定為預設值** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d5575-139">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="d5575-140">確定 IDE 已關閉瀏覽器，以進行應用程式、測試使用者或提供者設定的任何變更。</span><span class="sxs-lookup"><span data-stu-id="d5575-140">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="d5575-141">執行伺服器應用程式</span><span class="sxs-lookup"><span data-stu-id="d5575-141">Run the Server app</span></span>

<span data-ttu-id="d5575-142">測試及疑難排解託管 Blazor 應用程式時，請確定您是從專案執行應用程式 **`Server`** 。</span><span class="sxs-lookup"><span data-stu-id="d5575-142">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="d5575-143">例如，在 Visual Studio 中，請在使用下列任何一種方法啟動應用程式之前，先確認 **方案總管** 中的伺服器專案已反白顯示：</span><span class="sxs-lookup"><span data-stu-id="d5575-143">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="d5575-144">選取 [執行] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="d5575-144">Select the **Run** button.</span></span>
* <span data-ttu-id="d5575-145">從功能表使用 **Debug**  >  **開始調試**。</span><span class="sxs-lookup"><span data-stu-id="d5575-145">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="d5575-146">按 <kbd>F5</kbd>。</span><span class="sxs-lookup"><span data-stu-id="d5575-146">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="d5575-147">檢查 JSON Web 權杖的內容 (JWT) </span><span class="sxs-lookup"><span data-stu-id="d5575-147">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="d5575-148">若要解碼 (JWT) 的 JSON Web 權杖，請使用 Microsoft 的 [jwt.ms](https://jwt.ms/) 工具。</span><span class="sxs-lookup"><span data-stu-id="d5575-148">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="d5575-149">UI 中的值永遠不會離開您的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="d5575-149">Values in the UI never leave your browser.</span></span>
