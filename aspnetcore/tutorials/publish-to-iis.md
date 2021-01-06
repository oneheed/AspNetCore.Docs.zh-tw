---
title: 將 ASP.NET Core 應用程式發佈到 IIS
author: rick-anderson
description: 了解如何在 IIS 伺服器上裝載 ASP.NET Core 應用程式。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/03/2019
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
uid: tutorials/publish-to-iis
ms.openlocfilehash: 0f70b5f12b9097f8710c9641404b3e085968fc3f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753149"
---
# <a name="publish-an-aspnet-core-app-to-iis"></a>將 ASP.NET Core 應用程式發佈到 IIS

本教學課程會示範如何在 IIS 伺服器上裝載 ASP.NET Core 應用程式。

本教學課程涵蓋下列主題：

> [!div class="checklist"]
> * 在 Windows Server 上安裝 .NET Core 裝載套件組合。
> * 在 IIS 管理員中建立 IIS 網站。
> * 部署 ASP.NET Core 應用程式。

## <a name="prerequisites"></a>先決條件

* 在部署電腦上安裝 [.NET Core SDK](/dotnet/core/sdk)。
* 使用 **Web Server (IIS)** 伺服器角色設定的 Windows Server。 若您的伺服器並未設為使用 IIS 來裝載網站，請遵循 <xref:host-and-deploy/iis/index#iis-configuration> 一文中＜IIS 組態＞一節內的指導，然後返回本教學課程。

