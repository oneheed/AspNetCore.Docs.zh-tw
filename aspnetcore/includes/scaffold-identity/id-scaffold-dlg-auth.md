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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551695"
---
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3cc99-101">執行 Identity scaffolder：</span><span class="sxs-lookup"><span data-stu-id="3cc99-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc99-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc99-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3cc99-103">在 **方案總管** 中，以滑鼠右鍵按一下專案，> [ **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="3cc99-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3cc99-104">從 [ **新增 Scaffold** ] 對話方塊的左窗格中，選取 [ **Identity** > **新增**]。</span><span class="sxs-lookup"><span data-stu-id="3cc99-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="3cc99-105">在 [**新增 Identity** ] 對話方塊中，選取您要的選項。</span><span class="sxs-lookup"><span data-stu-id="3cc99-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="3cc99-106">選取現有的版面配置頁面，因此不會以不正確的標記覆寫您的版面配置檔案。</span><span class="sxs-lookup"><span data-stu-id="3cc99-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="3cc99-107">選取現有的 *\_ 版面配置. cshtml* 檔案時，**不** 會覆寫該檔案。</span><span class="sxs-lookup"><span data-stu-id="3cc99-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="3cc99-108">例如：</span><span class="sxs-lookup"><span data-stu-id="3cc99-108">For example:</span></span>
    * <span data-ttu-id="3cc99-109">`~/Pages/Shared/_Layout.cshtml` 針對 Razor Blazor Server 具有現有 Razor 頁面基礎結構的頁面或專案</span><span class="sxs-lookup"><span data-stu-id="3cc99-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="3cc99-110">`~/Views/Shared/_Layout.cshtml` 針對 Blazor Server 具有現有 mvc 基礎結構的 mvc 專案或專案</span><span class="sxs-lookup"><span data-stu-id="3cc99-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="3cc99-111">若要使用現有的資料內容，請至少選取一個要覆寫的檔案。</span><span class="sxs-lookup"><span data-stu-id="3cc99-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="3cc99-112">您必須至少選取一個檔案，才能新增資料內容。</span><span class="sxs-lookup"><span data-stu-id="3cc99-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="3cc99-113">選取您的資料內容類別。</span><span class="sxs-lookup"><span data-stu-id="3cc99-113">Select your data context class.</span></span>
  * <span data-ttu-id="3cc99-114">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3cc99-114">Select **Add**.</span></span>
* <span data-ttu-id="3cc99-115">若要建立新的使用者內容，並可能建立的自訂使用者類別 Identity ：</span><span class="sxs-lookup"><span data-stu-id="3cc99-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="3cc99-116">選取 **+** 按鈕以建立新的 **資料內容類別**。</span><span class="sxs-lookup"><span data-stu-id="3cc99-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="3cc99-117">接受預設值或指定類別 (例如 `MyApplication.Data.ApplicationDbContext`) 。</span><span class="sxs-lookup"><span data-stu-id="3cc99-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="3cc99-118">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3cc99-118">Select **Add**.</span></span>

