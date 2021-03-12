---
title: 教學課程：執行繼承-使用 EF Core ASP.NET MVC
description: 本教學課程將說明如何在 ASP.NET Core 應用程式中使用 Entity Framework Core，以實作資料模型中的繼承。
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
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
uid: data/ef-mvc/inheritance
ms.openlocfilehash: 299e00b223d287c4e2ece3d1e250581e2a7565e5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586367"
---
# <a name="tutorial-implement-inheritance---aspnet-mvc-with-ef-core"></a><span data-ttu-id="aea48-103">教學課程：執行繼承-使用 EF Core ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="aea48-103">Tutorial: Implement inheritance - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="aea48-104">在上一個教學課程中，您已處理並行存取例外狀況。</span><span class="sxs-lookup"><span data-stu-id="aea48-104">In the previous tutorial, you handled concurrency exceptions.</span></span> <span data-ttu-id="aea48-105">本教學課程將示範如何在資料模型中實作繼承。</span><span class="sxs-lookup"><span data-stu-id="aea48-105">This tutorial will show you how to implement inheritance in the data model.</span></span>

<span data-ttu-id="aea48-106">在物件導向程式設計中，您可以使用繼承，以便重複使用程式碼。</span><span class="sxs-lookup"><span data-stu-id="aea48-106">In object-oriented programming, you can use inheritance to facilitate code reuse.</span></span> <span data-ttu-id="aea48-107">在本教學課程中，您將變更 `Instructor` 和 `Student` 類別，讓它們衍生自 `Person` 基底類別，而此基底類別包含講師和學生通用的屬性，例如 `LastName`。</span><span class="sxs-lookup"><span data-stu-id="aea48-107">In this tutorial, you'll change the `Instructor` and `Student` classes so that they derive from a `Person` base class which contains properties such as `LastName` that are common to both instructors and students.</span></span> <span data-ttu-id="aea48-108">您不會新增或變更任何網頁，但是您將變更一些程式碼，這些變更將會自動反映在資料庫中。</span><span class="sxs-lookup"><span data-stu-id="aea48-108">You won't add or change any web pages, but you'll change some of the code and those changes will be automatically reflected in the database.</span></span>

<span data-ttu-id="aea48-109">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="aea48-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aea48-110">將繼承對應至資料庫</span><span class="sxs-lookup"><span data-stu-id="aea48-110">Map inheritance to database</span></span>
> * <span data-ttu-id="aea48-111">建立 Person 類別</span><span class="sxs-lookup"><span data-stu-id="aea48-111">Create the Person class</span></span>
> * <span data-ttu-id="aea48-112">更新 Instructor 和 Student</span><span class="sxs-lookup"><span data-stu-id="aea48-112">Update Instructor and Student</span></span>
> * <span data-ttu-id="aea48-113">將 Person 新增至模型</span><span class="sxs-lookup"><span data-stu-id="aea48-113">Add Person to the model</span></span>
> * <span data-ttu-id="aea48-114">建立及更新移轉</span><span class="sxs-lookup"><span data-stu-id="aea48-114">Create and update migrations</span></span>
> * <span data-ttu-id="aea48-115">測試實作</span><span class="sxs-lookup"><span data-stu-id="aea48-115">Test the implementation</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aea48-116">必要條件</span><span class="sxs-lookup"><span data-stu-id="aea48-116">Prerequisites</span></span>

* [<span data-ttu-id="aea48-117">處理並行</span><span class="sxs-lookup"><span data-stu-id="aea48-117">Handle Concurrency</span></span>](concurrency.md)

## <a name="map-inheritance-to-database"></a><span data-ttu-id="aea48-118">將繼承對應至資料庫</span><span class="sxs-lookup"><span data-stu-id="aea48-118">Map inheritance to database</span></span>

<span data-ttu-id="aea48-119">School 資料模型中的 `Instructor` 和 `Student` 類別有數個完全相同的屬性：</span><span class="sxs-lookup"><span data-stu-id="aea48-119">The `Instructor` and `Student` classes in the School data model have several properties that are identical:</span></span>

![Student 和 Instructor 類別。](inheritance/_static/no-inheritance.png)