> [!WARNING]
> **IIS 組態和網站安全性涉及本教學課程並未涵蓋的概念。** 請諮詢 [Microsoft IIS 文件](https://www.iis.net/)和[使用 IIS 進行裝載的 ASP.NET Core 文章](xref:host-and-deploy/iis/index)，再於 IIS 上裝載生產應用程式。
>
> 本教學課程中尚未涵蓋的 IIS 裝載重要案例包括：
>
> * [建立 ASP.NET Core 資料保護的登錄區](xref:host-and-deploy/iis/advanced#data-protection)
> * [設定應用程式集區的存取控制清單 (ACL)](xref:host-and-deploy/iis/advanced#application-pool-identity)
> * 為了聚焦在 IIS 部署概念，本教學課程會在並未於 IIS 上設定 HTTPS 安全性的情況下部署應用程式。 如需裝載啟用 HTTPS 通訊協定應用程式的詳細資訊，請參閱本文[＜其他資源＞](#additional-resources)一節中的安全性主題。 <xref:host-and-deploy/iis/index> 一文中提供了裝載 ASP.NET Core 應用程式的進一步指導。

## <a name="install-the-net-core-hosting-bundle"></a>安裝 .NET Core 裝載套件組合

在 IIS 伺服器上安裝 *.NET Core 裝載套件組合*。 套件組合會安裝 .NET Core 執行時間、.NET Core 程式庫和 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。 此模組可讓 ASP.NET Core 應用程式在 IIS 背後執行。

使用下列連結下載安裝程式：

[目前的 .NET Core 裝載套件組合安裝程式 (直接下載)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. 在 IIS 伺服器上執行安裝程式。

1. 重新開機伺服器，或 `net stop was /y` 在命令列介面中執行，然後執行 `net start w3svc` 。

## <a name="create-the-iis-site"></a>建立 IIS 網站

1. 在 IIS 伺服器上，建立資料夾以包含應用程式的發佈資料夾和檔案。 在下列步驟中，您提供資料夾路徑給 IIS，作為應用程式的實體路徑。 如需應用程式之部署資料夾和檔案配置的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure>。

1. 在 [IIS 管理員] 中 **，在 [連線] 面板中** 開啟伺服器的節點。 以滑鼠右鍵按一下 [網站] 資料夾。 從操作功能表選取 [新增網站]。

1. 提供 **網站名稱**，並將 **實體路徑** 設定為您建立的應用程式部署資料夾。 提供 **繫結** 組態，然後選取 [確定] 來建立網站。

   > [!WARNING]
   > 請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。 最上層萬用字元繫結可能暴露您的應用程式安全性弱點。 這對強式與弱式萬用字元皆適用。 請使用明確主機名稱，而非萬用字元。 若您擁有整個父網域 (與具弱點的 `*.com` 相對) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 就沒有此安全性風險。 如需詳細資訊，請參閱 [rfc7230 5.4 節](https://tools.ietf.org/html/rfc7230#section-5.4)。

1. 確認處理序模型身分識別具有適當的權限。

   如果應用程式集區的預設身分識別 (**進程模型**  >  **Identity**) 已從變更 `ApplicationPoolIdentity` 為其他身分識別，請確認新的身分識別具有存取應用程式資料夾、資料庫和其他必要資源的必要許可權。 例如，應用程式集區需要針對應用程式讀取和寫入檔案的資料夾取得讀取和寫入權限。

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a>建立 ASP.NET Core Razor 頁面應用程式

遵循 <xref:getting-started> 教學課程來建立 Razor 頁面應用程式。

## <a name="publish-and-deploy-the-app"></a>發佈及部署應用程式

「發佈應用程式」表示產生可由伺服器裝載的編譯應用程式。 「部署應用程式」表示將發佈後的應用程式移動到裝載系統。 發佈步驟會由 [.NET Core SDK](/dotnet/core/sdk) 處理，部署步驟則是可以透過各種方式處理。 本教學課程採用「資料夾」部署方法，其中：
 
* 應用程式會發佈到資料夾。
* 資料夾內容會移動到 IIS 網站的資料夾 (指向 IIS 管理員中網站的 **實體路徑**)。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 以滑鼠右鍵按一下 **方案總管** 中的專案，然後選取 [ **發行**]。
1. 在 [挑選發佈目標] 對話方塊中，選取 [資料夾] 發佈選項。
1. 設定 [資料夾或檔案共用] 路徑。
   * 若您針對部署電腦上提供為網路共用的 IIS 網站建立資料夾，則請提供指向共用的路徑。 目前的使用者必須具備寫入存取權限才能發佈到共用。
   * 如果您無法直接部署至 IIS 伺服器上的 IIS 網站資料夾，請發行至卸載式媒體上的資料夾，並實際將已發佈的應用程式移至伺服器上的 IIS 網站資料夾，也就是 IIS 管理員中的網站 **實體路徑** 。 將資料夾的內容移 `bin/Release/{TARGET FRAMEWORK}/publish` 至伺服器上的 iis 網站資料夾，也就是 [Iis 管理員] 中的網站 **實體路徑** 。
1. 選取 [發佈] 按鈕。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

1. 在命令殼層中，使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令，以發行組態來發佈應用程式：

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. 將資料夾的內容移 `bin/Release/{TARGET FRAMEWORK}/publish` 至伺服器上的 iis 網站資料夾，也就是 [Iis 管理員] 中的網站 **實體路徑** 。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在 [解決方案] 中按一下專案，然後選取 [發佈] > [發佈到資料夾]。
1. 設定 [選取資料夾] 路徑。
   * 若您針對部署電腦上提供為網路共用的 IIS 網站建立資料夾，則請提供指向共用的路徑。 目前的使用者必須具備寫入存取權限才能發佈到共用。
   * 若您無法直接部署到 IIS 伺服器上的 IIS 網站資料夾，請發佈到抽取式媒體上資料夾並透過物理方式將所發佈應用程式實際移動到伺服器上的 IIS 網站資料夾，即 IIS 管理員中的 **實體路徑**。 將資料夾的內容移 `bin/Release/{TARGET FRAMEWORK}/publish` 至伺服器上的 iis 網站資料夾，也就是 [Iis 管理員] 中的網站 **實體路徑** 。
1. 選取 [發佈] 按鈕。

---

## <a name="browse-the-website"></a>瀏覽網站

應用程式會在收到第一個要求時，在瀏覽器中提供存取。 在您於 IIS 管理員中為網站建立的端點繫結處，對應用程式發出要求。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 在 Windows Server 上安裝 .NET Core 裝載套件組合。
> * 在 IIS 管理員中建立 IIS 網站。
> * 部署 ASP.NET Core 應用程式。

若要深入了解在 IIS 上裝載 ASP.NET Core 應用程式，請參閱 IIS 概觀文章：

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a>其他資源

### <a name="articles-in-the-aspnet-core-documentation-set"></a>ASP.NET Core 文件集中的文章

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a>與 ASP.NET Core 應用程式部署相關的文章

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [使用 Visual Studio for Mac 將 Web 應用程式發佈到資料夾](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a>IIS HTTPS 組態的相關文章

* [在 IIS 管理員中設定 SSL](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [如何在 IIS 上設定 SSL](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a>IIS 和 Windows Server 的相關文章

* [Microsoft IIS 官方網站](https://www.iis.net/)
* [Windows Server 技術內容庫](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a>IIS 系統管理員的部署資源

* [IIS 文件](/iis)
* [IIS 中的 IIS 管理員使用者入門](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [.NET Core 應用程式部署](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