<span data-ttu-id="3cc99-119">注意：如果您要建立新的使用者內容，則不需要選取要覆寫的檔案。</span><span class="sxs-lookup"><span data-stu-id="3cc99-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3cc99-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3cc99-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3cc99-121">如果您先前未安裝 ASP.NET Core scaffolder，請立即安裝：</span><span class="sxs-lookup"><span data-stu-id="3cc99-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="3cc99-122">將必要的 NuGet 套件參考新增至專案檔， (*.csproj*) 。</span><span class="sxs-lookup"><span data-stu-id="3cc99-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="3cc99-123">在專案目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3cc99-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="3cc99-124">執行下列命令以列出 Identity scaffolder 選項：</span><span class="sxs-lookup"><span data-stu-id="3cc99-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="3cc99-125">在專案資料夾中， Identity 使用您想要的選項來執行 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="3cc99-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="3cc99-126">例如，若要使用預設 UI 和檔案數目下限來設定身分識別，請執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="3cc99-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="3cc99-127">針對您的資料庫內容使用正確的完整名稱：</span><span class="sxs-lookup"><span data-stu-id="3cc99-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="3cc99-128">PowerShell 使用分號做為命令分隔符號。</span><span class="sxs-lookup"><span data-stu-id="3cc99-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="3cc99-129">使用 PowerShell 時，請將檔案清單中的分號 escape，或將檔案清單放在雙引號中。</span><span class="sxs-lookup"><span data-stu-id="3cc99-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="3cc99-130">例如：</span><span class="sxs-lookup"><span data-stu-id="3cc99-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="3cc99-131">如果您在 Identity 未指定旗標或旗標的情況下執行 scaffolder `--files` ，則 `--useDefaultUI` Identity 會在您的專案中建立所有可用的 UI 頁面。</span><span class="sxs-lookup"><span data-stu-id="3cc99-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3cc99-132">執行 Identity scaffolder：</span><span class="sxs-lookup"><span data-stu-id="3cc99-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3cc99-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cc99-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3cc99-134">在 **方案總管** 中，以滑鼠右鍵按一下專案，> [ **加入** > **新的 scaffold 專案**]。</span><span class="sxs-lookup"><span data-stu-id="3cc99-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3cc99-135">從 [ **新增 Scaffold** ] 對話方塊的左窗格中，選取 [ **Identity** > **新增**]。</span><span class="sxs-lookup"><span data-stu-id="3cc99-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="3cc99-136">在 [**新增 Identity** ] 對話方塊中，選取您要的選項。</span><span class="sxs-lookup"><span data-stu-id="3cc99-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="3cc99-137">選取您現有的版面配置頁面，否則您的版面配置檔案將會以不正確的標記覆寫。</span><span class="sxs-lookup"><span data-stu-id="3cc99-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="3cc99-138">選取現有的 *\_ 版面配置. cshtml* 檔案時，**不** 會覆寫該檔案。</span><span class="sxs-lookup"><span data-stu-id="3cc99-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="3cc99-139">例如：</span><span class="sxs-lookup"><span data-stu-id="3cc99-139">For example:</span></span>
    * <span data-ttu-id="3cc99-140">`~/Pages/Shared/_Layout.cshtml` 適用于 Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="3cc99-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="3cc99-141">`~/Views/Shared/_Layout.cshtml` 適用于 MVC 專案</span><span class="sxs-lookup"><span data-stu-id="3cc99-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="3cc99-142">若要使用現有的資料內容，請至少選取一個要覆寫的檔案。</span><span class="sxs-lookup"><span data-stu-id="3cc99-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="3cc99-143">您必須至少選取一個檔案，才能新增資料內容。</span><span class="sxs-lookup"><span data-stu-id="3cc99-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="3cc99-144">選取您的資料內容類別。</span><span class="sxs-lookup"><span data-stu-id="3cc99-144">Select your data context class.</span></span>
  * <span data-ttu-id="3cc99-145">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3cc99-145">Select **Add**.</span></span>
* <span data-ttu-id="3cc99-146">若要建立新的使用者內容，並可能建立的自訂使用者類別 Identity ：</span><span class="sxs-lookup"><span data-stu-id="3cc99-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="3cc99-147">選取 **+** 按鈕以建立新的 **資料內容類別**。</span><span class="sxs-lookup"><span data-stu-id="3cc99-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="3cc99-148">接受預設值或指定類別 (例如 `MyApplication.Data.ApplicationDbContext`) 。</span><span class="sxs-lookup"><span data-stu-id="3cc99-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="3cc99-149">選取 [新增]。</span><span class="sxs-lookup"><span data-stu-id="3cc99-149">Select **Add**.</span></span>

<span data-ttu-id="3cc99-150">注意：如果您要建立新的使用者內容，則不需要選取要覆寫的檔案。</span><span class="sxs-lookup"><span data-stu-id="3cc99-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3cc99-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3cc99-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3cc99-152">如果您先前未安裝 ASP.NET Core scaffolder，請立即安裝：</span><span class="sxs-lookup"><span data-stu-id="3cc99-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="3cc99-153">將 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 的套件參考新增至專案檔， (*.csproj*) 。</span><span class="sxs-lookup"><span data-stu-id="3cc99-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="3cc99-154">在專案目錄中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3cc99-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="3cc99-155">執行下列命令以列出 Identity scaffolder 選項：</span><span class="sxs-lookup"><span data-stu-id="3cc99-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="3cc99-156">在專案資料夾中， Identity 使用您想要的選項來執行 scaffolder。</span><span class="sxs-lookup"><span data-stu-id="3cc99-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="3cc99-157">例如，若要使用預設 UI 和檔案數目下限來設定身分識別，請執行下列命令。</span><span class="sxs-lookup"><span data-stu-id="3cc99-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="3cc99-158">針對您的資料庫內容使用正確的完整名稱：</span><span class="sxs-lookup"><span data-stu-id="3cc99-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="3cc99-159">PowerShell 使用分號做為命令分隔符號。</span><span class="sxs-lookup"><span data-stu-id="3cc99-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="3cc99-160">使用 PowerShell 時，請將檔案清單中的分號 escape，或將檔案清單放在雙引號中。</span><span class="sxs-lookup"><span data-stu-id="3cc99-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="3cc99-161">例如：</span><span class="sxs-lookup"><span data-stu-id="3cc99-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="3cc99-162">如果您在 Identity 未指定旗標或旗標的情況下執行 scaffolder `--files` ，則 `--useDefaultUI` Identity 會在您的專案中建立所有可用的 UI 頁面。</span><span class="sxs-lookup"><span data-stu-id="3cc99-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