<span data-ttu-id="aea48-121">假設您想要針對 `Instructor` 和 `Student` 實體所共用的屬性消除多餘的程式碼。</span><span class="sxs-lookup"><span data-stu-id="aea48-121">Suppose you want to eliminate the redundant code for the properties that are shared by the `Instructor` and `Student` entities.</span></span> <span data-ttu-id="aea48-122">或者，您想要撰寫服務，以便用來格式化名稱，而無需在意名稱是來自講師還是學生。</span><span class="sxs-lookup"><span data-stu-id="aea48-122">Or you want to write a service that can format names without caring whether the name came from an instructor or a student.</span></span> <span data-ttu-id="aea48-123">您可以建立只包含這些共用屬性的 `Person` 基底類別，然後使 `Instructor` 和 `Student` 類別繼承自該基底類別，如下圖所示：</span><span class="sxs-lookup"><span data-stu-id="aea48-123">You could create a `Person` base class that contains only those shared properties, then make the `Instructor` and `Student` classes inherit from that base class, as shown in the following illustration:</span></span>

![衍生自 Person 類別的 Student 和 Instructor 類別](inheritance/_static/inheritance.png)

<span data-ttu-id="aea48-125">有幾種方式可以在資料庫中表示此繼承結構。</span><span class="sxs-lookup"><span data-stu-id="aea48-125">There are several ways this inheritance structure could be represented in the database.</span></span> <span data-ttu-id="aea48-126">您擁有的 Person 資料表可能在單一資料表中同時包含學生和講師的資訊。</span><span class="sxs-lookup"><span data-stu-id="aea48-126">You could have a Person table that includes information about both students and instructors in a single table.</span></span> <span data-ttu-id="aea48-127">有些資料行僅適用於講師 (HireDate)，有些只適用於學生 (EnrollmentDate)，有些則兩者通用 (LastName、FirstName)。</span><span class="sxs-lookup"><span data-stu-id="aea48-127">Some of the columns could apply only to instructors (HireDate), some only to students (EnrollmentDate), some to both (LastName, FirstName).</span></span> <span data-ttu-id="aea48-128">一般而言，您必須有指出每個資料列代表哪種類型的鑑別子資料行。</span><span class="sxs-lookup"><span data-stu-id="aea48-128">Typically, you'd have a discriminator column to indicate which type each row represents.</span></span> <span data-ttu-id="aea48-129">例如，鑑別子資料行的 "Instructor" 代表講師，而 "Student" 代表學生。</span><span class="sxs-lookup"><span data-stu-id="aea48-129">For example, the discriminator column might have "Instructor" for instructors and "Student" for students.</span></span>

![單表 (Table-per-hierarchy) 範例](inheritance/_static/tph.png)

<span data-ttu-id="aea48-131">從單一資料庫資料表產生實體繼承結構的這種模式稱為單表 (TPH) 繼承。</span><span class="sxs-lookup"><span data-stu-id="aea48-131">This pattern of generating an entity inheritance structure from a single database table is called table-per-hierarchy (TPH) inheritance.</span></span>

<span data-ttu-id="aea48-132">替代方法是讓資料庫看起來更像繼承結構。</span><span class="sxs-lookup"><span data-stu-id="aea48-132">An alternative is to make the database look more like the inheritance structure.</span></span> <span data-ttu-id="aea48-133">比方說，您可以只在 Person 資料表中包含名稱欄位，而在個別的 Instructor 和 Student 資料表中包含日期欄位。</span><span class="sxs-lookup"><span data-stu-id="aea48-133">For example, you could have only the name fields in the Person table and have separate Instructor and Student tables with the date fields.</span></span>

> [!WARNING]
> <span data-ttu-id="aea48-134">EF Core 3.x 不支援 (TPT) 的每個類型資料表，但已在 [Ef core 5.0](/ef/core/what-is-new/ef-core-5.0/plan)中執行。</span><span class="sxs-lookup"><span data-stu-id="aea48-134">Table Per Type (TPT) is not supported by EF Core 3.x, however it is has been implemented in [EF Core 5.0](/ef/core/what-is-new/ef-core-5.0/plan).</span></span>

![一類一表 (Table-Per-Type) 繼承](inheritance/_static/tpt.png)

<span data-ttu-id="aea48-136">針對每個實體類別建立資料庫資料表的這種模式稱為一類一表 (TPT) 繼承。</span><span class="sxs-lookup"><span data-stu-id="aea48-136">This pattern of making a database table for each entity class is called table per type (TPT) inheritance.</span></span>

