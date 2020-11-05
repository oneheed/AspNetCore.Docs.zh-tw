---
title: 教學課程：開始使用 ASP.NET MVC web 應用程式中的 EF Core
description: 此頁面是一系列教學課程中的第一個教學課程，說明如何建立 Contoso 大學範例 EF/MVC 應用程式」
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
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
uid: data/ef-mvc/intro
ms.openlocfilehash: 77cf1e9ad51b7044a35e1a9b2c125b0fdd91435e
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365369"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>教學課程：開始使用 ASP.NET MVC web 應用程式中的 EF Core

作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Contoso 大學範例 web ap 示範如何使用 Entity Framework (EF) Core 和 Visual Studio 建立 ASP.NET Core MVC web 應用程式。

這個範例應用程式是虛構的 Contoso 大學網站。 其中包括的功能有學生入學許可、課程建立、教師指派。 這是一系列教學課程中的第一篇，說明如何建立 Contoso 大學範例應用程式。

## <a name="prerequisites"></a>先決條件

* 如果您是 ASP.NET Core MVC 的新手，請先完成 [ASP.NET CORE mvc](xref:tutorials/first-mvc-app/start-mvc) 教學課程系列的「開始使用」，再開始這一系列。

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a>資料庫引擎

Visual Studio 說明會使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是一種只在 Windows 上執行的 SQL Server Express 版本。

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a>解決問題和疑難排解

