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
# <a name="publish-an-aspnet-core-app-to-iis"></a><span data-ttu-id="cc6b1-103">將 ASP.NET Core 應用程式發佈到 IIS</span><span class="sxs-lookup"><span data-stu-id="cc6b1-103">Publish an ASP.NET Core app to IIS</span></span>

<span data-ttu-id="cc6b1-104">本教學課程會示範如何在 IIS 伺服器上裝載 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-104">This tutorial shows how to host an ASP.NET Core app on an IIS server.</span></span>

<span data-ttu-id="cc6b1-105">本教學課程涵蓋下列主題：</span><span class="sxs-lookup"><span data-stu-id="cc6b1-105">This tutorial covers the following subjects:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cc6b1-106">在 Windows Server 上安裝 .NET Core 裝載套件組合。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-106">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="cc6b1-107">在 IIS 管理員中建立 IIS 網站。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-107">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="cc6b1-108">部署 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-108">Deploy an ASP.NET Core app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cc6b1-109">先決條件</span><span class="sxs-lookup"><span data-stu-id="cc6b1-109">Prerequisites</span></span>

* <span data-ttu-id="cc6b1-110">在部署電腦上安裝 [.NET Core SDK](/dotnet/core/sdk)。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-110">[.NET Core SDK](/dotnet/core/sdk) installed on the development machine.</span></span>
* <span data-ttu-id="cc6b1-111">使用 **Web Server (IIS)** 伺服器角色設定的 Windows Server。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-111">Windows Server configured with the **Web Server (IIS)** server role.</span></span> <span data-ttu-id="cc6b1-112">若您的伺服器並未設為使用 IIS 來裝載網站，請遵循 <xref:host-and-deploy/iis/index#iis-configuration> 一文中＜IIS 組態＞一節內的指導，然後返回本教學課程。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-112">If your server isn't configured to host websites with IIS, follow the guidance in the *IIS configuration* section of the <xref:host-and-deploy/iis/index#iis-configuration> article and then return to this tutorial.</span></span>