<span data-ttu-id="aea48-137">還有另一個選項是將所有的非抽象類型對應至個別資料表。</span><span class="sxs-lookup"><span data-stu-id="aea48-137">Yet another option is to map all non-abstract types to individual tables.</span></span> <span data-ttu-id="aea48-138">類別的所有屬性 (包括繼承的屬性) 都會對應至對應資料表的資料行。</span><span class="sxs-lookup"><span data-stu-id="aea48-138">All properties of a class, including inherited properties, map to columns of the corresponding table.</span></span> <span data-ttu-id="aea48-139">這個模式稱為一實體類一表 (TPC) 繼承。</span><span class="sxs-lookup"><span data-stu-id="aea48-139">This pattern is called Table-per-Concrete Class (TPC) inheritance.</span></span> <span data-ttu-id="aea48-140">如果您已針對 Person、Student 和 Instructor 類別實作 TPC 繼承 (如上所示)，Student 和 Instructor 資料表在實作繼承之後，看起來與實作之前一樣。</span><span class="sxs-lookup"><span data-stu-id="aea48-140">If you implemented TPC inheritance for the Person, Student, and Instructor classes as shown earlier, the Student and Instructor tables would look no different after implementing inheritance than they did before.</span></span>

<span data-ttu-id="aea48-141">比起 TPT 繼承模式，TPC 和 TPH 繼承模式通常會提供更好的效能，因為 TPT 模式可能會導致複雜的聯結查詢。</span><span class="sxs-lookup"><span data-stu-id="aea48-141">TPC and TPH inheritance patterns generally deliver better performance than TPT inheritance patterns, because TPT patterns can result in complex join queries.</span></span>

<span data-ttu-id="aea48-142">本教學課程將示範如何實作 TPH 繼承。</span><span class="sxs-lookup"><span data-stu-id="aea48-142">This tutorial demonstrates how to implement TPH inheritance.</span></span> <span data-ttu-id="aea48-143">TPH 是 Entity Framework Core 支援的唯一繼承模式。</span><span class="sxs-lookup"><span data-stu-id="aea48-143">TPH is the only inheritance pattern that the Entity Framework Core supports.</span></span>  <span data-ttu-id="aea48-144">您要做的是建立 `Person` 類別、變更 `Instructor` 和 `Student` 類別以衍生自 `Person`、將新類別新增至 `DbContext`，以及建立移轉。</span><span class="sxs-lookup"><span data-stu-id="aea48-144">What you'll do is create a `Person` class, change the `Instructor` and `Student` classes to derive from `Person`, add the new class to the `DbContext`, and create a migration.</span></span>

> [!TIP]
> <span data-ttu-id="aea48-145">在進行下列變更之前，請考慮儲存專案的複本。</span><span class="sxs-lookup"><span data-stu-id="aea48-145">Consider saving a copy of the project before making the following changes.</span></span>  <span data-ttu-id="aea48-146">那麼，當您遇到問題而需要從新開始時，就可以輕鬆地從儲存的專案開始，而不是還原本教學課程完成的步驟或回到整個系列的開頭。</span><span class="sxs-lookup"><span data-stu-id="aea48-146">Then if you run into problems and need to start over, it will be easier to start from the saved project instead of reversing steps done for this tutorial or going back to the beginning of the whole series.</span></span>

## <a name="create-the-person-class"></a><span data-ttu-id="aea48-147">建立 Person 類別</span><span class="sxs-lookup"><span data-stu-id="aea48-147">Create the Person class</span></span>

<span data-ttu-id="aea48-148">在 Models 資料夾中，建立 Person.cs，並以下列程式碼取代範本程式碼：</span><span class="sxs-lookup"><span data-stu-id="aea48-148">In the Models folder, create Person.cs and replace the template code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/Person.cs)]

## <a name="update-instructor-and-student"></a><span data-ttu-id="aea48-149">更新 Instructor 和 Student</span><span class="sxs-lookup"><span data-stu-id="aea48-149">Update Instructor and Student</span></span>

