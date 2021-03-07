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
## <a name="troubleshoot"></a><span data-ttu-id="99bfa-101">疑難排解</span><span class="sxs-lookup"><span data-stu-id="99bfa-101">Troubleshoot</span></span>

### <a name="common-errors"></a><span data-ttu-id="99bfa-102">常見錯誤</span><span class="sxs-lookup"><span data-stu-id="99bfa-102">Common errors</span></span>

* <span data-ttu-id="99bfa-103">應用程式或 Identity 提供者 (IP) 的設定不正確</span><span class="sxs-lookup"><span data-stu-id="99bfa-103">Misconfiguration of the app or Identity Provider (IP)</span></span>

  <span data-ttu-id="99bfa-104">最常見的錯誤是因為設定不正確所造成。</span><span class="sxs-lookup"><span data-stu-id="99bfa-104">The most common errors are caused by incorrect configuration.</span></span> <span data-ttu-id="99bfa-105">以下是一些範例：</span><span class="sxs-lookup"><span data-stu-id="99bfa-105">The following are a few examples:</span></span>
  
  * <span data-ttu-id="99bfa-106">視案例的需求而定，遺失或不正確的授權單位、實例、租使用者識別碼、租使用者網域、用戶端識別碼或重新導向 URI 會防止應用程式驗證用戶端。</span><span class="sxs-lookup"><span data-stu-id="99bfa-106">Depending on the requirements of the scenario, a missing or incorrect Authority, Instance, Tenant ID, Tenant domain, Client ID, or Redirect URI prevents an app from authenticating clients.</span></span>
  * <span data-ttu-id="99bfa-107">不正確的存取權杖範圍會防止用戶端存取伺服器 web API 端點。</span><span class="sxs-lookup"><span data-stu-id="99bfa-107">An incorrect access token scope prevents clients from accessing server web API endpoints.</span></span>
  * <span data-ttu-id="99bfa-108">不正確或遺失的伺服器 API 許可權會防止用戶端存取伺服器 web API 端點。</span><span class="sxs-lookup"><span data-stu-id="99bfa-108">Incorrect or missing server API permissions prevent clients from accessing server web API endpoints.</span></span>
  * <span data-ttu-id="99bfa-109">在與提供者應用程式註冊的重新導向 URI 中設定的不同埠上執行應用程式 Identity 。</span><span class="sxs-lookup"><span data-stu-id="99bfa-109">Running the app at a different port than is configured in the Redirect URI of the Identity Provider's app registration.</span></span>
  
  <span data-ttu-id="99bfa-110">本文指導方針的設定章節會顯示正確設定的範例。</span><span class="sxs-lookup"><span data-stu-id="99bfa-110">Configuration sections of this article's guidance show examples of the correct configuration.</span></span> <span data-ttu-id="99bfa-111">請仔細檢查文章的每一節，尋找應用程式和 IP 設定錯誤。</span><span class="sxs-lookup"><span data-stu-id="99bfa-111">Carefully check each section of the article looking for app and IP misconfiguration.</span></span>
  
  <span data-ttu-id="99bfa-112">如果設定顯示為正確：</span><span class="sxs-lookup"><span data-stu-id="99bfa-112">If the configuration appears correct:</span></span>
  
  * <span data-ttu-id="99bfa-113">分析應用程式記錄檔。</span><span class="sxs-lookup"><span data-stu-id="99bfa-113">Analyze application logs.</span></span>
  * <span data-ttu-id="99bfa-114">使用瀏覽器的開發人員工具，檢查用戶端應用程式和 IP 或伺服器應用程式之間的網路流量。</span><span class="sxs-lookup"><span data-stu-id="99bfa-114">Examine the network traffic between the client app and the IP or server app with the browser's developer tools.</span></span> <span data-ttu-id="99bfa-115">通常，在提出要求之後，IP 或伺服器應用程式會將確切的錯誤訊息或訊息與造成問題之原因的線索傳回給用戶端。</span><span class="sxs-lookup"><span data-stu-id="99bfa-115">Often, an exact error message or a message with a clue to what's causing the problem is returned to the client by the IP or server app after making a request.</span></span> <span data-ttu-id="99bfa-116">您可以在下列文章中找到開發人員工具指導方針：</span><span class="sxs-lookup"><span data-stu-id="99bfa-116">Developer tools guidance is found in the following articles:</span></span>

    * <span data-ttu-id="99bfa-117">Google [Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (google 檔) </span><span class="sxs-lookup"><span data-stu-id="99bfa-117">[Google Chrome](https://developers.google.com/web/tools/chrome-devtools/network) (Google documentation)</span></span>
    * [<span data-ttu-id="99bfa-118">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="99bfa-118">Microsoft Edge</span></span>](/microsoft-edge/devtools-guide-chromium/network/)
    * <span data-ttu-id="99bfa-119">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (mozilla 檔) </span><span class="sxs-lookup"><span data-stu-id="99bfa-119">[Mozilla Firefox](https://developer.mozilla.org/docs/Tools/Network_Monitor) (Mozilla documentation)</span></span>

  * <span data-ttu-id="99bfa-120">根據發生問題的位置，將 JSON Web 權杖的內容解碼 (JWT) 用來驗證用戶端或存取伺服器 Web API。</span><span class="sxs-lookup"><span data-stu-id="99bfa-120">Decode the contents of a JSON Web Token (JWT) used for authenticating a client or accessing a server web API, depending on where the problem is occurring.</span></span> <span data-ttu-id="99bfa-121">如需詳細資訊，請參閱 [檢查 JSON Web 權杖的內容 (JWT) ](#inspect-the-content-of-a-json-web-token-jwt)。</span><span class="sxs-lookup"><span data-stu-id="99bfa-121">For more information, see [Inspect the content of a JSON Web Token (JWT)](#inspect-the-content-of-a-json-web-token-jwt).</span></span>
  
  <span data-ttu-id="99bfa-122">檔團隊會在文章中回應檔的意見反應和 bug (**從此頁面** 的意見反應一節中提出問題) 但無法提供產品支援。</span><span class="sxs-lookup"><span data-stu-id="99bfa-122">The documenation team responds to document feedback and bugs in articles (open an issue from the **This page** feedback section) but is unable to provide product support.</span></span> <span data-ttu-id="99bfa-123">有數個公開支援論壇可協助您針對應用程式進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="99bfa-123">Several public support forums are available to assist with troubleshooting an app.</span></span> <span data-ttu-id="99bfa-124">我們的建議如下：</span><span class="sxs-lookup"><span data-stu-id="99bfa-124">We recommend the following:</span></span>
  
  * [<span data-ttu-id="99bfa-125">堆疊溢位 (標記： `blazor`) </span><span class="sxs-lookup"><span data-stu-id="99bfa-125">Stack Overflow (tag: `blazor`)</span></span>](https://stackoverflow.com/questions/tagged/blazor)
  * [<span data-ttu-id="99bfa-126">ASP.NET 核心時差小組</span><span class="sxs-lookup"><span data-stu-id="99bfa-126">ASP.NET Core Slack Team</span></span>](http://tattoocoder.com/aspnet-slack-sign-up/)
  * <span data-ttu-id="99bfa-127">[Blazor Gitter](https://gitter.im/aspnet/Blazor)</span><span class="sxs-lookup"><span data-stu-id="99bfa-127">[Blazor Gitter](https://gitter.im/aspnet/Blazor)</span></span>
  
  <span data-ttu-id="99bfa-128">若為非安全性、非機密及非機密的可重現架構 bug 報告，請 [開啟 ASP.NET 核心產品單元的問題](https://github.com/dotnet/aspnetcore/issues)。</span><span class="sxs-lookup"><span data-stu-id="99bfa-128">For non-security, non-sensitive, and non-confidential reproducible framework bug reports, [open an issue with the ASP.NET Core product unit](https://github.com/dotnet/aspnetcore/issues).</span></span> <span data-ttu-id="99bfa-129">除非您徹底調查問題的原因，而且無法自行解決問題，並透過「公開支援論壇」上的「社區協助」來解決問題，否則請不要在產品單位開立問題。</span><span class="sxs-lookup"><span data-stu-id="99bfa-129">Don't open an issue with the product unit until you've thoroughly investigated the cause of a problem and can't resolve it on your own and with the help of the community on a public support forum.</span></span> <span data-ttu-id="99bfa-130">產品單位無法針對因簡單設定錯誤或涉及協力廠商服務的使用案例而中斷的個別應用程式進行疑難排解。</span><span class="sxs-lookup"><span data-stu-id="99bfa-130">The product unit isn't able to troubleshoot individual apps that are broken due to simple misconfiguration or use cases involving third-party services.</span></span> <span data-ttu-id="99bfa-131">如果報表本質上是敏感性或機密，或描述攻擊者可能會利用的產品潛在安全性瑕疵，請參閱 [ (dotnet/Aspnetcore GitHub 存放庫) 報告安全性問題和 bug ](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs)。</span><span class="sxs-lookup"><span data-stu-id="99bfa-131">If a report is sensitive or confidential in nature or describes a potential security flaw in the product that attackers may exploit, see [Reporting security issues and bugs (dotnet/aspnetcore GitHub repository)](https://github.com/dotnet/aspnetcore/blob/main/CONTRIBUTING.md#reporting-security-issues-and-bugs).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="99bfa-132">未授權的 AAD 用戶端</span><span class="sxs-lookup"><span data-stu-id="99bfa-132">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="99bfa-133">資訊： AspNetCore。 DefaultAuthorizationService [2] 授權失敗。</span><span class="sxs-lookup"><span data-stu-id="99bfa-133">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="99bfa-134">不符合這些需求： DenyAnonymousAuthorizationRequirement：需要經過驗證的使用者。</span><span class="sxs-lookup"><span data-stu-id="99bfa-134">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="99bfa-135">來自 AAD 的登入回呼錯誤：</span><span class="sxs-lookup"><span data-stu-id="99bfa-135">Login callback error from AAD:</span></span>

  * <span data-ttu-id="99bfa-136">錯誤：`unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="99bfa-136">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="99bfa-137">說明：`AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="99bfa-137">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="99bfa-138">若要解決此錯誤：</span><span class="sxs-lookup"><span data-stu-id="99bfa-138">To resolve the error:</span></span>

  1. <span data-ttu-id="99bfa-139">在 Azure 入口網站中，存取 [應用程式的資訊清單](/azure/active-directory/develop/reference-app-manifest)。</span><span class="sxs-lookup"><span data-stu-id="99bfa-139">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="99bfa-140">將[ `allowPublicClient` 屬性](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute)設為 `null` 或 `true` 。</span><span class="sxs-lookup"><span data-stu-id="99bfa-140">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="99bfa-141">Cookies 和網站資料</span><span class="sxs-lookup"><span data-stu-id="99bfa-141">Cookies and site data</span></span>

<span data-ttu-id="99bfa-142">Cookie和網站資料可以跨應用程式更新保存，並干擾測試和疑難排解。</span><span class="sxs-lookup"><span data-stu-id="99bfa-142">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="99bfa-143">進行應用程式程式碼變更、與提供者的使用者帳戶變更或提供者應用程式設定變更時，請清除下列各項：</span><span class="sxs-lookup"><span data-stu-id="99bfa-143">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="99bfa-144">使用者 cookie 登入</span><span class="sxs-lookup"><span data-stu-id="99bfa-144">User sign-in cookies</span></span>
* <span data-ttu-id="99bfa-145">應用 cookie 程式</span><span class="sxs-lookup"><span data-stu-id="99bfa-145">App cookies</span></span>
* <span data-ttu-id="99bfa-146">快取和儲存的網站資料</span><span class="sxs-lookup"><span data-stu-id="99bfa-146">Cached and stored site data</span></span>

<span data-ttu-id="99bfa-147">防止延遲 cookie 和網站資料干擾測試和疑難排解的其中一種方法是：</span><span class="sxs-lookup"><span data-stu-id="99bfa-147">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="99bfa-148">設定瀏覽器</span><span class="sxs-lookup"><span data-stu-id="99bfa-148">Configure a browser</span></span>
  * <span data-ttu-id="99bfa-149">使用瀏覽器進行測試，您可以設定在 cookie 每次關閉瀏覽器時刪除所有和網站資料。</span><span class="sxs-lookup"><span data-stu-id="99bfa-149">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="99bfa-150">請確定已手動關閉瀏覽器，或透過 IDE 來變更應用程式、測試使用者或提供者設定。</span><span class="sxs-lookup"><span data-stu-id="99bfa-150">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="99bfa-151">使用自訂命令，在 Visual Studio 中以 incognito 或私用模式開啟瀏覽器：</span><span class="sxs-lookup"><span data-stu-id="99bfa-151">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="99bfa-152">從 Visual Studio 的 [**執行**] 按鈕開啟 **[流覽方式**] 對話方塊。</span><span class="sxs-lookup"><span data-stu-id="99bfa-152">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="99bfa-153">選取 [新增] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="99bfa-153">Select the **Add** button.</span></span>
  * <span data-ttu-id="99bfa-154">在 [ **程式** ] 欄位中提供瀏覽器的路徑。</span><span class="sxs-lookup"><span data-stu-id="99bfa-154">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="99bfa-155">下列可執行檔路徑是適用于 Windows 10 的一般安裝位置。</span><span class="sxs-lookup"><span data-stu-id="99bfa-155">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="99bfa-156">如果您的瀏覽器安裝在不同的位置，或您未使用 Windows 10，請提供瀏覽器可執行檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="99bfa-156">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="99bfa-157">Microsoft Edge： `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="99bfa-157">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="99bfa-158">Google Chrome： `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="99bfa-158">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="99bfa-159">Mozilla Firefox： `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="99bfa-159">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="99bfa-160">在 [ **引數** ] 欄位中，提供瀏覽器用來在 incognito 或私用模式中開啟的命令列選項。</span><span class="sxs-lookup"><span data-stu-id="99bfa-160">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="99bfa-161">某些瀏覽器需要應用程式的 URL。</span><span class="sxs-lookup"><span data-stu-id="99bfa-161">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="99bfa-162">Microsoft Edge：使用 `-inprivate` 。</span><span class="sxs-lookup"><span data-stu-id="99bfa-162">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="99bfa-163">Google Chrome：使用 `--incognito --new-window {URL}` ，其中預留位置 `{URL}` 是要開啟的 URL (例如 `https://localhost:5001`) 。</span><span class="sxs-lookup"><span data-stu-id="99bfa-163">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="99bfa-164">Mozilla Firefox：使用 `-private -url {URL}` ，其中預留位置 `{URL}` 是要開啟的 URL (例如 `https://localhost:5001`) 。</span><span class="sxs-lookup"><span data-stu-id="99bfa-164">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="99bfa-165">在 [ **易記名稱** ] 欄位中提供名稱。</span><span class="sxs-lookup"><span data-stu-id="99bfa-165">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="99bfa-166">例如： `Firefox Auth Testing` 。</span><span class="sxs-lookup"><span data-stu-id="99bfa-166">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="99bfa-167">選取 [ **確定]** 按鈕。</span><span class="sxs-lookup"><span data-stu-id="99bfa-167">Select the **OK** button.</span></span>
  * <span data-ttu-id="99bfa-168">若要避免需要針對使用應用程式測試的每個反復專案選取瀏覽器設定檔，請將設定檔設定為預設值，並將 [ **設定為預設值** ] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="99bfa-168">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="99bfa-169">確定 IDE 已關閉瀏覽器，以進行應用程式、測試使用者或提供者設定的任何變更。</span><span class="sxs-lookup"><span data-stu-id="99bfa-169">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="app-upgrades"></a><span data-ttu-id="99bfa-170">應用程式升級</span><span class="sxs-lookup"><span data-stu-id="99bfa-170">App upgrades</span></span>

<span data-ttu-id="99bfa-171">在開發電腦上升級 .NET Core SDK 或變更應用程式內的套件版本之後，正常運作的應用程式可能會立即失敗。</span><span class="sxs-lookup"><span data-stu-id="99bfa-171">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="99bfa-172">在某些情況下，執行主要升級時，不一致的套件可能會中斷應用程式。</span><span class="sxs-lookup"><span data-stu-id="99bfa-172">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="99bfa-173">大多數這些問題都可依照下列指示來進行修正：</span><span class="sxs-lookup"><span data-stu-id="99bfa-173">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="99bfa-174">從命令列介面執行，以清除本機系統的 NuGet 套件快取 [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) 。</span><span class="sxs-lookup"><span data-stu-id="99bfa-174">Clear the local system's NuGet package caches by executing [`dotnet nuget locals all --clear`](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>
1. <span data-ttu-id="99bfa-175">刪除專案的 `bin` 和 `obj` 資料夾。</span><span class="sxs-lookup"><span data-stu-id="99bfa-175">Delete the project's `bin` and `obj` folders.</span></span>
1. <span data-ttu-id="99bfa-176">還原並重建專案。</span><span class="sxs-lookup"><span data-stu-id="99bfa-176">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="99bfa-177">重新部署應用程式之前，請先刪除伺服器上 [部署] 資料夾中的所有檔案。</span><span class="sxs-lookup"><span data-stu-id="99bfa-177">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

> [!NOTE]
> <span data-ttu-id="99bfa-178">不支援使用與應用程式的目標架構不相容的套件版本。</span><span class="sxs-lookup"><span data-stu-id="99bfa-178">Use of package versions incompatible with the app's target framework isn't supported.</span></span> <span data-ttu-id="99bfa-179">如需封裝的相關資訊，請使用 [NuGet 資源庫](https://www.nuget.org) 或 [FuGet 套件瀏覽器](https://www.fuget.org)。</span><span class="sxs-lookup"><span data-stu-id="99bfa-179">For information on a package, use the [NuGet Gallery](https://www.nuget.org) or [FuGet Package Explorer](https://www.fuget.org).</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="99bfa-180">執行伺服器應用程式</span><span class="sxs-lookup"><span data-stu-id="99bfa-180">Run the Server app</span></span>

<span data-ttu-id="99bfa-181">測試及疑難排解託管解決方案時 Blazor ，請確定您是從專案執行應用程式 **`Server`** 。</span><span class="sxs-lookup"><span data-stu-id="99bfa-181">When testing and troubleshooting a hosted Blazor solution, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="99bfa-182">例如，在 Visual Studio 中，使用下列任何方法來啟動應用程式之前，請先確認已在 [ **方案 Explorer** ] 中反白顯示伺服器專案：</span><span class="sxs-lookup"><span data-stu-id="99bfa-182">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="99bfa-183">選取 [執行] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="99bfa-183">Select the **Run** button.</span></span>
* <span data-ttu-id="99bfa-184">從功能表使用 **Debug**  >  **開始調試**。</span><span class="sxs-lookup"><span data-stu-id="99bfa-184">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="99bfa-185">按 <kbd>F5</kbd>。</span><span class="sxs-lookup"><span data-stu-id="99bfa-185">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="99bfa-186">檢查 JSON Web 權杖的內容 (JWT) </span><span class="sxs-lookup"><span data-stu-id="99bfa-186">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="99bfa-187">若要解碼 (JWT) 的 JSON Web 權杖，請使用 Microsoft 的 [jwt.ms](https://jwt.ms/) 工具。</span><span class="sxs-lookup"><span data-stu-id="99bfa-187">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="99bfa-188">UI 中的值永遠不會離開您的瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="99bfa-188">Values in the UI never leave your browser.</span></span>

<span data-ttu-id="99bfa-189">顯示) 的範例編碼 JWT (縮寫：</span><span class="sxs-lookup"><span data-stu-id="99bfa-189">Example encoded JWT (shortened for display):</span></span>

> <span data-ttu-id="99bfa-190">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ...bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span><span class="sxs-lookup"><span data-stu-id="99bfa-190">eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1j ... bQdHBHGcQQRbW7Wmo6SWYG4V_bU55Ug_PW4pLPr20tTS8Ct7_uwy9DWrzCMzpD-EiwT5IjXwlGX3IXVjHIlX50IVIydBoPQtadvT7saKo1G5Jmutgq41o-dmz6-yBMKV2_nXA25Q</span></span>

<span data-ttu-id="99bfa-191">針對 Azure AAD B2C 進行驗證之應用程式的工具所解碼的範例 JWT：</span><span class="sxs-lookup"><span data-stu-id="99bfa-191">Example JWT decoded by the tool for an app that authenticates against Azure AAD B2C:</span></span>

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
