---
title: ':::no-loc(Razor)::: ASP.NET Core 中有 Entity Framework Core 的頁面-教學課程 1/8'
author: rick-anderson
description: '說明如何 :::no-loc(Razor)::: 使用 Entity Framework Core 建立頁面應用程式'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/intro
ms.openlocfilehash: c4b4f2b89be2018857abaafb448f052c3848ec59
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054069"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="3dddd-103">:::no-loc(Razor)::: ASP.NET Core 中有 Entity Framework Core 的頁面-教學課程 1/8</span><span class="sxs-lookup"><span data-stu-id="3dddd-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="3dddd-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3dddd-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3dddd-105">這是一系列教學課程中的第一個教學課程，示範如何在 [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) 應用程式中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="3dddd-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="3dddd-106">教學課程會為虛構的 Contoso 大學建置網站。</span><span class="sxs-lookup"><span data-stu-id="3dddd-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="3dddd-107">網站包含學生入學許可、課程建立和講師指派等功能。</span><span class="sxs-lookup"><span data-stu-id="3dddd-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="3dddd-108">本教學課程使用 code first 方法。</span><span class="sxs-lookup"><span data-stu-id="3dddd-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="3dddd-109">如需使用 database first 方法來遵循本教學課程的詳細資訊，請參閱 [此 Github 問題](https://github.com/dotnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="3dddd-110">下載或檢視已完成的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="3dddd-111">[下載指示](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3dddd-112">必要條件</span><span class="sxs-lookup"><span data-stu-id="3dddd-112">Prerequisites</span></span>

* <span data-ttu-id="3dddd-113">如果您還不熟悉 :::no-loc(Razor)::: 頁面，請先流覽「開始 [使用 :::no-loc(Razor)::: 頁面](xref:tutorials/razor-pages/razor-pages-start) 」教學課程系列，再開始此課程。</span><span class="sxs-lookup"><span data-stu-id="3dddd-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="3dddd-116">資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="3dddd-116">Database engines</span></span>

<span data-ttu-id="3dddd-117">Visual Studio 說明會使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是一種只在 Windows 上執行的 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="3dddd-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="3dddd-118">Visual Studio Code 說明則會使用 [SQLite](https://www.sqlite.org/)，它是一種跨平台的資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="3dddd-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="3dddd-119">若您選擇使用 SQLite，請下載及安裝協力廠商工具來管理和檢視 SQLite 資料庫，例如 [DB Browser for SQLite](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3dddd-120">疑難排解</span><span class="sxs-lookup"><span data-stu-id="3dddd-120">Troubleshooting</span></span>

<span data-ttu-id="3dddd-121">若您遇到無法解決的問題，請將您的程式碼與[已完成的專案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)進行比較。</span><span class="sxs-lookup"><span data-stu-id="3dddd-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="3dddd-122">取得協助的其中一種好方法是將問題張貼到 StackOverflow.com，並使用 [ASP.NET Core 標籤](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 標籤](https://stackoverflow.com/questions/tagged/entity-framework-core)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="3dddd-123">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="3dddd-123">The sample app</span></span>

<span data-ttu-id="3dddd-124">在教學課程中建立的應用程式，是一個基本的大學網站。</span><span class="sxs-lookup"><span data-stu-id="3dddd-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="3dddd-125">使用者可以檢視和更新學生、課程和教師資訊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="3dddd-126">以下是幾個在教學課程中建立的畫面。</span><span class="sxs-lookup"><span data-stu-id="3dddd-126">Here are a few of the screens created in the tutorial.</span></span>

![Students [索引] 頁面](intro/_static/students-index30.png)

![Students [編輯] 頁面](intro/_static/student-edit30.png)

<span data-ttu-id="3dddd-129">本網站的 UI 風格是以內建的專案範本為基礎。</span><span class="sxs-lookup"><span data-stu-id="3dddd-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="3dddd-130">本教學課程的重點在於如何搭配使用 EF Core 與 ASP.NET Core，而不是如何自訂 UI。</span><span class="sxs-lookup"><span data-stu-id="3dddd-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="3dddd-131">建立 Web 應用程式專案</span><span class="sxs-lookup"><span data-stu-id="3dddd-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3dddd-133">從 Visual Studio 的 [檔案] 功能表中，選取 [新增] **[專案]** >  。</span><span class="sxs-lookup"><span data-stu-id="3dddd-133">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3dddd-134">選取 **ASP.NET Core Web 應用程式** 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-134">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3dddd-135">將專案命名為 *ContosoUniversity* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="3dddd-136">使用與此名稱完全相符的名稱非常重要 (包括大寫)，這樣做可以讓命名空間在您複製和貼上程式碼時相符。</span><span class="sxs-lookup"><span data-stu-id="3dddd-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="3dddd-137">在下拉式清單中選取 [ **.Net Core** ] 和 [ **ASP.NET Core 5.0** ]，然後選取 [ **Web 應用程式** ]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3dddd-139">在終端機中，巡覽至應建立專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="3dddd-140">執行下列命令，以建立 :::no-loc(Razor)::: 頁面專案並 `cd` 加入至新的專案資料夾：</span><span class="sxs-lookup"><span data-stu-id="3dddd-140">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="3dddd-141">設定網站樣式</span><span class="sxs-lookup"><span data-stu-id="3dddd-141">Set up the site style</span></span>

<span data-ttu-id="3dddd-142">將下列程式碼複製並貼入 *Pages/Shared/_Layout cshtml* 檔案： [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="3dddd-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="3dddd-143">版面配置檔案會設定網站頁首、頁尾和功能表。</span><span class="sxs-lookup"><span data-stu-id="3dddd-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="3dddd-144">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="3dddd-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="3dddd-145">每次出現 "ContosoUniversity" 至 "Contoso 大學"。</span><span class="sxs-lookup"><span data-stu-id="3dddd-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="3dddd-146">共有三個發生次數。</span><span class="sxs-lookup"><span data-stu-id="3dddd-146">There are three occurrences.</span></span>
* <span data-ttu-id="3dddd-147">[ **首頁** ] 和 [ **隱私權** ] 功能表項目會被刪除。</span><span class="sxs-lookup"><span data-stu-id="3dddd-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="3dddd-148">針對「 **關於** 」、「 **學生** 」、「 **課程** 」、「 **講師** 」和「 **部門** 」新增專案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-148">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="3dddd-149">在 *Pages/Index. cshtml* 中，以下列程式碼取代檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="3dddd-149">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="3dddd-150">上述程式碼會將關於 ASP.NET Core 的文字取代為此應用程式的相關文字。</span><span class="sxs-lookup"><span data-stu-id="3dddd-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="3dddd-151">執行應用程式來驗證首頁是否正常顯示。</span><span class="sxs-lookup"><span data-stu-id="3dddd-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="3dddd-152">資料模型</span><span class="sxs-lookup"><span data-stu-id="3dddd-152">The data model</span></span>

<span data-ttu-id="3dddd-153">下列各節會建立資料模型：</span><span class="sxs-lookup"><span data-stu-id="3dddd-153">The following sections create a data model:</span></span>

![Course-Enrollment-Student 資料模型圖表](intro/_static/data-model-diagram.png)

<span data-ttu-id="3dddd-155">學生可以註冊任何數量的課程，課程也能讓任意數量的學生註冊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="3dddd-156">Student 實體</span><span class="sxs-lookup"><span data-stu-id="3dddd-156">The Student entity</span></span>

![Student 實體圖表](intro/_static/student-entity.png)

* <span data-ttu-id="3dddd-158">在專案資料夾中建立 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="3dddd-159">使用下列程式碼建立 *Models/Student.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="3dddd-160">`ID` 屬性會成為對應到此類別資料庫資料表的主索引鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="3dddd-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="3dddd-161">EF Core 預設會解譯名為 `ID` 或 `classnameID` 作為主索引鍵的屬性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="3dddd-162">因此 `Student` 類別主索引鍵的替代自動識別名稱為 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="3dddd-163">如需詳細資訊，請參閱 [EF Core 索引鍵](/ef/core/modeling/keys?tabs=data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="3dddd-164">`Enrollments` 屬性為[導覽屬性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="3dddd-165">導覽屬性會保留與此實體相關的其他實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="3dddd-166">在這種情況下，`Student` 實體的 `Enrollments` 屬性會保留所有與該 Student 相關的 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="3dddd-167">例如，若資料庫中的 Student 資料列有兩個相關的 Enrollment 資料列，則 `Enrollments` 導覽屬性便會包含這兩個 Enrollment 項目。</span><span class="sxs-lookup"><span data-stu-id="3dddd-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="3dddd-168">在資料庫中，若 Enrollment 資料列的 StudentID 資料行包含學生的識別碼值，則該資料列便會與 Student 資料列相關。</span><span class="sxs-lookup"><span data-stu-id="3dddd-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="3dddd-169">例如，假設某 Student 資料列的識別碼為 1。</span><span class="sxs-lookup"><span data-stu-id="3dddd-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="3dddd-170">相關的 Enrollment 資料列將會擁有 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="3dddd-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="3dddd-171">StudentID 是 Enrollment 資料表中的「外部索引鍵」。</span><span class="sxs-lookup"><span data-stu-id="3dddd-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="3dddd-172">`Enrollments` 屬性會定義為 `ICollection<Enrollment>`，因為可能會有多個相關的 Enrollment 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="3dddd-173">您可以使用其他集合型別，例如 `List<Enrollment>` 或 `HashSet<Enrollment>`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="3dddd-174">如使用 `ICollection<Enrollment>`，EF Core 預設將建立 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="3dddd-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="3dddd-175">Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="3dddd-175">The Enrollment entity</span></span>

![Enrollment 實體圖表](intro/_static/enrollment-entity.png)

<span data-ttu-id="3dddd-177">使用下列程式碼建立 *Models/Enrollment.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="3dddd-178">`EnrollmentID` 屬性是主索引鍵；這個實體會使用 `classnameID` 模式，而非 `ID` 本身。</span><span class="sxs-lookup"><span data-stu-id="3dddd-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="3dddd-179">針對生產資料模型，請選擇一個模式並一致地使用它。</span><span class="sxs-lookup"><span data-stu-id="3dddd-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="3dddd-180">本教學課程同時使用兩者的方式只是為了示範兩者都可運作。</span><span class="sxs-lookup"><span data-stu-id="3dddd-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="3dddd-181">在不使用 `classname` 的情況下使用 `ID` 可讓實作某些類型的資料模型變更更容易。</span><span class="sxs-lookup"><span data-stu-id="3dddd-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="3dddd-182">`Grade` 屬性為一個 `enum`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="3dddd-183">`Grade` 型別宣告後方的問號表示 `Grade` 屬性[可為 Null](/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="3dddd-184">為 Null 的成績不同於成績為零&mdash;Null 表示成績未知或尚未指派。</span><span class="sxs-lookup"><span data-stu-id="3dddd-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="3dddd-185">`StudentID` 屬性是外部索引鍵，對應的導覽屬性是 `Student`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="3dddd-186">一個 `Enrollment` 實體與一個 `Student` 實體建立關聯，因此該屬性包含單一 `Student` 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="3dddd-187">`CourseID` 屬性是外部索引鍵，對應的導覽屬性是 `Course`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="3dddd-188">一個 `Enrollment` 實體與一個 `Course` 實體建立關聯。</span><span class="sxs-lookup"><span data-stu-id="3dddd-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="3dddd-189">如果實體名為 `<navigation property name><primary key property name>`，則 EF Core 會將之解釋為外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="3dddd-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="3dddd-190">例如，`StudentID` 是 `Student` 導覽屬性的外部索引鍵，因為 `Student` 實體的主索引鍵是 `ID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="3dddd-191">外部索引鍵屬性也可命名為 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="3dddd-192">例如 `CourseID`，因為 `Course` 實體的主索引鍵是 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="3dddd-193">Course 實體</span><span class="sxs-lookup"><span data-stu-id="3dddd-193">The Course entity</span></span>

![Course 實體圖表](intro/_static/course-entity.png)

<span data-ttu-id="3dddd-195">使用下列程式碼建立 *Models/Course.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="3dddd-196">`Enrollments` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="3dddd-197">`Course` 實體可以與任何數量的 `Enrollment` 實體相關。</span><span class="sxs-lookup"><span data-stu-id="3dddd-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="3dddd-198">`DatabaseGenerated` 屬性可讓應用程式指定主索引鍵，而非讓資料庫產生它。</span><span class="sxs-lookup"><span data-stu-id="3dddd-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="3dddd-199">建置專案以驗證沒有任何編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="3dddd-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="3dddd-200">Scaffold Student 頁面</span><span class="sxs-lookup"><span data-stu-id="3dddd-200">Scaffold Student pages</span></span>

<span data-ttu-id="3dddd-201">在本節中，您會使用 ASP.NET scaffolding 工具來產生：</span><span class="sxs-lookup"><span data-stu-id="3dddd-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="3dddd-202">EF Core `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="3dddd-203">內容是協調指定資料模型 Entity Framework 功能的主類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="3dddd-204">它衍生自 <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> 類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="3dddd-205">:::no-loc(Razor)::: 處理實體之建立、讀取、更新和刪除 (CRUD) 作業的頁面 `Student` 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-205">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3dddd-207">建立 *Pages/Students* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="3dddd-208">在 [方案總管] 中，以滑鼠右鍵按一下 *Page/Students* 資料夾，然後選取 [新增] **[新增 Scaffold 項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-208">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3dddd-209">在 [ **加入新的 Scaffold 專案** ] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="3dddd-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="3dddd-210">在左側索引標籤中，選取 [ **已安裝 > 一般 > :::no-loc(Razor)::: 頁面** ]</span><span class="sxs-lookup"><span data-stu-id="3dddd-210">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="3dddd-211">**:::no-loc(Razor)::: 使用 Entity Framework (CRUD)** 新增] 選取頁面 > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-211">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="3dddd-212">在 [ **:::no-loc(Razor)::: 使用 Entity Framework 加入頁面] (CRUD)** 對話方塊：</span><span class="sxs-lookup"><span data-stu-id="3dddd-212">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="3dddd-213">在 [模型類別] 下拉式清單中，選取 [學生 (ContosoUniversity.Models)]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="3dddd-214">在 [資料內容類別] 資料列中，選取 **+** (加號)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="3dddd-215">將資料內容名稱變更為 end， `SchoolContext` 而不是 `ContosoUniversityContext` 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="3dddd-216">更新的內容名稱： `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="3dddd-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="3dddd-217">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="3dddd-217">Select **Add**.</span></span>

<span data-ttu-id="3dddd-218">會自動安裝下列套件：</span><span class="sxs-lookup"><span data-stu-id="3dddd-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3dddd-220">執行下列 .NET Core CLI 命令來安裝必要的 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="3dddd-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="3dddd-221">Microsoft.VisualStudio.Web.CodeGeneration.Design 套件是進行 scaffolding 時的必要項目。</span><span class="sxs-lookup"><span data-stu-id="3dddd-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="3dddd-222">雖然應用程式不會使用 SQL Server，scaffolding 工具仍需要 SQL Server 套件。</span><span class="sxs-lookup"><span data-stu-id="3dddd-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="3dddd-223">建立 *Pages/Students* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="3dddd-224">執行下列命令來安裝 [aspnet-codegenerator scaffolding 工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="3dddd-225">執行下列命令來 scaffold Student 頁面。</span><span class="sxs-lookup"><span data-stu-id="3dddd-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="3dddd-226">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="3dddd-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="3dddd-227">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="3dddd-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="3dddd-228">如果上述步驟失敗，請建立專案，然後重試 scaffold 步驟。</span><span class="sxs-lookup"><span data-stu-id="3dddd-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="3dddd-229">Scaffolding 流程：</span><span class="sxs-lookup"><span data-stu-id="3dddd-229">The scaffolding process:</span></span>

* <span data-ttu-id="3dddd-230">:::no-loc(Razor):::在 [ *頁面/學生* ] 資料夾中建立頁面：</span><span class="sxs-lookup"><span data-stu-id="3dddd-230">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="3dddd-231">*Create.cshtml* 和 *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="3dddd-232">*Delete.cshtml* 和 *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="3dddd-233">*Details.cshtml* 和 *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="3dddd-234">*Edit.cshtml* 和 *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="3dddd-235">*Index.cshtml* 和 *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="3dddd-236">建立 *Data/SchoolContext.cs* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-236">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="3dddd-237">將內容新增到 *Startup.cs* 中的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="3dddd-237">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="3dddd-238">將資料庫連接字串加入至 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-238">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="3dddd-239">資料庫連接字串</span><span class="sxs-lookup"><span data-stu-id="3dddd-239">Database connection string</span></span>

<span data-ttu-id="3dddd-240">「樣板」工具會在檔案中產生連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-240">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3dddd-242">連接字串會指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)：</span><span class="sxs-lookup"><span data-stu-id="3dddd-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="3dddd-243">LocalDB 是輕量版的 SQL Server Express Database Engine，旨在用於應用程序開發，而不是生產用途。</span><span class="sxs-lookup"><span data-stu-id="3dddd-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="3dddd-244">根據預設，LocalDB 會在 `C:/Users/<user>` 目錄中建立 *.mdf* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3dddd-246">將 SQLite 連接字串縮短為 *CU. db* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-246">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="3dddd-247">更新資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="3dddd-247">Update the database context class</span></span>

<span data-ttu-id="3dddd-248">協調指定資料模型 EF Core 功能的主類別是資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="3dddd-249">內容衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3dddd-250">內容會指定哪些實體會包含在資料模型中。</span><span class="sxs-lookup"><span data-stu-id="3dddd-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="3dddd-251">在此專案中，類別命名為 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="3dddd-252">使用下列程式碼來更新 *Data/SchoolContext.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="3dddd-253">上述程式碼會從單數變更 `DbSet<Student> Student` 為複數 `DbSet<Student> Students` 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="3dddd-254">若要讓 :::no-loc(Razor)::: 頁面程式碼符合新的 `DBSet` 名稱，請從下列內容進行全域變更： `_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="3dddd-254">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="3dddd-255">自： `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="3dddd-255">to: `_context.Students.`</span></span>

<span data-ttu-id="3dddd-256">會有 8 次變更。</span><span class="sxs-lookup"><span data-stu-id="3dddd-256">There are 8 occurrences.</span></span>

<span data-ttu-id="3dddd-257">因為實體集包含多個實體，所以許多開發人員偏好 `DBSet` 屬性名稱應為複數。</span><span class="sxs-lookup"><span data-stu-id="3dddd-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="3dddd-258">反白顯示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="3dddd-258">The highlighted code:</span></span>

* <span data-ttu-id="3dddd-259">建立每個實體集的[DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="3dddd-260">在 EF Core 用語中：</span><span class="sxs-lookup"><span data-stu-id="3dddd-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="3dddd-261">實體集通常會對應到資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="3dddd-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="3dddd-262">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3dddd-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="3dddd-263">呼叫 <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>。</span><span class="sxs-lookup"><span data-stu-id="3dddd-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="3dddd-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="3dddd-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="3dddd-265">當已 `SchoolContext` 初始化，但模型已鎖定並用來初始化內容之前，會呼叫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="3dddd-266">是必要的，因為稍後在教學課程中， `Student` 實體會有其他實體的參考。</span><span class="sxs-lookup"><span data-stu-id="3dddd-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="3dddd-267">建置專案以確認沒有任何編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="3dddd-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="3dddd-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3dddd-268">Startup.cs</span></span>

<span data-ttu-id="3dddd-269">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3dddd-270">等服務 `SchoolContext` 會在應用程式啟動期間註冊相依性插入。</span><span class="sxs-lookup"><span data-stu-id="3dddd-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="3dddd-271">需要這些服務的元件（例如 :::no-loc(Razor)::: 頁面）是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="3dddd-271">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="3dddd-272">取得資料庫內容執行個體的建構函式程式碼會顯示在本教學課程稍後部分。</span><span class="sxs-lookup"><span data-stu-id="3dddd-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3dddd-273">Scaffolding 工具會自動對相依性插入容器註冊內容類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3dddd-275">Scaffolder 已新增下列醒目提示的行：</span><span class="sxs-lookup"><span data-stu-id="3dddd-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3dddd-277">確認 scaffolder 呼叫所加入的程式碼 `UseSqlite` 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="3dddd-278">如需使用生產資料庫的詳細資訊，請參閱 [使用 SQLite 進行開發、SQL Server 用於生產環境](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="3dddd-279">連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="3dddd-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3dddd-280">針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="3dddd-281">新增資料庫例外狀況篩選準則</span><span class="sxs-lookup"><span data-stu-id="3dddd-281">Add the database exception filter</span></span>

<span data-ttu-id="3dddd-282">將加入 `AddDatabaseDeveloperPageExceptionFilter` 至， `ConfigureServices` 如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="3dddd-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="3dddd-284">新增 [AspNetCore Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="3dddd-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="3dddd-285">在 PMC 中，輸入下列命令以新增 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="3dddd-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="3dddd-287">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet 套件提供 Entity Framework Core 錯誤頁面 ASP.NET Core 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="3dddd-288">此中介軟體有助於偵測並診斷 Entity Framework Core 遷移的錯誤。</span><span class="sxs-lookup"><span data-stu-id="3dddd-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="3dddd-289">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="3dddd-289">Create the database</span></span>

<span data-ttu-id="3dddd-290">若未存在資料庫，則請更新 *Program.cs* 來加以建立：</span><span class="sxs-lookup"><span data-stu-id="3dddd-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="3dddd-291">若已存在內容的資料庫，則 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法便不會採取任何動作。</span><span class="sxs-lookup"><span data-stu-id="3dddd-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="3dddd-292">若資料庫不存在，則它會建立資料庫和結構描述。</span><span class="sxs-lookup"><span data-stu-id="3dddd-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="3dddd-293">`EnsureCreated` 會啟用下列工作流程來處理資料模型變更：</span><span class="sxs-lookup"><span data-stu-id="3dddd-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="3dddd-294">刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-294">Delete the database.</span></span> <span data-ttu-id="3dddd-295">任何現有的資料都會遺失。</span><span class="sxs-lookup"><span data-stu-id="3dddd-295">Any existing data is lost.</span></span>
* <span data-ttu-id="3dddd-296">變更資料模型。</span><span class="sxs-lookup"><span data-stu-id="3dddd-296">Change the data model.</span></span> <span data-ttu-id="3dddd-297">例如，新增 `EmailAddress` 欄位。</span><span class="sxs-lookup"><span data-stu-id="3dddd-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="3dddd-298">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-298">Run the app.</span></span>
* <span data-ttu-id="3dddd-299">`EnsureCreated` 會使用新的結構描述來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="3dddd-300">只要您不需要保存資料，此工作流程在開發初期結構描述快速變化時的運作效果便會相當良好。</span><span class="sxs-lookup"><span data-stu-id="3dddd-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="3dddd-301">當資料輸入資料庫且需要進行保存時，狀況則會不同。</span><span class="sxs-lookup"><span data-stu-id="3dddd-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="3dddd-302">在這種情況下，請使用移轉。</span><span class="sxs-lookup"><span data-stu-id="3dddd-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="3dddd-303">在教學課程系列的稍後部分，您會刪除 `EnsureCreated` 建立的資料庫並改為使用移轉。</span><span class="sxs-lookup"><span data-stu-id="3dddd-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="3dddd-304">`EnsureCreated` 建立的資料庫無法使用移轉來更新。</span><span class="sxs-lookup"><span data-stu-id="3dddd-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3dddd-305">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="3dddd-305">Test the app</span></span>

* <span data-ttu-id="3dddd-306">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-306">Run the app.</span></span>
* <span data-ttu-id="3dddd-307">選取 [學生] 連結，然後選取 [新建]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-307">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="3dddd-308">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="3dddd-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="3dddd-309">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="3dddd-309">Seed the database</span></span>

<span data-ttu-id="3dddd-310">`EnsureCreated` 方法會建立空白資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="3dddd-311">本節會新增程式碼以使用測試資料來填入資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="3dddd-312">使用下列程式碼建立 *Data/DbInitializer.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="3dddd-313">程式碼會檢查資料庫中是否有任何學生。</span><span class="sxs-lookup"><span data-stu-id="3dddd-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="3dddd-314">若沒有任何學生，它便會將測試資料新增到資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="3dddd-315">它會以陣列的方式建立測試資料，而非 `List<T>` 集合，來最佳化效能。</span><span class="sxs-lookup"><span data-stu-id="3dddd-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="3dddd-316">在 *Program.cs* 中，將 `EnsureCreated` 呼叫替換成 `DbInitializer.Initialize` 呼叫：</span><span class="sxs-lookup"><span data-stu-id="3dddd-316">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3dddd-318">停止應用程式 (如果它正在執行)，並在 **套件管理員主控台** (PMC) 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3dddd-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="3dddd-319">回應 `Y` 以刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3dddd-321">若應用程式正在執行中，請停止它，然後刪除 *CU.db* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="3dddd-322">重新啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-322">Restart the app.</span></span>
* <span data-ttu-id="3dddd-323">選取 Students 頁面來查看植入的資料。</span><span class="sxs-lookup"><span data-stu-id="3dddd-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="3dddd-324">檢視資料庫</span><span class="sxs-lookup"><span data-stu-id="3dddd-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3dddd-326">從 Visual Studio 中的 **View** 功能表開啟 **SQL Server 物件總管** (SSOX)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="3dddd-327">在 SSOX 中，選取 [(localdb)\MSSQLLocalDB] > [資料庫] > [SchoolContext-{GUID}]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="3dddd-328">資料庫名稱是以您稍早所提供的內容名稱加上虛線和 GUID 所產生。</span><span class="sxs-lookup"><span data-stu-id="3dddd-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="3dddd-329">展開 **Tables** 節點。</span><span class="sxs-lookup"><span data-stu-id="3dddd-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="3dddd-330">以滑鼠右鍵按一下 **Students** 資料表，並按一下 [檢視資料] 查看建立的資料行、插入資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3dddd-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="3dddd-331">以滑鼠右鍵按一下 **Student** 資料表然後按一下 [檢視程式碼] 來查看 `Student` 模型對應到 `Student` 資料表結構描述的方式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3dddd-333">使用 SQLite 工具來檢視資料庫結構描述和植入的資料。</span><span class="sxs-lookup"><span data-stu-id="3dddd-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="3dddd-334">資料庫檔案名為 *CU.db* 且位於專案資料夾中。</span><span class="sxs-lookup"><span data-stu-id="3dddd-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="3dddd-335">非同步程式碼</span><span class="sxs-lookup"><span data-stu-id="3dddd-335">Asynchronous code</span></span>

<span data-ttu-id="3dddd-336">非同步程式設計是預設的 ASP.NET Core 和 EF Core 模式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="3dddd-337">網頁伺服器的可用執行緒數量有限，而且在高負載情況下，可能會使用所有可用的執行緒。</span><span class="sxs-lookup"><span data-stu-id="3dddd-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="3dddd-338">發生此情況時，伺服器將無法處理新的要求，直到執行緒空出來。</span><span class="sxs-lookup"><span data-stu-id="3dddd-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="3dddd-339">使用同步程式碼時，許多執行緒可能會在未執行工作的情況下進行系結，因為它們正在等候 i/o 完成。</span><span class="sxs-lookup"><span data-stu-id="3dddd-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="3dddd-340">使用非同步程式碼，處理程序在等候 I/O 完成時，其執行緒將會空出來以讓伺服器處理其他要求。</span><span class="sxs-lookup"><span data-stu-id="3dddd-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="3dddd-341">因此，非同步程式碼可以更有效率地使用伺服器資源，且伺服器可處理更多流量而不會造成延遲。</span><span class="sxs-lookup"><span data-stu-id="3dddd-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="3dddd-342">非同步程式碼會在執行階段導致少量的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="3dddd-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="3dddd-343">在低流量情況下，對效能的衝擊非常微小；在高流量情況下，潛在的效能改善則相當大。</span><span class="sxs-lookup"><span data-stu-id="3dddd-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="3dddd-344">在下列程式碼中，[async](/dotnet/csharp/language-reference/keywords/async) 關鍵字、`Task<T>` 傳回值、`await` 關鍵字和 `ToListAsync` 方法會使程式碼以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="3dddd-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="3dddd-345">`async` 關鍵字會指示編譯器：</span><span class="sxs-lookup"><span data-stu-id="3dddd-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="3dddd-346">為方法主體的組件產生回呼。</span><span class="sxs-lookup"><span data-stu-id="3dddd-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="3dddd-347">建立傳回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 物件。</span><span class="sxs-lookup"><span data-stu-id="3dddd-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="3dddd-348">`Task<T>` 傳回型別代表正在進行的工作。</span><span class="sxs-lookup"><span data-stu-id="3dddd-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="3dddd-349">`await` 關鍵字會使編譯器將方法分割為兩部分。</span><span class="sxs-lookup"><span data-stu-id="3dddd-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="3dddd-350">第一個部分會與非同步啟動的作業一同結束。</span><span class="sxs-lookup"><span data-stu-id="3dddd-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="3dddd-351">第二個部分則會放入作業完成時呼叫的回呼方法中。</span><span class="sxs-lookup"><span data-stu-id="3dddd-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="3dddd-352">`ToListAsync` 是 `ToList` 擴充方法的非同步版本。</span><span class="sxs-lookup"><span data-stu-id="3dddd-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="3dddd-353">若要撰寫使用 EF Core 的非同步程式碼，請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="3dddd-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="3dddd-354">只有讓查詢或命令傳送至資料庫的陳述式，會以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="3dddd-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="3dddd-355">其中包含 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="3dddd-356">不會包含只變更 `IQueryable` 的陳述式，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="3dddd-357">EF Core 內容在執行緒中並不安全：不要嘗試執行多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="3dddd-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="3dddd-358">若要利用非同步程式碼所帶來的效能利益，請驗證該程式庫套件 (例如用於分頁) 在呼叫傳送查詢到資料庫的 EF Core 方法時使用 async。</span><span class="sxs-lookup"><span data-stu-id="3dddd-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="3dddd-359">如需非同步方法的詳細資訊，請參閱 [Async 概觀](/dotnet/standard/async)和[使用 Async 和 Await 設計非同步程式](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="3dddd-360">效能考量</span><span class="sxs-lookup"><span data-stu-id="3dddd-360">Performance considerations</span></span>

<span data-ttu-id="3dddd-361">一般而言，網頁不應該載入任意數目的資料列。</span><span class="sxs-lookup"><span data-stu-id="3dddd-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="3dddd-362">查詢應該使用分頁或限制方法。</span><span class="sxs-lookup"><span data-stu-id="3dddd-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="3dddd-363">例如，上述查詢可以用 `Take` 來限制傳回的資料列：</span><span class="sxs-lookup"><span data-stu-id="3dddd-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="3dddd-364">如果資料庫例外狀況是透過列舉的一部分發生，則在 view 中列舉大型資料表可能會傳回部分建立的 HTTP 200 回應。</span><span class="sxs-lookup"><span data-stu-id="3dddd-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="3dddd-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> 預設為1024。</span><span class="sxs-lookup"><span data-stu-id="3dddd-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="3dddd-366">下列程式碼會設定 `MaxModelBindingCollectionSize` ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="3dddd-367">本教學課程稍後會討論分頁。</span><span class="sxs-lookup"><span data-stu-id="3dddd-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3dddd-368">後續步驟</span><span class="sxs-lookup"><span data-stu-id="3dddd-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3dddd-369">下一個教學課程</span><span class="sxs-lookup"><span data-stu-id="3dddd-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3dddd-370">這是一系列教學課程中的第一個教學課程，示範如何在 [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) 應用程式中使用 Entity Framework (EF) Core。</span><span class="sxs-lookup"><span data-stu-id="3dddd-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="3dddd-371">教學課程會為虛構的 Contoso 大學建置網站。</span><span class="sxs-lookup"><span data-stu-id="3dddd-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="3dddd-372">網站包含學生入學許可、課程建立和講師指派等功能。</span><span class="sxs-lookup"><span data-stu-id="3dddd-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="3dddd-373">本教學課程使用 code first 方法。</span><span class="sxs-lookup"><span data-stu-id="3dddd-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="3dddd-374">如需使用 database first 方法來遵循本教學課程的詳細資訊，請參閱 [此 Github 問題](https://github.com/dotnet/AspNetCore.Docs/issues/16897)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="3dddd-375">下載或檢視已完成的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="3dddd-376">[下載指示](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3dddd-377">必要條件</span><span class="sxs-lookup"><span data-stu-id="3dddd-377">Prerequisites</span></span>

* <span data-ttu-id="3dddd-378">如果您還不熟悉 :::no-loc(Razor)::: 頁面，請先流覽「開始 [使用 :::no-loc(Razor)::: 頁面](xref:tutorials/razor-pages/razor-pages-start) 」教學課程系列，再開始此課程。</span><span class="sxs-lookup"><span data-stu-id="3dddd-378">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="3dddd-381">資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="3dddd-381">Database engines</span></span>

<span data-ttu-id="3dddd-382">Visual Studio 說明會使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是一種只在 Windows 上執行的 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="3dddd-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="3dddd-383">Visual Studio Code 說明則會使用 [SQLite](https://www.sqlite.org/)，它是一種跨平台的資料庫引擎。</span><span class="sxs-lookup"><span data-stu-id="3dddd-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="3dddd-384">若您選擇使用 SQLite，請下載及安裝協力廠商工具來管理和檢視 SQLite 資料庫，例如 [DB Browser for SQLite](https://sqlitebrowser.org/)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3dddd-385">疑難排解</span><span class="sxs-lookup"><span data-stu-id="3dddd-385">Troubleshooting</span></span>

<span data-ttu-id="3dddd-386">若您遇到無法解決的問題，請將您的程式碼與[已完成的專案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)進行比較。</span><span class="sxs-lookup"><span data-stu-id="3dddd-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="3dddd-387">取得協助的其中一種好方法是將問題張貼到 StackOverflow.com，並使用 [ASP.NET Core 標籤](https://stackoverflow.com/questions/tagged/asp.net-core)或 [EF Core 標籤](https://stackoverflow.com/questions/tagged/entity-framework-core)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="3dddd-388">範例應用程式</span><span class="sxs-lookup"><span data-stu-id="3dddd-388">The sample app</span></span>

<span data-ttu-id="3dddd-389">在教學課程中建立的應用程式，是一個基本的大學網站。</span><span class="sxs-lookup"><span data-stu-id="3dddd-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="3dddd-390">使用者可以檢視和更新學生、課程和教師資訊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="3dddd-391">以下是幾個在教學課程中建立的畫面。</span><span class="sxs-lookup"><span data-stu-id="3dddd-391">Here are a few of the screens created in the tutorial.</span></span>

![Students [索引] 頁面](intro/_static/students-index30.png)

![Students [編輯] 頁面](intro/_static/student-edit30.png)

<span data-ttu-id="3dddd-394">本網站的 UI 風格是以內建的專案範本為基礎。</span><span class="sxs-lookup"><span data-stu-id="3dddd-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="3dddd-395">教學課程的重點在於如何使用 EF Core，而非如何自訂 UI。</span><span class="sxs-lookup"><span data-stu-id="3dddd-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="3dddd-396">請遵循頁面頂端的連結來取得已完成專案的原始程式碼。</span><span class="sxs-lookup"><span data-stu-id="3dddd-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="3dddd-397">*cu30* 資料夾包含本教學課程 ASP.NET Core 3.0 版本的程式碼。</span><span class="sxs-lookup"><span data-stu-id="3dddd-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="3dddd-398">您可以在 *cu30snapshots* 資料夾中找到反映教學課程 1 到 7 程式碼狀態的檔案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3dddd-400">在下載已完成的專案後執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="3dddd-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="3dddd-401">建置專案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-401">Build the project.</span></span>
* <span data-ttu-id="3dddd-402">在套件管理器主控台 (PMC) 中，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3dddd-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="3dddd-403">執行專案來植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3dddd-405">在下載已完成的專案後執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="3dddd-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="3dddd-406">刪除 *ContosoUniversity.csproj* ，並將 *ContosoUniversitySQLite.csproj* 重新命名為 *ContosoUniversity.csproj* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-406">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="3dddd-407">在 *Program.cs* 中， `#define Startup` `StartupSQLite` 將會使用批註。</span><span class="sxs-lookup"><span data-stu-id="3dddd-407">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="3dddd-408">刪除 *appSettings.json* ，並將 *appSettingsSQLite.json* 重新命名為 *appSettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-408">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="3dddd-409">刪除 *Migrations* 資料夾，並將 *MigrationsSQL* 重新命名為 *Migrations* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="3dddd-410">進行 `#if SQLiteVersion` 和移除的全域搜尋 `#if SQLiteVersion` 以及相關聯的 `#endif` 語句。</span><span class="sxs-lookup"><span data-stu-id="3dddd-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="3dddd-411">建置專案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-411">Build the project.</span></span>
* <span data-ttu-id="3dddd-412">在專案資料夾中的命令提示字元內，執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3dddd-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="3dddd-413">在您的 SQLite 工具中，執行下列 SQL 陳述式：</span><span class="sxs-lookup"><span data-stu-id="3dddd-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="3dddd-414">執行專案來植入資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="3dddd-415">建立 Web 應用程式專案</span><span class="sxs-lookup"><span data-stu-id="3dddd-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3dddd-417">從 Visual Studio 的 [檔案] 功能表中，選取 [新增] **[專案]** >  。</span><span class="sxs-lookup"><span data-stu-id="3dddd-417">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3dddd-418">選取 **ASP.NET Core Web 應用程式** 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-418">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3dddd-419">將專案命名為 *ContosoUniversity* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-419">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="3dddd-420">使用與此名稱完全相符的名稱非常重要 (包括大寫)，這樣做可以讓命名空間在您複製和貼上程式碼時相符。</span><span class="sxs-lookup"><span data-stu-id="3dddd-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="3dddd-421">在下拉式清單中選取 [.NET Core] 及 [ASP.NET Core 3.0]，然後選取 [Web 應用程式]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3dddd-423">在終端機中，巡覽至應建立專案資料夾的資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="3dddd-424">執行下列命令，以建立 :::no-loc(Razor)::: 頁面專案並 `cd` 加入至新的專案資料夾：</span><span class="sxs-lookup"><span data-stu-id="3dddd-424">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="3dddd-425">設定網站樣式</span><span class="sxs-lookup"><span data-stu-id="3dddd-425">Set up the site style</span></span>

<span data-ttu-id="3dddd-426">更新 *Pages/Shared/_Layout.cshtml* 來設定網站頁首、頁尾和功能表：</span><span class="sxs-lookup"><span data-stu-id="3dddd-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="3dddd-427">將每個出現的 "ContosoUniversity" 都變更為 "Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="3dddd-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="3dddd-428">共有三個發生次數。</span><span class="sxs-lookup"><span data-stu-id="3dddd-428">There are three occurrences.</span></span>

* <span data-ttu-id="3dddd-429">刪除 [Home] 和 [Privacy] 功能表項目，然後新增 [About]、[Students]、[Courses]、[Instructors] 和 [Departments] 的項目。</span><span class="sxs-lookup"><span data-stu-id="3dddd-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="3dddd-430">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="3dddd-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="3dddd-431">在 *Pages/Index.cshtml* 中，將檔案內容替換成下列程式碼，將 ASP.NET Core 相關文字取代成此應用程式的相關文字：</span><span class="sxs-lookup"><span data-stu-id="3dddd-431">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="3dddd-432">執行應用程式來驗證首頁是否正常顯示。</span><span class="sxs-lookup"><span data-stu-id="3dddd-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="3dddd-433">資料模型</span><span class="sxs-lookup"><span data-stu-id="3dddd-433">The data model</span></span>

<span data-ttu-id="3dddd-434">下列各節會建立資料模型：</span><span class="sxs-lookup"><span data-stu-id="3dddd-434">The following sections create a data model:</span></span>

![Course-Enrollment-Student 資料模型圖表](intro/_static/data-model-diagram.png)

<span data-ttu-id="3dddd-436">學生可以註冊任何數量的課程，課程也能讓任意數量的學生註冊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="3dddd-437">Student 實體</span><span class="sxs-lookup"><span data-stu-id="3dddd-437">The Student entity</span></span>

![Student 實體圖表](intro/_static/student-entity.png)

* <span data-ttu-id="3dddd-439">在專案資料夾中建立 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="3dddd-440">使用下列程式碼建立 *Models/Student.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="3dddd-441">`ID` 屬性會成為對應到此類別資料庫資料表的主索引鍵資料行。</span><span class="sxs-lookup"><span data-stu-id="3dddd-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="3dddd-442">EF Core 預設會解譯名為 `ID` 或 `classnameID` 作為主索引鍵的屬性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="3dddd-443">因此 `Student` 類別主索引鍵的替代自動識別名稱為 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="3dddd-444">如需詳細資訊，請參閱 [EF Core 索引鍵](/ef/core/modeling/keys?tabs=data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="3dddd-445">`Enrollments` 屬性為[導覽屬性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="3dddd-446">導覽屬性會保留與此實體相關的其他實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="3dddd-447">在這種情況下，`Student` 實體的 `Enrollments` 屬性會保留所有與該 Student 相關的 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="3dddd-448">例如，若資料庫中的 Student 資料列有兩個相關的 Enrollment 資料列，則 `Enrollments` 導覽屬性便會包含這兩個 Enrollment 項目。</span><span class="sxs-lookup"><span data-stu-id="3dddd-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="3dddd-449">在資料庫中，若 Enrollment 資料列的 StudentID 資料行包含學生的識別碼值，則該資料列便會與 Student 資料列相關。</span><span class="sxs-lookup"><span data-stu-id="3dddd-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="3dddd-450">例如，假設某 Student 資料列的識別碼為 1。</span><span class="sxs-lookup"><span data-stu-id="3dddd-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="3dddd-451">相關的 Enrollment 資料列將會擁有 StudentID = 1。</span><span class="sxs-lookup"><span data-stu-id="3dddd-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="3dddd-452">StudentID 是 Enrollment 資料表中的「外部索引鍵」。</span><span class="sxs-lookup"><span data-stu-id="3dddd-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="3dddd-453">`Enrollments` 屬性會定義為 `ICollection<Enrollment>`，因為可能會有多個相關的 Enrollment 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="3dddd-454">您可以使用其他集合型別，例如 `List<Enrollment>` 或 `HashSet<Enrollment>`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="3dddd-455">如使用 `ICollection<Enrollment>`，EF Core 預設將建立 `HashSet<Enrollment>` 集合。</span><span class="sxs-lookup"><span data-stu-id="3dddd-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="3dddd-456">Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="3dddd-456">The Enrollment entity</span></span>

![Enrollment 實體圖表](intro/_static/enrollment-entity.png)

<span data-ttu-id="3dddd-458">使用下列程式碼建立 *Models/Enrollment.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="3dddd-459">`EnrollmentID` 屬性是主索引鍵；這個實體會使用 `classnameID` 模式，而非 `ID` 本身。</span><span class="sxs-lookup"><span data-stu-id="3dddd-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="3dddd-460">針對生產資料模型，請選擇一個模式並一致地使用它。</span><span class="sxs-lookup"><span data-stu-id="3dddd-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="3dddd-461">本教學課程同時使用兩者的方式只是為了示範兩者都可運作。</span><span class="sxs-lookup"><span data-stu-id="3dddd-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="3dddd-462">在不使用 `classname` 的情況下使用 `ID` 可讓實作某些類型的資料模型變更更容易。</span><span class="sxs-lookup"><span data-stu-id="3dddd-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="3dddd-463">`Grade` 屬性為一個 `enum`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="3dddd-464">`Grade` 型別宣告後方的問號表示 `Grade` 屬性[可為 Null](/dotnet/csharp/programming-guide/nullable-types/)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="3dddd-465">為 Null 的成績不同於成績為零&mdash;Null 表示成績未知或尚未指派。</span><span class="sxs-lookup"><span data-stu-id="3dddd-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="3dddd-466">`StudentID` 屬性是外部索引鍵，對應的導覽屬性是 `Student`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="3dddd-467">一個 `Enrollment` 實體與一個 `Student` 實體建立關聯，因此該屬性包含單一 `Student` 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="3dddd-468">`CourseID` 屬性是外部索引鍵，對應的導覽屬性是 `Course`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="3dddd-469">一個 `Enrollment` 實體與一個 `Course` 實體建立關聯。</span><span class="sxs-lookup"><span data-stu-id="3dddd-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="3dddd-470">如果實體名為 `<navigation property name><primary key property name>`，則 EF Core 會將之解釋為外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="3dddd-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="3dddd-471">例如，`StudentID` 是 `Student` 導覽屬性的外部索引鍵，因為 `Student` 實體的主索引鍵是 `ID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="3dddd-472">外部索引鍵屬性也可命名為 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="3dddd-473">例如 `CourseID`，因為 `Course` 實體的主索引鍵是 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="3dddd-474">Course 實體</span><span class="sxs-lookup"><span data-stu-id="3dddd-474">The Course entity</span></span>

![Course 實體圖表](intro/_static/course-entity.png)

<span data-ttu-id="3dddd-476">使用下列程式碼建立 *Models/Course.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="3dddd-477">`Enrollments` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="3dddd-478">`Course` 實體可以與任何數量的 `Enrollment` 實體相關。</span><span class="sxs-lookup"><span data-stu-id="3dddd-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="3dddd-479">`DatabaseGenerated` 屬性可讓應用程式指定主索引鍵，而非讓資料庫產生它。</span><span class="sxs-lookup"><span data-stu-id="3dddd-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="3dddd-480">建置專案以驗證沒有任何編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="3dddd-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="3dddd-481">Scaffold Student 頁面</span><span class="sxs-lookup"><span data-stu-id="3dddd-481">Scaffold Student pages</span></span>

<span data-ttu-id="3dddd-482">在本節中，您會使用 ASP.NET scaffolding 工具來產生：</span><span class="sxs-lookup"><span data-stu-id="3dddd-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="3dddd-483">EF Core「內容」類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-483">An EF Core *context* class.</span></span> <span data-ttu-id="3dddd-484">內容是協調指定資料模型 Entity Framework 功能的主類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="3dddd-485">它衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="3dddd-486">:::no-loc(Razor)::: 處理實體之建立、讀取、更新和刪除 (CRUD) 作業的頁面 `Student` 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-486">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3dddd-488">在 *Pages* 資料夾中建立 *Students* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="3dddd-489">在 [方案總管] 中，以滑鼠右鍵按一下 *Page/Students* 資料夾，然後選取 [新增] **[新增 Scaffold 項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-489">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3dddd-490">在 [ **新增 Scaffold** ] 對話方塊中， **:::no-loc(Razor)::: 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-490">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="3dddd-491">在 [ **:::no-loc(Razor)::: 使用 Entity Framework 加入頁面] (CRUD)** 對話方塊：</span><span class="sxs-lookup"><span data-stu-id="3dddd-491">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="3dddd-492">在 [模型類別] 下拉式清單中，選取 [學生 (ContosoUniversity.Models)]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="3dddd-493">在 [資料內容類別] 資料列中，選取 **+** (加號)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="3dddd-494">將資料內容的名稱從 *ContosoUniversity.Models.ContosoUniversityContext* 變更為 *ContosoUniversity.Data.SchoolContext* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="3dddd-495">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="3dddd-495">Select **Add**.</span></span>

<span data-ttu-id="3dddd-496">會自動安裝下列套件：</span><span class="sxs-lookup"><span data-stu-id="3dddd-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3dddd-498">執行下列 .NET Core CLI 命令來安裝必要的 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="3dddd-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="3dddd-499">Microsoft.VisualStudio.Web.CodeGeneration.Design 套件是進行 scaffolding 時的必要項目。</span><span class="sxs-lookup"><span data-stu-id="3dddd-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="3dddd-500">雖然應用程式不會使用 SQL Server，scaffolding 工具仍需要 SQL Server 套件。</span><span class="sxs-lookup"><span data-stu-id="3dddd-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="3dddd-501">建立 *Pages/Students* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="3dddd-502">執行下列命令來安裝 [aspnet-codegenerator scaffolding 工具](xref:fundamentals/tools/dotnet-aspnet-codegenerator)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="3dddd-503">執行下列命令來 scaffold Student 頁面。</span><span class="sxs-lookup"><span data-stu-id="3dddd-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="3dddd-504">**在 Windows 上**</span><span class="sxs-lookup"><span data-stu-id="3dddd-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="3dddd-505">**在 macOS 或 Linux 上**</span><span class="sxs-lookup"><span data-stu-id="3dddd-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="3dddd-506">若您在上述步驟中遇到問題，請建置專案並重試 scaffold 步驟。</span><span class="sxs-lookup"><span data-stu-id="3dddd-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="3dddd-507">Scaffolding 流程：</span><span class="sxs-lookup"><span data-stu-id="3dddd-507">The scaffolding process:</span></span>

* <span data-ttu-id="3dddd-508">:::no-loc(Razor):::在 [ *頁面/學生* ] 資料夾中建立頁面：</span><span class="sxs-lookup"><span data-stu-id="3dddd-508">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="3dddd-509">*Create.cshtml* 和 *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="3dddd-510">*Delete.cshtml* 和 *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="3dddd-511">*Details.cshtml* 和 *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="3dddd-512">*Edit.cshtml* 和 *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="3dddd-513">*Index.cshtml* 和 *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="3dddd-514">建立 *Data/SchoolContext.cs* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-514">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="3dddd-515">將內容新增到 *Startup.cs* 中的相依性插入。</span><span class="sxs-lookup"><span data-stu-id="3dddd-515">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="3dddd-516">將資料庫連接字串加入至 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-516">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="3dddd-517">資料庫連接字串</span><span class="sxs-lookup"><span data-stu-id="3dddd-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3dddd-519">檔案會 *:::no-loc(appsettings.json):::* 指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)的連接字串。</span><span class="sxs-lookup"><span data-stu-id="3dddd-519">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="3dddd-520">LocalDB 是輕量版的 SQL Server Express Database Engine，旨在用於應用程序開發，而不是生產用途。</span><span class="sxs-lookup"><span data-stu-id="3dddd-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="3dddd-521">根據預設，LocalDB 會在 `C:/Users/<user>` 目錄中建立 *.mdf* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3dddd-523">將連接字串變更為指向名為 *CU.db* 的 SQLite 資料庫檔案：</span><span class="sxs-lookup"><span data-stu-id="3dddd-523">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="3dddd-524">更新資料庫內容類別</span><span class="sxs-lookup"><span data-stu-id="3dddd-524">Update the database context class</span></span>

<span data-ttu-id="3dddd-525">協調指定資料模型 EF Core 功能的主類別是資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="3dddd-526">內容衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3dddd-527">內容會指定哪些實體會包含在資料模型中。</span><span class="sxs-lookup"><span data-stu-id="3dddd-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="3dddd-528">在此專案中，類別命名為 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="3dddd-529">使用下列程式碼來更新 *Data/SchoolContext.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="3dddd-530">反白顯示的程式碼會為每個實體集建立[DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="3dddd-531">在 EF Core 用語中：</span><span class="sxs-lookup"><span data-stu-id="3dddd-531">In EF Core terminology:</span></span>

* <span data-ttu-id="3dddd-532">實體集通常會對應到資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="3dddd-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="3dddd-533">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3dddd-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3dddd-534">因為實體集會包含多個實體，所以 DBSet 屬性應為複數名稱。</span><span class="sxs-lookup"><span data-stu-id="3dddd-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="3dddd-535">因為 scaffolding 工具建立了 `Student` DBSet，所以此步驟會將它變更為複數的 `Students`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="3dddd-536">若要讓 :::no-loc(Razor)::: 頁面程式碼符合新的 DBSet 名稱，請在的整個專案之間進行全域 `_context.Student` 變更 `_context.Students` 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-536">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="3dddd-537">會有 8 次變更。</span><span class="sxs-lookup"><span data-stu-id="3dddd-537">There are 8 occurrences.</span></span>

<span data-ttu-id="3dddd-538">建置專案以確認沒有任何編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="3dddd-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="3dddd-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="3dddd-539">Startup.cs</span></span>

<span data-ttu-id="3dddd-540">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3dddd-541">服務 (例如 EF Core 資料庫內容) 會在應用程式啟動期間對相依性插入進行註冊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3dddd-542">需要這些服務的元件 (例如 :::no-loc(Razor)::: 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="3dddd-542">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3dddd-543">取得資料庫內容執行個體的建構函式程式碼會顯示在本教學課程稍後部分。</span><span class="sxs-lookup"><span data-stu-id="3dddd-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3dddd-544">Scaffolding 工具會自動對相依性插入容器註冊內容類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3dddd-546">在 `ConfigureServices` 中，Scaffolder 會新增下列醒目提示行：</span><span class="sxs-lookup"><span data-stu-id="3dddd-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3dddd-548">在 `ConfigureServices` 中，確認 Scaffolder 新增的程式碼會呼叫 `UseSqlite`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="3dddd-549">連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="3dddd-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3dddd-550">針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="3dddd-551">建立資料庫</span><span class="sxs-lookup"><span data-stu-id="3dddd-551">Create the database</span></span>

<span data-ttu-id="3dddd-552">若未存在資料庫，則請更新 *Program.cs* 來加以建立：</span><span class="sxs-lookup"><span data-stu-id="3dddd-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="3dddd-553">若已存在內容的資料庫，則 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) 方法便不會採取任何動作。</span><span class="sxs-lookup"><span data-stu-id="3dddd-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="3dddd-554">若資料庫不存在，則它會建立資料庫和結構描述。</span><span class="sxs-lookup"><span data-stu-id="3dddd-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="3dddd-555">`EnsureCreated` 會啟用下列工作流程來處理資料模型變更：</span><span class="sxs-lookup"><span data-stu-id="3dddd-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="3dddd-556">刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-556">Delete the database.</span></span> <span data-ttu-id="3dddd-557">任何現有的資料都會遺失。</span><span class="sxs-lookup"><span data-stu-id="3dddd-557">Any existing data is lost.</span></span>
* <span data-ttu-id="3dddd-558">變更資料模型。</span><span class="sxs-lookup"><span data-stu-id="3dddd-558">Change the data model.</span></span> <span data-ttu-id="3dddd-559">例如，新增 `EmailAddress` 欄位。</span><span class="sxs-lookup"><span data-stu-id="3dddd-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="3dddd-560">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-560">Run the app.</span></span>
* <span data-ttu-id="3dddd-561">`EnsureCreated` 會使用新的結構描述來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="3dddd-562">只要您不需要保存資料，此工作流程在開發初期結構描述快速變化時的運作效果便會相當良好。</span><span class="sxs-lookup"><span data-stu-id="3dddd-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="3dddd-563">當資料輸入資料庫且需要進行保存時，狀況則會不同。</span><span class="sxs-lookup"><span data-stu-id="3dddd-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="3dddd-564">在這種情況下，請使用移轉。</span><span class="sxs-lookup"><span data-stu-id="3dddd-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="3dddd-565">在教學課程系列的稍後部分，您會刪除 `EnsureCreated` 建立的資料庫並改為使用移轉。</span><span class="sxs-lookup"><span data-stu-id="3dddd-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="3dddd-566">`EnsureCreated` 建立的資料庫無法使用移轉來更新。</span><span class="sxs-lookup"><span data-stu-id="3dddd-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3dddd-567">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="3dddd-567">Test the app</span></span>

* <span data-ttu-id="3dddd-568">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-568">Run the app.</span></span>
* <span data-ttu-id="3dddd-569">選取 [學生] 連結，然後選取 [新建]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-569">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="3dddd-570">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="3dddd-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="3dddd-571">植入資料庫</span><span class="sxs-lookup"><span data-stu-id="3dddd-571">Seed the database</span></span>

<span data-ttu-id="3dddd-572">`EnsureCreated` 方法會建立空白資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="3dddd-573">本節會新增程式碼以使用測試資料來填入資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="3dddd-574">使用下列程式碼建立 *Data/DbInitializer.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="3dddd-575">程式碼會檢查資料庫中是否有任何學生。</span><span class="sxs-lookup"><span data-stu-id="3dddd-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="3dddd-576">若沒有任何學生，它便會將測試資料新增到資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="3dddd-577">它會以陣列的方式建立測試資料，而非 `List<T>` 集合，來最佳化效能。</span><span class="sxs-lookup"><span data-stu-id="3dddd-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="3dddd-578">在 *Program.cs* 中，將 `EnsureCreated` 呼叫替換成 `DbInitializer.Initialize` 呼叫：</span><span class="sxs-lookup"><span data-stu-id="3dddd-578">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3dddd-580">停止應用程式 (如果它正在執行)，並在 **套件管理員主控台** (PMC) 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3dddd-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3dddd-582">若應用程式正在執行中，請停止它，然後刪除 *CU.db* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="3dddd-583">重新啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-583">Restart the app.</span></span>

* <span data-ttu-id="3dddd-584">選取 Students 頁面來查看植入的資料。</span><span class="sxs-lookup"><span data-stu-id="3dddd-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="3dddd-585">檢視資料庫</span><span class="sxs-lookup"><span data-stu-id="3dddd-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3dddd-587">從 Visual Studio 中的 **View** 功能表開啟 **SQL Server 物件總管** (SSOX)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="3dddd-588">在 SSOX 中，選取 [(localdb)\MSSQLLocalDB] > [資料庫] > [SchoolContext-{GUID}]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="3dddd-589">資料庫名稱是以您稍早所提供的內容名稱加上虛線和 GUID 所產生。</span><span class="sxs-lookup"><span data-stu-id="3dddd-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="3dddd-590">展開 **Tables** 節點。</span><span class="sxs-lookup"><span data-stu-id="3dddd-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="3dddd-591">以滑鼠右鍵按一下 **Students** 資料表，並按一下 [檢視資料] 查看建立的資料行、插入資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3dddd-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="3dddd-592">以滑鼠右鍵按一下 **Student** 資料表然後按一下 [檢視程式碼] 來查看 `Student` 模型對應到 `Student` 資料表結構描述的方式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3dddd-594">使用 SQLite 工具來檢視資料庫結構描述和植入的資料。</span><span class="sxs-lookup"><span data-stu-id="3dddd-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="3dddd-595">資料庫檔案名為 *CU.db* 且位於專案資料夾中。</span><span class="sxs-lookup"><span data-stu-id="3dddd-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="3dddd-596">非同步程式碼</span><span class="sxs-lookup"><span data-stu-id="3dddd-596">Asynchronous code</span></span>

<span data-ttu-id="3dddd-597">非同步程式設計是預設的 ASP.NET Core 和 EF Core 模式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="3dddd-598">網頁伺服器的可用執行緒數量有限，而且在高負載情況下，可能會使用所有可用的執行緒。</span><span class="sxs-lookup"><span data-stu-id="3dddd-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="3dddd-599">發生此情況時，伺服器將無法處理新的要求，直到執行緒空出來。</span><span class="sxs-lookup"><span data-stu-id="3dddd-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="3dddd-600">使用同步程式碼，許多執行緒可能在實際上並未執行任何工作時受到占用，原因是在等候 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="3dddd-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="3dddd-601">使用非同步程式碼，處理程序在等候 I/O 完成時，其執行緒將會空出來以讓伺服器處理其他要求。</span><span class="sxs-lookup"><span data-stu-id="3dddd-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="3dddd-602">因此，非同步程式碼可以更有效率地使用伺服器資源，且伺服器可處理更多流量而不會造成延遲。</span><span class="sxs-lookup"><span data-stu-id="3dddd-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="3dddd-603">非同步程式碼會在執行階段導致少量的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="3dddd-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="3dddd-604">在低流量情況下，對效能的衝擊非常微小；在高流量情況下，潛在的效能改善則相當大。</span><span class="sxs-lookup"><span data-stu-id="3dddd-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="3dddd-605">在下列程式碼中，[async](/dotnet/csharp/language-reference/keywords/async) 關鍵字、`Task<T>` 傳回值、`await` 關鍵字和 `ToListAsync` 方法會使程式碼以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="3dddd-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="3dddd-606">`async` 關鍵字會指示編譯器：</span><span class="sxs-lookup"><span data-stu-id="3dddd-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="3dddd-607">為方法主體的組件產生回呼。</span><span class="sxs-lookup"><span data-stu-id="3dddd-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="3dddd-608">建立傳回的 [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) 物件。</span><span class="sxs-lookup"><span data-stu-id="3dddd-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="3dddd-609">`Task<T>` 傳回型別代表正在進行的工作。</span><span class="sxs-lookup"><span data-stu-id="3dddd-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="3dddd-610">`await` 關鍵字會使編譯器將方法分割為兩部分。</span><span class="sxs-lookup"><span data-stu-id="3dddd-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="3dddd-611">第一個部分會與非同步啟動的作業一同結束。</span><span class="sxs-lookup"><span data-stu-id="3dddd-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="3dddd-612">第二個部分則會放入作業完成時呼叫的回呼方法中。</span><span class="sxs-lookup"><span data-stu-id="3dddd-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="3dddd-613">`ToListAsync` 是 `ToList` 擴充方法的非同步版本。</span><span class="sxs-lookup"><span data-stu-id="3dddd-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="3dddd-614">若要撰寫使用 EF Core 的非同步程式碼，請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="3dddd-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="3dddd-615">只有讓查詢或命令傳送至資料庫的陳述式，會以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="3dddd-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="3dddd-616">其中包含 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="3dddd-617">不會包含只變更 `IQueryable` 的陳述式，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="3dddd-618">EF Core 內容在執行緒中並不安全：不要嘗試執行多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="3dddd-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="3dddd-619">若要利用非同步程式碼所帶來的效能利益，請驗證該程式庫套件 (例如用於分頁) 在呼叫傳送查詢到資料庫的 EF Core 方法時使用 async。</span><span class="sxs-lookup"><span data-stu-id="3dddd-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="3dddd-620">如需非同步方法的詳細資訊，請參閱 [Async 概觀](/dotnet/standard/async)和[使用 Async 和 Await 設計非同步程式](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="3dddd-621">後續步驟</span><span class="sxs-lookup"><span data-stu-id="3dddd-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="3dddd-622">下一個教學課程</span><span class="sxs-lookup"><span data-stu-id="3dddd-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3dddd-623">Contoso 大學範例 web 應用程式示範如何 :::no-loc(Razor)::: 使用 Entity Framework (EF) Core 來建立 ASP.NET Core 頁面應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="3dddd-624">這個範例應用程式是虛構的 Contoso 大學網站。</span><span class="sxs-lookup"><span data-stu-id="3dddd-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="3dddd-625">其中包括的功能有學生入學許可、課程建立、教師指派。</span><span class="sxs-lookup"><span data-stu-id="3dddd-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="3dddd-626">此頁面是說明如何建立 Contoso 大學範例應用程式教學課程系列中的第一頁。</span><span class="sxs-lookup"><span data-stu-id="3dddd-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="3dddd-627">下載或檢視已完成的應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="3dddd-628">[下載指示](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3dddd-629">必要條件</span><span class="sxs-lookup"><span data-stu-id="3dddd-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="3dddd-632">熟悉[ :::no-loc(Razor)::: 頁面](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-632">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="3dddd-633">新的程式設計人員應該在開始本系列之前，先完成 [ :::no-loc(Razor)::: 頁面的入門](xref:tutorials/razor-pages/razor-pages-start) 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-633">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="3dddd-634">疑難排解</span><span class="sxs-lookup"><span data-stu-id="3dddd-634">Troubleshooting</span></span>

<span data-ttu-id="3dddd-635">如果您執行您不能解決問題，您可以藉由比較您的程式碼通常找到方案[已完成的專案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="3dddd-636">取得協助的好方法是將問題公佈到 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) 以詢問 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="3dddd-637">Contoso 大學 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="3dddd-637">The Contoso University web app</span></span>

<span data-ttu-id="3dddd-638">在教學課程中建立的應用程式，是一個基本的大學網站。</span><span class="sxs-lookup"><span data-stu-id="3dddd-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="3dddd-639">使用者可以檢視和更新學生、課程和教師資訊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="3dddd-640">以下是幾個在教學課程中建立的畫面。</span><span class="sxs-lookup"><span data-stu-id="3dddd-640">Here are a few of the screens created in the tutorial.</span></span>

![Students [索引] 頁面](intro/_static/students-index.png)

![Students [編輯] 頁面](intro/_static/student-edit.png)

<span data-ttu-id="3dddd-643">此網站的 UI 樣式接近內建範本所產生的內容。</span><span class="sxs-lookup"><span data-stu-id="3dddd-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="3dddd-644">本教學課程著重于 EF Core :::no-loc(Razor)::: 頁面，而不是 UI。</span><span class="sxs-lookup"><span data-stu-id="3dddd-644">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="3dddd-645">建立 ContosoUniversity :::no-loc(Razor)::: 頁面 web 應用程式</span><span class="sxs-lookup"><span data-stu-id="3dddd-645">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3dddd-647">從 Visual Studio 的 [檔案] 功能表中，選取 [新增] **[專案]** >  。</span><span class="sxs-lookup"><span data-stu-id="3dddd-647">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3dddd-648">建立新的 ASP.NET Core Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="3dddd-649">將專案命名為 **ContosoUniversity** 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-649">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="3dddd-650">請務必將專案命名為 *ContosoUniversity* ，當您複製/貼上程式碼時，命名空間才會相符。</span><span class="sxs-lookup"><span data-stu-id="3dddd-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="3dddd-651">在下拉式清單中選取 [ASP.NET Core 2.1]，然後選取 [Web 應用程式]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="3dddd-652">如需上述步驟的影像，請參閱 [建立 :::no-loc(Razor)::: web 應用程式](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-652">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="3dddd-653">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="3dddd-655">設定網站樣式</span><span class="sxs-lookup"><span data-stu-id="3dddd-655">Set up the site style</span></span>

<span data-ttu-id="3dddd-656">一些變更設定了網站的功能表、配置和首頁。</span><span class="sxs-lookup"><span data-stu-id="3dddd-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="3dddd-657">使用下列變更更新 *Pages/Shared/_Layout.cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="3dddd-658">將每個出現的 "ContosoUniversity" 都變更為 "Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="3dddd-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="3dddd-659">共有三個發生次數。</span><span class="sxs-lookup"><span data-stu-id="3dddd-659">There are three occurrences.</span></span>

* <span data-ttu-id="3dddd-660">為 **Students** 、 **Courses** 、 **Instructors** 、 **Departments** 新增功能表項目，並刪除 **Contact** 功能表項目。</span><span class="sxs-lookup"><span data-stu-id="3dddd-660">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="3dddd-661">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="3dddd-661">The changes are highlighted.</span></span> <span data-ttu-id="3dddd-662">(所有標記都「不會」顯示。)</span><span class="sxs-lookup"><span data-stu-id="3dddd-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="3dddd-663">在 *Pages/Index.cshtml* 中用下列程式碼取代檔案內容，以使用關於此應用程式的文字來取代關於 ASP.NET 和 MVC 的文字：</span><span class="sxs-lookup"><span data-stu-id="3dddd-663">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="3dddd-664">建立資料模型</span><span class="sxs-lookup"><span data-stu-id="3dddd-664">Create the data model</span></span>

<span data-ttu-id="3dddd-665">為 Contoso 大學應用程式建立實體類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="3dddd-666">從下列三個實體開始：</span><span class="sxs-lookup"><span data-stu-id="3dddd-666">Start with the following three entities:</span></span>

![Course-Enrollment-Student 資料模型圖表](intro/_static/data-model-diagram.png)

<span data-ttu-id="3dddd-668">在 `Student` 和 `Enrollment` 實體之間會有一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="3dddd-669">在 `Course` 和 `Enrollment` 實體之間會有一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="3dddd-670">一位學生可註冊任何數目的課程。</span><span class="sxs-lookup"><span data-stu-id="3dddd-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="3dddd-671">一堂課程可以讓任意數目的學生註冊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="3dddd-672">在下列一節中，將為這些實體建立各自的類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="3dddd-673">Student 實體</span><span class="sxs-lookup"><span data-stu-id="3dddd-673">The Student entity</span></span>

![Student 實體圖表](intro/_static/student-entity.png)

<span data-ttu-id="3dddd-675">建立 *Models* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-675">Create a *Models* folder.</span></span> <span data-ttu-id="3dddd-676">在 *Models* 資料夾中，用下列程式碼建立一個名為 *Student.cs* 的類別檔案：</span><span class="sxs-lookup"><span data-stu-id="3dddd-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="3dddd-677">`ID` 屬性會成為資料庫 (DB) 資料表中的主索引鍵資料行，並對應至這個類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="3dddd-678">EF Core 預設會解譯名為 `ID` 或 `classnameID` 作為主索引鍵的屬性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="3dddd-679">在 `classnameID` 中，`classname` 是類別的名稱。</span><span class="sxs-lookup"><span data-stu-id="3dddd-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="3dddd-680">另一個自動識別的主索引鍵是上述範例中的 `StudentID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="3dddd-681">`Enrollments` 屬性為[導覽屬性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="3dddd-682">導覽屬性會連結至與此實體相關的其他實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="3dddd-683">在這個案例中，`Student entity` 中的 `Enrollments` 屬性會保有與 `Student` 相關的所有 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="3dddd-684">例如，如果資料庫中 Student 資料列有兩個相關的 Enrollment 資料列，則 `Enrollments` 導覽屬性會包含這兩個 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="3dddd-685">相關的 `Enrollment` 資料列，包含該學生在 `StudentID` 資料行中的主索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="3dddd-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="3dddd-686">例如，假設 student with ID=1 在 `Enrollment` 資料表中有兩個資料列。</span><span class="sxs-lookup"><span data-stu-id="3dddd-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="3dddd-687">`Enrollment` 資料表有兩個包含 `StudentID`=1 的資料列。</span><span class="sxs-lookup"><span data-stu-id="3dddd-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="3dddd-688">`StudentID` 為 `Enrollment` 資料表中的外部索引鍵，會指定在 `Student` 資料表中的學生。</span><span class="sxs-lookup"><span data-stu-id="3dddd-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="3dddd-689">如果導覽屬性可以保存多個實體，該導覽屬性必然是清單類型，例如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="3dddd-690">您可以指定 `ICollection<T>`，或是如 `List<T>`、`HashSet<T>` 的類型。</span><span class="sxs-lookup"><span data-stu-id="3dddd-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="3dddd-691">如使用 `ICollection<T>`，EF Core 預設將建立 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="3dddd-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="3dddd-692">保存多個實體的導覽屬性，來自多對多和一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="3dddd-693">Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="3dddd-693">The Enrollment entity</span></span>

![Enrollment 實體圖表](intro/_static/enrollment-entity.png)

<span data-ttu-id="3dddd-695">在 *Models* 資料夾中，用下列程式碼建立 *Enrollment.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="3dddd-696">`EnrollmentID` 屬性是主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="3dddd-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="3dddd-697">這個實體會使用 `classnameID` 模式，而不是像 `Student` 實體一樣的 `ID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="3dddd-698">開發人員通常會選擇其中一個模式，並使用於整個資料模型。</span><span class="sxs-lookup"><span data-stu-id="3dddd-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="3dddd-699">在稍後的教學課程中，將示範使用不具類別名稱的 ID，使得在數據模型中實作繼承更加簡單。</span><span class="sxs-lookup"><span data-stu-id="3dddd-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="3dddd-700">`Grade` 屬性為一個 `enum`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="3dddd-701">問號之後的 `Grade` 類型宣告表示 `Grade` 屬性可為 Null。</span><span class="sxs-lookup"><span data-stu-id="3dddd-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="3dddd-702">為 Null 的成績不同於成績為零：Null 表示成績未知或尚未指派。</span><span class="sxs-lookup"><span data-stu-id="3dddd-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="3dddd-703">`StudentID` 屬性是外部索引鍵，對應的導覽屬性是 `Student`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="3dddd-704">一個 `Enrollment` 實體與一個 `Student` 實體建立關聯，因此該屬性包含單一 `Student` 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="3dddd-705">`Student` 實體與 `Student.Enrollments` 導覽屬性不同，導覽屬性包含多個 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="3dddd-706">`CourseID` 屬性是外部索引鍵，對應的導覽屬性是 `Course`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="3dddd-707">一個 `Enrollment` 實體與一個 `Course` 實體建立關聯。</span><span class="sxs-lookup"><span data-stu-id="3dddd-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="3dddd-708">如果實體名為 `<navigation property name><primary key property name>`，則 EF Core 會將之解釋為外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="3dddd-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="3dddd-709">例如，`StudentID` 為 `Student` 導覽屬性，因為 `Student` 實體的主索引鍵是 `ID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="3dddd-710">外部索引鍵屬性也可命名為 `<primary key property name>`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="3dddd-711">例如 `CourseID`，因為 `Course` 實體的主索引鍵是 `CourseID`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="3dddd-712">Course 實體</span><span class="sxs-lookup"><span data-stu-id="3dddd-712">The Course entity</span></span>

![Course 實體圖表](intro/_static/course-entity.png)

<span data-ttu-id="3dddd-714">在 *Models* 資料夾中，用下列程式碼建立 *Course.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="3dddd-715">`Enrollments` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="3dddd-716">`Course` 實體可以與任何數量的 `Enrollment` 實體相關。</span><span class="sxs-lookup"><span data-stu-id="3dddd-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="3dddd-717">`DatabaseGenerated` 屬性 (attribute) 可讓應用程式指定主索引鍵，而不是讓 DB 來產生。</span><span class="sxs-lookup"><span data-stu-id="3dddd-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="3dddd-718">Scaffold 學生模型</span><span class="sxs-lookup"><span data-stu-id="3dddd-718">Scaffold the student model</span></span>

<span data-ttu-id="3dddd-719">在本節中會 scaffold 學生模型。</span><span class="sxs-lookup"><span data-stu-id="3dddd-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="3dddd-720">亦即 Scaffolding 工具會產生學生模型的建立、讀取、更新和刪除 (CRUD) 作業頁面。</span><span class="sxs-lookup"><span data-stu-id="3dddd-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="3dddd-721">建置專案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-721">Build the project.</span></span>
* <span data-ttu-id="3dddd-722">建立 *Pages/Students* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="3dddd-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3dddd-724">在 [方案總管] 中，以滑鼠右鍵按一下 *Pages/Students* 資料夾 > [新增] **[新增 Scaffold 項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-724">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3dddd-725">在 [ **新增 Scaffold** ] 對話方塊中， **:::no-loc(Razor)::: 使用 Entity Framework (CRUD)** 新增] 選取 [頁面] > \*\*\*\* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-725">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="3dddd-726">**:::no-loc(Razor)::: 使用 ENTITY FRAMEWORK (CRUD) 對話方塊來完成 [新增頁面** ]：</span><span class="sxs-lookup"><span data-stu-id="3dddd-726">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="3dddd-727">在 [模型類別] 下拉式清單中，選取 [學生 (ContosoUniversity.Models)]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="3dddd-728">在 [資料內容類別] 列中，選取 **+** (加號) 符號，並將產生的名稱變更為 **ContosoUniversity.Models.SchoolContext** 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="3dddd-729">在 [資料內容類別] 下拉式清單中，選取 **ContosoUniversity.Models.SchoolContext**</span><span class="sxs-lookup"><span data-stu-id="3dddd-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="3dddd-730">選取 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="3dddd-730">Select **Add**.</span></span>

![CRUD 對話方塊](intro/_static/s1.png)

<span data-ttu-id="3dddd-732">如果您有上一個步驟的問題，請參閱 [Scaffold 影片模型](xref:tutorials/razor-pages/model#scaffold-the-movie-model)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3dddd-734">執行下列命令來 Scaffold 學生模型。</span><span class="sxs-lookup"><span data-stu-id="3dddd-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="3dddd-735">隨即建立 Scaffold 處理序並變更下列檔案：</span><span class="sxs-lookup"><span data-stu-id="3dddd-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="3dddd-736">建立的檔案</span><span class="sxs-lookup"><span data-stu-id="3dddd-736">Files created</span></span>

* <span data-ttu-id="3dddd-737">*Pages/Students* 建立、刪除、詳細資料、編輯、索引。</span><span class="sxs-lookup"><span data-stu-id="3dddd-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="3dddd-738">*Data/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="3dddd-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="3dddd-739">檔案更新</span><span class="sxs-lookup"><span data-stu-id="3dddd-739">File updates</span></span>

* <span data-ttu-id="3dddd-740">*Startup.cs* ：這個檔案的變更會於下一節詳述。</span><span class="sxs-lookup"><span data-stu-id="3dddd-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="3dddd-741">*:::no-loc(appsettings.json):::* ：已加入用來連接到本機資料庫的連接字串。</span><span class="sxs-lookup"><span data-stu-id="3dddd-741">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="3dddd-742">檢查使用相依性插入所註冊的內容</span><span class="sxs-lookup"><span data-stu-id="3dddd-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="3dddd-743">ASP.NET Core 內建[相依性插入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3dddd-744">服務 (例如 EF Core DB 內容) 是在應用程式啟動期間使用相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="3dddd-745">需要這些服務的元件 (例如 :::no-loc(Razor)::: 頁面) 是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="3dddd-745">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="3dddd-746">取得資料庫內容執行個體的建構函式程式碼，在本教學課程稍後會示範。</span><span class="sxs-lookup"><span data-stu-id="3dddd-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="3dddd-747">Scaffolding 工具會自動建立資料庫內容，並向相依性插入容器註冊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="3dddd-748">檢查 *Startup.cs* 中的 `ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="3dddd-748">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="3dddd-749">強調顯示的行由 Scaffolder 新增：</span><span class="sxs-lookup"><span data-stu-id="3dddd-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="3dddd-750">連接字串的名稱，會透過對 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 物件呼叫方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="3dddd-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="3dddd-751">針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *:::no-loc(appsettings.json):::* 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="3dddd-752">更新 main</span><span class="sxs-lookup"><span data-stu-id="3dddd-752">Update main</span></span>

<span data-ttu-id="3dddd-753">在 *Program.cs* 中，修改 `Main` 方法來執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="3dddd-753">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="3dddd-754">從相依性插入容器中取得資料庫內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="3dddd-755">呼叫 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="3dddd-756">`EnsureCreated` 方法完成時處理內容。</span><span class="sxs-lookup"><span data-stu-id="3dddd-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="3dddd-757">下列程式碼顯示已更新的 *Program.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="3dddd-758">`EnsureCreated` 可確保內容的資料庫存在。</span><span class="sxs-lookup"><span data-stu-id="3dddd-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="3dddd-759">如果存在，不會採取任何動作。</span><span class="sxs-lookup"><span data-stu-id="3dddd-759">If it exists, no action is taken.</span></span> <span data-ttu-id="3dddd-760">如果不存在，則會建立資料庫及其所有結構描述。</span><span class="sxs-lookup"><span data-stu-id="3dddd-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="3dddd-761">`EnsureCreated` 不會使用移轉來建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="3dddd-762">使用 `EnsureCreated` 建立的資料庫稍後無法使用移轉來加以更新。</span><span class="sxs-lookup"><span data-stu-id="3dddd-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="3dddd-763">應用程式啟動時會呼叫 `EnsureCreated` 以允許下列工作流程：</span><span class="sxs-lookup"><span data-stu-id="3dddd-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="3dddd-764">刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-764">Delete the DB.</span></span>
* <span data-ttu-id="3dddd-765">變更資料庫結構描述 (例如新增 `EmailAddress` 欄位)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="3dddd-766">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-766">Run the app.</span></span>
* <span data-ttu-id="3dddd-767">`EnsureCreated` 會建立包含 `EmailAddress` 資料行的資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="3dddd-768">在開發初期，結構描述快速發展之時，`EnsureCreated` 很方便。</span><span class="sxs-lookup"><span data-stu-id="3dddd-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="3dddd-769">稍後在本教學課程中，會刪除資料庫並使用移轉。</span><span class="sxs-lookup"><span data-stu-id="3dddd-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="3dddd-770">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="3dddd-770">Test the app</span></span>

<span data-ttu-id="3dddd-771">執行應用程式並接受 :::no-loc(cookie)::: 原則。</span><span class="sxs-lookup"><span data-stu-id="3dddd-771">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="3dddd-772">此應用程式不會保留個人資訊。</span><span class="sxs-lookup"><span data-stu-id="3dddd-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="3dddd-773">您可以參閱 EU 的 :::no-loc(cookie)::: 原則 [一般資料保護規定 (GDPR) 支援人員](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-773">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="3dddd-774">選取 [學生] 連結，然後選取 [新建]。</span><span class="sxs-lookup"><span data-stu-id="3dddd-774">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="3dddd-775">測試 [編輯]、[詳細資料] 和 [刪除] 連結。</span><span class="sxs-lookup"><span data-stu-id="3dddd-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="3dddd-776">檢查 SchoolContext 資料庫內容</span><span class="sxs-lookup"><span data-stu-id="3dddd-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="3dddd-777">為給定的資料模型協調 EF Core 功能的主要類別是資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="3dddd-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="3dddd-778">資料內容衍生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="3dddd-779">資料內容會指定資料模型包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="3dddd-780">在此專案中，類別命名為 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="3dddd-781">使用下列程式碼來更新 *SchoolContext.cs* ：</span><span class="sxs-lookup"><span data-stu-id="3dddd-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="3dddd-782">反白顯示的程式碼會為每個實體集建立[DbSet \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1)屬性。</span><span class="sxs-lookup"><span data-stu-id="3dddd-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="3dddd-783">在 EF Core 用語中：</span><span class="sxs-lookup"><span data-stu-id="3dddd-783">In EF Core terminology:</span></span>

* <span data-ttu-id="3dddd-784">實體集通常會對應至資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="3dddd-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="3dddd-785">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3dddd-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="3dddd-786">`DbSet<Enrollment>` 和 `DbSet<Course>` 可加以忽略。</span><span class="sxs-lookup"><span data-stu-id="3dddd-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="3dddd-787">EF Core 會隱含它們，因為 `Student` 實體引用 `Enrollment` 實體，而 `Enrollment` 實體引用 `Course` 實體。</span><span class="sxs-lookup"><span data-stu-id="3dddd-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="3dddd-788">本教學課程中，請在 `SchoolContext` 保留 `DbSet<Enrollment>` 和 `DbSet<Course>`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="3dddd-789">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="3dddd-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="3dddd-790">連接字串會指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="3dddd-791">LocalDB 是輕量版的 SQL Server Express Database Engine，旨在用於應用程序開發，而不是生產用途。</span><span class="sxs-lookup"><span data-stu-id="3dddd-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="3dddd-792">LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。</span><span class="sxs-lookup"><span data-stu-id="3dddd-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="3dddd-793">LocalDB 預設會在 `C:/Users/<user>` 目錄中建立 *.mdf* 資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="3dddd-794">新增程式碼來初始化含有測試資料的資料庫</span><span class="sxs-lookup"><span data-stu-id="3dddd-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="3dddd-795">EF Core 會建立空白資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="3dddd-796">在本節中，會寫入 `Initialize` 方法並以測試資料來填入該資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="3dddd-797">在 *Data* 資料夾中，建立名為 *DbInitializer.cs* 的新類別檔案並新增下列程式碼：</span><span class="sxs-lookup"><span data-stu-id="3dddd-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="3dddd-798">注意：上述程式碼會使用做 `Models` 為命名空間 (`namespace ContosoUniversity.Models`) 而不是 `Data` 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="3dddd-799">`Models` 與產生框架的程式碼一致。</span><span class="sxs-lookup"><span data-stu-id="3dddd-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="3dddd-800">如需詳細資訊，請參閱[此 GitHub Scaffolding 問題](https://github.com/aspnet/Scaffolding/issues/822)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="3dddd-801">程式碼會檢查資料庫中是否有任何學生。</span><span class="sxs-lookup"><span data-stu-id="3dddd-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="3dddd-802">如果資料庫中沒有學生，則會使用測試資料初始化資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="3dddd-803">會將測試資料載入至陣列而非 `List<T>` 集合，以最佳化效能。</span><span class="sxs-lookup"><span data-stu-id="3dddd-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="3dddd-804">`EnsureCreated` 方法會自動為資料庫內容建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="3dddd-805">如果資料庫已存在，則 `EnsureCreated` 將會傳回而不修改該資料庫。</span><span class="sxs-lookup"><span data-stu-id="3dddd-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="3dddd-806">在 *Program.cs* 中，修改 `Main` 方法來呼叫 `Initialize`：</span><span class="sxs-lookup"><span data-stu-id="3dddd-806">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="3dddd-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3dddd-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3dddd-808">停止應用程式 (如果它正在執行)，並在 **套件管理員主控台** (PMC) 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="3dddd-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="3dddd-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3dddd-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3dddd-810">若應用程式正在執行中，請停止它，然後刪除 *CU.db* 檔案。</span><span class="sxs-lookup"><span data-stu-id="3dddd-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="3dddd-811">檢視資料庫</span><span class="sxs-lookup"><span data-stu-id="3dddd-811">View the DB</span></span>

<span data-ttu-id="3dddd-812">資料庫名稱是以您稍早所提供的內容名稱加上虛線和 GUID 所產生。</span><span class="sxs-lookup"><span data-stu-id="3dddd-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="3dddd-813">因此，資料庫名稱將會是 "SchoolContext-{GUID}"。</span><span class="sxs-lookup"><span data-stu-id="3dddd-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="3dddd-814">GUID 針對每個使用者都將不同。</span><span class="sxs-lookup"><span data-stu-id="3dddd-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="3dddd-815">從 Visual Studio 中的 **View** 功能表開啟 **SQL Server 物件總管** (SSOX)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="3dddd-816">在 SSOX 中，按一下 **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** 。</span><span class="sxs-lookup"><span data-stu-id="3dddd-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="3dddd-817">展開 **Tables** 節點。</span><span class="sxs-lookup"><span data-stu-id="3dddd-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="3dddd-818">以滑鼠右鍵按一下 **Students** 資料表，並按一下 [檢視資料] 查看建立的資料行、插入資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="3dddd-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="3dddd-819">非同步程式碼</span><span class="sxs-lookup"><span data-stu-id="3dddd-819">Asynchronous code</span></span>

<span data-ttu-id="3dddd-820">非同步程式設計是預設的 ASP.NET Core 和 EF Core 模式。</span><span class="sxs-lookup"><span data-stu-id="3dddd-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="3dddd-821">網頁伺服器的可用執行緒數量有限，而且在高負載情況下，可能會使用所有可用的執行緒。</span><span class="sxs-lookup"><span data-stu-id="3dddd-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="3dddd-822">發生此情況時，伺服器將無法處理新的要求，直到執行緒空出來。</span><span class="sxs-lookup"><span data-stu-id="3dddd-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="3dddd-823">使用同步程式碼，許多執行緒可能在實際上並未執行任何工作時受到占用，原因是在等候 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="3dddd-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="3dddd-824">使用非同步程式碼，處理程序在等候 I/O 完成時，其執行緒將會空出來以讓伺服器處理其他要求。</span><span class="sxs-lookup"><span data-stu-id="3dddd-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="3dddd-825">因此，非同步程式碼可讓伺服器資源更有效率地使用，而且伺服器可處理更多流量而不會造成延遲。</span><span class="sxs-lookup"><span data-stu-id="3dddd-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="3dddd-826">非同步程式碼會在執行階段導致少量的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="3dddd-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="3dddd-827">在低流量情況下，對效能的衝擊非常微小；在高流量情況下，潛在的效能改善則相當大。</span><span class="sxs-lookup"><span data-stu-id="3dddd-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="3dddd-828">在下列程式碼中，[async](/dotnet/csharp/language-reference/keywords/async) 關鍵字、`Task<T>` 傳回值、`await` 關鍵字和 `ToListAsync` 方法會使程式碼以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="3dddd-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="3dddd-829">`async` 關鍵字會指示編譯器：</span><span class="sxs-lookup"><span data-stu-id="3dddd-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="3dddd-830">為方法主體的組件產生回呼。</span><span class="sxs-lookup"><span data-stu-id="3dddd-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="3dddd-831">自動建立傳回的 [Task](/dotnet/api/system.threading.tasks.task) 物件。</span><span class="sxs-lookup"><span data-stu-id="3dddd-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="3dddd-832">如需詳細資訊，請參閱 [Task 傳回型別](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="3dddd-833">隱含傳回型別 `Task` 代表進行中的工作。</span><span class="sxs-lookup"><span data-stu-id="3dddd-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="3dddd-834">`await` 關鍵字會使編譯器將方法分割為兩部分。</span><span class="sxs-lookup"><span data-stu-id="3dddd-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="3dddd-835">第一個部分會與非同步啟動的作業一同結束。</span><span class="sxs-lookup"><span data-stu-id="3dddd-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="3dddd-836">第二個部分則會放入作業完成時呼叫的回呼方法中。</span><span class="sxs-lookup"><span data-stu-id="3dddd-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="3dddd-837">`ToListAsync` 是 `ToList` 擴充方法的非同步版本。</span><span class="sxs-lookup"><span data-stu-id="3dddd-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="3dddd-838">若要撰寫使用 EF Core 的非同步程式碼，請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="3dddd-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="3dddd-839">只有讓查詢或命令傳送至資料庫的陳述式，會以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="3dddd-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="3dddd-840">包含 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="3dddd-841">不會包含只變更 `IQueryable` 的陳述式，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="3dddd-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="3dddd-842">EF Core 內容在執行緒中並不安全：不要嘗試執行多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="3dddd-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="3dddd-843">若要利用非同步程式碼的效能優勢，請確認程式庫套件 (例如分頁) 在呼叫將查詢傳送至資料庫的 EF Core 方法時是使用非同步。</span><span class="sxs-lookup"><span data-stu-id="3dddd-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="3dddd-844">如需非同步方法的詳細資訊，請參閱 [Async 概觀](/dotnet/standard/async)和[使用 Async 和 Await 設計非同步程式](/dotnet/csharp/programming-guide/concepts/async/)。</span><span class="sxs-lookup"><span data-stu-id="3dddd-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="3dddd-845">在下一個教學課程中，將會檢視基本的 CRUD (建立、讀取、更新、刪除) 作業。</span><span class="sxs-lookup"><span data-stu-id="3dddd-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="3dddd-846">其他資源</span><span class="sxs-lookup"><span data-stu-id="3dddd-846">Additional resources</span></span>

* [<span data-ttu-id="3dddd-847">本教學課程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="3dddd-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="3dddd-848">下一步</span><span class="sxs-lookup"><span data-stu-id="3dddd-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