<span data-ttu-id="aea48-150">在 *Instructor.cs* 中，從 Person 類別衍生 Instructor 類別，並移除索引鍵和名稱欄位。</span><span class="sxs-lookup"><span data-stu-id="aea48-150">In *Instructor.cs*, derive the Instructor class from the Person class and remove the key and name fields.</span></span> <span data-ttu-id="aea48-151">程式碼看起來應該如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="aea48-151">The code will look like the following example:</span></span>

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_AfterInheritance&highlight=8)]

<span data-ttu-id="aea48-152">在 *Student.cs* 中進行相同的變更。</span><span class="sxs-lookup"><span data-stu-id="aea48-152">Make the same changes in *Student.cs*.</span></span>

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_AfterInheritance&highlight=8)]

## <a name="add-person-to-the-model"></a><span data-ttu-id="aea48-153">將 Person 新增至模型</span><span class="sxs-lookup"><span data-stu-id="aea48-153">Add Person to the model</span></span>

<span data-ttu-id="aea48-154">將 Person 實體類型新增至 *SchoolContext.cs*。</span><span class="sxs-lookup"><span data-stu-id="aea48-154">Add the Person entity type to *SchoolContext.cs*.</span></span> <span data-ttu-id="aea48-155">新增的行已醒目提示。</span><span class="sxs-lookup"><span data-stu-id="aea48-155">The new lines are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_AfterInheritance&highlight=19,30)]

<span data-ttu-id="aea48-156">這就是 Entity Framework 為了設定單表繼承而必須執行的所有工作。</span><span class="sxs-lookup"><span data-stu-id="aea48-156">This is all that the Entity Framework needs in order to configure table-per-hierarchy inheritance.</span></span> <span data-ttu-id="aea48-157">如您所見，當資料庫更新時，會有一個 Person 資料表來替代 Student 和 Instructor 資料表。</span><span class="sxs-lookup"><span data-stu-id="aea48-157">As you'll see, when the database is updated, it will have a Person table in place of the Student and Instructor tables.</span></span>

## <a name="create-and-update-migrations"></a><span data-ttu-id="aea48-158">建立及更新移轉</span><span class="sxs-lookup"><span data-stu-id="aea48-158">Create and update migrations</span></span>

<span data-ttu-id="aea48-159">儲存您的變更，並建置專案。</span><span class="sxs-lookup"><span data-stu-id="aea48-159">Save your changes and build the project.</span></span> <span data-ttu-id="aea48-160">然後，在專案資料夾中開啟命令視窗，並輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="aea48-160">Then open the command window in the project folder and enter the following command:</span></span>

```dotnetcli
dotnet ef migrations add Inheritance
```

<span data-ttu-id="aea48-161">還不要執行 `database update` 命令。</span><span class="sxs-lookup"><span data-stu-id="aea48-161">Don't run the `database update` command yet.</span></span> <span data-ttu-id="aea48-162">該命令將導致資料遺失，因為它會卸除 Instructor 資料表，然後將 Student 資料表重新命名為 Person。</span><span class="sxs-lookup"><span data-stu-id="aea48-162">That command will result in lost data because it will drop the Instructor table and rename the Student table to Person.</span></span> <span data-ttu-id="aea48-163">您必須提供自訂程式碼來保留現有的資料。</span><span class="sxs-lookup"><span data-stu-id="aea48-163">You need to provide custom code to preserve existing data.</span></span>

<span data-ttu-id="aea48-164">開啟 [ *遷移/ \<timestamp> _Inheritance* ]，並 `Up` 以下列程式碼取代方法：</span><span class="sxs-lookup"><span data-stu-id="aea48-164">Open *Migrations/\<timestamp>_Inheritance.cs* and replace the `Up` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170216215525_Inheritance.cs?name=snippet_Up)]

<span data-ttu-id="aea48-165">此程式碼負責下列資料庫更新工作：</span><span class="sxs-lookup"><span data-stu-id="aea48-165">This code takes care of the following database update tasks:</span></span>

* <span data-ttu-id="aea48-166">移除指向 Student 資料表的外部索引鍵條件約束和索引。</span><span class="sxs-lookup"><span data-stu-id="aea48-166">Removes foreign key constraints and indexes that point to the Student table.</span></span>

* <span data-ttu-id="aea48-167">將 Instructor 資料表重新命名為 Person，並對其進行所需的變更來儲存 Student 資料：</span><span class="sxs-lookup"><span data-stu-id="aea48-167">Renames the Instructor table as Person and makes changes needed for it to store Student data:</span></span>

