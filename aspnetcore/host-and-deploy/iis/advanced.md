---
title: 進階組態
author: rick-anderson
description: 使用 ASP.NET Core 模組和 Internet Information Services (IIS) 的 Advanced configuration。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: ad2480faeea2f07e51585f5bc6a1c63b3a0b1668
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755243"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a>ASP.NET Core 模組和 IIS 的 Advanced configuration

本文涵蓋 ASP.NET Core 模組和 IIS 的 advanced configuration 選項和案例。

## <a name="modify-the-stack-size"></a>修改堆疊大小

*只有在使用同進程裝載模型時才適用。*

使用檔案中的 `stackSize` 設定（以位元組為單位）來設定受控堆疊大小 `web.config` 。 預設大小為1048576個位元組 (1 MB) 。 下列範例會將堆疊大小變更為 2 MB (2097152 個位元組) ：

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Proxy 組態使用 HTTP 通訊協定和配對權杖

僅適用於跨處理序裝載。**

在 ASP.NET Core 模組與 Kestrel 之間建立的 Proxy 會使用 HTTP 通訊協定。 沒有從伺服器外的位置竊聽模組與 Kestrel 之間流量的風險。

配對權杖用來保證 Kestrel 所接收的要求已由 IIS 代理，而且不是來自其他來源。 模組會建立配對權杖，並將其設定成環境變數 (`ASPNETCORE_TOKEN`)。 配對權杖也會設定成每個代理要求的標頭 (`MS-ASPNETCORE-TOKEN`)。 IIS 中介軟體會檢查其收到的每個要求，以確認配對權杖的標頭值符合環境變數值。 如果權杖值不相符，將記錄並拒絕要求。 使用者無法從伺服器外的位置存取配對權杖環境變數，以及模組與 Kestrel 之間的流量。 在不知道配對權杖值的情況下，攻擊者無法略過 IIS 中介軟體的檢查送出要求。

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>具有 IIS 共用設定的 ASP.NET Core 模組

ASP.NET Core 模組安裝程式會以帳戶的許可權執行 `TrustedInstaller` 。 由於本機系統帳戶對於 IIS 共用設定所使用的共用路徑沒有 modify 許可權，因此，安裝程式在嘗試于共用上的檔案中設定模組設定時，會擲回「拒絕存取」錯誤 `applicationHost.config` 。

在與 IIS 安裝相同的電腦上使用 IIS 共用設定時，請執行 ASP.NET Core 裝載套件組合安裝程式並將 `OPT_NO_SHARED_CONFIG_CHECK` 參數設為 `1`：

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

若共用設定的路徑位於與 IIS 安裝不同的電腦上，請遵循下列步驟：

1. 停用「IIS 共用設定」。
1. 執行安裝程式。
1. 將更新的檔案匯出 `applicationHost.config` 至檔案共用。
1. 重新啟用「IIS 共用設定」。

## <a name="data-protection"></a>資料保護

[ASP.NET Core 資料保護堆疊](xref:security/data-protection/introduction)是由數個 ASP.NET Core [中介軟體](xref:fundamentals/middleware/index)所使用，包括用於驗證的中介軟體。 即使資料保護 API 不是由使用者程式碼呼叫，仍應使用部署指令碼或是在使用者程式碼中設定資料保護，以建立持續性的密碼編譯[金鑰存放區](xref:security/data-protection/implementation/key-management)。 如不設定資料保護，金鑰會保留在記憶體中，並於應用程式重新啟動時捨棄。

當應用程式重新開機時，如果資料保護金鑰環形儲存在記憶體中：

