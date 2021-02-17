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

執行 Identity scaffolder：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **方案總管** 中，以滑鼠右鍵按一下專案，> [ **加入** > **新的 scaffold 專案**]。
* 從 [ **新增 Scaffold** ] 對話方塊的左窗格中，選取 [ **Identity** > **新增**]。
* 在 [**新增 Identity** ] 對話方塊中，選取您要的選項。
  * 選取現有的版面配置頁面，因此不會以不正確的標記覆寫您的版面配置檔案。 選取現有的 *\_ 版面配置. cshtml* 檔案時，**不** 會覆寫該檔案。 例如：
    * `~/Pages/Shared/_Layout.cshtml` 針對 Razor Blazor Server 具有現有 Razor 頁面基礎結構的頁面或專案
    * `~/Views/Shared/_Layout.cshtml` 針對 Blazor Server 具有現有 mvc 基礎結構的 mvc 專案或專案
* 若要使用現有的資料內容，請至少選取一個要覆寫的檔案。 您必須至少選取一個檔案，才能新增資料內容。
  * 選取您的資料內容類別。
  * 選取 [新增]。
* 若要建立新的使用者內容，並可能建立的自訂使用者類別 Identity ：
  * 選取 **+** 按鈕以建立新的 **資料內容類別**。 接受預設值或指定類別 (例如 `MyApplication.Data.ApplicationDbContext`) 。
  * 選取 [新增]。

注意：如果您要建立新的使用者內容，則不需要選取要覆寫的檔案。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果您先前未安裝 ASP.NET Core scaffolder，請立即安裝：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

將必要的 NuGet 套件參考新增至專案檔， (*.csproj*) 。 在專案目錄中執行下列命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

執行下列命令以列出 Identity scaffolder 選項：

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

在專案資料夾中， Identity 使用您想要的選項來執行 scaffolder。 例如，若要使用預設 UI 和檔案數目下限來設定身分識別，請執行下列命令。 針對您的資料庫內容使用正確的完整名稱：

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell 使用分號做為命令分隔符號。 使用 PowerShell 時，請將檔案清單中的分號 escape，或將檔案清單放在雙引號中。 例如：

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

如果您在 Identity 未指定旗標或旗標的情況下執行 scaffolder `--files` ，則 `--useDefaultUI` Identity 會在您的專案中建立所有可用的 UI 頁面。

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

執行 Identity scaffolder：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **方案總管** 中，以滑鼠右鍵按一下專案，> [ **加入** > **新的 scaffold 專案**]。
* 從 [ **新增 Scaffold** ] 對話方塊的左窗格中，選取 [ **Identity** > **新增**]。
* 在 [**新增 Identity** ] 對話方塊中，選取您要的選項。
  * 選取您現有的版面配置頁面，否則您的版面配置檔案將會以不正確的標記覆寫。 選取現有的 *\_ 版面配置. cshtml* 檔案時，**不** 會覆寫該檔案。 例如：
    * `~/Pages/Shared/_Layout.cshtml` 適用于 Razor 頁面
    * `~/Views/Shared/_Layout.cshtml` 適用于 MVC 專案
* 若要使用現有的資料內容，請至少選取一個要覆寫的檔案。 您必須至少選取一個檔案，才能新增資料內容。
  * 選取您的資料內容類別。
  * 選取 [新增]。
* 若要建立新的使用者內容，並可能建立的自訂使用者類別 Identity ：
  * 選取 **+** 按鈕以建立新的 **資料內容類別**。 接受預設值或指定類別 (例如 `MyApplication.Data.ApplicationDbContext`) 。
  * 選取 [新增]。

注意：如果您要建立新的使用者內容，則不需要選取要覆寫的檔案。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果您先前未安裝 ASP.NET Core scaffolder，請立即安裝：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

將 [VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) 的套件參考新增至專案檔， (*.csproj*) 。 在專案目錄中執行下列命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

執行下列命令以列出 Identity scaffolder 選項：

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

在專案資料夾中， Identity 使用您想要的選項來執行 scaffolder。 例如，若要使用預設 UI 和檔案數目下限來設定身分識別，請執行下列命令。 針對您的資料庫內容使用正確的完整名稱：

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell 使用分號做為命令分隔符號。 使用 PowerShell 時，請將檔案清單中的分號 escape，或將檔案清單放在雙引號中。 例如：

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

如果您在 Identity 未指定旗標或旗標的情況下執行 scaffolder `--files` ，則 `--useDefaultUI` Identity 會在您的專案中建立所有可用的 UI 頁面。

---

::: moniker-end
