---
title: 教學課程：開始使用 ASP.NET MVC web 應用程式中的 EF Core
description: 此頁面是一系列教學課程中的第一個教學課程，說明如何建立 Contoso 大學範例 EF/MVC 應用程式」
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: data/ef-mvc/intro
ms.openlocfilehash: ef1d94ce7a0aa853336260b8d73b9d4036c907ac
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340006"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a><span data-ttu-id="6ad07-103">教學課程：開始使用 ASP.NET MVC web 應用程式中的 EF Core</span><span class="sxs-lookup"><span data-stu-id="6ad07-103">Tutorial: Get started with EF Core in an ASP.NET MVC web app</span></span>

<span data-ttu-id="6ad07-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6ad07-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="6ad07-105">Contoso 大學範例 web ap 示範如何使用 Entity Framework (EF) Core 和 Visual Studio 建立 ASP.NET Core MVC web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-105">The Contoso University sample web ap demonstrates how to create an ASP.NET Core MVC web app using Entity Framework (EF) Core and Visual Studio.</span></span>

<span data-ttu-id="6ad07-106">這個範例應用程式是虛構的 Contoso 大學網站。</span><span class="sxs-lookup"><span data-stu-id="6ad07-106">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="6ad07-107">其中包括的功能有學生入學許可、課程建立、教師指派。</span><span class="sxs-lookup"><span data-stu-id="6ad07-107">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="6ad07-108">這是一系列教學課程中的第一篇，說明如何建立 Contoso 大學範例應用程式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-108">This is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ad07-109">必要條件</span><span class="sxs-lookup"><span data-stu-id="6ad07-109">Prerequisites</span></span>

* <span data-ttu-id="6ad07-110">如果您是 ASP.NET Core MVC 的新手，請先完成 [ASP.NET CORE mvc](xref:tutorials/first-mvc-app/start-mvc) 教學課程系列的「開始使用」，再開始這一系列。</span><span class="sxs-lookup"><span data-stu-id="6ad07-110">If you're new to ASP.NET Core MVC, go through the [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) tutorial series before starting this one.</span></span>

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a><span data-ttu-id="6ad07-111">資料庫引擎</span><span class="sxs-lookup"><span data-stu-id="6ad07-111">Database engines</span></span>

<span data-ttu-id="6ad07-112">Visual Studio 說明會使用 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)，它是一種只在 Windows 上執行的 SQL Server Express 版本。</span><span class="sxs-lookup"><span data-stu-id="6ad07-112">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a><span data-ttu-id="6ad07-113">解決問題和疑難排解</span><span class="sxs-lookup"><span data-stu-id="6ad07-113">Solve problems and troubleshoot</span></span>