> [!WARNING]
> <span data-ttu-id="cc6b1-113">**IIS 組態和網站安全性涉及本教學課程並未涵蓋的概念。**</span><span class="sxs-lookup"><span data-stu-id="cc6b1-113">**IIS configuration and website security involve concepts that aren't covered by this tutorial.**</span></span> <span data-ttu-id="cc6b1-114">請諮詢 [Microsoft IIS 文件](https://www.iis.net/)和[使用 IIS 進行裝載的 ASP.NET Core 文章](xref:host-and-deploy/iis/index)，再於 IIS 上裝載生產應用程式。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-114">Consult the IIS guidance in the [Microsoft IIS documentation](https://www.iis.net/) and the [ASP.NET Core article on hosting with IIS](xref:host-and-deploy/iis/index) before hosting production apps on IIS.</span></span>
>
> <span data-ttu-id="cc6b1-115">本教學課程中尚未涵蓋的 IIS 裝載重要案例包括：</span><span class="sxs-lookup"><span data-stu-id="cc6b1-115">Important scenarios for IIS hosting not covered by this tutorial include:</span></span>
>
> * [<span data-ttu-id="cc6b1-116">建立 ASP.NET Core 資料保護的登錄區</span><span class="sxs-lookup"><span data-stu-id="cc6b1-116">Creation of a registry hive for ASP.NET Core Data Protection</span></span>](xref:host-and-deploy/iis/advanced#data-protection)
> * [<span data-ttu-id="cc6b1-117">設定應用程式集區的存取控制清單 (ACL)</span><span class="sxs-lookup"><span data-stu-id="cc6b1-117">Configuration of the app pool's Access Control List (ACL)</span></span>](xref:host-and-deploy/iis/advanced#application-pool-identity)
> * <span data-ttu-id="cc6b1-118">為了聚焦在 IIS 部署概念，本教學課程會在並未於 IIS 上設定 HTTPS 安全性的情況下部署應用程式。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-118">To focus on IIS deployment concepts, this tutorial deploys an app without HTTPS security configured in IIS.</span></span> <span data-ttu-id="cc6b1-119">如需裝載啟用 HTTPS 通訊協定應用程式的詳細資訊，請參閱本文[＜其他資源＞](#additional-resources)一節中的安全性主題。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-119">For more information on hosting an app enabled for HTTPS protocol, see the security topics in the [Additional resources](#additional-resources) section of this article.</span></span> <span data-ttu-id="cc6b1-120"><xref:host-and-deploy/iis/index> 一文中提供了裝載 ASP.NET Core 應用程式的進一步指導。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-120">Further guidance for hosting ASP.NET Core apps is provided in the <xref:host-and-deploy/iis/index> article.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="cc6b1-121">安裝 .NET Core 裝載套件組合</span><span class="sxs-lookup"><span data-stu-id="cc6b1-121">Install the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="cc6b1-122">在 IIS 伺服器上安裝 *.NET Core 裝載套件組合*。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-122">Install the *.NET Core Hosting Bundle* on the IIS server.</span></span> <span data-ttu-id="cc6b1-123">套件組合會安裝 .NET Core 執行時間、.NET Core 程式庫和 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-123">The bundle installs the .NET Core Runtime, .NET Core Library, and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="cc6b1-124">此模組可讓 ASP.NET Core 應用程式在 IIS 背後執行。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-124">The module allows ASP.NET Core apps to run behind IIS.</span></span>

<span data-ttu-id="cc6b1-125">使用下列連結下載安裝程式：</span><span class="sxs-lookup"><span data-stu-id="cc6b1-125">Download the installer using the following link:</span></span>

[<span data-ttu-id="cc6b1-126">目前的 .NET Core 裝載套件組合安裝程式 (直接下載)</span><span class="sxs-lookup"><span data-stu-id="cc6b1-126">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

1. <span data-ttu-id="cc6b1-127">在 IIS 伺服器上執行安裝程式。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-127">Run the installer on the IIS server.</span></span>

1. <span data-ttu-id="cc6b1-128">重新開機伺服器，或 `net stop was /y` 在命令列介面中執行，然後執行 `net start w3svc` 。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-128">Restart the server or execute `net stop was /y` followed by `net start w3svc` in a command shell.</span></span>

## <a name="create-the-iis-site"></a><span data-ttu-id="cc6b1-129">建立 IIS 網站</span><span class="sxs-lookup"><span data-stu-id="cc6b1-129">Create the IIS site</span></span>

1. <span data-ttu-id="cc6b1-130">在 IIS 伺服器上，建立資料夾以包含應用程式的發佈資料夾和檔案。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-130">On the IIS server, create a folder to contain the app's published folders and files.</span></span> <span data-ttu-id="cc6b1-131">在下列步驟中，您提供資料夾路徑給 IIS，作為應用程式的實體路徑。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-131">In a following step, the folder's path is provided to IIS as the physical path to the app.</span></span> <span data-ttu-id="cc6b1-132">如需應用程式之部署資料夾和檔案配置的詳細資訊，請參閱 <xref:host-and-deploy/directory-structure>。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-132">For more information on an app's deployment folder and file layout, see <xref:host-and-deploy/directory-structure>.</span></span>

1. <span data-ttu-id="cc6b1-133">在 [IIS 管理員] 中 **，在 [連線] 面板中** 開啟伺服器的節點。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-133">In IIS Manager, open the server's node in the **Connections** panel.</span></span> <span data-ttu-id="cc6b1-134">以滑鼠右鍵按一下 [網站] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-134">Right-click the **Sites** folder.</span></span> <span data-ttu-id="cc6b1-135">從操作功能表選取 [新增網站]。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-135">Select **Add Website** from the contextual menu.</span></span>

1. <span data-ttu-id="cc6b1-136">提供 **網站名稱**，並將 **實體路徑** 設定為您建立的應用程式部署資料夾。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-136">Provide a **Site name** and set the **Physical path** to the app's deployment folder that you created.</span></span> <span data-ttu-id="cc6b1-137">提供 **繫結** 組態，然後選取 [確定] 來建立網站。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-137">Provide the **Binding** configuration and create the website by selecting **OK**.</span></span>

   > [!WARNING]
   > <span data-ttu-id="cc6b1-138">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-138">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="cc6b1-139">最上層萬用字元繫結可能暴露您的應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-139">Top-level wildcard bindings can open up your app to security vulnerabilities.</span></span> <span data-ttu-id="cc6b1-140">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-140">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="cc6b1-141">請使用明確主機名稱，而非萬用字元。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-141">Use explicit host names rather than wildcards.</span></span> <span data-ttu-id="cc6b1-142">若您擁有整個父網域 (與具弱點的 `*.com` 相對) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 就沒有此安全性風險。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-142">Subdomain wildcard binding (for example, `*.mysub.com`) doesn't have this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="cc6b1-143">如需詳細資訊，請參閱 [rfc7230 5.4 節](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-143">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

1. <span data-ttu-id="cc6b1-144">確認處理序模型身分識別具有適當的權限。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-144">Confirm the process model identity has the proper permissions.</span></span>

   <span data-ttu-id="cc6b1-145">如果應用程式集區的預設身分識別 (**進程模型**  >  **Identity**) 已從變更 `ApplicationPoolIdentity` 為其他身分識別，請確認新的身分識別具有存取應用程式資料夾、資料庫和其他必要資源的必要許可權。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-145">If the default identity of the app pool (**Process Model** > **Identity**) is changed from `ApplicationPoolIdentity` to another identity, verify that the new identity has the required permissions to access the app's folder, database, and other required resources.</span></span> <span data-ttu-id="cc6b1-146">例如，應用程式集區需要針對應用程式讀取和寫入檔案的資料夾取得讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-146">For example, the app pool requires read and write access to folders where the app reads and writes files.</span></span>

## <a name="create-an-aspnet-core-no-locrazor-pages-app"></a><span data-ttu-id="cc6b1-147">建立 ASP.NET Core Razor 頁面應用程式</span><span class="sxs-lookup"><span data-stu-id="cc6b1-147">Create an ASP.NET Core Razor Pages app</span></span>

<span data-ttu-id="cc6b1-148">遵循 <xref:getting-started> 教學課程來建立 Razor 頁面應用程式。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-148">Follow the <xref:getting-started> tutorial to create a Razor Pages app.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="cc6b1-149">發佈及部署應用程式</span><span class="sxs-lookup"><span data-stu-id="cc6b1-149">Publish and deploy the app</span></span>

<span data-ttu-id="cc6b1-150">「發佈應用程式」表示產生可由伺服器裝載的編譯應用程式。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-150">*Publish an app* means to produce a compiled app that can be hosted by a server.</span></span> <span data-ttu-id="cc6b1-151">「部署應用程式」表示將發佈後的應用程式移動到裝載系統。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-151">*Deploy an app* means to move the published app to a hosting system.</span></span> <span data-ttu-id="cc6b1-152">發佈步驟會由 [.NET Core SDK](/dotnet/core/sdk) 處理，部署步驟則是可以透過各種方式處理。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-152">The publish step is handled by the [.NET Core SDK](/dotnet/core/sdk), while the deployment step can be handled by a variety of approaches.</span></span> <span data-ttu-id="cc6b1-153">本教學課程採用「資料夾」部署方法，其中：</span><span class="sxs-lookup"><span data-stu-id="cc6b1-153">This tutorial adopts the *folder* deployment approach, where:</span></span>
 
* <span data-ttu-id="cc6b1-154">應用程式會發佈到資料夾。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-154">The app is published to a folder.</span></span>
* <span data-ttu-id="cc6b1-155">資料夾內容會移動到 IIS 網站的資料夾 (指向 IIS 管理員中網站的 **實體路徑**)。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-155">The folder's contents are moved to the IIS site's folder (the **Physical path** to the site in IIS Manager).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cc6b1-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cc6b1-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="cc6b1-157">以滑鼠右鍵按一下 **方案總管** 中的專案，然後選取 [ **發行**]。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-157">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>
1. <span data-ttu-id="cc6b1-158">在 [挑選發佈目標] 對話方塊中，選取 [資料夾] 發佈選項。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-158">In the **Pick a publish target** dialog, select the **Folder** publish option.</span></span>
1. <span data-ttu-id="cc6b1-159">設定 [資料夾或檔案共用] 路徑。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-159">Set the **Folder or File Share** path.</span></span>
   * <span data-ttu-id="cc6b1-160">若您針對部署電腦上提供為網路共用的 IIS 網站建立資料夾，則請提供指向共用的路徑。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-160">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="cc6b1-161">目前的使用者必須具備寫入存取權限才能發佈到共用。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-161">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="cc6b1-162">如果您無法直接部署至 IIS 伺服器上的 IIS 網站資料夾，請發行至卸載式媒體上的資料夾，並實際將已發佈的應用程式移至伺服器上的 IIS 網站資料夾，也就是 IIS 管理員中的網站 **實體路徑** 。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-162">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="cc6b1-163">將資料夾的內容移 `bin/Release/{TARGET FRAMEWORK}/publish` 至伺服器上的 iis 網站資料夾，也就是 [Iis 管理員] 中的網站 **實體路徑** 。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-163">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>
1. <span data-ttu-id="cc6b1-164">選取 [發佈] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-164">Select the **Publish** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cc6b1-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="cc6b1-165">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="cc6b1-166">在命令殼層中，使用 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令，以發行組態來發佈應用程式：</span><span class="sxs-lookup"><span data-stu-id="cc6b1-166">In a command shell, publish the app in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

   ```dotnetcli
   dotnet publish --configuration Release
   ```

1. <span data-ttu-id="cc6b1-167">將資料夾的內容移 `bin/Release/{TARGET FRAMEWORK}/publish` 至伺服器上的 iis 網站資料夾，也就是 [Iis 管理員] 中的網站 **實體路徑** 。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-167">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="cc6b1-168">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="cc6b1-168">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="cc6b1-169">在 [解決方案] 中按一下專案，然後選取 [發佈] > [發佈到資料夾]。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-169">Right-click on the project in **Solution** and select **Publish** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="cc6b1-170">設定 [選取資料夾] 路徑。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-170">Set the **Choose a folder** path.</span></span>
   * <span data-ttu-id="cc6b1-171">若您針對部署電腦上提供為網路共用的 IIS 網站建立資料夾，則請提供指向共用的路徑。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-171">If you created a folder for the IIS site that's available on the development machine as a network share, provide the path to the share.</span></span> <span data-ttu-id="cc6b1-172">目前的使用者必須具備寫入存取權限才能發佈到共用。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-172">The current user must have write access to publish to the share.</span></span>
   * <span data-ttu-id="cc6b1-173">若您無法直接部署到 IIS 伺服器上的 IIS 網站資料夾，請發佈到抽取式媒體上資料夾並透過物理方式將所發佈應用程式實際移動到伺服器上的 IIS 網站資料夾，即 IIS 管理員中的 **實體路徑**。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-173">If you're unable to deploy directly to the IIS site folder on the IIS server, publish to a folder on removeable media and physically move the published app to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span> <span data-ttu-id="cc6b1-174">將資料夾的內容移 `bin/Release/{TARGET FRAMEWORK}/publish` 至伺服器上的 iis 網站資料夾，也就是 [Iis 管理員] 中的網站 **實體路徑** 。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-174">Move the contents of the `bin/Release/{TARGET FRAMEWORK}/publish` folder to the IIS site folder on the server, which is the site's **Physical path** in IIS Manager.</span></span>
1. <span data-ttu-id="cc6b1-175">選取 [發佈] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-175">Select the **Publish** button.</span></span>

---

## <a name="browse-the-website"></a><span data-ttu-id="cc6b1-176">瀏覽網站</span><span class="sxs-lookup"><span data-stu-id="cc6b1-176">Browse the website</span></span>

<span data-ttu-id="cc6b1-177">應用程式會在收到第一個要求時，在瀏覽器中提供存取。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-177">The app is accessible in a browser after it receives the first request.</span></span> <span data-ttu-id="cc6b1-178">在您於 IIS 管理員中為網站建立的端點繫結處，對應用程式發出要求。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-178">Make a request to the app at the endpoint binding that you established in IIS Manager for the site.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cc6b1-179">後續步驟</span><span class="sxs-lookup"><span data-stu-id="cc6b1-179">Next steps</span></span>

<span data-ttu-id="cc6b1-180">在本教學課程中，您已了解如何：</span><span class="sxs-lookup"><span data-stu-id="cc6b1-180">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="cc6b1-181">在 Windows Server 上安裝 .NET Core 裝載套件組合。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-181">Install the .NET Core Hosting Bundle on Windows Server.</span></span>
> * <span data-ttu-id="cc6b1-182">在 IIS 管理員中建立 IIS 網站。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-182">Create an IIS site in IIS Manager.</span></span>
> * <span data-ttu-id="cc6b1-183">部署 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="cc6b1-183">Deploy an ASP.NET Core app.</span></span>

<span data-ttu-id="cc6b1-184">若要深入了解在 IIS 上裝載 ASP.NET Core 應用程式，請參閱 IIS 概觀文章：</span><span class="sxs-lookup"><span data-stu-id="cc6b1-184">To learn more about hosting ASP.NET Core apps on IIS, see the IIS Overview article:</span></span>

> [!div class="nextstepaction"]
> <xref:host-and-deploy/iis/index>

## <a name="additional-resources"></a><span data-ttu-id="cc6b1-185">其他資源</span><span class="sxs-lookup"><span data-stu-id="cc6b1-185">Additional resources</span></span>

### <a name="articles-in-the-aspnet-core-documentation-set"></a><span data-ttu-id="cc6b1-186">ASP.NET Core 文件集中的文章</span><span class="sxs-lookup"><span data-stu-id="cc6b1-186">Articles in the ASP.NET Core documentation set</span></span>

* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:test/troubleshoot-azure-iis>
* <xref:security/enforcing-ssl>

### <a name="articles-pertaining-to-aspnet-core-app-deployment"></a><span data-ttu-id="cc6b1-187">與 ASP.NET Core 應用程式部署相關的文章</span><span class="sxs-lookup"><span data-stu-id="cc6b1-187">Articles pertaining to ASP.NET Core app deployment</span></span>

* <xref:tutorials/publish-to-azure-webapp-using-vs>
* <xref:tutorials/publish-to-azure-webapp-using-vscode>
* <xref:host-and-deploy/visual-studio-publish-profiles>
* [<span data-ttu-id="cc6b1-188">使用 Visual Studio for Mac 將 Web 應用程式發佈到資料夾</span><span class="sxs-lookup"><span data-stu-id="cc6b1-188">Publish a Web app to a folder using Visual Studio for Mac</span></span>](/visualstudio/mac/publish-folder)

### <a name="articles-on-iis-https-configuration"></a><span data-ttu-id="cc6b1-189">IIS HTTPS 組態的相關文章</span><span class="sxs-lookup"><span data-stu-id="cc6b1-189">Articles on IIS HTTPS configuration</span></span>

* [<span data-ttu-id="cc6b1-190">在 IIS 管理員中設定 SSL</span><span class="sxs-lookup"><span data-stu-id="cc6b1-190">Configuring SSL in IIS Manager</span></span>](/iis/manage/configuring-security/configuring-ssl-in-iis-manager)
* [<span data-ttu-id="cc6b1-191">如何在 IIS 上設定 SSL</span><span class="sxs-lookup"><span data-stu-id="cc6b1-191">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)

### <a name="articles-on-iis-and-windows-server"></a><span data-ttu-id="cc6b1-192">IIS 和 Windows Server 的相關文章</span><span class="sxs-lookup"><span data-stu-id="cc6b1-192">Articles on IIS and Windows Server</span></span>

* [<span data-ttu-id="cc6b1-193">Microsoft IIS 官方網站</span><span class="sxs-lookup"><span data-stu-id="cc6b1-193">The Official Microsoft IIS Site</span></span>](https://www.iis.net/)
* [<span data-ttu-id="cc6b1-194">Windows Server 技術內容庫</span><span class="sxs-lookup"><span data-stu-id="cc6b1-194">Windows Server technical content library</span></span>](/windows-server/windows-server)

### <a name="deployment-resources-for-iis-administrators"></a><span data-ttu-id="cc6b1-195">IIS 系統管理員的部署資源</span><span class="sxs-lookup"><span data-stu-id="cc6b1-195">Deployment resources for IIS administrators</span></span>

* [<span data-ttu-id="cc6b1-196">IIS 文件</span><span class="sxs-lookup"><span data-stu-id="cc6b1-196">IIS documentation</span></span>](/iis)
* [<span data-ttu-id="cc6b1-197">IIS 中的 IIS 管理員使用者入門</span><span class="sxs-lookup"><span data-stu-id="cc6b1-197">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="cc6b1-198">.NET Core 應用程式部署</span><span class="sxs-lookup"><span data-stu-id="cc6b1-198">.NET Core application deployment</span></span>](/dotnet/core/deploying/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/directory-structure>
* <xref:host-and-deploy/iis/modules>
* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