* 所有 cookie 的驗證權杖都會失效。 
* 當使用者提出下一個要求時，需要再次登入。 
* 所有以 Keyring 保護的資料都無法再解密。 這可能包括 [CSRF 權杖](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) 和 [ASP.NET Core MVC TempData cookie s](xref:fundamentals/app-state#tempdata)。

若要在 IIS 下設定資料保護以保存 Keyring，請使用下列其中**一種**方法：

* **建立資料保護登錄機碼**

  ASP.NET Core apps 使用的資料保護金鑰會儲存在應用程式外部的登錄中。 若要保存指定應用程式的金鑰，請為應用程式集區建立登錄機碼。

  若為獨立的非Web 伺服陣列 IIS 安裝，請針對搭配使用 ASP.NET Core 應用程式的每個應用程式集區，使用[資料保護 Provision-AutoGenKeys.ps1 PowerShell 指令碼](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1)。 此腳本會在 HKLM 登錄中建立登錄機碼，只有應用程式的應用程式集區的背景工作進程帳戶才能存取。 在待用期間使用 DPAPI 和全電腦金鑰加密金鑰。

  在 web 伺服陣列案例中，您可以將應用程式設定為使用 UNC 路徑來儲存其資料保護金鑰環形。 依預設，金鑰不會加密。 確定網路共用的檔案許可權僅限於執行應用程式的 Windows 帳戶。 可以使用 X509 憑證來保護待用的金鑰。 請考慮允許使用者上傳憑證的機制。 將憑證放入使用者的受信任憑證存放區，並確保在執行使用者應用程式的所有電腦上都能使用這些憑證。 如需詳細資訊，請參閱<xref:security/data-protection/configuration/overview>。

* **設定 IIS 應用程式集區載入使用者設定檔**

  此設定位在應用程式集區 [進階設定]**** 下的 [處理序模型]**** 區段中。 將 [載入使用者設定檔]**** 設為 `True`。 當設定為 `True` 時，金鑰會儲存在使用者設定檔目錄中，且使用具有使用者帳戶專屬金鑰的 DPAPI 保護。 金鑰會保存在 `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` 資料夾中。

  您也必須啟用應用程式集區的[ `setProfileEnvironment` 屬性](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)。 `setProfileEnvironment` 的預設值為 `true`。 在某些情況下 (例如 Windows OS)，`setProfileEnvironment` 會設為 `false`。 如果金鑰並未如預期地儲存在使用者設定檔目錄中：

  1. 瀏覽到 `%windir%/system32/inetsrv/config` 資料夾。
  1. 開啟 `applicationHost.config` 檔案。
  1. 找出 `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` 元素。
  1. 確認 `setProfileEnvironment` 屬性不存在 (其預設值為 `true`)，或明確地將屬性值設為 `true`。

* **將檔案系統當作 Keyring 存放區使用**

  調整應用程式程式碼，以[將檔案系統當作 Keyring 存放區使用](xref:security/data-protection/configuration/overview)。 使用 X509 憑證來保護 Keyring，並確保憑證是受信任的憑證。 如果憑證為自我簽署，請將憑證放在受信任的根存放區。

  在 Web 伺服陣列中使用 IIS 時：

  * 請使用所有電腦都可以存取的檔案共用。
  * 將 X509 憑證部署到每一部電腦。 [在程式碼中設定資料保護](xref:security/data-protection/configuration/overview)。

* **設定適用于資料保護的全電腦原則**

  針對取用資料保護 Api 的所有應用程式，資料保護系統的支援有限，可設定預設的全 [電腦原則](xref:security/data-protection/configuration/machine-wide-policy) 。 如需詳細資訊，請參閱<xref:security/data-protection/introduction>。

## <a name="iis-configuration"></a>IIS 組態

**Windows Server 作業系統**

啟用**網頁伺服器 (IIS)** 伺服器角色，並建立角色服務。

1. 使用來自 [管理]**** 功能表的 [新增角色及功能]**** 精靈，或是 [伺服器管理員]**** 中的連結。 在**伺服器角色**步驟中，核取 [網頁伺服器 (IIS)]**** 方塊。

   ![在選取伺服器角色步驟中選取網頁伺服器 IIS 角色。](index/_static/server-roles-ws2016.png)

1. 在 [功能]**** 步驟之後，[角色服務]**** 步驟會針對網頁伺服器 (IIS) 進行載入。 選取所需的 IIS 角色服務或接受所提供的預設角色服務。

   ![在選取角色服務步驟中，選取預設的角色服務。](index/_static/role-services-ws2016.png)

   **Windows 驗證 (選擇性)**  
   若要啟用 Windows 驗證，請展開下列節點：**網頁伺服器**  >  **安全性**。 選取 [Windows 驗證]**** 功能。 如需詳細資訊，請參閱[Windows 驗證 `<windowsAuthentication>` ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。

   **WebSocket (選擇性)**  
   WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。 若要啟用 websocket，請展開下列節點：**網頁伺服器**  >  **應用程式開發**。 選取 [WebSocket 通訊協定]**** 功能。 如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。

1. 透過**確認**步驟繼續作業，安裝網頁伺服器角色和服務。 在安裝 **Web 服務器 (iis) ** 角色之後，不需要重新開機伺服器。

**Windows 桌面作業系統**

啟用 [IIS 管理主控台]**** 和 [World Wide Web 服務]****。

1. 流覽至**主控台**  >  **程式**的 [  >  **程式和功能]，**  >  **開啟或關閉**畫面) 左側 (的 [Windows 功能]。

1. 開啟 [Internet Information Services]**** 節點。 開啟 [Web 管理工具]**** 節點。

1. 核取 [IIS 管理主控台]**** 方塊。

1. [World Wide Web Services] (全球資訊網服務)**** 核取方塊。

1. 接受**全球資訊網服務**的預設功能，或自訂 IIS 功能。

   **Windows 驗證 (選擇性)**  
   若要啟用 Windows 驗證，請展開下列節點： **World Wide Web 服務**  >  **安全性**。 選取 [Windows 驗證]**** 功能。 如需詳細資訊，請參閱[Windows 驗證 `<windowsAuthentication>` ](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/)和[設定 Windows 驗證](xref:security/authentication/windowsauth)。

   **WebSocket (選擇性)**  
   WebSocket 由 ASP.NET Core 1.1 或更新版本所支援。 若要啟用 websocket，請展開下列節點： **World Wide Web 服務**  >  **應用程式開發功能**。 選取 [WebSocket 通訊協定]**** 功能。 如需詳細資訊，請參閱 [WebSockets](xref:fundamentals/websockets)。

1. 若 IIS 安裝需要重新啟動，請重新啟動系統。

![選取 [Windows 功能] 中的 [IIS 管理主控台] 和 [World Wide Web Services] (全球資訊網服務)。](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a>虛擬目錄

ASP.NET Core 應用程式不支援 [IIS 虛擬目錄](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)。 應用程式能以[子應用程式](#sub-applications)的形式裝載。

## <a name="sub-applications"></a>子應用程式

ASP.NET Core 應用程式能以 [IIS 子應用程式](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications)的形式裝載。 子應用程式的路徑會成為根應用程式 URL 的一部分。

子應用程式內的靜態資產連結應該使用波狀符號與斜線 (`~/`) 標記法。 波狀符號與斜線標記法會觸發[標記協助程式](xref:mvc/views/tag-helpers/intro)以將子應用程式的路徑基底附加到轉譯的相對連結前面。 針對位於 `/subapp_path` 的子應用程式，使用 `src="~/image.png"` 連結的影像會轉譯為 `src="/subapp_path/image.png"`。 根應用程式的靜態檔案中介軟體不會處理靜態檔案要求。 要求會由子應用程式的靜態檔案中介軟體處理。

若靜態資產的 `src` 屬性是設定為絕對路徑 (例如，`src="/image.png"`)，會以不使用子應用程式路徑基底的方式轉譯連結。 根應用程式的靜態檔案中介軟體會嘗試從根應用程式的 [webroot](xref:fundamentals/index#web-root) 提供資產，這會導致「404 - 找不到」** 回應 (除非根應用程式可存取靜態資產)。

裝載 ASP.NET Core 應用程式做為另一個 ASP.NET Core 應用程式下的子應用程式：

1. 為子應用程式建立應用程式集區。 將 [.NET CLR 版本]**** 設定為 [沒有受控碼]****，因為會將核心通用語言執行平台 (CoreCLR) 開機以在背景工作處理序中裝載應用程式，而非在桌面 CLR (.NET CLR) 中裝載。

1. 使用根網站下資料夾中的子應用程式在 IIS 管理員中新增根網站。

1. 以滑鼠右鍵按一下 IIS 管理員中的子應用程式資料夾，然後選取 [轉換成應用程式]****。

1. 在 [新增應用程式]**** 對話方塊中，使用 [應用程式集區]**** 的[選取]**** 按鈕來指派您為子應用程式建立的應用程式集區。 選取 [確定]  。

將不同的應用程式集區指派給子應用程式是使用同處理序裝載模型。

如需有關同進程裝載模型和設定 ASP.NET Core 模組的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module> 。

## <a name="application-pools"></a>應用程式集區

應用程式集區隔離取決於裝載模型：

* 同進程裝載：應用程式必須在不同的應用程式集區中執行。
* 跨進程裝載：建議您在應用程式本身的應用程式集區中執行每個應用程式，以隔離彼此的應用程式。

IIS [新增網站]**** 對話方塊預設每個應用程式皆為單一應用程式集區。 當提供**網站名稱**時，文字會自動轉移至 [應用程式集區]**** 文字方塊。 新增網站時，會使用該網站名稱建立新的應用程式集區。

## <a name="application-pool-no-locidentity"></a>應用程式集區 Identity

應用程式集區身分識別帳戶可讓應用程式在唯一的帳戶下執行，不必建立及管理網域或本機帳戶。 在 IIS 8.0 或更新版本中，IIS 管理背景工作處理序 (WAS) 會使用新的應用程式集區名稱建立虛擬帳戶，並預設在此帳戶下執行應用程式集區的背景工作處理序。 在 [IIS 管理主控台] 中，于應用程式集區的 [ **Advanced Settings** ] 底下，確定 **Identity** 已設定為 [使用 `ApplicationPoolIdentity` ：

![應用程式集區進階設定對話方塊](index/_static/apppool-identity.png)

IIS 管理程序會在 Windows 安全系統中，以應用程式集區的名稱建立安全識別碼。 可使用此身分識別來保護資源。 不過，此身分識別不是真正的使用者帳戶，也不會顯示在 Windows 使用者管理主控台中。

如果 IIS 背景工作處理序需要提升應用程式的存取權限，請修改包含應用程式的目錄存取控制清單 (ACL)：

1. 開啟 Windows 檔案總管，巡覽至目錄。

1. 以滑鼠右鍵按一下目錄並選取 [屬性]****。

1. 依序選取 [安全性]**** 索引標籤下的 [編輯]**** 按鈕和 [新增]**** 按鈕。

1. 選取 [位置]**** 按鈕，並確定選取系統。

1. 輸入 `IIS AppPool\{APP POOL NAME}` 格式，其中預留位置 `{APP POOL NAME}` 是應用程式集區名稱，在 [ **輸入物件名稱來選取** ] 區域中。 選取 [檢查名稱]**** 按鈕。 若為 *DefaultAppPool* ，請使用來檢查名稱 `IIS AppPool\DefaultAppPool` 。 選取 [ **檢查名稱** ] 按鈕時，的值 `DefaultAppPool` 會在 [物件名稱] 區域中指出。 您無法直接將應用程式集區名稱輸入至物件名稱區域。 `IIS AppPool\{APP POOL NAME}`檢查物件名稱時，請使用格式，其中預留位置 `{APP POOL NAME}` 是應用程式集區名稱。

   ![針對應用程式資料夾選取使用者或群組對話方塊：在選取 [檢查名稱] 之前，"DefaultAppPool" 這個應用程式集區名稱在物件名稱區域中會附加至 "IIS AppPool\"。](index/_static/select-users-or-groups-1.png)

1. 選取 [確定]  。

   ![針對應用程式資料夾選取使用者或群組對話方塊：選取 [檢查名稱] 之後，物件名稱 "DefaultAppPool" 會顯示在物件名稱區域中。](index/_static/select-users-or-groups-2.png)

1. 預設應該會授與讀取 &amp; 執行權限。 請視需要提供其他權限。

也可使用 **ICACLS** 工具透過命令提示字元授與存取權限。 使用 *DefaultAppPool*作為範例，將會使用下列命令：

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

如需詳細資訊，請參閱 [icacls](/windows-server/administration/windows-commands/icacls) 主題。

## <a name="http2-support"></a>HTTP/2 支援

在下列 IIS 部署案例中，ASP.NET Core 支援 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：

* 內含式
  * Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本
  * TLS 1.2 或更新版本連線
* 跨處理序
  * Windows Server 2016/Windows 10 或更新版本；IIS 10 或更新版本
  * 公開 Edge Server 連線使用 HTTP/2，但是對 [Kestrel 伺服器](xref:fundamentals/servers/kestrel)的反向 Proxy 連線使用 HTTP/1.1。
  * TLS 1.2 或更新版本連線

若為建立 HTTP/2 連接時的同進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/2` 。 若為建立 HTTP/2 連接時的跨進程部署，則為 [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 報告 `HTTP/1.1` 。

如需有關同處理序和跨處理序主控模型的詳細資訊，請參閱 <xref:host-and-deploy/aspnet-core-module>。

HTTP/2 預設為啟用。 如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。 如需使用 IIS 部署之 HTTP/2 設定的詳細資訊，請參閱 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。

## <a name="cors-preflight-requests"></a>CORS 預檢要求

*此節只適用於以 .NET Framework 為目標的 ASP.NET Core 應用程式。*

針對以 .NET Framework 為目標的 ASP.NET Core 應用程式，在 IIS 中OPTIONS 要求預設不會傳遞到應用程式。 若要瞭解如何在中設定應用程式的 IIS 處理常式 `web.config` 來傳遞選項要求，請參閱 [在 ASP.NET Web API 2： CORS 的運作方式中啟用跨原始來源要求](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works)。

## <a name="application-initialization-module-and-idle-timeout"></a>應用程式初始化模組與閒置逾時

在 IIS 中由 ASP.NET Core 模組版本 2 裝載時：

* [應用程式初始化模組](#application-initialization-module)：應用程式裝載的同 [進程](xref:host-and-deploy/iis/in-process-hosting) 或 [跨進程](xref:host-and-deploy/iis/out-of-process-hosting) 可設定為在背景工作進程重新開機或伺服器重新開機時自動啟動。
* [閒置 Timeout](#idle-timeout)：應用程式裝載的同 [進程](xref:host-and-deploy/iis/in-process-hosting) 可設定為在無活動期間停止執行時間。

### <a name="application-initialization-module"></a>應用程式初始化模組

*套用到應用程式裝載同處理序與非同處理序。*

[IIS 應用程式初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)是一項 IIS 功能，可在應用程式集區啟動或回收時，將 HTTP 要求傳送給應用程式。 要求會觸發應用程式啟動。 根據預設，IIS 會發出應用程式根 URL (`/`) 的要求以初始化應用程式 (如需有關設定的詳細資訊，請參閱[額外資源](#application-initialization-module-and-idle-timeout-additional-resources))。

確認已啟用「IIS 應用程式初始化」角色功能：

在 Windows 7 或更新的電腦系統上，當在本機使用 IIS 時：

1. 流覽至**主控台**  >  **程式**的 [  >  **程式和功能]，**  >  **開啟或關閉**畫面) 左側 (的 [Windows 功能]。
1. 開啟**Internet Information Services**  >  **World Wide Web 服務**  >  **應用程式開發功能**。
1. 選取 [應用程式初始化]**** 的核取方塊。

在 Windows Server 2008 R2 或更新版本上：

1. 開啟「新增角色與功能精靈」****。
1. 在 [選取角色服務]**** 面板中，開啟 [應用程式開發]**** 節點。
1. 選取 [應用程式初始化]**** 的核取方塊。

使用下列任一方式為網站啟用應用程式初始化模組：

* 使用 IIS 管理員：

  1. 選取 [連線]**** 面板中的 [應用程式集區]****。
  1. 以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]****。
  1. 預設 **啟動模式** 為 `OnDemand` 。 將 [ **啟動模式]** 設定為 `AlwaysRunning` 。 選取 [確定]  。
  1. 開啟 [連線]**** 面板中的 [站台]**** 節點。
  1. 以滑鼠右鍵按一下應用程式，然後選取 [**管理網站**  >  **Advanced Settings**]。
  1. 預設 **預先載入已啟用** 的設定為 `False` 。 將 [ **預先載入已啟用** ] 設定為 `True` 。 選取 [確定]  。

* 使用 `web.config` ，將設定為的專案加入 `<applicationInitialization>` `doAppInitAfterRestart` `true` 至 `<system.webServer>` 應用程式檔案中的元素 `web.config` ：

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>閒置逾時

*僅適用於應用程式裝載同處理序。*

若要防止應用程式閒置，請使用 IIS 管理員設定應用程式集區的閒置逾時：

1. 選取 [連線]**** 面板中的 [應用程式集區]****。
1. 以滑鼠右鍵按一下清單中應用程式的應用程式集區，然後選取 [進階設定]****。
1. 預設 **閒置時間 (分鐘) ** 為 `20` 分鐘。 將 **閒置超時 (分鐘) ** 設定為 `0` (零) 。 選取 [確定]  。
1. 回收背景工作處理序。

若要防止應用程式裝載[非同處理序](xref:host-and-deploy/iis/out-of-process-hosting)逾時，請使用下列任一方式：

* 從外部服務對應用程式執行 Ping 以讓它繼續執行。
* 若應用程式只裝載背景服務，避免 IIS 裝載並使用 [Windows 服務來裝載 ASP.NET Core 應用程式](xref:host-and-deploy/windows-service)。

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>應用程式初始化模組與閒置逾時額外資源

* [IIS 8.0 應用程式初始化](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [應用程式 `<applicationInitialization>` 初始化](/iis/configuration/system.webserver/applicationinitialization/)。
* [應用程式集 `<processModel>` 區的進程模型設定](/iis/configuration/system.applicationhost/applicationpools/add/processmodel)。

## <a name="module-schema-and-configuration-file-locations"></a>模組、結構描述及設定檔位置

### <a name="module"></a>模組

**IIS (x86/amd64) **：

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64) **：

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>結構描述

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>組態

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio： `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* Cli： `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

您可以藉由在檔案中搜尋來找到檔案 `aspnetcore` `applicationHost.config` 。
