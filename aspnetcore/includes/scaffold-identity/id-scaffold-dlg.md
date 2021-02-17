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
執行 Identity scaffolder：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 **方案總管** 中，以滑鼠右鍵按一下專案，> [**加入**  >  **新的 scaffold 專案**]。
* 在 [**加入新的 scaffold 專案**] 對話方塊的左窗格中，選取 [ **Identity**  >  **加入**]。
* 在 [**新增 Identity** ] 對話方塊中，選取您要的選項。
  * 選取您現有的版面配置頁面，否則您的版面配置檔案將會以不正確的標記覆寫：
    * `~/Pages/Shared/_Layout.cshtml` 適用于 Razor 頁面
    * `~/Views/Shared/_Layout.cshtml` 適用于 MVC 專案
    * Blazor ServerBlazor Server `blazorserver` 依預設，不會針對頁面或 MVC 設定從範本 (建立的應用程式) Razor 。 將版面配置頁面專案保留空白。
  * 選取 **+** 按鈕以建立新的 **資料內容類別**。 接受預設值或指定類別 (例如 `MyApplication.Data.ApplicationDbContext`) 。
* 選取 [新增]。

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

在專案資料夾中， Identity 使用您想要的選項來執行 scaffolder。 例如，若要使用預設 UI 和檔案數目下限來設定身分識別，請執行下列命令：

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