<span data-ttu-id="6ad07-114">如果您執行您不能解決問題，您可以藉由比較您的程式碼通常找到方案[已完成的專案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-114">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="6ad07-115">如需常見的錯誤以及如何解決這些問題的清單，請參閱[ 數列中的最後一個教學課程疑難排解 > 一節](advanced.md#common-errors)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-115">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="6ad07-116">如果您找不到您需要那里，您可以張貼問題的 StackOverflow.com [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-116">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="6ad07-117">這是 10 個教學的系列課程，當中的每一個課程都是建置於先前教學課程的成果上。</span><span class="sxs-lookup"><span data-stu-id="6ad07-117">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="6ad07-118">成功完成每一個教學課程後，請儲存專案的複本。</span><span class="sxs-lookup"><span data-stu-id="6ad07-118">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="6ad07-119">如果您遇到問題，您可以從上一個教學課程來重新開始，而不需從系列的一開始從頭來過。</span><span class="sxs-lookup"><span data-stu-id="6ad07-119">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="6ad07-120">Contoso 大學 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="6ad07-120">Contoso University web app</span></span>

<span data-ttu-id="6ad07-121">在教學課程中建立的應用程式，是一個基本的大學網站。</span><span class="sxs-lookup"><span data-stu-id="6ad07-121">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="6ad07-122">使用者可以檢視和更新學生、課程和教師資訊。</span><span class="sxs-lookup"><span data-stu-id="6ad07-122">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="6ad07-123">以下是應用程式中的幾個畫面：</span><span class="sxs-lookup"><span data-stu-id="6ad07-123">Here are a few of the screens in the app:</span></span>

![Students [索引] 頁面](intro/_static/students-index.png)

![Students [編輯] 頁面](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="6ad07-126">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="6ad07-126">Create web app</span></span>

1. <span data-ttu-id="6ad07-127">啟動 Visual Studio，然後選取 [建立新專案]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-127">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="6ad07-128">在 [ **建立新專案** ] 對話方塊中，選取 [ **ASP.NET Core Web 應用程式** > **]** 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-128">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="6ad07-129">在 [ **設定您的新專案** ] 對話方塊中，輸入 [ `ContosoUniversity` **專案名稱** ]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-129">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="6ad07-130">請務必使用此完整名稱（包括大小寫），以便 `namespace` 在複製程式碼時使用每個相符專案。</span><span class="sxs-lookup"><span data-stu-id="6ad07-130">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="6ad07-131">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="6ad07-131">Select **Create**.</span></span>
1. <span data-ttu-id="6ad07-132">在 [ **建立新的 ASP.NET Core web 應用程式** ] 對話方塊中，選取：</span><span class="sxs-lookup"><span data-stu-id="6ad07-132">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="6ad07-133">下拉式清單中的 **.Net Core** 和 **ASP.NET Core 5.0** 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-133">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="6ad07-134">**ASP.NET Core Web 應用程式 (模型-視圖控制器)** 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-134">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="6ad07-135">**Create** 
       建立 ![新增 ASP.NET Core 專案對話方塊](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="6ad07-135">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>

## <a name="set-up-the-site-style"></a><span data-ttu-id="6ad07-136">設定網站樣式</span><span class="sxs-lookup"><span data-stu-id="6ad07-136">Set up the site style</span></span>

<span data-ttu-id="6ad07-137">有幾個基本變更會設定網站功能表、版面配置和首頁。</span><span class="sxs-lookup"><span data-stu-id="6ad07-137">A few basic changes set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="6ad07-138">開啟 *Views/Shared/_Layout.cshtml* 並進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="6ad07-138">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="6ad07-139">將每個出現的變更 `ContosoUniversity` 為 `Contoso University` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-139">Change each occurrence of `ContosoUniversity` to `Contoso University`.</span></span> <span data-ttu-id="6ad07-140">共有三個發生次數。</span><span class="sxs-lookup"><span data-stu-id="6ad07-140">There are three occurrences.</span></span>
* <span data-ttu-id="6ad07-141">為 **About** 、 **Students** 、 **Courses** 、 **Instructors** 及 **Departments** 新增功能表項目，並刪除 **Privacy** 功能表項目。</span><span class="sxs-lookup"><span data-stu-id="6ad07-141">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="6ad07-142">上述變更會在下列程式碼中反白顯示：</span><span class="sxs-lookup"><span data-stu-id="6ad07-142">The preceding changes are highlighted in the following code:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

<span data-ttu-id="6ad07-143">在 *Views/Home/Index. cshtml* 中，以下列標記取代檔案的內容：</span><span class="sxs-lookup"><span data-stu-id="6ad07-143">In *Views/Home/Index.cshtml* , replace the contents of the file with the following markup:</span></span>

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

<span data-ttu-id="6ad07-144">按 CTRL+F5 來執行專案，或從功能表選擇 [偵錯] > [啟動但不偵錯]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-144">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="6ad07-145">首頁會顯示在本教學課程中建立之頁面的索引標籤。</span><span class="sxs-lookup"><span data-stu-id="6ad07-145">The home page is displayed with tabs for the pages created in this tutorial.</span></span>

![Contoso 大學首頁](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a><span data-ttu-id="6ad07-147">EF Core NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="6ad07-147">EF Core NuGet packages</span></span>

<span data-ttu-id="6ad07-148">本教學課程使用 SQL Server，其提供者套件為 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-148">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span>

<span data-ttu-id="6ad07-149">EF SQL Server 套件及其相依性， `Microsoft.EntityFrameworkCore` 以及 `Microsoft.EntityFrameworkCore.Relational` 提供 ef 的執行時間支援。</span><span class="sxs-lookup"><span data-stu-id="6ad07-149">The EF SQL Server package and its dependencies, `Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`, provide runtime support for EF.</span></span>

<span data-ttu-id="6ad07-150">新增 [AspNetCore Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) nuget 套件和 [AspNetCore. microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) nuget 套件中的程式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-150">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package and the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span> <span data-ttu-id="6ad07-151">在 [Program Manager 主控台] (PMC) 中，輸入下列命令以新增 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="6ad07-151">In the Program Manager Console (PMC), enter the following commands to add the NuGet packages:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
Install-Package Microsoft.EntityFrameworkCore.SqlServer -Version 5.0.0-rc.2.20475.6
```

<span data-ttu-id="6ad07-152">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet 套件提供 EF Core 錯誤頁面 ASP.NET Core 中介軟體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-152">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for EF Core error pages.</span></span> <span data-ttu-id="6ad07-153">此中介軟體有助於偵測並診斷 EF Core 遷移的錯誤。</span><span class="sxs-lookup"><span data-stu-id="6ad07-153">This middleware helps to detect and diagnose errors with EF Core migrations.</span></span>

<span data-ttu-id="6ad07-154">如需其他可用於 EF Core 之資料庫提供者的詳細資訊，請參閱 [資料庫提供者](/ef/core/providers/)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-154">For information about other database providers that are available for EF Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="6ad07-155">建立資料模型</span><span class="sxs-lookup"><span data-stu-id="6ad07-155">Create the data model</span></span>

<span data-ttu-id="6ad07-156">系統會為此應用程式建立下列實體類別：</span><span class="sxs-lookup"><span data-stu-id="6ad07-156">The following entity classes are created for this app:</span></span>

![Course-Enrollment-Student 資料模型圖表](intro/_static/data-model-diagram.png)

<span data-ttu-id="6ad07-158">上述實體具有下列關聯性：</span><span class="sxs-lookup"><span data-stu-id="6ad07-158">The preceding entities have the following relationships:</span></span>

* <span data-ttu-id="6ad07-159">與實體之間有一對多關聯性 `Student` `Enrollment` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-159">A one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="6ad07-160">學生可以在任意數目的課程中註冊。</span><span class="sxs-lookup"><span data-stu-id="6ad07-160">A student can be enrolled in any number of courses.</span></span>
* <span data-ttu-id="6ad07-161">與實體之間有一對多關聯性 `Course` `Enrollment` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-161">A one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="6ad07-162">一堂課程可以讓任意數目的學生註冊。</span><span class="sxs-lookup"><span data-stu-id="6ad07-162">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="6ad07-163">在下列各節中，會為每個實體建立類別。</span><span class="sxs-lookup"><span data-stu-id="6ad07-163">In the following sections, a class is created for each of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="6ad07-164">Student 實體</span><span class="sxs-lookup"><span data-stu-id="6ad07-164">The Student entity</span></span>

![Student 實體圖表](intro/_static/student-entity.png)

<span data-ttu-id="6ad07-166">在 [ *模型* ] 資料夾中， `Student` 使用下列程式碼建立類別：</span><span class="sxs-lookup"><span data-stu-id="6ad07-166">In the *Models* folder, create the `Student` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="6ad07-167">`ID`屬性（property）是對應至這個類別的資料庫資料表中， ( **PK** ) 資料行的主鍵。</span><span class="sxs-lookup"><span data-stu-id="6ad07-167">The `ID` property is the primary key ( **PK** ) column of the database table that corresponds to this class.</span></span> <span data-ttu-id="6ad07-168">根據預設，EF 會將名為或的 `ID` 屬性 `classnameID` 視為主要索引鍵。</span><span class="sxs-lookup"><span data-stu-id="6ad07-168">By default, EF interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="6ad07-169">例如，PK 可以命名為， `StudentID` 而不是 `ID` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-169">For example, the PK could be named `StudentID` rather than `ID`.</span></span>

<span data-ttu-id="6ad07-170">`Enrollments` 屬性為[導覽屬性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-170">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="6ad07-171">導覽屬性會保留與此實體相關的其他實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-171">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="6ad07-172">`Enrollments`實體的屬性 `Student` ：</span><span class="sxs-lookup"><span data-stu-id="6ad07-172">The `Enrollments` property of a `Student` entity:</span></span>

* <span data-ttu-id="6ad07-173">包含與 `Enrollment` 該實體相關的所有實體 `Student` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-173">Contains all of the `Enrollment` entities that are related to that `Student` entity.</span></span>
* <span data-ttu-id="6ad07-174">如果 `Student` 資料庫中的特定資料列有兩個相關的資料 `Enrollment` 列：</span><span class="sxs-lookup"><span data-stu-id="6ad07-174">If a specific `Student` row in the database has two related `Enrollment` rows:</span></span>
  * <span data-ttu-id="6ad07-175">該 `Student` 實體的 `Enrollments` 導覽屬性包含這兩個 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-175">That `Student` entity's `Enrollments` navigation property contains those two `Enrollment` entities.</span></span>
  
<span data-ttu-id="6ad07-176">`Enrollment` 資料列會在 `StudentID` 外鍵 ( **FK** ) 資料行中包含學生的 PK 值。</span><span class="sxs-lookup"><span data-stu-id="6ad07-176">`Enrollment` rows contain a student's PK value in the `StudentID` foreign key ( **FK** ) column.</span></span>

<span data-ttu-id="6ad07-177">如果導覽屬性可以保存多個實體：</span><span class="sxs-lookup"><span data-stu-id="6ad07-177">If a navigation property can hold multiple entities:</span></span>

 * <span data-ttu-id="6ad07-178">型別必須是清單，例如 `ICollection<T>` 、 `List<T>` 或 `HashSet<T>` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-178">The type must be a list, such as `ICollection<T>`, `List<T>`, or `HashSet<T>`.</span></span>
 * <span data-ttu-id="6ad07-179">您可以新增、刪除和更新實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-179">Entities can be added, deleted, and updated.</span></span>

<span data-ttu-id="6ad07-180">多對多和一對多導覽關聯性可包含多個實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-180">Many-to-many and one-to-many navigation relationships can contain multiple entities.</span></span> <span data-ttu-id="6ad07-181">`ICollection<T>`使用時，EF 預設會建立 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="6ad07-181">When `ICollection<T>` is used, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="6ad07-182">Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="6ad07-182">The Enrollment entity</span></span>

![Enrollment 實體圖表](intro/_static/enrollment-entity.png)

<span data-ttu-id="6ad07-184">在 [ *模型* ] 資料夾中， `Enrollment` 使用下列程式碼建立類別：</span><span class="sxs-lookup"><span data-stu-id="6ad07-184">In the *Models* folder, create the `Enrollment` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="6ad07-185">`EnrollmentID`屬性是 PK。</span><span class="sxs-lookup"><span data-stu-id="6ad07-185">The `EnrollmentID` property is the PK.</span></span> <span data-ttu-id="6ad07-186">這個實體會使用 `classnameID` 模式，而不是 `ID` 本身。</span><span class="sxs-lookup"><span data-stu-id="6ad07-186">This entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="6ad07-187">`Student`使用模式的實體 `ID` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-187">The `Student` entity used the `ID` pattern.</span></span> <span data-ttu-id="6ad07-188">有些開發人員偏好在資料模型中使用一種模式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-188">Some developers prefer to use one pattern throughout the data model.</span></span> <span data-ttu-id="6ad07-189">在本教學課程中，變化說明可以使用任一種模式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-189">In this tutorial, the variation illustrates that either pattern can be used.</span></span> <span data-ttu-id="6ad07-190">[稍後的教學](inheritance.md)課程會示範如何使用 `ID` 不含 classname，讓您更輕鬆地在資料模型中執行繼承。</span><span class="sxs-lookup"><span data-stu-id="6ad07-190">A [later tutorial](inheritance.md) shows how using `ID` without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="6ad07-191">`Grade` 屬性為一個 `enum`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-191">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="6ad07-192">型別宣告 `?` 之後的 `Grade` 會指出該 `Grade` 屬性可 [為 null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-192">The `?` after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types).</span></span> <span data-ttu-id="6ad07-193">其等級與 `null` 零級不同。</span><span class="sxs-lookup"><span data-stu-id="6ad07-193">A grade that's `null` is different from a zero grade.</span></span> <span data-ttu-id="6ad07-194">`null` 表示不知道或尚未指派等級。</span><span class="sxs-lookup"><span data-stu-id="6ad07-194">`null` means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="6ad07-195">`StudentID`屬性是 (FK) 的外鍵，而對應的導覽屬性為 `Student` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-195">The `StudentID` property is a foreign key (FK), and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="6ad07-196">`Enrollment`實體與一個實體相關聯 `Student` ，因此屬性只能包含單一 `Student` 實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-196">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity.</span></span> <span data-ttu-id="6ad07-197">這不同于 `Student.Enrollments` 可保存多個實體的導覽屬性 `Enrollment` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-197">This differs from the `Student.Enrollments` navigation property, which can hold multiple `Enrollment` entities.</span></span>

<span data-ttu-id="6ad07-198">`CourseID`屬性是 FK，而對應的導覽屬性為 `Course` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-198">The `CourseID` property is a FK, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="6ad07-199">一個 `Enrollment` 實體與一個 `Course` 實體建立關聯。</span><span class="sxs-lookup"><span data-stu-id="6ad07-199">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="6ad07-200">Entity Framework 會將屬性（property）解讀為 FK 屬性（property），並將其命名為「 `<` 導覽屬性名稱 `><` 主鍵屬性名稱」 `>` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-200">Entity Framework interprets a property as a FK property if it's named `<`navigation property name`><`primary key property name`>`.</span></span> <span data-ttu-id="6ad07-201">例如， `StudentID` `Student` 因為 `Student` 實體的 PK 是，所以針對導覽屬性 `ID` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-201">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="6ad07-202">FK 屬性也可以命名  `<` 為主鍵屬性名稱 `>` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-202">FK properties can also be named  `<`primary key property name`>`.</span></span> <span data-ttu-id="6ad07-203">例如， `CourseID` 因為 `Course` 實體的 PK 為 `CourseID` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-203">For example, `CourseID` because the `Course` entity's PK is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="6ad07-204">Course 實體</span><span class="sxs-lookup"><span data-stu-id="6ad07-204">The Course entity</span></span>

![Course 實體圖表](intro/_static/course-entity.png)

<span data-ttu-id="6ad07-206">在 [ *模型* ] 資料夾中， `Course` 使用下列程式碼建立類別：</span><span class="sxs-lookup"><span data-stu-id="6ad07-206">In the *Models* folder, create the `Course` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="6ad07-207">`Enrollments` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="6ad07-207">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="6ad07-208">`Course` 實體可以與任何數量的 `Enrollment` 實體相關。</span><span class="sxs-lookup"><span data-stu-id="6ad07-208">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="6ad07-209">[DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute)屬性將在[稍後的教學](complex-data-model.md)課程中說明。</span><span class="sxs-lookup"><span data-stu-id="6ad07-209">The [DatabaseGenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) attribute is explained in a [later tutorial](complex-data-model.md).</span></span> <span data-ttu-id="6ad07-210">這個屬性可讓您在課程中輸入 PK，而非讓資料庫產生它。</span><span class="sxs-lookup"><span data-stu-id="6ad07-210">This attribute allows entering the PK for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="6ad07-211">建立資料庫內容</span><span class="sxs-lookup"><span data-stu-id="6ad07-211">Create the database context</span></span>

<span data-ttu-id="6ad07-212">協調指定資料模型之 EF 功能的主要類別是 <xref:Microsoft.EntityFrameworkCore.DbContext> 資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="6ad07-212">The main class that coordinates EF functionality for a given data model is the <xref:Microsoft.EntityFrameworkCore.DbContext> database context class.</span></span> <span data-ttu-id="6ad07-213">此類別是透過衍生自 `Microsoft.EntityFrameworkCore.DbContext` 類別來建立。</span><span class="sxs-lookup"><span data-stu-id="6ad07-213">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="6ad07-214">`DbContext`衍生類別會指定要包含在資料模型中的實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-214">The `DbContext` derived class specifies which entities are included in the data model.</span></span> <span data-ttu-id="6ad07-215">某些 EF 行為可以自訂。</span><span class="sxs-lookup"><span data-stu-id="6ad07-215">Some EF behaviors can be customized.</span></span> <span data-ttu-id="6ad07-216">在此專案中，類別命名為 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-216">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="6ad07-217">在專案資料夾中，建立名為的資料夾 `Data` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-217">In the project folder, create a folder named `Data`.</span></span>

<span data-ttu-id="6ad07-218">在 [ *Data* ] 資料夾中， `SchoolContext` 使用下列程式碼建立類別：</span><span class="sxs-lookup"><span data-stu-id="6ad07-218">In the *Data* folder create a `SchoolContext` class with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="6ad07-219">上述程式碼會 `DbSet` 為每個實體集建立屬性。</span><span class="sxs-lookup"><span data-stu-id="6ad07-219">The preceding code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="6ad07-220">EF 術語：</span><span class="sxs-lookup"><span data-stu-id="6ad07-220">In EF terminology:</span></span>

* <span data-ttu-id="6ad07-221">實體集通常會對應到資料庫資料表。</span><span class="sxs-lookup"><span data-stu-id="6ad07-221">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="6ad07-222">實體會對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="6ad07-222">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6ad07-223">您 `DbSet<Enrollment>` `DbSet<Course>` 可以省略和語句，其運作方式也相同。</span><span class="sxs-lookup"><span data-stu-id="6ad07-223">The `DbSet<Enrollment>` and `DbSet<Course>` statements could be omitted and it would work the same.</span></span> <span data-ttu-id="6ad07-224">EF 會隱含地包含它們，因為：</span><span class="sxs-lookup"><span data-stu-id="6ad07-224">EF would include them implicitly because:</span></span>

* <span data-ttu-id="6ad07-225">`Student`實體參考 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-225">The `Student` entity references the `Enrollment` entity.</span></span>
* <span data-ttu-id="6ad07-226">`Enrollment`實體參考 `Course` 實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-226">The `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="6ad07-227">資料庫建立時，EF 會建立和 `DbSet` 屬性名稱相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="6ad07-227">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="6ad07-228">集合的屬性名稱通常是複數。</span><span class="sxs-lookup"><span data-stu-id="6ad07-228">Property names for collections are typically plural.</span></span> <span data-ttu-id="6ad07-229">例如， `Students` 而不是 `Student` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-229">For example, `Students` rather than `Student`.</span></span> <span data-ttu-id="6ad07-230">針對是否要複數化資料表名稱，開發人員並沒有共識。</span><span class="sxs-lookup"><span data-stu-id="6ad07-230">Developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="6ad07-231">在這些教學課程中，會藉由在中指定單一資料表名稱來覆寫預設行為 `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-231">For these tutorials, the default behavior is overridden by specifying singular table names in the `DbContext`.</span></span> <span data-ttu-id="6ad07-232">若要完成這項操作，請在最後一個 DbSet 屬性後方新增下列醒目提示程式碼。</span><span class="sxs-lookup"><span data-stu-id="6ad07-232">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a><span data-ttu-id="6ad07-233">註冊 `SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="6ad07-233">Register the `SchoolContext`</span></span>

<span data-ttu-id="6ad07-234">ASP.NET Core 包含了[相依性插入](../../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-234">ASP.NET Core includes [dependency injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="6ad07-235">服務（例如 EF 資料庫內容）會在應用程式啟動期間以相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="6ad07-235">Services, such as the EF database context, are registered with dependency injection during app startup.</span></span> <span data-ttu-id="6ad07-236">需要這些服務的元件（例如 MVC 控制器）是透過函式參數提供這些服務。</span><span class="sxs-lookup"><span data-stu-id="6ad07-236">Components that require these services, such as MVC controllers, are provided these services via constructor parameters.</span></span> <span data-ttu-id="6ad07-237">本教學課程稍後會顯示取得內容實例的控制器程式碼。</span><span class="sxs-lookup"><span data-stu-id="6ad07-237">The controller constructor code that gets a context instance is shown later in this tutorial.</span></span>

<span data-ttu-id="6ad07-238">若要將 `SchoolContext` 註冊為服務，請開啟 *Startup.cs* ，並將醒目標示的程式碼新增至 `ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ad07-238">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

<span data-ttu-id="6ad07-239">連接字串的名稱，會透過呼叫 `DbContextOptionsBuilder` 物件上的方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="6ad07-239">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="6ad07-240">針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-240">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="6ad07-241">開啟檔案 *appsettings.json* ，並加入連接字串，如下列標記所示：</span><span class="sxs-lookup"><span data-stu-id="6ad07-241">Open the *appsettings.json* file and add a connection string as shown in the following markup:</span></span>

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="6ad07-242">新增資料庫例外狀況篩選準則</span><span class="sxs-lookup"><span data-stu-id="6ad07-242">Add the database exception filter</span></span>

<span data-ttu-id="6ad07-243">將加入 <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> 至， `ConfigureServices` 如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="6ad07-243">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=1=2,22-23)]

<span data-ttu-id="6ad07-244">會 `AddDatabaseDeveloperPageExceptionFilter` 在 [開發環境](xref:fundamentals/environments)中提供有用的錯誤資訊。</span><span class="sxs-lookup"><span data-stu-id="6ad07-244">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="6ad07-245">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="6ad07-245">SQL Server Express LocalDB</span></span>

<span data-ttu-id="6ad07-246">連接字串會指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="6ad07-247">LocalDB 是輕量版的 SQL Server Express Database Engine，旨在用於應用程序開發，而不是生產用途。</span><span class="sxs-lookup"><span data-stu-id="6ad07-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="6ad07-248">LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。</span><span class="sxs-lookup"><span data-stu-id="6ad07-248">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="6ad07-249">LocalDB 預設會在 `C:/Users/<user>` 目錄中建立 *.mdf* 資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="6ad07-249">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="6ad07-250">使用測試資料將 DB 初始化</span><span class="sxs-lookup"><span data-stu-id="6ad07-250">Initialize DB with test data</span></span>

<span data-ttu-id="6ad07-251">EF 會建立空的資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-251">EF creates an empty database.</span></span> <span data-ttu-id="6ad07-252">在本節中，會加入在建立資料庫之後呼叫的方法，以便將測試資料填入。</span><span class="sxs-lookup"><span data-stu-id="6ad07-252">In this section, a method is added that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="6ad07-253">`EnsureCreated`方法是用來自動建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-253">The `EnsureCreated` method is used to automatically create the database.</span></span> <span data-ttu-id="6ad07-254">在 [稍後的教學](migrations.md)課程中，您將瞭解如何使用 Code First 移轉來變更資料庫架構，而不是卸載和重新建立資料庫，來處理模型變更。</span><span class="sxs-lookup"><span data-stu-id="6ad07-254">In a [later tutorial](migrations.md), you see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="6ad07-255">在 [ *資料* ] 資料夾中，使用下列程式碼來建立名為的新類別 `DbInitializer` ：</span><span class="sxs-lookup"><span data-stu-id="6ad07-255">In the *Data* folder, create a new class named `DbInitializer` with the following code:</span></span>

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

<span data-ttu-id="6ad07-256">上述程式碼會檢查資料庫是否存在：</span><span class="sxs-lookup"><span data-stu-id="6ad07-256">The preceding code checks if the database exists:</span></span>

* <span data-ttu-id="6ad07-257">如果找不到資料庫，則為，</span><span class="sxs-lookup"><span data-stu-id="6ad07-257">If the database is not found;</span></span>
  * <span data-ttu-id="6ad07-258">它會建立並載入測試資料。</span><span class="sxs-lookup"><span data-stu-id="6ad07-258">It is created and loaded with test data.</span></span> <span data-ttu-id="6ad07-259">會將測試資料載入至陣列而非 `List<T>` 集合，以最佳化效能。</span><span class="sxs-lookup"><span data-stu-id="6ad07-259">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>
* <span data-ttu-id="6ad07-260">如果找到資料庫，則不會採取任何動作。</span><span class="sxs-lookup"><span data-stu-id="6ad07-260">If the database if found, it takes no action.</span></span>

<span data-ttu-id="6ad07-261">使用下列程式碼更新 *Program.cs* ：</span><span class="sxs-lookup"><span data-stu-id="6ad07-261">Update *Program.cs* with the following code:</span></span>

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

<span data-ttu-id="6ad07-262">*Program.cs* 會在應用程式啟動時執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="6ad07-262">*Program.cs* does the following on app startup:</span></span>

* <span data-ttu-id="6ad07-263">從相依性插入容器中取得資料庫內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-263">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="6ad07-264">呼叫 `DbInitializer.Initialize` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ad07-264">Call the `DbInitializer.Initialize` method.</span></span>
* <span data-ttu-id="6ad07-265">當方法完成時處置內容， `Initialize` 如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="6ad07-265">Dispose the context when the `Initialize` method completes as shown in the following code:</span></span>

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

<span data-ttu-id="6ad07-266">第一次執行應用程式時，會建立資料庫並載入測試資料。</span><span class="sxs-lookup"><span data-stu-id="6ad07-266">The first time the app is run, the database is created and loaded with test data.</span></span> <span data-ttu-id="6ad07-267">每次資料模型變更時：</span><span class="sxs-lookup"><span data-stu-id="6ad07-267">Whenever the data model changes:</span></span>

* <span data-ttu-id="6ad07-268">刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-268">Delete the database.</span></span>
* <span data-ttu-id="6ad07-269">更新種子方法，並以新的資料庫開始再次。</span><span class="sxs-lookup"><span data-stu-id="6ad07-269">Update the seed method, and start afresh with a new database.</span></span>

 <span data-ttu-id="6ad07-270">在稍後的教學課程中，資料庫會在資料模型變更時進行修改，而不需要刪除然後再重新建立。</span><span class="sxs-lookup"><span data-stu-id="6ad07-270">In later tutorials, the database is modified when the data model changes, without deleting and re-creating it.</span></span> <span data-ttu-id="6ad07-271">當資料模型變更時，不會遺失任何資料。</span><span class="sxs-lookup"><span data-stu-id="6ad07-271">No data is lost when the data model changes.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="6ad07-272">建立控制器和檢視</span><span class="sxs-lookup"><span data-stu-id="6ad07-272">Create controller and views</span></span>

<span data-ttu-id="6ad07-273">使用 Visual Studio 中的「樣板」引擎來新增 MVC 控制器和將使用 EF 來查詢和儲存資料的視圖。</span><span class="sxs-lookup"><span data-stu-id="6ad07-273">Use the scaffolding engine in Visual Studio to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="6ad07-274">自動建立 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 動作方法和視圖稱為「樣板」。</span><span class="sxs-lookup"><span data-stu-id="6ad07-274">The automatic creation of [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) action methods and views is known as scaffolding.</span></span>

* <span data-ttu-id="6ad07-275">在 **方案總管** 中，以滑鼠右鍵按一下 `Controllers` 資料夾，然後選取 [ **加入 > 新的 scaffold 專案** ]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-275">In **Solution Explorer** , right-click the `Controllers` folder  and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="6ad07-276">在 [新增 Scaffold] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="6ad07-276">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="6ad07-277">選取 [使用 Entity Framework 執行檢視的 MVC 控制器]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-277">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="6ad07-278">按一下 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="6ad07-278">Click **Add**.</span></span> <span data-ttu-id="6ad07-279">[ **Entity Framework 使用 Scaffold 新增 MVC 控制器** ] 對話方塊隨即出現： ![ Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="6ad07-279">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="6ad07-280">在 [ **模型類別** ] 中選取 [ **Student** ]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-280">In **Model class** , select **Student**.</span></span>
  * <span data-ttu-id="6ad07-281">在 [ **資料內容類別** ] 中，選取 [ **SchoolCoNtext** ]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-281">In **Data context class** , select **SchoolContext**.</span></span>
  * <span data-ttu-id="6ad07-282">接受預設的 **StudentsController** 作為名稱。</span><span class="sxs-lookup"><span data-stu-id="6ad07-282">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="6ad07-283">按一下 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="6ad07-283">Click **Add**.</span></span>

<span data-ttu-id="6ad07-284">Visual Studio 的樣板引擎會建立一個檔案 `StudentsController.cs` ，以及一組 `*.cshtml` 與控制器一起使用的 (檔案) 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-284">The Visual Studio scaffolding engine creates a `StudentsController.cs` file and a set of views (`*.cshtml` files) that work with the controller.</span></span>

<span data-ttu-id="6ad07-285">請注意，控制器會採用作為函式 `SchoolContext` 參數。</span><span class="sxs-lookup"><span data-stu-id="6ad07-285">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="6ad07-286">ASP.NET Core 相依性插入會負責傳遞 `SchoolContext` 的執行個體給控制器。</span><span class="sxs-lookup"><span data-stu-id="6ad07-286">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="6ad07-287">您已在類別中進行設定 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-287">You configured that in the `Startup` class.</span></span>

<span data-ttu-id="6ad07-288">控制器含有一個 `Index` 動作方法，該方法會顯示資料庫中的所有學生。</span><span class="sxs-lookup"><span data-stu-id="6ad07-288">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="6ad07-289">方法會藉由讀取資料庫內容執行個體的 `Students` 屬性，來從 Students 實體集中取得學生的清單：</span><span class="sxs-lookup"><span data-stu-id="6ad07-289">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="6ad07-290">本教學課程稍後會說明此程式碼中的非同步程式設計項目。</span><span class="sxs-lookup"><span data-stu-id="6ad07-290">The asynchronous programming elements in this code are explained later in the tutorial.</span></span>

<span data-ttu-id="6ad07-291">*Views/Students/Index.cshtml* 檢視會在一個資料表中顯示這份清單：</span><span class="sxs-lookup"><span data-stu-id="6ad07-291">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="6ad07-292">按 CTRL+F5 來執行專案，或從功能表選擇 [偵錯] > [啟動但不偵錯]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-292">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="6ad07-293">按一下 [Students] 索引標籤來查看 `DbInitializer.Initialize` 方法插入的測試資料。</span><span class="sxs-lookup"><span data-stu-id="6ad07-293">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="6ad07-294">取決於您瀏覽器視窗的寬度，您可能會在頁面的頂端看到 `Students` 索引標籤連結，或是按一下位於右上角的導覽圖示來查看連結。</span><span class="sxs-lookup"><span data-stu-id="6ad07-294">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso 大學首頁 (窄)](intro/_static/home-page-narrow.png)

![Students [索引] 頁面](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="6ad07-297">檢視資料庫</span><span class="sxs-lookup"><span data-stu-id="6ad07-297">View the database</span></span>

<span data-ttu-id="6ad07-298">當應用程式啟動時，方法就會 `DbInitializer.Initialize` 呼叫 `EnsureCreated` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-298">When the app is started, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="6ad07-299">EF 看到沒有資料庫：</span><span class="sxs-lookup"><span data-stu-id="6ad07-299">EF saw that there was no database:</span></span>

* <span data-ttu-id="6ad07-300">所以它會建立一個資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-300">So it created a database.</span></span>
* <span data-ttu-id="6ad07-301">`Initialize`方法程式碼會將資料填入資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-301">The `Initialize` method code populated the database with data.</span></span>

<span data-ttu-id="6ad07-302">使用 **SQL Server 物件總管** (SSOX) 來查看 Visual Studio 中的資料庫：</span><span class="sxs-lookup"><span data-stu-id="6ad07-302">Use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio:</span></span>

* <span data-ttu-id="6ad07-303">從 Visual Studio 的 [ **View** ] 功能表中選取 [ **SQL Server 物件總管** ]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-303">Select **SQL Server Object Explorer** from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="6ad07-304">在 SSOX 中，選取 **(localdb) \mssqllocaldb > 資料庫** 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-304">In SSOX, select **(localdb)\MSSQLLocalDB > Databases**.</span></span>
* <span data-ttu-id="6ad07-305">在檔案的 `ContosoUniversity1` 連接字串中，選取資料庫名稱的專案 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-305">Select `ContosoUniversity1`, the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="6ad07-306">展開 [ **資料表]** 節點，查看資料庫中的資料表。</span><span class="sxs-lookup"><span data-stu-id="6ad07-306">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX 中的資料表](intro/_static/ssox-tables.png)

<span data-ttu-id="6ad07-308">以滑鼠右鍵按一下 **Student** 資料表，然後按一下 [ **View data** ] 以查看資料表中的資料。</span><span class="sxs-lookup"><span data-stu-id="6ad07-308">Right-click the **Student** table and click **View Data** to see the data in the table.</span></span>

![SSOX 中的 Student 資料表](intro/_static/ssox-student-table.png)

<span data-ttu-id="6ad07-310">`*.mdf`和資料庫檔案位於 `*.ldf` *C:\Users \\ \<username>* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="6ad07-310">The `*.mdf` and `*.ldf` database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="6ad07-311">因為 `EnsureCreated` 會在應用程式啟動時執行的初始化運算式方法中呼叫，所以您可以：</span><span class="sxs-lookup"><span data-stu-id="6ad07-311">Because `EnsureCreated` is called in the initializer method that runs on app start, you could:</span></span>

* <span data-ttu-id="6ad07-312">對類別進行變更 `Student` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-312">Make a change to the `Student` class.</span></span>
* <span data-ttu-id="6ad07-313">刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-313">Delete the database.</span></span>
* <span data-ttu-id="6ad07-314">停止，然後啟動應用程式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-314">Stop, then start the app.</span></span> <span data-ttu-id="6ad07-315">系統會自動重新建立資料庫以符合變更。</span><span class="sxs-lookup"><span data-stu-id="6ad07-315">The database is automatically re-created to match the change.</span></span>

<span data-ttu-id="6ad07-316">例如，如果將某個 `EmailAddress` 屬性加入至 `Student` 類別， `EmailAddress` 則會在重新建立的資料表中加入新的資料行。</span><span class="sxs-lookup"><span data-stu-id="6ad07-316">For example, if an `EmailAddress` property is added to the `Student` class, a new `EmailAddress` column in the re-created table.</span></span> <span data-ttu-id="6ad07-317">視圖歸類將不會顯示新的 `EmailAddress` 屬性。</span><span class="sxs-lookup"><span data-stu-id="6ad07-317">The view classed won't display the new `EmailAddress` property.</span></span>

## <a name="conventions"></a><span data-ttu-id="6ad07-318">慣例</span><span class="sxs-lookup"><span data-stu-id="6ad07-318">Conventions</span></span>

<span data-ttu-id="6ad07-319">為了讓 EF 建立完整資料庫而撰寫的程式碼數量很少，因為使用 EF 所使用的慣例：</span><span class="sxs-lookup"><span data-stu-id="6ad07-319">The amount of code written in order for the EF to to create a complete database is minimal because of the use of the conventions EF uses:</span></span>

* <span data-ttu-id="6ad07-320">`DbSet` 屬性的名稱會用於資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="6ad07-320">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="6ad07-321">針對 `DbSet` 屬性並未參考的實體，實體類別名稱會用於資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="6ad07-321">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="6ad07-322">實體屬性名稱會用於資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="6ad07-322">Entity property names are used for column names.</span></span>
* <span data-ttu-id="6ad07-323">名為或的實體屬性會被辨識 `ID` `classnameID` 為 PK 屬性。</span><span class="sxs-lookup"><span data-stu-id="6ad07-323">Entity properties that are named `ID` or `classnameID` are recognized as PK properties.</span></span>
* <span data-ttu-id="6ad07-324">如果屬性名稱為「 `<` 導覽屬性名稱 PK」屬性名稱，則會將屬性視為 FK 屬性 `><` `>` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-324">A property is interpreted as a FK property if it's named `<`navigation property name`><`PK property name`>`.</span></span> <span data-ttu-id="6ad07-325">例如， `StudentID` `Student` 因為 `Student` 實體的 PK 是，所以針對導覽屬性 `ID` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-325">For example, `StudentID` for the `Student` navigation property since the `Student` entity's PK is `ID`.</span></span> <span data-ttu-id="6ad07-326">FK 屬性也可以命名 `<` 為主鍵屬性名稱 `>` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-326">FK properties can also be named `<`primary key property name`>`.</span></span> <span data-ttu-id="6ad07-327">例如， `EnrollmentID` 因為 `Enrollment` 實體的 PK 為 `EnrollmentID` 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-327">For example, `EnrollmentID` since the `Enrollment` entity's PK is `EnrollmentID`.</span></span>

<span data-ttu-id="6ad07-328">慣例行為可以被覆寫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-328">Conventional behavior can be overridden.</span></span> <span data-ttu-id="6ad07-329">例如，您可以明確指定資料表名稱，如本教學課程稍早所述。</span><span class="sxs-lookup"><span data-stu-id="6ad07-329">For example, table names can be explicitly specified, as shown earlier in this tutorial.</span></span> <span data-ttu-id="6ad07-330">資料行名稱和任何屬性都可以設定為 PK 或 FK。</span><span class="sxs-lookup"><span data-stu-id="6ad07-330">Column names and any property can be set as a PK or FK.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="6ad07-331">非同步程式碼</span><span class="sxs-lookup"><span data-stu-id="6ad07-331">Asynchronous code</span></span>

<span data-ttu-id="6ad07-332">非同步程式設計是預設的 ASP.NET Core 和 EF Core 模式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="6ad07-333">網頁伺服器的可用執行緒數量有限，而且在高負載情況下，可能會使用所有可用的執行緒。</span><span class="sxs-lookup"><span data-stu-id="6ad07-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="6ad07-334">發生此情況時，伺服器將無法處理新的要求，直到執行緒空出來。</span><span class="sxs-lookup"><span data-stu-id="6ad07-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="6ad07-335">使用同步程式碼，許多執行緒可能在實際上並未執行任何工作時受到占用，原因是在等候 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="6ad07-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="6ad07-336">使用非同步程式碼，處理程序在等候 I/O 完成時，其執行緒將會空出來以讓伺服器處理其他要求。</span><span class="sxs-lookup"><span data-stu-id="6ad07-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="6ad07-337">因此，非同步程式碼可讓伺服器資源更有效率地使用，而且伺服器可處理更多流量而不會造成延遲。</span><span class="sxs-lookup"><span data-stu-id="6ad07-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="6ad07-338">非同步程式碼雖然的確會在執行階段造成少量的負荷，但在低流量情況下，對效能的衝擊非常微小；在高流量情況下，潛在的效能改善則相當大。</span><span class="sxs-lookup"><span data-stu-id="6ad07-338">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="6ad07-339">在下列程式碼中，、、 `async` `Task<T>` 和會使程式 `await` `ToListAsync` 代碼以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="6ad07-339">In the following code, `async`, `Task<T>`, `await`, and `ToListAsync` make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="6ad07-340">`async` 關鍵字會告訴編譯器為方法本體的一部分產生回呼，並自動建立傳回的 `Task<IActionResult>` 物件。</span><span class="sxs-lookup"><span data-stu-id="6ad07-340">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="6ad07-341">傳回類型 `Task<IActionResult>` 代表了正在進行的工作，其結果為 `IActionResult` 類型。</span><span class="sxs-lookup"><span data-stu-id="6ad07-341">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="6ad07-342">`await` 關鍵字會使編譯器將方法分割為兩部分。</span><span class="sxs-lookup"><span data-stu-id="6ad07-342">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="6ad07-343">第一個部分會與非同步啟動的作業一同結束。</span><span class="sxs-lookup"><span data-stu-id="6ad07-343">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="6ad07-344">第二個部分則會放入作業完成時呼叫的回呼方法中。</span><span class="sxs-lookup"><span data-stu-id="6ad07-344">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="6ad07-345">`ToListAsync` 是 `ToList` 擴充方法的非同步版本。</span><span class="sxs-lookup"><span data-stu-id="6ad07-345">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="6ad07-346">撰寫使用 EF 的非同步程式碼時，必須注意的一些事項：</span><span class="sxs-lookup"><span data-stu-id="6ad07-346">Some things to be aware of when  writing asynchronous code that uses EF:</span></span>

* <span data-ttu-id="6ad07-347">只有讓查詢或命令傳送至資料庫的陳述式，會以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="6ad07-347">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="6ad07-348">其中包含，例如 `ToListAsync`、`SingleOrDefaultAsync`，以及 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-348">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="6ad07-349">其中不包含，例如：僅變更 `IQueryable` 的陳述式，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-349">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="6ad07-350">EF 內容在執行緒中並不安全：不要嘗試執行多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="6ad07-350">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="6ad07-351">當您呼叫任何 async EF 方法時，請一律使用 `await` 關鍵字。</span><span class="sxs-lookup"><span data-stu-id="6ad07-351">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="6ad07-352">若要利用非同步程式碼的效能優勢，請確定任何使用的程式庫套件如果呼叫任何 EF 方法，而導致查詢傳送到資料庫，則請確定任何使用的程式庫套件也都使用 async。</span><span class="sxs-lookup"><span data-stu-id="6ad07-352">To take advantage of the performance benefits of async code, make sure that any library packages used also use async if they call any EF methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="6ad07-353">如需在 .NET 中非同步程式設計的詳細資訊，請參閱[非同步總覽](/dotnet/articles/standard/async)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-353">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="limit-entities-fetched"></a><span data-ttu-id="6ad07-354">已提取的實體數目</span><span class="sxs-lookup"><span data-stu-id="6ad07-354">Limit entities fetched</span></span>

<span data-ttu-id="6ad07-355">請參閱 [效能考慮](xref:data/ef-rp/intro) ，以取得限制查詢所傳回之數目或實體的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="6ad07-355">See [Performance considerations](xref:data/ef-rp/intro) for information on limiting the number or entities returned from a query.</span></span>

<span data-ttu-id="6ad07-356">若要了解如何執行基本的 CRUD (建立、讀取、更新、刪除) 作業，請前往下一個教學課程。</span><span class="sxs-lookup"><span data-stu-id="6ad07-356">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6ad07-357">實作基本的 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="6ad07-357">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

<span data-ttu-id="6ad07-358">Contoso 大學範例 Web 應用程式示範如何使用 Entity Framework (EF) Core 2.2 和 Visual Studio 2017 或 2019 建立 ASP.NET Core 2.2 MVC Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-358">The Contoso University sample web application demonstrates how to create ASP.NET Core 2.2 MVC web applications using Entity Framework (EF) Core 2.2 and Visual Studio 2017 or 2019.</span></span>

<span data-ttu-id="6ad07-359">本教學課程尚未針對 ASP.NET Core 3.1 進行更新。</span><span class="sxs-lookup"><span data-stu-id="6ad07-359">This tutorial has not been updated for ASP.NET Core 3.1.</span></span> <span data-ttu-id="6ad07-360">它已針對 [ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0)進行更新。</span><span class="sxs-lookup"><span data-stu-id="6ad07-360">It has been updated for [ASP.NET Core 5.0](xref:data/ef-mvc/intro?view=aspnetcore-5.0).</span></span>

<span data-ttu-id="6ad07-361">這個範例應用程式是虛構的 Contoso 大學網站。</span><span class="sxs-lookup"><span data-stu-id="6ad07-361">The sample application is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="6ad07-362">其中包括的功能有學生入學許可、課程建立、教師指派。</span><span class="sxs-lookup"><span data-stu-id="6ad07-362">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="6ad07-363">這是說明如何從零開始建立 Contoso 大學範例應用程式教學課程系列中的第一頁。</span><span class="sxs-lookup"><span data-stu-id="6ad07-363">This is the first in a series of tutorials that explain how to build the Contoso University sample application from scratch.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6ad07-364">必要條件</span><span class="sxs-lookup"><span data-stu-id="6ad07-364">Prerequisites</span></span>

* [<span data-ttu-id="6ad07-365">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="6ad07-365">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download)
* <span data-ttu-id="6ad07-366">包含下列工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)：</span><span class="sxs-lookup"><span data-stu-id="6ad07-366">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the following workloads:</span></span>
  * <span data-ttu-id="6ad07-367">**ASP.NET 與網頁程式開發** 工作負載</span><span class="sxs-lookup"><span data-stu-id="6ad07-367">**ASP.NET and web development** workload</span></span>
  * <span data-ttu-id="6ad07-368">**.NET Core 跨平台開發** 工作負載</span><span class="sxs-lookup"><span data-stu-id="6ad07-368">**.NET Core cross-platform development** workload</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="6ad07-369">疑難排解</span><span class="sxs-lookup"><span data-stu-id="6ad07-369">Troubleshooting</span></span>

<span data-ttu-id="6ad07-370">如果您執行您不能解決問題，您可以藉由比較您的程式碼通常找到方案[已完成的專案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples).</span></span> <span data-ttu-id="6ad07-371">如需常見的錯誤以及如何解決這些問題的清單，請參閱[ 數列中的最後一個教學課程疑難排解 > 一節](advanced.md#common-errors)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-371">For a list of common errors and how to solve them, see [the Troubleshooting section of the last tutorial in the series](advanced.md#common-errors).</span></span> <span data-ttu-id="6ad07-372">如果您找不到您需要那里，您可以張貼問題的 StackOverflow.com [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-372">If you don't find what you need there, you can post a question to StackOverflow.com for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

> [!TIP]
> <span data-ttu-id="6ad07-373">這是 10 個教學的系列課程，當中的每一個課程都是建置於先前教學課程的成果上。</span><span class="sxs-lookup"><span data-stu-id="6ad07-373">This is a series of 10 tutorials, each of which builds on what is done in earlier tutorials.</span></span> <span data-ttu-id="6ad07-374">成功完成每一個教學課程後，請儲存專案的複本。</span><span class="sxs-lookup"><span data-stu-id="6ad07-374">Consider saving a copy of the project after each successful tutorial completion.</span></span> <span data-ttu-id="6ad07-375">如果您遇到問題，您可以從上一個教學課程來重新開始，而不需從系列的一開始從頭來過。</span><span class="sxs-lookup"><span data-stu-id="6ad07-375">Then if you run into problems, you can start over from the previous tutorial instead of going back to the beginning of the whole series.</span></span>

## <a name="contoso-university-web-app"></a><span data-ttu-id="6ad07-376">Contoso 大學 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="6ad07-376">Contoso University web app</span></span>

<span data-ttu-id="6ad07-377">您在這些教學課程中會建置的應用程式為一個簡單的大學網站。</span><span class="sxs-lookup"><span data-stu-id="6ad07-377">The application you'll be building in these tutorials is a simple university web site.</span></span>

<span data-ttu-id="6ad07-378">使用者可以檢視和更新學生、課程和教師資訊。</span><span class="sxs-lookup"><span data-stu-id="6ad07-378">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="6ad07-379">以下是您會建立的幾個畫面。</span><span class="sxs-lookup"><span data-stu-id="6ad07-379">Here are a few of the screens you'll create.</span></span>

![Students [索引] 頁面](intro/_static/students-index.png)

![Students [編輯] 頁面](intro/_static/student-edit.png)

## <a name="create-web-app"></a><span data-ttu-id="6ad07-382">建立 Web 應用程式</span><span class="sxs-lookup"><span data-stu-id="6ad07-382">Create web app</span></span>

* <span data-ttu-id="6ad07-383">開啟 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="6ad07-383">Open Visual Studio.</span></span>

* <span data-ttu-id="6ad07-384">從 [檔案] 功能表選取[新增] > [專案] 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-384">From the **File** menu, select **New > Project**.</span></span>

* <span data-ttu-id="6ad07-385">從左側窗格中，選取 [已安裝] > [Visual C#] > [Web]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-385">From the left pane, select **Installed > Visual C# > Web**.</span></span>

* <span data-ttu-id="6ad07-386">選取 [ASP.NET Core Web 應用程式] 專案範本。</span><span class="sxs-lookup"><span data-stu-id="6ad07-386">Select the **ASP.NET Core Web Application** project template.</span></span>

* <span data-ttu-id="6ad07-387">輸入 **ContosoUniversity** 作為名稱，然後按一下 [確定]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-387">Enter **ContosoUniversity** as the name and click **OK**.</span></span>

  ![[新增專案] 對話方塊](intro/_static/new-project2.png)

* <span data-ttu-id="6ad07-389">等候 [新增 ASP.NET Core Web 應用程式] 對話方塊出現。</span><span class="sxs-lookup"><span data-stu-id="6ad07-389">Wait for the **New ASP.NET Core Web Application** dialog to appear.</span></span>

* <span data-ttu-id="6ad07-390">選取 [.NET Core]、[ASP.NET Core 2.2] 和 [Web 應用程式 (Model-View-Controller)] 範本。</span><span class="sxs-lookup"><span data-stu-id="6ad07-390">Select **.NET Core** , **ASP.NET Core 2.2** and the **Web Application (Model-View-Controller)** template.</span></span>

* <span data-ttu-id="6ad07-391">確定 [ **驗證** ] 設定為 [ **無驗證** ]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-391">Make sure **Authentication** is set to **No Authentication**.</span></span>

* <span data-ttu-id="6ad07-392">選取 [確定]</span><span class="sxs-lookup"><span data-stu-id="6ad07-392">Select **OK**</span></span>

  ![[新增 ASP.NET Core 專案] 對話方塊](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a><span data-ttu-id="6ad07-394">設定網站樣式</span><span class="sxs-lookup"><span data-stu-id="6ad07-394">Set up the site style</span></span>

<span data-ttu-id="6ad07-395">一些簡單的變更會設定網站的功能表、配置和首頁。</span><span class="sxs-lookup"><span data-stu-id="6ad07-395">A few simple changes will set up the site menu, layout, and home page.</span></span>

<span data-ttu-id="6ad07-396">開啟 *Views/Shared/_Layout.cshtml* 並進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="6ad07-396">Open *Views/Shared/_Layout.cshtml* and make the following changes:</span></span>

* <span data-ttu-id="6ad07-397">將每個出現的 "ContosoUniversity" 都變更為 "Contoso University"。</span><span class="sxs-lookup"><span data-stu-id="6ad07-397">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="6ad07-398">共有三個發生次數。</span><span class="sxs-lookup"><span data-stu-id="6ad07-398">There are three occurrences.</span></span>

* <span data-ttu-id="6ad07-399">為 **About** 、 **Students** 、 **Courses** 、 **Instructors** 及 **Departments** 新增功能表項目，並刪除 **Privacy** 功能表項目。</span><span class="sxs-lookup"><span data-stu-id="6ad07-399">Add menu entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Privacy** menu entry.</span></span>

<span data-ttu-id="6ad07-400">所做的變更已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="6ad07-400">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

<span data-ttu-id="6ad07-401">在 *Views/Home/Index.cshtml* 中用下列程式碼取代檔案內容，以使用關於此應用程式的文字來取代關於 ASP.NET 和 MVC 的文字：</span><span class="sxs-lookup"><span data-stu-id="6ad07-401">In *Views/Home/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this application:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

<span data-ttu-id="6ad07-402">按 CTRL+F5 來執行專案，或從功能表選擇 [偵錯] > [啟動但不偵錯]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-402">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span> <span data-ttu-id="6ad07-403">您會看到在這些教學課程中，您將建立之頁面的索引標籤和首頁。</span><span class="sxs-lookup"><span data-stu-id="6ad07-403">You see the home page with tabs for the pages you'll create in these tutorials.</span></span>

![Contoso 大學首頁](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a><span data-ttu-id="6ad07-405">關於 EF Core NuGet 套件</span><span class="sxs-lookup"><span data-stu-id="6ad07-405">About EF Core NuGet packages</span></span>

<span data-ttu-id="6ad07-406">若要將 EF Core 支援新增至專案，請安裝您欲使用之資料庫的提供者。</span><span class="sxs-lookup"><span data-stu-id="6ad07-406">To add EF Core support to a project, install the database provider that you want to target.</span></span> <span data-ttu-id="6ad07-407">本教學課程使用 SQL Server，其提供者套件為 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-407">This tutorial uses SQL Server, and the provider package is [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/).</span></span> <span data-ttu-id="6ad07-408">此套件包含在 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中，因此您不需要參考該套件。</span><span class="sxs-lookup"><span data-stu-id="6ad07-408">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to reference the package.</span></span>

<span data-ttu-id="6ad07-409">EF SQL Server 套件及其相依性 (`Microsoft.EntityFrameworkCore` 及 `Microsoft.EntityFrameworkCore.Relational`) 提供了 EF 的執行階段支援。</span><span class="sxs-lookup"><span data-stu-id="6ad07-409">The EF SQL Server package and its dependencies (`Microsoft.EntityFrameworkCore` and `Microsoft.EntityFrameworkCore.Relational`) provide runtime support for EF.</span></span> <span data-ttu-id="6ad07-410">您會在稍後的[移轉](migrations.md)教學課程中新增工具套件。</span><span class="sxs-lookup"><span data-stu-id="6ad07-410">You'll add a tooling package later, in the [Migrations](migrations.md) tutorial.</span></span>

<span data-ttu-id="6ad07-411">如需其他 Entity Framework Core 可用之資料庫提供者的資訊，請參閱[資料庫提供者](/ef/core/providers/)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-411">For information about other database providers that are available for Entity Framework Core, see [Database providers](/ef/core/providers/).</span></span>

## <a name="create-the-data-model"></a><span data-ttu-id="6ad07-412">建立資料模型</span><span class="sxs-lookup"><span data-stu-id="6ad07-412">Create the data model</span></span>

<span data-ttu-id="6ad07-413">接下來您會為 Contoso 大學應用程式建立實體類別。</span><span class="sxs-lookup"><span data-stu-id="6ad07-413">Next you'll create entity classes for the Contoso University application.</span></span> <span data-ttu-id="6ad07-414">您會從下列三個實體開始。</span><span class="sxs-lookup"><span data-stu-id="6ad07-414">You'll start with the following three entities.</span></span>

![Course-Enrollment-Student 資料模型圖表](intro/_static/data-model-diagram.png)

<span data-ttu-id="6ad07-416">在 `Student` 和 `Enrollment` 實體之間存在一對多關聯性，`Course` 與 `Enrollment` 實體之間也存在一對多關聯性。</span><span class="sxs-lookup"><span data-stu-id="6ad07-416">There's a one-to-many relationship between `Student` and `Enrollment` entities, and there's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="6ad07-417">換句話說，一位學生可以註冊並參加任何數目的課程，而一個課程也可以有任何數目的學生註冊。</span><span class="sxs-lookup"><span data-stu-id="6ad07-417">In other words, a student can be enrolled in any number of courses, and a course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="6ad07-418">在下節中，您會為這些實體建立各自的類別。</span><span class="sxs-lookup"><span data-stu-id="6ad07-418">In the following sections you'll create a class for each one of these entities.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="6ad07-419">Student 實體</span><span class="sxs-lookup"><span data-stu-id="6ad07-419">The Student entity</span></span>

![Student 實體圖表](intro/_static/student-entity.png)

<span data-ttu-id="6ad07-421">在 *Models* 資料夾中，建立一個名為 *Student.cs* 的類別檔案，然後使用下列程式碼取代範本程式碼。</span><span class="sxs-lookup"><span data-stu-id="6ad07-421">In the *Models* folder, create a class file named *Student.cs* and replace the template code with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="6ad07-422">`ID` 屬性會成為資料庫資料表中的主索引鍵資料行，並對應至這個類別。</span><span class="sxs-lookup"><span data-stu-id="6ad07-422">The `ID` property will become the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="6ad07-423">根據預設，Entity Framework 會將名為 `ID` 或 `classnameID` 的屬性解譯為主索引鍵。</span><span class="sxs-lookup"><span data-stu-id="6ad07-423">By default, the Entity Framework interprets a property that's named `ID` or `classnameID` as the primary key.</span></span>

<span data-ttu-id="6ad07-424">`Enrollments` 屬性為[導覽屬性](/ef/core/modeling/relationships)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-424">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="6ad07-425">導覽屬性會保留與此實體相關的其他實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-425">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="6ad07-426">在這個案例中，`Student entity` 的 `Enrollments` 屬性會保有與該 `Student` 實體相關的所有 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-426">In this case, the `Enrollments` property of a `Student entity` will hold all of the `Enrollment` entities that are related to that `Student` entity.</span></span> <span data-ttu-id="6ad07-427">換句話說，如果 `Student` 資料庫中的資料列有兩個相關 `Enrollment` 的資料列 (在其 StudentID 外鍵資料行中包含該學生的主鍵值的資料列) ，該 `Student` 實體的 `Enrollments` 導覽屬性將會包含這兩個 `Enrollment` 實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-427">In other words, if a `Student` row in the database has two related `Enrollment` rows (rows that contain that student's primary key value in their StudentID foreign key column), that `Student` entity's `Enrollments` navigation property will contain those two `Enrollment` entities.</span></span>

<span data-ttu-id="6ad07-428">若導覽屬性可保有多個實體 (例如在多對多或一對多關聯性中的情況)，其類型必須為一個清單，使得實體可以在該清單中新增、刪除或更新，例如 `ICollection<T>`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-428">If a navigation property can hold multiple entities (as in many-to-many or one-to-many relationships), its type must be a list in which entries can be added, deleted, and updated, such as `ICollection<T>`.</span></span> <span data-ttu-id="6ad07-429">您可以指定 `ICollection<T>` 或如 `List<T>` 或 `HashSet<T>` 等類型。</span><span class="sxs-lookup"><span data-stu-id="6ad07-429">You can specify `ICollection<T>` or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="6ad07-430">若您指定了 `ICollection<T>`，EF 會根據預設建立一個 `HashSet<T>` 集合。</span><span class="sxs-lookup"><span data-stu-id="6ad07-430">If you specify `ICollection<T>`, EF creates a `HashSet<T>` collection by default.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="6ad07-431">Enrollment 實體</span><span class="sxs-lookup"><span data-stu-id="6ad07-431">The Enrollment entity</span></span>

![Enrollment 實體圖表](intro/_static/enrollment-entity.png)

<span data-ttu-id="6ad07-433">在 *Models* 資料夾中，建立 *Enrollment.cs* ，然後使用下列程式碼取代現有的程式碼：</span><span class="sxs-lookup"><span data-stu-id="6ad07-433">In the *Models* folder, create *Enrollment.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="6ad07-434">`EnrollmentID` 屬性將為主索引鍵。此實體會使用 `classnameID` 模式，而非您在 `Student` 實體中所見到的自身 `ID`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-434">The `EnrollmentID` property will be the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself as you saw in the `Student` entity.</span></span> <span data-ttu-id="6ad07-435">通常您會選擇一個模式，然後在您整個資料模型中使用此模式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-435">Ordinarily you would choose one pattern and use it throughout your data model.</span></span> <span data-ttu-id="6ad07-436">在這裡，此變化僅作為向您展示使用不同模式之用。</span><span class="sxs-lookup"><span data-stu-id="6ad07-436">Here, the variation illustrates that you can use either pattern.</span></span> <span data-ttu-id="6ad07-437">在[稍後的教學課程](inheritance.md)中，您會了解使用沒有 classname 的識別碼可讓在資料模型中實作繼承變得更為簡單。</span><span class="sxs-lookup"><span data-stu-id="6ad07-437">In a [later tutorial](inheritance.md), you'll see how using ID without classname makes it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="6ad07-438">`Grade` 屬性為一個 `enum`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-438">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="6ad07-439">問號之後的 `Grade` 類型宣告表示 `Grade` 屬性可為 Null。</span><span class="sxs-lookup"><span data-stu-id="6ad07-439">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="6ad07-440">為 Null 的成績不同於成績為零：Null 表示成績未知或尚未指派。</span><span class="sxs-lookup"><span data-stu-id="6ad07-440">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="6ad07-441">`StudentID` 屬性是外部索引鍵，對應的導覽屬性是 `Student`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-441">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="6ad07-442">`Enrollment` 實體與一個 `Student` 實體關聯，因此屬性僅能保有單一 `Student` 實體 (不像您先前看到的 `Student.Enrollments` 導覽屬性可保有多個 `Enrollment` 實體)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-442">An `Enrollment` entity is associated with one `Student` entity, so the property can only hold a single `Student` entity (unlike the `Student.Enrollments` navigation property you saw earlier, which can hold multiple `Enrollment` entities).</span></span>

<span data-ttu-id="6ad07-443">`CourseID` 屬性是外部索引鍵，對應的導覽屬性是 `Course`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-443">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="6ad07-444">一個 `Enrollment` 實體與一個 `Course` 實體建立關聯。</span><span class="sxs-lookup"><span data-stu-id="6ad07-444">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="6ad07-445">Entity Framework 會將名為 `<navigation property name><primary key property name>` 的屬性解譯為外部索引鍵屬性 (例如 `Student` 導覽屬性的 `StudentID`，因為 `Student` 實體的主索引鍵為 `ID`)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-445">Entity Framework interprets a property as a foreign key property if it's named `<navigation property name><primary key property name>` (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="6ad07-446">外部索引鍵屬性也可以簡單的命名為 `<primary key property name>` (例如 `CourseID`，因為 `Course` 實體的主索引鍵為 `CourseID`)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-446">Foreign key properties can also be named simply `<primary key property name>` (for example, `CourseID` since the `Course` entity's primary key is `CourseID`).</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="6ad07-447">Course 實體</span><span class="sxs-lookup"><span data-stu-id="6ad07-447">The Course entity</span></span>

![Course 實體圖表](intro/_static/course-entity.png)

<span data-ttu-id="6ad07-449">在 *Models* 資料夾中，建立 *Course.cs* ，然後使用下列程式碼取代現有的程式碼：</span><span class="sxs-lookup"><span data-stu-id="6ad07-449">In the *Models* folder, create *Course.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="6ad07-450">`Enrollments` 屬性為導覽屬性。</span><span class="sxs-lookup"><span data-stu-id="6ad07-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="6ad07-451">`Course` 實體可以與任何數量的 `Enrollment` 實體相關。</span><span class="sxs-lookup"><span data-stu-id="6ad07-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="6ad07-452">我們會在此系列[稍後的教學課程](complex-data-model.md)中進一步討論 `DatabaseGenerated` 屬性。</span><span class="sxs-lookup"><span data-stu-id="6ad07-452">We'll say more about the `DatabaseGenerated` attribute in a [later tutorial](complex-data-model.md) in this series.</span></span> <span data-ttu-id="6ad07-453">基本上，此屬性可讓您為課程輸入主索引鍵，而非讓資料庫產生它。</span><span class="sxs-lookup"><span data-stu-id="6ad07-453">Basically, this attribute lets you enter the primary key for the course rather than having the database generate it.</span></span>

## <a name="create-the-database-context"></a><span data-ttu-id="6ad07-454">建立資料庫內容</span><span class="sxs-lookup"><span data-stu-id="6ad07-454">Create the database context</span></span>

<span data-ttu-id="6ad07-455">為指定資料模型協調 Entity Framework 功能的主要類別便是資料庫內容類別。</span><span class="sxs-lookup"><span data-stu-id="6ad07-455">The main class that coordinates Entity Framework functionality for a given data model is the database context class.</span></span> <span data-ttu-id="6ad07-456">若要建立此類別，您可以從 `Microsoft.EntityFrameworkCore.DbContext` 類別來衍生。</span><span class="sxs-lookup"><span data-stu-id="6ad07-456">You create this class by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span> <span data-ttu-id="6ad07-457">在您的程式碼中，您會指定資料模型中包含哪些實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-457">In your code you specify which entities are included in the data model.</span></span> <span data-ttu-id="6ad07-458">您也可以自訂某些 Entity Framework 行為。</span><span class="sxs-lookup"><span data-stu-id="6ad07-458">You can also customize certain Entity Framework behavior.</span></span> <span data-ttu-id="6ad07-459">在此專案中，類別命名為 `SchoolContext`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-459">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="6ad07-460">在專案資料夾中，建立名為 *Data* 的資料夾。</span><span class="sxs-lookup"><span data-stu-id="6ad07-460">In the project folder, create a folder named *Data*.</span></span>

<span data-ttu-id="6ad07-461">在 *Data* 資料夾中，建立名為 *SchoolContext.cs* 的新類別檔案，然後使用下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="6ad07-461">In the *Data* folder create a new class file named *SchoolContext.cs* , and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

<span data-ttu-id="6ad07-462">程式碼會為每一個實體集建立 `DbSet` 屬性。</span><span class="sxs-lookup"><span data-stu-id="6ad07-462">This code creates a `DbSet` property for each entity set.</span></span> <span data-ttu-id="6ad07-463">在 Entity Framework 詞彙中，實體集通常會對應至資料庫資料表，而實體則對應至資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="6ad07-463">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span>

<span data-ttu-id="6ad07-464">您可以省略 `DbSet<Enrollment>` 及 `DbSet<Course>` 陳述式，其結果也會是相同的。</span><span class="sxs-lookup"><span data-stu-id="6ad07-464">You could've omitted the `DbSet<Enrollment>` and `DbSet<Course>` statements and it would work the same.</span></span> <span data-ttu-id="6ad07-465">Entity Framework 會隱含它們，因為 `Student` 實體參考了 `Enrollment` 實體；而 `Enrollment` 實體參考了 `Course` 實體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-465">The Entity Framework would include them implicitly because the `Student` entity references the `Enrollment` entity and the `Enrollment` entity references the `Course` entity.</span></span>

<span data-ttu-id="6ad07-466">資料庫建立時，EF 會建立和 `DbSet` 屬性名稱相同的資料表。</span><span class="sxs-lookup"><span data-stu-id="6ad07-466">When the database is created, EF creates tables that have names the same as the `DbSet` property names.</span></span> <span data-ttu-id="6ad07-467">集合的屬性名稱通常都是複數 (Students 而非 Student)，但許多開發人員會為了資料表名稱究竟是否該是複數型態而爭論。</span><span class="sxs-lookup"><span data-stu-id="6ad07-467">Property names for collections are typically plural (Students rather than Student), but developers disagree about whether table names should be pluralized or not.</span></span> <span data-ttu-id="6ad07-468">在此系列教學課程中，您會藉由指定 DbContext 中的單數資料表名稱來覆寫預設行為。</span><span class="sxs-lookup"><span data-stu-id="6ad07-468">For these tutorials you'll override the default behavior by specifying singular table names in the DbContext.</span></span> <span data-ttu-id="6ad07-469">若要完成這項操作，請在最後一個 DbSet 屬性後方新增下列醒目提示程式碼。</span><span class="sxs-lookup"><span data-stu-id="6ad07-469">To do that, add the following highlighted code after the last DbSet property.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

<span data-ttu-id="6ad07-470">建置專案以檢查編譯器錯誤。</span><span class="sxs-lookup"><span data-stu-id="6ad07-470">Build the project as a check for compiler errors.</span></span>

## <a name="register-the-schoolcontext"></a><span data-ttu-id="6ad07-471">註冊 SchoolContext</span><span class="sxs-lookup"><span data-stu-id="6ad07-471">Register the SchoolContext</span></span>

<span data-ttu-id="6ad07-472">根據預設，ASP.NET Core 會實作[相依性插入](../../fundamentals/dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-472">ASP.NET Core implements [dependency injection](../../fundamentals/dependency-injection.md) by default.</span></span> <span data-ttu-id="6ad07-473">服務 (例如 EF 資料庫內容) 是在應用程式啟動期間使用相依性插入來註冊。</span><span class="sxs-lookup"><span data-stu-id="6ad07-473">Services (such as the EF database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="6ad07-474">接著，會透過建構函式參數，針對需要這些服務的元件 (例如 MVC 控制器) 來提供服務。</span><span class="sxs-lookup"><span data-stu-id="6ad07-474">Components that require these services (such as MVC controllers) are provided these services via constructor parameters.</span></span> <span data-ttu-id="6ad07-475">您會在此教學課程的稍後看到取得內容執行個體的控制器建構函式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-475">You'll see the controller constructor code that gets a context instance later in this tutorial.</span></span>

<span data-ttu-id="6ad07-476">若要將 `SchoolContext` 註冊為服務，請開啟 *Startup.cs* ，並將醒目標示的程式碼新增至 `ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="6ad07-476">To register `SchoolContext` as a service, open *Startup.cs* , and add the highlighted lines to the `ConfigureServices` method.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

<span data-ttu-id="6ad07-477">連接字串的名稱，會透過呼叫 `DbContextOptionsBuilder` 物件上的方法來傳遞至內容。</span><span class="sxs-lookup"><span data-stu-id="6ad07-477">The name of the connection string is passed in to the context by calling a method on a `DbContextOptionsBuilder` object.</span></span> <span data-ttu-id="6ad07-478">針對本機開發， [ASP.NET Core 設定系統](xref:fundamentals/configuration/index) 會從檔案讀取連接字串 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-478">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<span data-ttu-id="6ad07-479">為 `ContosoUniversity.Data` 和 `Microsoft.EntityFrameworkCore` 命名空間新增 `using` 陳述式，然後建置專案。</span><span class="sxs-lookup"><span data-stu-id="6ad07-479">Add `using` statements for `ContosoUniversity.Data` and `Microsoft.EntityFrameworkCore` namespaces, and then build the project.</span></span>

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

<span data-ttu-id="6ad07-480">開啟檔案 *appsettings.json* ，並加入連接字串，如下列範例所示。</span><span class="sxs-lookup"><span data-stu-id="6ad07-480">Open the *appsettings.json* file and add a connection string as shown in the following example.</span></span>

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a><span data-ttu-id="6ad07-481">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="6ad07-481">SQL Server Express LocalDB</span></span>

<span data-ttu-id="6ad07-482">連接字串會指定 SQL Server LocalDB 資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-482">The connection string specifies a SQL Server LocalDB database.</span></span> <span data-ttu-id="6ad07-483">LocalDB 是輕量版的 SQL Server Express Database Engine，旨在用於應用程式開發，而不是生產用途。</span><span class="sxs-lookup"><span data-stu-id="6ad07-483">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for application development, not production use.</span></span> <span data-ttu-id="6ad07-484">LocalDB 會依需求啟動，並以使用者模式執行，因此沒有複雜的組態。</span><span class="sxs-lookup"><span data-stu-id="6ad07-484">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="6ad07-485">LocalDB 預設會在 `C:/Users/<user>` 目錄中建立 *.mdf* 資料庫檔案。</span><span class="sxs-lookup"><span data-stu-id="6ad07-485">By default, LocalDB creates *.mdf* database files in the `C:/Users/<user>` directory.</span></span>

## <a name="initialize-db-with-test-data"></a><span data-ttu-id="6ad07-486">使用測試資料將 DB 初始化</span><span class="sxs-lookup"><span data-stu-id="6ad07-486">Initialize DB with test data</span></span>

<span data-ttu-id="6ad07-487">Entity Framework 會為您建立空白資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-487">The Entity Framework will create an empty database for you.</span></span> <span data-ttu-id="6ad07-488">在本節中，您會撰寫一個方法，該方法會在資料庫建立之後呼叫，以將測試資料填入資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-488">In this section, you write a method that's called after the database is created in order to populate it with test data.</span></span>

<span data-ttu-id="6ad07-489">在此您將使用 `EnsureCreated` 方法來自動建立資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-489">Here you'll use the `EnsureCreated` method to automatically create the database.</span></span> <span data-ttu-id="6ad07-490">在[稍後的教學課程](migrations.md)中，您將會了解到如何使用 Code First 移轉變更資料庫結構描述，而非卸除並重新建立資料庫，來處理模型的變更。</span><span class="sxs-lookup"><span data-stu-id="6ad07-490">In a [later tutorial](migrations.md) you'll see how to handle model changes by using Code First Migrations to change the database schema instead of dropping and re-creating the database.</span></span>

<span data-ttu-id="6ad07-491">在 *Data* 資料夾中，建立一個名為 *DbInitializer.cs* 的新類別檔案，使用下列程式碼取代範本程式碼。這些程式碼會在需要的時候建立資料庫，並將測試資料載入至新的資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-491">In the *Data* folder, create a new class file named *DbInitializer.cs* and replace the template code with the following code, which causes a database to be created when needed and loads test data into the new database.</span></span>

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="6ad07-492">程式碼會檢查資料庫中是否有任何學生。若沒有的話，它便會假設資料庫是新的資料庫，因此需要植入測試資料。</span><span class="sxs-lookup"><span data-stu-id="6ad07-492">The code checks if there are any students in the database, and if not, it assumes the database is new and needs to be seeded with test data.</span></span> <span data-ttu-id="6ad07-493">會將測試資料載入至陣列而非 `List<T>` 集合，以最佳化效能。</span><span class="sxs-lookup"><span data-stu-id="6ad07-493">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="6ad07-494">在 *Program.cs* 中，修改 `Main` 方法來在應用程式啟動期間執行下列動作：</span><span class="sxs-lookup"><span data-stu-id="6ad07-494">In *Program.cs* , modify the `Main` method to do the following on application startup:</span></span>

* <span data-ttu-id="6ad07-495">從相依性插入容器中取得資料庫內容執行個體。</span><span class="sxs-lookup"><span data-stu-id="6ad07-495">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="6ad07-496">呼叫種子方法，並將其傳遞給內容。</span><span class="sxs-lookup"><span data-stu-id="6ad07-496">Call the seed method, passing to it the context.</span></span>
* <span data-ttu-id="6ad07-497">種子方法完成時處理內容。</span><span class="sxs-lookup"><span data-stu-id="6ad07-497">Dispose the context when the seed method is done.</span></span>

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="6ad07-498">當您第一次執行應用程式時，將會建立資料庫並植入測試資料。</span><span class="sxs-lookup"><span data-stu-id="6ad07-498">The first time you run the application, the database will be created and seeded with test data.</span></span> <span data-ttu-id="6ad07-499">每當您變更資料模型時：</span><span class="sxs-lookup"><span data-stu-id="6ad07-499">Whenever you change the data model:</span></span>

 * <span data-ttu-id="6ad07-500">刪除資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-500">Delete the database.</span></span>
 * <span data-ttu-id="6ad07-501">更新種子方法，並以相同方式開始使用新的資料庫再次。</span><span class="sxs-lookup"><span data-stu-id="6ad07-501">Update the seed method, and start afresh with a new database the same way.</span></span>
 
<span data-ttu-id="6ad07-502">在稍後的教學課程中，您會了解如何在資料模型變更時修改資料庫，而不需要刪除和重新建立它。</span><span class="sxs-lookup"><span data-stu-id="6ad07-502">In later tutorials, you'll see how to modify the database when the data model changes, without deleting and re-creating it.</span></span>

## <a name="create-controller-and-views"></a><span data-ttu-id="6ad07-503">建立控制器和檢視</span><span class="sxs-lookup"><span data-stu-id="6ad07-503">Create controller and views</span></span>

<span data-ttu-id="6ad07-504">在本節中，Visual Studio 中的樣板引擎會用來新增 MVC 控制器和將使用 EF 來查詢和儲存資料的視圖。</span><span class="sxs-lookup"><span data-stu-id="6ad07-504">In this section, the scaffolding engine in Visual Studio is used to add an MVC controller and views that will use EF to query and save data.</span></span>

<span data-ttu-id="6ad07-505">自動建立 CRUD 動作方法和檢視稱為 Scaffolding。</span><span class="sxs-lookup"><span data-stu-id="6ad07-505">The automatic creation of CRUD action methods and views is known as scaffolding.</span></span> <span data-ttu-id="6ad07-506">Scaffolding 與產生程式碼不同。Scaffold 程式碼是一個開始點，使得您可以修改它以符合您的需求，然而您通常不會去修改產生的程式碼。</span><span class="sxs-lookup"><span data-stu-id="6ad07-506">Scaffolding differs from code generation in that the scaffolded code is a starting point that you can modify to suit your own requirements, whereas you typically don't modify generated code.</span></span> <span data-ttu-id="6ad07-507">當您需要自訂產生的程式碼時，您會使用部分類別，或者您會在事務變更時重新產生程式碼。</span><span class="sxs-lookup"><span data-stu-id="6ad07-507">When you need to customize generated code, you use partial classes or you regenerate the code when things change.</span></span>

* <span data-ttu-id="6ad07-508">在方案總管中的 **Controllers** 資料夾上以滑鼠右鍵按一下，然後選取 [新增] > [新增 Scaffold 項目]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-508">Right-click the **Controllers** folder in **Solution Explorer** and select **Add > New Scaffolded Item**.</span></span>
* <span data-ttu-id="6ad07-509">在 [新增 Scaffold] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="6ad07-509">In the **Add Scaffold** dialog box:</span></span>
  * <span data-ttu-id="6ad07-510">選取 [使用 Entity Framework 執行檢視的 MVC 控制器]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-510">Select **MVC controller with views, using Entity Framework**.</span></span>
  * <span data-ttu-id="6ad07-511">按一下 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="6ad07-511">Click **Add**.</span></span> <span data-ttu-id="6ad07-512">[ **Entity Framework 使用 Scaffold 新增 MVC 控制器** ] 對話方塊隨即出現： ![ Student](intro/_static/scaffold-student2.png)</span><span class="sxs-lookup"><span data-stu-id="6ad07-512">The **Add MVC Controller with views, using Entity Framework** dialog box appears: ![Scaffold Student](intro/_static/scaffold-student2.png)</span></span>
  * <span data-ttu-id="6ad07-513">在 [模型類別] 中，選取 [Student]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-513">In **Model class** select **Student**.</span></span>
  * <span data-ttu-id="6ad07-514">在 [資料內容類別] 中，選取 [SchoolContext]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-514">In **Data context class** select **SchoolContext**.</span></span>
  * <span data-ttu-id="6ad07-515">接受預設的 **StudentsController** 作為名稱。</span><span class="sxs-lookup"><span data-stu-id="6ad07-515">Accept the default **StudentsController** as the name.</span></span>
  * <span data-ttu-id="6ad07-516">按一下 [新增]  。</span><span class="sxs-lookup"><span data-stu-id="6ad07-516">Click **Add**.</span></span>

<span data-ttu-id="6ad07-517">Visual Studio 的樣板引擎會建立一個 *StudentsController.cs* 檔案和一組與控制器 (的 *cshtml* 檔案) 的視圖。</span><span class="sxs-lookup"><span data-stu-id="6ad07-517">The Visual Studio scaffolding engine creates a *StudentsController.cs* file and a set of views ( *.cshtml* files) that work with the controller.</span></span>

<span data-ttu-id="6ad07-518">請注意，控制器會採用作為函式 `SchoolContext` 參數。</span><span class="sxs-lookup"><span data-stu-id="6ad07-518">Notice the controller takes a `SchoolContext` as a constructor parameter.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

<span data-ttu-id="6ad07-519">ASP.NET Core 相依性插入會負責傳遞 `SchoolContext` 的執行個體給控制器。</span><span class="sxs-lookup"><span data-stu-id="6ad07-519">ASP.NET Core dependency injection takes care of passing an instance of `SchoolContext` into the controller.</span></span> <span data-ttu-id="6ad07-520">這是在 *Startup.cs* 檔案中設定的。</span><span class="sxs-lookup"><span data-stu-id="6ad07-520">That was configured  in the *Startup.cs* file.</span></span>

<span data-ttu-id="6ad07-521">控制器含有一個 `Index` 動作方法，該方法會顯示資料庫中的所有學生。</span><span class="sxs-lookup"><span data-stu-id="6ad07-521">The controller contains an `Index` action method, which displays all students in the database.</span></span> <span data-ttu-id="6ad07-522">方法會藉由讀取資料庫內容執行個體的 `Students` 屬性，來從 Students 實體集中取得學生的清單：</span><span class="sxs-lookup"><span data-stu-id="6ad07-522">The method gets a list of students from the Students entity set by reading the `Students` property of the database context instance:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

<span data-ttu-id="6ad07-523">您稍後會在本教學課程中瞭解此程式碼中的非同步程式設計項目。</span><span class="sxs-lookup"><span data-stu-id="6ad07-523">You learn about the asynchronous programming elements in this code later in the tutorial.</span></span>

<span data-ttu-id="6ad07-524">*Views/Students/Index.cshtml* 檢視會在一個資料表中顯示這份清單：</span><span class="sxs-lookup"><span data-stu-id="6ad07-524">The *Views/Students/Index.cshtml* view displays this list in a table:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

<span data-ttu-id="6ad07-525">按 CTRL+F5 來執行專案，或從功能表選擇 [偵錯] > [啟動但不偵錯]。</span><span class="sxs-lookup"><span data-stu-id="6ad07-525">Press CTRL+F5 to run the project or choose **Debug > Start Without Debugging** from the menu.</span></span>

<span data-ttu-id="6ad07-526">按一下 [Students] 索引標籤來查看 `DbInitializer.Initialize` 方法插入的測試資料。</span><span class="sxs-lookup"><span data-stu-id="6ad07-526">Click the Students tab to see the test data that the `DbInitializer.Initialize` method inserted.</span></span> <span data-ttu-id="6ad07-527">取決於您瀏覽器視窗的寬度，您可能會在頁面的頂端看到 `Students` 索引標籤連結，或是按一下位於右上角的導覽圖示來查看連結。</span><span class="sxs-lookup"><span data-stu-id="6ad07-527">Depending on how narrow your browser window is, you'll see the `Students` tab link at the top of the page or you'll have to click the navigation icon in the upper right corner to see the link.</span></span>

![Contoso 大學首頁 (窄)](intro/_static/home-page-narrow.png)

![Students [索引] 頁面](intro/_static/students-index.png)

## <a name="view-the-database"></a><span data-ttu-id="6ad07-530">檢視資料庫</span><span class="sxs-lookup"><span data-stu-id="6ad07-530">View the database</span></span>

<span data-ttu-id="6ad07-531">當您啟動應用程式時，`DbInitializer.Initialize` 方法會呼叫 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-531">When you started the application, the `DbInitializer.Initialize` method calls `EnsureCreated`.</span></span> <span data-ttu-id="6ad07-532">EF 看到不存在任何資料庫，於是便建立了一個資料庫，接著 `Initialize` 方法程式碼的剩餘部分便會將資料填入資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-532">EF saw that there was no database and so it created one, then the remainder of the `Initialize` method code populated the database with data.</span></span> <span data-ttu-id="6ad07-533">您可以使用 [SQL Server 物件總管 (SSOX) 來在 Visual Studio 中檢視資料庫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-533">You can use **SQL Server Object Explorer** (SSOX) to view the database in Visual Studio.</span></span>

<span data-ttu-id="6ad07-534">關閉瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="6ad07-534">Close the browser.</span></span>

<span data-ttu-id="6ad07-535">若 SSOX 視窗尚未開啟，請從 Visual Studio 中的 [檢視] 功能表選取它。</span><span class="sxs-lookup"><span data-stu-id="6ad07-535">If the SSOX window isn't already open, select it from the **View** menu in Visual Studio.</span></span>

<span data-ttu-id="6ad07-536">在 [SSOX] 中，按一下 **(localdb) \mssqllocaldb > 資料庫** ]，然後在檔案中的連接字串中，按一下資料庫名稱的專案 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-536">In SSOX, click **(localdb)\MSSQLLocalDB > Databases** , and then click the entry for the database name that's in the connection string in the *appsettings.json* file.</span></span>

<span data-ttu-id="6ad07-537">展開 [ **資料表]** 節點，查看資料庫中的資料表。</span><span class="sxs-lookup"><span data-stu-id="6ad07-537">Expand the **Tables** node to see the tables in the database.</span></span>

![SSOX 中的資料表](intro/_static/ssox-tables.png)

<span data-ttu-id="6ad07-539">以滑鼠右鍵按一下 **Students** 資料表，並按一下 [檢視資料] 查看建立的資料行及插入資料表中的資料列。</span><span class="sxs-lookup"><span data-stu-id="6ad07-539">Right-click the **Student** table and click **View Data** to see the columns that were created and the rows that were inserted into the table.</span></span>

![SSOX 中的 Student 資料表](intro/_static/ssox-student-table.png)

<span data-ttu-id="6ad07-541">*.mdf* 和 *.ldf* 資料庫檔案位於 *C:\Users\\\<username>* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="6ad07-541">The *.mdf* and *.ldf* database files are in the *C:\Users\\\<username>* folder.</span></span>

<span data-ttu-id="6ad07-542">因為您在應用程式啟動時執行的初始設定式方法中呼叫了 `EnsureCreated`，您現在可以對 `Student` 類別進行變更、刪除資料庫、重新執行應用程式，資料庫會自動重新建立以符合您所作出的變更。</span><span class="sxs-lookup"><span data-stu-id="6ad07-542">Because you're calling `EnsureCreated` in the initializer method that runs on app start, you could now make a change to the `Student` class, delete the database, run the application again, and the database would automatically be re-created to match your change.</span></span> <span data-ttu-id="6ad07-543">例如，若您將一個 `EmailAddress` 屬性新增到 `Student` 類別，您便會在重新建立的資料表中看到新的 `EmailAddress` 資料行。</span><span class="sxs-lookup"><span data-stu-id="6ad07-543">For example, if you add an `EmailAddress` property to the `Student` class, you'll see a new `EmailAddress` column in the re-created table.</span></span>

## <a name="conventions"></a><span data-ttu-id="6ad07-544">慣例</span><span class="sxs-lookup"><span data-stu-id="6ad07-544">Conventions</span></span>

<span data-ttu-id="6ad07-545">為了讓 Entity Framework 能夠建立一個完整資料庫，您所需要撰寫的程式碼非常少，多虧了慣例的使用及 Entity Framework 所做出的假設。</span><span class="sxs-lookup"><span data-stu-id="6ad07-545">The amount of code you had to write in order for the Entity Framework to be able to create a complete database for you is minimal because of the use of conventions, or assumptions that the Entity Framework makes.</span></span>

* <span data-ttu-id="6ad07-546">`DbSet` 屬性的名稱會用於資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="6ad07-546">The names of `DbSet` properties are used as table names.</span></span> <span data-ttu-id="6ad07-547">針對 `DbSet` 屬性並未參考的實體，實體類別名稱會用於資料表名稱。</span><span class="sxs-lookup"><span data-stu-id="6ad07-547">For entities not referenced by a `DbSet` property, entity class names are used as table names.</span></span>
* <span data-ttu-id="6ad07-548">實體屬性名稱會用於資料行名稱。</span><span class="sxs-lookup"><span data-stu-id="6ad07-548">Entity property names are used for column names.</span></span>
* <span data-ttu-id="6ad07-549">命名為 ID 或 classnameID 的實體屬性，會辨識為主索引鍵屬性。</span><span class="sxs-lookup"><span data-stu-id="6ad07-549">Entity properties that are named ID or classnameID are recognized as primary key properties.</span></span>
* <span data-ttu-id="6ad07-550">如果屬性名為 (的外鍵屬性，則會將其視為外鍵屬性 *\<navigation property name>\<primary key property name>* ，例如， `StudentID` `Student` 因為 `Student` 實體的主鍵是 `ID`) 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-550">A property is interpreted as a foreign key property if it's named *\<navigation property name>\<primary key property name>* (for example, `StudentID` for the `Student` navigation property since the `Student` entity's primary key is `ID`).</span></span> <span data-ttu-id="6ad07-551">外鍵屬性也可以簡單命名 *\<primary key property name>* (例如， `EnrollmentID` 因為 `Enrollment` 實體的主鍵是 `EnrollmentID`) 。</span><span class="sxs-lookup"><span data-stu-id="6ad07-551">Foreign key properties can also be named simply *\<primary key property name>* (for example, `EnrollmentID` since the `Enrollment` entity's primary key is `EnrollmentID`).</span></span>

<span data-ttu-id="6ad07-552">慣例行為可以被覆寫。</span><span class="sxs-lookup"><span data-stu-id="6ad07-552">Conventional behavior can be overridden.</span></span> <span data-ttu-id="6ad07-553">例如，您可以明確指定資料表名稱，如稍早在本教學課程中您所見到的。</span><span class="sxs-lookup"><span data-stu-id="6ad07-553">For example, you can explicitly specify table names, as you saw earlier in this tutorial.</span></span> <span data-ttu-id="6ad07-554">您可以設定資料行名稱以及將任何屬性設為主索引鍵或外部索引鍵，如同您在本系列[稍後的教學課程](complex-data-model.md)中所見。</span><span class="sxs-lookup"><span data-stu-id="6ad07-554">And you can set column names and set any property as primary key or foreign key, as you'll see in a [later tutorial](complex-data-model.md) in this series.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="6ad07-555">非同步程式碼</span><span class="sxs-lookup"><span data-stu-id="6ad07-555">Asynchronous code</span></span>

<span data-ttu-id="6ad07-556">非同步程式設計是預設的 ASP.NET Core 和 EF Core 模式。</span><span class="sxs-lookup"><span data-stu-id="6ad07-556">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="6ad07-557">網頁伺服器的可用執行緒數量有限，而且在高負載情況下，可能會使用所有可用的執行緒。</span><span class="sxs-lookup"><span data-stu-id="6ad07-557">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="6ad07-558">發生此情況時，伺服器將無法處理新的要求，直到執行緒空出來。</span><span class="sxs-lookup"><span data-stu-id="6ad07-558">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="6ad07-559">使用同步程式碼，許多執行緒可能在實際上並未執行任何工作時受到占用，原因是在等候 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="6ad07-559">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="6ad07-560">使用非同步程式碼，處理程序在等候 I/O 完成時，其執行緒將會空出來以讓伺服器處理其他要求。</span><span class="sxs-lookup"><span data-stu-id="6ad07-560">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="6ad07-561">因此，非同步程式碼可讓伺服器資源更有效率地使用，而且伺服器可處理更多流量而不會造成延遲。</span><span class="sxs-lookup"><span data-stu-id="6ad07-561">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="6ad07-562">非同步程式碼雖然的確會在執行階段造成少量的負荷，但在低流量情況下，對效能的衝擊非常微小；在高流量情況下，潛在的效能改善則相當大。</span><span class="sxs-lookup"><span data-stu-id="6ad07-562">Asynchronous code does introduce a small amount of overhead at run time, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="6ad07-563">在下列程式碼中，`async` 關鍵字、`Task<T>` 傳回值、`await` 關鍵字和 `ToListAsync` 方法使程式碼以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="6ad07-563">In the following code, the `async` keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="6ad07-564">`async` 關鍵字會告訴編譯器為方法本體的一部分產生回呼，並自動建立傳回的 `Task<IActionResult>` 物件。</span><span class="sxs-lookup"><span data-stu-id="6ad07-564">The `async` keyword tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<IActionResult>` object that's returned.</span></span>
* <span data-ttu-id="6ad07-565">傳回類型 `Task<IActionResult>` 代表了正在進行的工作，其結果為 `IActionResult` 類型。</span><span class="sxs-lookup"><span data-stu-id="6ad07-565">The return type `Task<IActionResult>` represents ongoing work with a result of type `IActionResult`.</span></span>
* <span data-ttu-id="6ad07-566">`await` 關鍵字會使編譯器將方法分割為兩部分。</span><span class="sxs-lookup"><span data-stu-id="6ad07-566">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="6ad07-567">第一個部分會與非同步啟動的作業一同結束。</span><span class="sxs-lookup"><span data-stu-id="6ad07-567">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="6ad07-568">第二個部分則會放入作業完成時呼叫的回呼方法中。</span><span class="sxs-lookup"><span data-stu-id="6ad07-568">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="6ad07-569">`ToListAsync` 是 `ToList` 擴充方法的非同步版本。</span><span class="sxs-lookup"><span data-stu-id="6ad07-569">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="6ad07-570">當您在撰寫使用 Entity Framework 的非同步程式碼時，請注意下列事項：</span><span class="sxs-lookup"><span data-stu-id="6ad07-570">Some things to be aware of when you are writing asynchronous code that uses the Entity Framework:</span></span>

* <span data-ttu-id="6ad07-571">只有讓查詢或命令傳送至資料庫的陳述式，會以非同步方式執行。</span><span class="sxs-lookup"><span data-stu-id="6ad07-571">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="6ad07-572">其中包含，例如 `ToListAsync`、`SingleOrDefaultAsync`，以及 `SaveChangesAsync`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-572">That includes, for example, `ToListAsync`, `SingleOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="6ad07-573">其中不包含，例如：僅變更 `IQueryable` 的陳述式，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。</span><span class="sxs-lookup"><span data-stu-id="6ad07-573">It doesn't include, for example, statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="6ad07-574">EF 內容在執行緒中並不安全：不要嘗試執行多個平行作業。</span><span class="sxs-lookup"><span data-stu-id="6ad07-574">An EF context isn't thread safe: don't try to do multiple operations in parallel.</span></span> <span data-ttu-id="6ad07-575">當您呼叫任何 async EF 方法時，請一律使用 `await` 關鍵字。</span><span class="sxs-lookup"><span data-stu-id="6ad07-575">When you call any async EF method, always use the `await` keyword.</span></span>
* <span data-ttu-id="6ad07-576">若您想要充分利用非同步程式碼帶來的效能優點，請確保任何您正在使用的程式庫 (例如分頁) 也使用了非同步 (若它們有呼叫任何可能會傳送查詢到資料庫的 Entity Framework 方法的話)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-576">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

<span data-ttu-id="6ad07-577">如需在 .NET 中非同步程式設計的詳細資訊，請參閱[非同步總覽](/dotnet/articles/standard/async)。</span><span class="sxs-lookup"><span data-stu-id="6ad07-577">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/articles/standard/async).</span></span>

## <a name="next-steps"></a><span data-ttu-id="6ad07-578">後續步驟</span><span class="sxs-lookup"><span data-stu-id="6ad07-578">Next steps</span></span>

<span data-ttu-id="6ad07-579">若要了解如何執行基本的 CRUD (建立、讀取、更新、刪除) 作業，請前往下一個教學課程。</span><span class="sxs-lookup"><span data-stu-id="6ad07-579">Advance to the next tutorial to learn how to perform basic CRUD (create, read, update, delete) operations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="6ad07-580">實作基本的 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="6ad07-580">Implement basic CRUD functionality</span></span>](crud.md)

::: moniker-end
