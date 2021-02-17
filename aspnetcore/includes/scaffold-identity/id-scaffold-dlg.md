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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551696"
---
<span data-ttu-id="9e55b-101">執行 Identity scaffolder：</span><span class="sxs-lookup"><span data-stu-id="9e55b-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e55b-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e55b-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9e55b-103">在 **方案總管** 中，以滑鼠右鍵按一下專案，> [**加入**  >  **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="9e55b-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="9e55b-104">在 [**加入新的 scaffold 專案**] 對話方塊的左窗格中，選取 [ **Identity**  >  **加入**]。</span><span class="sxs-lookup"><span data-stu-id="9e55b-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="9e55b-105">在 [**新增 Identity** ] 對話方塊中，選取您要的選項。</span><span class="sxs-lookup"><span data-stu-id="9e55b-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="9e55b-106">選取您現有的版面配置頁面，否則您的版面配置檔案將會以不正確的標記覆寫：</span><span class="sxs-lookup"><span data-stu-id="9e55b-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="9e55b-107">`~/Pages/Shared/_Layout.cshtml` 適用于 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="9e55b-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="9e55b-108">`~/Views/Shared/_Layout.cshtml` 適用于 MVC 專案</span><span class="sxs-lookup"><span data-stu-id="9e55b-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="9e55b-109">Blazor ServerBlazor Server `blazorserver` 依預設，不會針對頁面或 MVC 設定從範本 (建立的應用程式) Razor 。</span><span class="sxs-lookup"><span data-stu-id="9e55b-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="9e55b-110">將版面配置頁面專案保留空白。</span><span class="sxs-lookup"><span data-stu-id="9e55b-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="9e55b-111">選取 **+** 按鈕以建立新的 **資料內容類別**。</span><span class="sxs-lookup"><span data-stu-id="9e55b-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="9e55b-112">接受預設值或指定類別 (例如 `MyApplication.Data.ApplicationDbContext`) 。</span><span class="sxs-lookup"><span data-stu-id="9e55b-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="9e55b-113">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="9e55b-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="9e55b-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="9e55b-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9e55b-115">如果您先前未安裝 ASP.NET Core scaffolder，請立即安裝：</span><span class="sxs-lookup"><span data-stu-id="9e55b-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="9e55b-116">將必要的 NuGet 套件參考新增至專案檔， (*.csproj*) 。</span><span class="sxs-lookup"><span data-stu-id="9e55b-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="9e55b-117">在專案目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9e55b-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="9e55b-118">執行下列命令以列出 Identity scaffolder 選項：</span><span class="sxs-lookup"><span data-stu-id="9e55b-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="9e55b-119">在專案資料夾中， Identity 使用您想要的選項來執行 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="9e55b-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="9e55b-120">例如，若要使用預設 UI 和檔案數目下限來設定身分識別，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9e55b-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