* <span data-ttu-id="aea48-168">針對學生新增可為 null 的 EnrollmentDate。</span><span class="sxs-lookup"><span data-stu-id="aea48-168">Adds nullable EnrollmentDate for students.</span></span>

* <span data-ttu-id="aea48-169">新增 Discriminator 資料行，以指出資料列適用於學生或講師。</span><span class="sxs-lookup"><span data-stu-id="aea48-169">Adds Discriminator column to indicate whether a row is for a student or an instructor.</span></span>

* <span data-ttu-id="aea48-170">使 HireDate 成為可為 Null，因為學生資料列不會有雇用日期。</span><span class="sxs-lookup"><span data-stu-id="aea48-170">Makes HireDate nullable since student rows won't have hire dates.</span></span>

* <span data-ttu-id="aea48-171">新增暫存欄位，它將用來更新指向學生的外部索引鍵。</span><span class="sxs-lookup"><span data-stu-id="aea48-171">Adds a temporary field that will be used to update foreign keys that point to students.</span></span> <span data-ttu-id="aea48-172">當您將學生複製到 Person 資料表時，它們會取得新的主索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="aea48-172">When you copy students into the Person table they will get new primary key values.</span></span>

* <span data-ttu-id="aea48-173">將 Student 資料表中的資料複製到 Person 資料表。</span><span class="sxs-lookup"><span data-stu-id="aea48-173">Copies data from the Student table into the Person table.</span></span> <span data-ttu-id="aea48-174">這會導致學生獲指派新的主索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="aea48-174">This causes students to get assigned new primary key values.</span></span>

* <span data-ttu-id="aea48-175">修正指向學生的外部索引鍵值。</span><span class="sxs-lookup"><span data-stu-id="aea48-175">Fixes foreign key values that point to students.</span></span>

* <span data-ttu-id="aea48-176">重新建立外部索引鍵條件約束和索引，現在將它們指向 Person 資料表。</span><span class="sxs-lookup"><span data-stu-id="aea48-176">Re-creates foreign key constraints and indexes, now pointing them to the Person table.</span></span>