如果您執行您不能解決問題，您可以藉由比較您的程式碼通常找到方案[已完成的專案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)。 如需常見的錯誤以及如何解決這些問題的清單，請參閱[ 數列中的最後一個教學課程疑難排解 > 一節](advanced.md#common-errors)。 如果您找不到您需要那里，您可以張貼問題的 StackOverflow.com [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)。

> [!TIP]
> 這是 10 個教學的系列課程，當中的每一個課程都是建置於先前教學課程的成果上。 成功完成每一個教學課程後，請儲存專案的複本。 如果您遇到問題，您可以從上一個教學課程來重新開始，而不需從系列的一開始從頭來過。

## <a name="contoso-university-web-app"></a>Contoso 大學 Web 應用程式

在教學課程中建立的應用程式，是一個基本的大學網站。

使用者可以檢視和更新學生、課程和教師資訊。 以下是應用程式中的幾個畫面：

![Students [索引] 頁面](intro/_static/students-index.png)

![Students [編輯] 頁面](intro/_static/student-edit.png)

## <a name="create-web-app"></a>建立 Web 應用程式

* 開始 Visual Studio，然後選取 [ **ASP.NET Core Web 應用程式** > **]** 。
* 將專案命名為 `ContosoUniversity`。 請務必使用此完整名稱（包括大小寫），讓命名空間在複製程式碼時相符。
* 選取 [建立]  。
* 在下拉式清單中選取 [ **.Net Core** ] 和 [ **ASP.NET Core 5.0** ]，然後選取 [ **Web 應用程式] ([模型-視圖控制器])** 範本。
  ![[新增 ASP.NET Core 專案] 對話方塊](intro/_static/new-aspnet5.png)

## <a name="set-up-the-site-style"></a>設定網站樣式

有幾個基本變更會設定網站功能表、版面配置和首頁。

開啟 *Views/Shared/_Layout.cshtml* 並進行下列變更：

* 將每個出現的變更 `ContosoUniversity` 為 `Contoso University` 。 共有三個發生次數。
* 為 **About** 、 **Students** 、 **Courses** 、 **Instructors** 及 **Departments** 新增功能表項目，並刪除 **Privacy** 功能表項目。

上述變更會在下列程式碼中反白顯示：

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

在 *Views/Home/Index. cshtml* 中，以下列標記取代檔案的內容：

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

按 CTRL+F5 來執行專案，或從功能表選擇 [偵錯] > [啟動但不偵錯]。 首頁會顯示在本教學課程中建立之頁面的索引標籤。

![Contoso 大學首頁](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a>EF Core NuGet 套件

本教學課程使用 SQL Server，其提供者套件為 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。

EF SQL Server 套件及其相依性， `Microsoft.EntityFrameworkCore` 以及 `Microsoft.EntityFrameworkCore.Relational` 提供 ef 的執行時間支援。

新增 [AspNetCore Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) nuget 套件和 [AspNetCore. microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) nuget 套件中的程式。 在 [Program Manager 主控台] (PMC) 中，輸入下列命令以新增 NuGet 套件：

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet 套件提供 EF Core 錯誤頁面 ASP.NET Core 中介軟體。 此中介軟體有助於偵測並診斷 EF Core 遷移的錯誤。

如需其他可用於 EF Core 之資料庫提供者的詳細資訊，請參閱 [資料庫提供者](/ef/core/providers/)。

## <a name="create-the-data-model"></a>建立資料模型

系統會為此應用程式建立下列實體類別：

![Course-Enrollment-Student 資料模型圖表](intro/_static/data-model-diagram.png)

上述實體具有下列關聯性：

* 與實體之間有一對多關聯性 `Student` `Enrollment` 。 學生可以在任意數目的課程中註冊。
* 與實體之間有一對多關聯性 `Course` `Enrollment` 。 一堂課程可以讓任意數目的學生註冊。

在下列各節中，會為每個實體建立類別。

### <a name="the-student-entity"></a>Student 實體

![Student 實體圖表](intro/_static/student-entity.png)

在 [ *模型* ] 資料夾中， `Student` 使用下列程式碼建立類別：

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID`屬性（property）是對應至這個類別的資料庫資料表中， ( **PK** ) 資料行的主鍵。 根據預設，EF 會將名為或的 `ID` 屬性 `classnameID` 視為主要索引鍵。 例如，PK 可以命名為， `StudentID` 而不是 `ID` 。

`Enrollments` 屬性為[導覽屬性](/ef/core/modeling/relationships)。 導覽屬性會保留與此實體相關的其他實體。 `Enrollments`實體的屬性 `Student` ：

* 包含與 `Enrollment` 該實體相關的所有實體 `Student` 。
* 如果 `Student` 資料庫中的特定資料列有兩個相關的資料 `Enrollment` 列：
  * 該 `Student` 實體的 `Enrollments` 導覽屬性包含這兩個 `Enrollment` 實體。
  
`Enrollment` 資料列會在 `StudentID` 外鍵 ( **FK** ) 資料行中包含學生的 PK 值。

如果導覽屬性可以保存多個實體：

 * 型別必須是清單，例如 `ICollection<T>` 、 `List<T>` 或 `HashSet<T>` 。
 * 您可以新增、刪除和更新實體。

多對多和一對多導覽關聯性可包含多個實體。 `ICollection<T>`使用時，EF 預設會建立 `HashSet<T>` 集合。

### <a name="the-enrollment-entity"></a>Enrollment 實體

![Enrollment 實體圖表](intro/_static/enrollment-entity.png)

在 [ *模型* ] 資料夾中， `Enrollment` 使用下列程式碼建立類別：

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`屬性是 PK。 這個實體會使用 `classnameID` 模式，而不是 `ID` 本身。 `Student`使用模式的實體 `ID` 。 有些開發人員偏好在資料模型中使用一種模式。 在本教學課程中，變化說明可以使用任一種模式。 [稍後的教學](inheritance.md)課程會示範如何使用 `ID` 不含 classname，讓您更輕鬆地在資料模型中執行繼承。

`Grade` 屬性為一個 `enum`。 型別宣告 `?` 之後的 `Grade` 會指出該 `Grade` 屬性可 [為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)。 其等級與 `null` 零級不同。 `null` 表示不知道或尚未指派等級。

`StudentID`屬性是 (FK) 的外鍵，而對應的導覽屬性為 `Student` 。 `Enrollment`實體與一個實體相關聯 `Student` ，因此屬性只能包含單一 `Student` 實體。 這不同于 `Student.Enrollments` 可保存多個實體的導覽屬性 `Enrollment` 。

`CourseID`屬性是 FK，而對應的導覽屬性為 `Course` 。 一個 `Enrollment` 實體與一個 `Course` 實體建立關聯。

Entity Framework 會將屬性（property）解讀為 FK 屬性（property），並將其命名為「 `<` 導覽屬性名稱 `><` 主鍵屬性名稱」 `>` 。 例如， `StudentID` `Student` 因為 `Student` 實體的 PK 是，所以針對導覽屬性 `ID` 。 FK 屬性也可以命名  `<` 為主鍵屬性名稱 `>` 。 例如， `CourseID` 因為 `Course` 實體的 PK 為 `CourseID` 。

### <a name="the-course-entity"></a>Course 實體

![Course 實體圖表](intro/_static/course-entity.png)

在 [ *模型* ] 資料夾中， `Course` 使用下列程式碼建立類別：

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments` 屬性為導覽屬性。 `Course` 實體可以與任何數量的 `Enrollment` 實體相關。

[DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute)屬性將在[稍後的教學](complex-data-model.md)課程中說明。 這個屬性可讓您在課程中輸入 PK，而非讓資料庫產生它。

## <a name="create-the-database-context"></a>建立資料庫內容

協調指定資料模型之 EF 功能的主要類別是 <xref:Microsoft.EntityFrameworkCore.DbContext> 資料庫內容類別。 此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。 `DbContext`衍生類別會指定要包含在資料模型中的實體。 某些 EF 行為可以自訂。 在此專案中，類別命名為 `SchoolContext`。

在專案資料夾中，建立名為的資料夾 `Data` 。

在 [ *Data* ] 資料夾中， `SchoolContext` 使用下列程式碼建立類別：

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

上述程式碼會 `DbSet` 為每個實體集建立屬性。 EF 術語：

* 實體集通常會對應到資料庫資料表。
* 實體會對應至資料表中的資料列。

您 `DbSet<Enrollment>` `DbSet<Course>` 可以省略和語句，其運作方式也相同。 EF 會隱含地包含它們，因為：

* `Student`實體參考 `Enrollment` 實體。
* `Enrollment`實體參考 `Course` 實體。

資料庫建立時，EF 會建立和 `DbSet` 屬性名稱相同的資料表。 集合的屬性名稱通常是複數。 例如， `Students` 而不是 `Student` 。 針對是否要複數化資料表名稱，開發人員並沒有共識。 在這些教學課程中，會藉由在中指定單一資料表名稱來覆寫預設行為 `DbContext` 。 若要完成這項操作，請在最後一個 DbSet 屬性後方新增下列醒目提示程式碼。

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>註冊 `SchoolContext`

ASP.NET Core 包含了[相依性插入](../../fundamentals/dependency-injection.md)。 服務（例如 EF 資料庫內容）會在應用程式啟動期間以相依性插入來註冊。 需要這些服務的元件（例如 MVC 控制器）是透過函式參數提供這些服務。 本教學課程稍後會顯示取得內容實例的控制器程式碼。

若要將 `SchoolContext` 註冊為服務，請開啟 *Startup.cs* ，並將醒目標示的程式碼新增至 `ConfigureServices` 方法。

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

連接字串的名稱，會透過呼叫 `DbContextOptionsBuilder` 物件上的方法來傳遞至內容。 針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。

開啟檔案 *appsettings.json* ，並加入連接字串，如下列標記所示：

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a>新增資料庫例外狀況篩選準則

將加入 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> 至， `ConfigureServices` 如下列程式碼所示：

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

會 `AddDatabaseDeveloperPageExceptionFilter` 在 [開發環境](xref:fundamentals/environments)中提供有用的錯誤資訊。

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

連接字串會指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。 LocalDB 是輕量版的 SQL Server Express Database Engine，旨在用於應用程序開發，而不是生產用途。 LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。 LocalDB 預設會在 `C:/Users/<user>` 目錄中建立 *.mdf* 資料庫檔案。

## <a name="initialize-db-with-test-data"></a>使用測試資料將 DB 初始化

EF 會建立空的資料庫。 在本節中，會加入在建立資料庫之後呼叫的方法，以便將測試資料填入。

`EnsureCreated`方法是用來自動建立資料庫。 在 [稍後的教學](migrations.md)課程中，您將瞭解如何使用 Code First 移轉來變更資料庫架構，而不是卸載和重新建立資料庫，來處理模型變更。

在 [ *資料* ] 資料夾中，使用下列程式碼來建立名為的新類別 `DbInitializer` ：

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

上述程式碼會檢查資料庫是否存在：

* 如果找不到資料庫，則為，
  * 它會建立並載入測試資料。 會將測試資料載入至陣列而非 `List<T>` 集合，以最佳化效能。
* 如果找到資料庫，則不會採取任何動作。

使用下列程式碼更新 *Program.cs* ：

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

*Program.cs* 會在應用程式啟動時執行下列動作：

* 從相依性插入容器中取得資料庫內容執行個體。
* 呼叫 `DbInitializer.Initialize` 方法。
* 當方法完成時處置內容， `Initialize` 如下列程式碼所示：

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

第一次執行應用程式時，會建立資料庫並載入測試資料。 每次資料模型變更時：

* 刪除資料庫。
* 更新種子方法，並以新的資料庫開始再次。

 在稍後的教學課程中，資料庫會在資料模型變更時進行修改，而不需要刪除然後再重新建立。 當資料模型變更時，不會遺失任何資料。

## <a name="create-controller-and-views"></a>建立控制器和檢視

使用 Visual Studio 中的「樣板」引擎來新增 MVC 控制器和將使用 EF 來查詢和儲存資料的視圖。

自動建立 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 動作方法和視圖稱為「樣板」。

* 在 **方案總管** 中，以滑鼠右鍵按一下 `Controllers` 資料夾，然後選取 [ **加入 > 新的 scaffold 專案** ]。
* 在 [新增 Scaffold] 對話方塊中：
  * 選取 [使用 Entity Framework 執行檢視的 MVC 控制器]。
  * 按一下 [新增]  。 [ **Entity Framework 使用 Scaffold 新增 MVC 控制器** ] 對話方塊隨即出現： ![ Student](intro/_static/scaffold-student2.png)
  * 在 [ **模型類別** ] 中選取 [ **Student** ]。
  * 在 [ **資料內容類別** ] 中，選取 [ **SchoolCoNtext** ]。
  * 接受預設的 **StudentsController** 作為名稱。
  * 按一下 [新增]  。

Visual Studio 的樣板引擎會建立一個檔案 `StudentsController.cs` ，以及一組 `*.cshtml` 與控制器一起使用的 (檔案) 。

請注意，控制器會採用作為函式 `SchoolContext` 參數。

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Core 相依性插入會負責傳遞 `SchoolContext` 的執行個體給控制器。 您已在類別中進行設定 `Startup` 。

控制器含有一個 `Index` 動作方法，該方法會顯示資料庫中的所有學生。 方法會藉由讀取資料庫內容執行個體的 `Students` 屬性，來從 Students 實體集中取得學生的清單：

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

本教學課程稍後會說明此程式碼中的非同步程式設計項目。

*Views/Students/Index.cshtml* 檢視會在一個資料表中顯示這份清單：

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

按 CTRL+F5 來執行專案，或從功能表選擇 [偵錯] > [啟動但不偵錯]。

按一下 [Students] 索引標籤來查看 `DbInitializer.Initialize` 方法插入的測試資料。 取決於您瀏覽器視窗的寬度，您可能會在頁面的頂端看到 `Students` 索引標籤連結，或是按一下位於右上角的導覽圖示來查看連結。

![Contoso 大學首頁 (窄)](intro/_static/home-page-narrow.png)

![Students [索引] 頁面](intro/_static/students-index.png)

## <a name="view-the-database"></a>檢視資料庫

當應用程式啟動時，方法就會 `DbInitializer.Initialize` 呼叫 `EnsureCreated` 。 EF 看到沒有資料庫：

* 所以它會建立一個資料庫。
* `Initialize`方法程式碼會將資料填入資料庫。

使用 **SQL Server 物件總管** (SSOX) 來查看 Visual Studio 中的資料庫：

* 從 Visual Studio 的 [ **View** ] 功能表中選取 [ **SQL Server 物件總管** ]。
* 在 SSOX 中，選取 **(localdb) \mssqllocaldb > 資料庫** 。
* 在檔案的 `ContosoUniversity1` 連接字串中，選取資料庫名稱的專案 *appsettings.json* 。
* 展開 [ **資料表]** 節點，查看資料庫中的資料表。

![SSOX 中的資料表](intro/_static/ssox-tables.png)

以滑鼠右鍵按一下 **Student** 資料表，然後按一下 [ **View data** ] 以查看資料表中的資料。

![SSOX 中的 Student 資料表](intro/_static/ssox-student-table.png)

`*.mdf`和資料庫檔案位於 `*.ldf` *C:\Users \\ \<username>* 資料夾中。

因為 `EnsureCreated` 會在應用程式啟動時執行的初始化運算式方法中呼叫，所以您可以：

* 對類別進行變更 `Student` 。
* 刪除資料庫。
* 停止，然後啟動應用程式。 系統會自動重新建立資料庫以符合變更。

例如，如果將某個 `EmailAddress` 屬性加入至 `Student` 類別， `EmailAddress` 則會在重新建立的資料表中加入新的資料行。 視圖歸類將不會顯示新的 `EmailAddress` 屬性。

## <a name="conventions"></a>慣例

為了讓 EF 建立完整資料庫而撰寫的程式碼數量很少，因為使用 EF 所使用的慣例：

* `DbSet` 屬性的名稱會用於資料表名稱。 針對 `DbSet` 屬性並未參考的實體，實體類別名稱會用於資料表名稱。
* 實體屬性名稱會用於資料行名稱。
* 名為或的實體屬性會被辨識 `ID` `classnameID` 為 PK 屬性。
* 如果屬性名稱為「 `<` 導覽屬性名稱 PK」屬性名稱，則會將屬性視為 FK 屬性 `><` `>` 。 例如， `StudentID` `Student` 因為 `Student` 實體的 PK 是，所以針對導覽屬性 `ID` 。 FK 屬性也可以命名 `<` 為主鍵屬性名稱 `>` 。 例如， `EnrollmentID` 因為 `Enrollment` 實體的 PK 為 `EnrollmentID` 。

慣例行為可以被覆寫。 例如，您可以明確指定資料表名稱，如本教學課程稍早所述。 資料行名稱和任何屬性都可以設定為 PK 或 FK。

## <a name="asynchronous-code"></a>非同步程式碼

非同步程式設計是預設的 ASP.NET Core 和 EF Core 模式。

網頁伺服器的可用執行緒數量有限，而且在高負載情況下，可能會使用所有可用的執行緒。 發生此情況時，伺服器將無法處理新的要求，直到執行緒空出來。 使用同步程式碼，許多執行緒可能在實際上並未執行任何工作時受到占用，原因是在等候 I/O 完成。 使用非同步程式碼，處理程序在等候 I/O 完成時，其執行緒將會空出來以讓伺服器處理其他要求。 因此，非同步程式碼可讓伺服器資源更有效率地使用，而且伺服器可處理更多流量而不會造成延遲。

非同步程式碼雖然的確會在執行階段造成少量的負荷，但在低流量情況下，對效能的衝擊非常微小；在高流量情況下，潛在的效能改善則相當大。

在下列程式碼中，、、 `async` `Task<T>` 和會使程式 `await` `ToListAsync` 代碼以非同步方式執行。

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async` 關鍵字會告訴編譯器為方法本體的一部分產生回呼，並自動建立傳回的 `Task<IActionResult>` 物件。
* 傳回類型 `Task<IActionResult>` 代表了正在進行的工作，其結果為 `IActionResult` 類型。
* `await` 關鍵字會使編譯器將方法分割為兩部分。 第一個部分會與非同步啟動的作業一同結束。 第二個部分則會放入作業完成時呼叫的回呼方法中。
* `ToListAsync` 是 `ToList` 擴充方法的非同步版本。

撰寫使用 EF 的非同步程式碼時，必須注意的一些事項：

* 只有讓查詢或命令傳送至資料庫的陳述式，會以非同步方式執行。 其中包含，例如 `ToListAsync`、`SingleOrDefaultAsync`，以及 `SaveChangesAsync`。 其中不包含，例如：僅變更 `IQueryable` 的陳述式，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。
* EF 內容在執行緒中並不安全：不要嘗試執行多個平行作業。 當您呼叫任何 async EF 方法時，請一律使用 `await` 關鍵字。
* 若要利用非同步程式碼的效能優勢，請確定任何使用的程式庫套件如果呼叫任何 EF 方法，而導致查詢傳送到資料庫，則請確定任何使用的程式庫套件也都使用 async。

如需在 .NET 中非同步程式設計的詳細資訊，請參閱[非同步總覽](/dotnet/articles/standard/async)。

## <a name="limit-entities-fetched"></a>已提取的實體數目

請參閱 [效能考慮](xref:data/ef-rp/intro) ，以取得限制查詢所傳回之數目或實體的相關資訊。

若要了解如何執行基本的 CRUD (建立、讀取、更新、刪除) 作業，請前往下一個教學課程。

> [!div class="nextstepaction"]
> [實作基本的 CRUD 功能](crud.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Contoso 大學範例 Web 應用程式示範如何使用 Entity Framework (EF) Core 2.2 和 Visual Studio 2017 或 2019 建立 ASP.NET Core 2.2 MVC Web 應用程式。

這個範例應用程式是虛構的 Contoso 大學網站。 其中包括的功能有學生入學許可、課程建立、教師指派。 這是說明如何從零開始建立 Contoso 大學範例應用程式教學課程系列中的第一頁。

## <a name="prerequisites"></a>先決條件

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download)
* 包含下列工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)：
  * **ASP.NET 與網頁程式開發** 工作負載
  * **.NET Core 跨平台開發** 工作負載

## <a name="troubleshooting"></a>疑難排解

如果您執行您不能解決問題，您可以藉由比較您的程式碼通常找到方案[已完成的專案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)。 如需常見的錯誤以及如何解決這些問題的清單，請參閱[ 數列中的最後一個教學課程疑難排解 > 一節](advanced.md#common-errors)。 如果您找不到您需要那里，您可以張貼問題的 StackOverflow.com [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)。

> [!TIP]
> 這是 10 個教學的系列課程，當中的每一個課程都是建置於先前教學課程的成果上。 成功完成每一個教學課程後，請儲存專案的複本。 如果您遇到問題，您可以從上一個教學課程來重新開始，而不需從系列的一開始從頭來過。

## <a name="contoso-university-web-app"></a>Contoso 大學 Web 應用程式

您在這些教學課程中會建置的應用程式為一個簡單的大學網站。

使用者可以檢視和更新學生、課程和教師資訊。 以下是您會建立的幾個畫面。

![Students [索引] 頁面](intro/_static/students-index.png)

![Students [編輯] 頁面](intro/_static/student-edit.png)

## <a name="create-web-app"></a>建立 Web 應用程式

* 開啟 Visual Studio。

* 從 [檔案] 功能表選取[新增] > [專案] 。

* 從左側窗格中，選取 [已安裝] > [Visual C#] > [Web]。

* 選取 [ASP.NET Core Web 應用程式] 專案範本。

* 輸入 **ContosoUniversity** 作為名稱，然後按一下 [確定]。

  ![[新增專案] 對話方塊](intro/_static/new-project2.png)

* 等候 [新增 ASP.NET Core Web 應用程式] 對話方塊出現。

* 選取 [.NET Core]、[ASP.NET Core 2.2] 和 [Web 應用程式 (Model-View-Controller)] 範本。

* 確定 [ **驗證** ] 設定為 [ **無驗證** ]。

* 選取 [確定]

  ![[新增 ASP.NET Core 專案] 對話方塊](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>設定網站樣式

一些簡單的變更會設定網站的功能表、配置和首頁。

開啟 *Views/Shared/_Layout.cshtml* 並進行下列變更：

* 將每個出現的 "ContosoUniversity" 都變更為 "Contoso University"。 共有三個發生次數。

* 為 **About** 、 **Students** 、 **Courses** 、 **Instructors** 及 **Departments** 新增功能表項目，並刪除 **Privacy** 功能表項目。

所做的變更已醒目提示。

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

在 *Views/Home/Index.cshtml* 中用下列程式碼取代檔案內容，以使用關於此應用程式的文字來取代關於 ASP.NET 和 MVC 的文字：

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

按 CTRL+F5 來執行專案，或從功能表選擇 [偵錯] > [啟動但不偵錯]。 您會看到在這些教學課程中，您將建立之頁面的索引標籤和首頁。

![Contoso 大學首頁](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>關於 EF Core NuGet 套件

若要將 EF Core 支援新增至專案，請安裝您欲使用之資料庫的提供者。 本教學課程使用 SQL Server，其提供者套件為 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。 此套件包含在 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中，因此您不需要參考該套件。

EF SQL Server 套件及其相依性 (`Microsoft.EntityFrameworkCore` 及 `Microsoft.EntityFrameworkCore.Relational`) 提供了 EF 的執行階段支援。 您會在稍後的[移轉](migrations.md)教學課程中新增工具套件。

如需其他 Entity Framework Core 可用之資料庫提供者的資訊，請參閱[資料庫提供者](/ef/core/providers/)。

## <a name="create-the-data-model"></a>建立資料模型

接下來您會為 Contoso 大學應用程式建立實體類別。 您會從下列三個實體開始。

![Course-Enrollment-Student 資料模型圖表](intro/_static/data-model-diagram.png)

在 `Student` 和 `Enrollment` 實體之間存在一對多關聯性，`Course` 與 `Enrollment` 實體之間也存在一對多關聯性。 換句話說，一位學生可以註冊並參加任何數目的課程，而一個課程也可以有任何數目的學生註冊。

在下節中，您會為這些實體建立各自的類別。

### <a name="the-student-entity"></a>Student 實體

![Student 實體圖表](intro/_static/student-entity.png)

在 *Models* 資料夾中，建立一個名為 *Student.cs* 的類別檔案，然後使用下列程式碼取代範本程式碼。

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID` 屬性會成為資料庫資料表中的主索引鍵資料行，並對應至這個類別。 根據預設，Entity Framework 會將名為 `ID` 或 `classnameID` 的屬性解譯為主索引鍵。

`Enrollments` 屬性為[導覽屬性](/ef/core/modeling/relationships)。 導覽屬性會保留與此實體相關的其他實體。 在這個案例中，`Student entity` 的 `Enrollments` 屬性會保有與該 `Student` 實體相關的所有 `Enrollment` 實體。 換句話說，如果 `Student` 資料庫中的資料列有兩個相關 `Enrollment` 的資料列 (在其 StudentID 外鍵資料行中包含該學生的主鍵值的資料列) ，該 `Student` 實體的 `Enrollments` 導覽屬性將會包含這兩個 `Enrollment` 實體。

若導覽屬性可保有多個實體 (例如在多對多或一對多關聯性中的情況)，其類型必須為一個清單，使得實體可以在該清單中新增、刪除或更新，例如 `ICollection<T>`。 您可以指定 `ICollection<T>` 或如 `List<T>` 或 `HashSet<T>` 等類型。 若您指定了 `ICollection<T>`，EF 會根據預設建立一個 `HashSet<T>` 集合。

### <a name="the-enrollment-entity"></a>Enrollment 實體

![Enrollment 實體圖表](intro/_static/enrollment-entity.png)

在 *Models* 資料夾中，建立 *Enrollment.cs* ，然後使用下列程式碼取代現有的程式碼：

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` 屬性將為主索引鍵。此實體會使用 `classnameID` 模式，而非您在 `Student` 實體中所見到的自身 `ID`。 通常您會選擇一個模式，然後在您整個資料模型中使用此模式。 在這裡，此變化僅作為向您展示使用不同模式之用。 在[稍後的教學課程](inheritance.md)中，您會了解使用沒有 classname 的識別碼可讓在資料模型中實作繼承變得更為簡單。

`Grade` 屬性為一個 `enum`。 問號之後的 `Grade` 類型宣告表示 `Grade` 屬性可為 Null。 為 Null 的成績不同於成績為零：Null 表示成績未知或尚未指派。

`StudentID` 屬性是外部索引鍵，對應的導覽屬性是 `Student`。 `Enrollment` 實體與一個 `Student` 實體關聯，因此屬性僅能保有單一 `Student` 實體 (不像您先前看到的 `Student.Enrollments` 導覽屬性可保有多個 `Enrollment` 實體)。

`CourseID` 屬性是外部索引鍵，對應的導覽屬性是 `Course`。 一個 `Enrollment` 實體與一個 `Course` 實體建立關聯。

Entity Framework 會將名為 `<navigation property name><primary key property name>` 的屬性解譯為外部索引鍵屬性 (例如 `Student` 導覽屬性的 `StudentID`，因為 `Student` 實體的主索引鍵為 `ID`)。 外部索引鍵屬性也可以簡單的命名為 `<primary key property name>` (例如 `CourseID`，因為 `Course` 實體的主索引鍵為 `CourseID`)。

### <a name="the-course-entity"></a>Course 實體

![Course 實體圖表](intro/_static/course-entity.png)

在 *Models* 資料夾中，建立 *Course.cs* ，然後使用下列程式碼取代現有的程式碼：

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments` 屬性為導覽屬性。 `Course` 實體可以與任何數量的 `Enrollment` 實體相關。

我們會在此系列[稍後的教學課程](complex-data-model.md)中進一步討論 `DatabaseGenerated` 屬性。 基本上，此屬性可讓您為課程輸入主索引鍵，而非讓資料庫產生它。

## <a name="create-the-database-context"></a>建立資料庫內容

為指定資料模型協調 Entity Framework 功能的主要類別便是資料庫內容類別。 若要建立此類別，您可以從 `Microsoft.EntityFrameworkCore.DbContext` 類別來衍生。 在您的程式碼中，您會指定資料模型中包含哪些實體。 您也可以自訂某些 Entity Framework 行為。 在此專案中，類別命名為 `SchoolContext`。

在專案資料夾中，建立名為 *Data* 的資料夾。

在 *Data* 資料夾中，建立名為 *SchoolContext.cs* 的新類別檔案，然後使用下列程式碼取代範本程式碼：

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

程式碼會為每一個實體集建立 `DbSet` 屬性。 在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。

您可以省略 `DbSet<Enrollment>` 及 `DbSet<Course>` 陳述式，其結果也會是相同的。 Entity Framework 會隱含它們，因為 `Student` 實體參考了 `Enrollment` 實體；而 `Enrollment` 實體參考了 `Course` 實體。

資料庫建立時，EF 會建立和 `DbSet` 屬性名稱相同的資料表。 集合的屬性名稱通常都是複數 (Students 而非 Student)，但許多開發人員會為了資料表名稱究竟是否該是複數型態而爭論。 在此系列教學課程中，您會藉由指定 DbContext 中的單數資料表名稱來覆寫預設行為。 若要完成這項操作，請在最後一個 DbSet 屬性後方新增下列醒目提示程式碼。

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

建置專案以檢查編譯器錯誤。

## <a name="register-the-schoolcontext"></a>註冊 SchoolContext

根據預設，ASP.NET Core 會實作[相依性插入](../../fundamentals/dependency-injection.md)。 服務 (例如 EF 資料庫內容) 是在應用程式啟動期間使用相依性插入來註冊。 接著，會透過建構函式參數，針對需要這些服務的元件 (例如 MVC 控制器) 來提供服務。 您會在此教學課程的稍後看到取得內容執行個體的控制器建構函式。

若要將 `SchoolContext` 註冊為服務，請開啟 *Startup.cs* ，並將醒目標示的程式碼新增至 `ConfigureServices` 方法。

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

連接字串的名稱，會透過呼叫 `DbContextOptionsBuilder` 物件上的方法來傳遞至內容。 針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。

為 `ContosoUniversity.Data` 和 `Microsoft.EntityFrameworkCore` 命名空間新增 `using` 陳述式，然後建置專案。

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

開啟檔案 *appsettings.json* ，並加入連接字串，如下列範例所示。

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

連接字串會指定 SQL Server LocalDB 資料庫。 LocalDB 是輕量版的 SQL Server Express Database Engine，旨在用於應用程式開發，而不是生產用途。 LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。 LocalDB 預設會在 `C:/Users/<user>` 目錄中建立 *.mdf* 資料庫檔案。

## <a name="initialize-db-with-test-data"></a>使用測試資料將 DB 初始化

Entity Framework 會為您建立空白資料庫。 在本節中，您會撰寫一個方法，該方法會在資料庫建立之後呼叫，以將測試資料填入資料庫。

在此您將使用 `EnsureCreated` 方法來自動建立資料庫。 在[稍後的教學課程](migrations.md)中，您將會了解到如何使用 Code First 移轉變更資料庫結構描述，而非卸除並重新建立資料庫，來處理模型的變更。

在 *Data* 資料夾中，建立一個名為 *DbInitializer.cs* 的新類別檔案，使用下列程式碼取代範本程式碼。這些程式碼會在需要的時候建立資料庫，並將測試資料載入至新的資料庫。

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

程式碼會檢查資料庫中是否有任何學生。若沒有的話，它便會假設資料庫是新的資料庫，因此需要植入測試資料。 會將測試資料載入至陣列而非 `List<T>` 集合，以最佳化效能。

在 *Program.cs* 中，修改 `Main` 方法來在應用程式啟動期間執行下列動作：

* 從相依性插入容器中取得資料庫內容執行個體。
* 呼叫種子方法，並將其傳遞給內容。
* 種子方法完成時處理內容。

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

當您第一次執行應用程式時，將會建立資料庫並植入測試資料。 每當您變更資料模型時：

 * 刪除資料庫。
 * 更新種子方法，並以相同方式開始使用新的資料庫再次。
 
在稍後的教學課程中，您會了解如何在資料模型變更時修改資料庫，而不需要刪除和重新建立它。

## <a name="create-controller-and-views"></a>建立控制器和檢視

在本節中，Visual Studio 中的樣板引擎會用來新增 MVC 控制器和將使用 EF 來查詢和儲存資料的視圖。

自動建立 CRUD 動作方法和檢視稱為 Scaffolding。 Scaffolding 與產生程式碼不同。Scaffold 程式碼是一個開始點，使得您可以修改它以符合您的需求，然而您通常不會去修改產生的程式碼。 當您需要自訂產生的程式碼時，您會使用部分類別，或者您會在事務變更時重新產生程式碼。

* 在方案總管中的 **Controllers** 資料夾上以滑鼠右鍵按一下，然後選取 [新增] > [新增 Scaffold 項目]。
* 在 [新增 Scaffold] 對話方塊中：
  * 選取 [使用 Entity Framework 執行檢視的 MVC 控制器]。
  * 按一下 [新增]  。 [ **Entity Framework 使用 Scaffold 新增 MVC 控制器** ] 對話方塊隨即出現： ![ Student](intro/_static/scaffold-student2.png)
  * 在 [模型類別] 中，選取 [Student]。
  * 在 [資料內容類別] 中，選取 [SchoolContext]。
  * 接受預設的 **StudentsController** 作為名稱。
  * 按一下 [新增]  。

Visual Studio 的樣板引擎會建立一個 *StudentsController.cs* 檔案和一組與控制器 (的 *cshtml* 檔案) 的視圖。

請注意，控制器會採用作為函式 `SchoolContext` 參數。

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Core 相依性插入會負責傳遞 `SchoolContext` 的執行個體給控制器。 這是在 *Startup.cs* 檔案中設定的。

控制器含有一個 `Index` 動作方法，該方法會顯示資料庫中的所有學生。 方法會藉由讀取資料庫內容執行個體的 `Students` 屬性，來從 Students 實體集中取得學生的清單：

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

您稍後會在本教學課程中瞭解此程式碼中的非同步程式設計項目。

*Views/Students/Index.cshtml* 檢視會在一個資料表中顯示這份清單：

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

按 CTRL+F5 來執行專案，或從功能表選擇 [偵錯] > [啟動但不偵錯]。

按一下 [Students] 索引標籤來查看 `DbInitializer.Initialize` 方法插入的測試資料。 取決於您瀏覽器視窗的寬度，您可能會在頁面的頂端看到 `Students` 索引標籤連結，或是按一下位於右上角的導覽圖示來查看連結。

![Contoso 大學首頁 (窄)](intro/_static/home-page-narrow.png)

![Students [索引] 頁面](intro/_static/students-index.png)

## <a name="view-the-database"></a>檢視資料庫

當您啟動應用程式時，`DbInitializer.Initialize` 方法會呼叫 `EnsureCreated`。 EF 看到不存在任何資料庫，於是便建立了一個資料庫，接著 `Initialize` 方法程式碼的剩餘部分便會將資料填入資料庫。 您可以使用 [SQL Server 物件總管 (SSOX) 來在 Visual Studio 中檢視資料庫。

關閉瀏覽器。

若 SSOX 視窗尚未開啟，請從 Visual Studio 中的 [檢視] 功能表選取它。

在 [SSOX] 中，按一下 **(localdb) \mssqllocaldb > 資料庫** ]，然後在檔案中的連接字串中，按一下資料庫名稱的專案 *appsettings.json* 。

展開 [ **資料表]** 節點，查看資料庫中的資料表。

![SSOX 中的資料表](intro/_static/ssox-tables.png)

以滑鼠右鍵按一下 **Students** 資料表，並按一下 [檢視資料] 查看建立的資料行及插入資料表中的資料列。

![SSOX 中的 Student 資料表](intro/_static/ssox-student-table.png)

*.mdf* 和 *.ldf* 資料庫檔案位於 *C:\Users\\\<username>* 資料夾中。

因為您在應用程式啟動時執行的初始設定式方法中呼叫了 `EnsureCreated`，您現在可以對 `Student` 類別進行變更、刪除資料庫、重新執行應用程式，資料庫會自動重新建立以符合您所作出的變更。 例如，若您將一個 `EmailAddress` 屬性新增到 `Student` 類別，您便會在重新建立的資料表中看到新的 `EmailAddress` 資料行。

## <a name="conventions"></a>慣例

為了讓 Entity Framework 能夠建立一個完整資料庫，您所需要撰寫的程式碼非常少，多虧了慣例的使用及 Entity Framework 所做出的假設。

* `DbSet` 屬性的名稱會用於資料表名稱。 針對 `DbSet` 屬性並未參考的實體，實體類別名稱會用於資料表名稱。
* 實體屬性名稱會用於資料行名稱。
* 命名為 ID 或 classnameID 的實體屬性，會辨識為主索引鍵屬性。
* 如果屬性名為 (的外鍵屬性，則會將其視為外鍵屬性 *\<navigation property name>\<primary key property name>* ，例如， `StudentID` `Student` 因為 `Student` 實體的主鍵是 `ID`) 。 外鍵屬性也可以簡單命名 *\<primary key property name>* (例如， `EnrollmentID` 因為 `Enrollment` 實體的主鍵是 `EnrollmentID`) 。

慣例行為可以被覆寫。 例如，您可以明確指定資料表名稱，如稍早在本教學課程中您所見到的。 您可以設定資料行名稱以及將任何屬性設為主索引鍵或外部索引鍵，如同您在本系列[稍後的教學課程](complex-data-model.md)中所見。

## <a name="asynchronous-code"></a>非同步程式碼

非同步程式設計是預設的 ASP.NET Core 和 EF Core 模式。

網頁伺服器的可用執行緒數量有限，而且在高負載情況下，可能會使用所有可用的執行緒。 發生此情況時，伺服器將無法處理新的要求，直到執行緒空出來。 使用同步程式碼，許多執行緒可能在實際上並未執行任何工作時受到占用，原因是在等候 I/O 完成。 使用非同步程式碼，處理程序在等候 I/O 完成時，其執行緒將會空出來以讓伺服器處理其他要求。 因此，非同步程式碼可讓伺服器資源更有效率地使用，而且伺服器可處理更多流量而不會造成延遲。

非同步程式碼雖然的確會在執行階段造成少量的負荷，但在低流量情況下，對效能的衝擊非常微小；在高流量情況下，潛在的效能改善則相當大。

在下列程式碼中，`async` 關鍵字、`Task<T>` 傳回值、`await` 關鍵字和 `ToListAsync` 方法使程式碼以非同步方式執行。

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async` 關鍵字會告訴編譯器為方法本體的一部分產生回呼，並自動建立傳回的 `Task<IActionResult>` 物件。
* 傳回類型 `Task<IActionResult>` 代表了正在進行的工作，其結果為 `IActionResult` 類型。
* `await` 關鍵字會使編譯器將方法分割為兩部分。 第一個部分會與非同步啟動的作業一同結束。 第二個部分則會放入作業完成時呼叫的回呼方法中。
* `ToListAsync` 是 `ToList` 擴充方法的非同步版本。

當您在撰寫使用 Entity Framework 的非同步程式碼時，請注意下列事項：

* 只有讓查詢或命令傳送至資料庫的陳述式，會以非同步方式執行。 其中包含，例如 `ToListAsync`、`SingleOrDefaultAsync`，以及 `SaveChangesAsync`。 其中不包含，例如：僅變更 `IQueryable` 的陳述式，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。
* EF 內容在執行緒中並不安全：不要嘗試執行多個平行作業。 當您呼叫任何 async EF 方法時，請一律使用 `await` 關鍵字。
* 若您想要充分利用非同步程式碼帶來的效能優點，請確保任何您正在使用的程式庫 (例如分頁) 也使用了非同步 (若它們有呼叫任何可能會傳送查詢到資料庫的 Entity Framework 方法的話)。

如需在 .NET 中非同步程式設計的詳細資訊，請參閱[非同步總覽](/dotnet/articles/standard/async)。

## <a name="next-steps"></a>後續步驟

若要了解如何執行基本的 CRUD (建立、讀取、更新、刪除) 作業，請前往下一個教學課程。

> [!div class="nextstepaction"]
> [實作基本的 CRUD 功能](crud.md)

::: moniker-end