<span data-ttu-id="aea48-177">(如果您使用 GUID 而不是整數作為主索引鍵類型，學生的主索引鍵值將無需變更，而且可能已省略其中幾個步驟。)</span><span class="sxs-lookup"><span data-stu-id="aea48-177">(If you had used GUID instead of integer as the primary key type, the student primary key values wouldn't have to change, and several of these steps could have been omitted.)</span></span>

<span data-ttu-id="aea48-178">執行 `database update` 命令：</span><span class="sxs-lookup"><span data-stu-id="aea48-178">Run the `database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="aea48-179">(在生產環境系統中，您會對 `Down` 方法進行對應的變更，以防您必須使用該方法來回到先前的資料庫版本。</span><span class="sxs-lookup"><span data-stu-id="aea48-179">(In a production system you would make corresponding changes to the `Down` method in case you ever had to use that to go back to the previous database version.</span></span> <span data-ttu-id="aea48-180">在此教學課程中，您將不會使用 `Down` 方法。)</span><span class="sxs-lookup"><span data-stu-id="aea48-180">For this tutorial you won't be using the `Down` method.)</span></span>

> [!NOTE]
> <span data-ttu-id="aea48-181">在具有現有資料的資料庫中進行結構描述變更時，可能會收到其他錯誤。</span><span class="sxs-lookup"><span data-stu-id="aea48-181">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="aea48-182">如果收到無法解決的移轉錯誤，您可以變更連接字串中的資料庫名稱，或刪除該資料庫。</span><span class="sxs-lookup"><span data-stu-id="aea48-182">If you get migration errors that you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="aea48-183">使用新資料庫時，沒有可移轉的資料，因此 update-database 命令更可能會在沒有錯誤的情況下完成。</span><span class="sxs-lookup"><span data-stu-id="aea48-183">With a new database, there's no data to migrate, and the update-database command is more likely to complete without errors.</span></span> <span data-ttu-id="aea48-184">若要刪除資料庫，請使用 SSOX 或執行 `database drop` CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="aea48-184">To delete the database, use SSOX or run the `database drop` CLI command.</span></span>

## <a name="test-the-implementation"></a><span data-ttu-id="aea48-185">測試實作</span><span class="sxs-lookup"><span data-stu-id="aea48-185">Test the implementation</span></span>

<span data-ttu-id="aea48-186">執行應用程式，然後嘗試各種頁面。</span><span class="sxs-lookup"><span data-stu-id="aea48-186">Run the app and try various pages.</span></span> <span data-ttu-id="aea48-187">一切項目的運作與之前一樣。</span><span class="sxs-lookup"><span data-stu-id="aea48-187">Everything works the same as it did before.</span></span>

<span data-ttu-id="aea48-188">在 [SQL Server 物件總管] 中，展開 [資料連線/SchoolContext]，然後展開 [資料表]，您會看到 Person 資料表已取代 Student 和 Instructor 資料表。</span><span class="sxs-lookup"><span data-stu-id="aea48-188">In **SQL Server Object Explorer**, expand **Data Connections/SchoolContext** and then **Tables**, and you see that the Student and Instructor tables have been replaced by a Person table.</span></span> <span data-ttu-id="aea48-189">開啟 Person 資料表設計工具，您會看到它包含了過去位在 Student 和 Instructor 資料表中的所有資料行。</span><span class="sxs-lookup"><span data-stu-id="aea48-189">Open the Person table designer and you see that it has all of the columns that used to be in the Student and Instructor tables.</span></span>

![SSOX 中的 Person 資料表](inheritance/_static/ssox-person-table.png)

<span data-ttu-id="aea48-191">以滑鼠右鍵按一下 Person 資料表，然後按一下 [顯示資料表資料] 以查看鑑別子資料行。</span><span class="sxs-lookup"><span data-stu-id="aea48-191">Right-click the Person table, and then click **Show Table Data** to see the discriminator column.</span></span>

![SSOX 中的 Person 資料 - 資料表資料](inheritance/_static/ssox-person-data.png)

## <a name="get-the-code"></a><span data-ttu-id="aea48-193">取得程式碼</span><span class="sxs-lookup"><span data-stu-id="aea48-193">Get the code</span></span>

[<span data-ttu-id="aea48-194">下載或檢視已完成的應用程式。</span><span class="sxs-lookup"><span data-stu-id="aea48-194">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="aea48-195">其他資源</span><span class="sxs-lookup"><span data-stu-id="aea48-195">Additional resources</span></span>

<span data-ttu-id="aea48-196">如需 Entity Framework Core 中有關繼承的詳細資訊，請參閱[繼承](/ef/core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="aea48-196">For more information about inheritance in Entity Framework Core, see [Inheritance](/ef/core/modeling/inheritance).</span></span>

## <a name="next-steps"></a><span data-ttu-id="aea48-197">後續步驟</span><span class="sxs-lookup"><span data-stu-id="aea48-197">Next steps</span></span>

<span data-ttu-id="aea48-198">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="aea48-198">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aea48-199">將繼承對應至資料庫</span><span class="sxs-lookup"><span data-stu-id="aea48-199">Mapped inheritance to database</span></span>
> * <span data-ttu-id="aea48-200">建立 Person 類別</span><span class="sxs-lookup"><span data-stu-id="aea48-200">Created the Person class</span></span>
> * <span data-ttu-id="aea48-201">更新 Instructor 和 Student</span><span class="sxs-lookup"><span data-stu-id="aea48-201">Updated Instructor and Student</span></span>
> * <span data-ttu-id="aea48-202">將 Person 新增至模型</span><span class="sxs-lookup"><span data-stu-id="aea48-202">Added Person to the model</span></span>
> * <span data-ttu-id="aea48-203">建立及更新移轉</span><span class="sxs-lookup"><span data-stu-id="aea48-203">Created and update migrations</span></span>
> * <span data-ttu-id="aea48-204">測試實作</span><span class="sxs-lookup"><span data-stu-id="aea48-204">Tested the implementation</span></span>

<span data-ttu-id="aea48-205">若要了解如何處理各種較進階的 Entity Framework 案例，請前往下一個教學課程。</span><span class="sxs-lookup"><span data-stu-id="aea48-205">Advance to the next tutorial to learn how to handle a variety of relatively advanced Entity Framework scenarios.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="aea48-206">下一步： Advanced 主題</span><span class="sxs-lookup"><span data-stu-id="aea48-206">Next: Advanced topics</span></span>](advanced.md)