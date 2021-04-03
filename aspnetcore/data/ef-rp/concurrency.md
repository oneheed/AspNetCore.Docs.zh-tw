---
title: 第8部分： Razor ASP.NET Core 並行中具有 EF Core 的頁面
author: rick-anderson
description: 第8部分的 Razor 頁面和 Entity Framework 的教學課程系列。
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: d95a395ba91801fd066f0e75f7e5fe5b3ef46f73
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957479"
---
# <a name="part-8-razor-pages-with-ef-core-in-aspnet-core---concurrency"></a><span data-ttu-id="9e5c1-103">第8部分： Razor ASP.NET Core 並行中具有 EF Core 的頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-103">Part 8, Razor Pages with EF Core in ASP.NET Core - Concurrency</span></span>

<span data-ttu-id="9e5c1-104">[Tom Dykstra](https://github.com/tdykstra)和 [Jon P Smith](https://twitter.com/thereformedprog)</span><span class="sxs-lookup"><span data-stu-id="9e5c1-104">[Tom Dykstra](https://github.com/tdykstra), and [Jon P Smith](https://twitter.com/thereformedprog)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9e5c1-105">本教學課程說明如何在多位使用者同時更新實體時處理衝突。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-105">This tutorial shows how to handle conflicts when multiple users update an entity concurrently.</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="9e5c1-106">並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-106">Concurrency conflicts</span></span>

<span data-ttu-id="9e5c1-107">並行衝突發生的時機：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-107">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="9e5c1-108">使用者巡覽至實體的編輯頁面。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-108">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="9e5c1-109">另一位使用者在第一位使用者的變更寫入到資料庫前更新相同實體。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-109">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="9e5c1-110">若未啟用並行偵測，則最後更新資料庫的任何使用者所作出變更會覆寫前一位使用者所作出變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-110">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="9e5c1-111">若您可以接受這種風險，則並行程式設計所帶來的成本便可能會超過其效益。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-111">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency"></a><span data-ttu-id="9e5c1-112">封閉式平行存取</span><span class="sxs-lookup"><span data-stu-id="9e5c1-112">Pessimistic concurrency</span></span>

<span data-ttu-id="9e5c1-113">其中一種避免並行衝突的方式是使用資料庫鎖定。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-113">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="9e5c1-114">這稱之為封閉式並行存取。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-114">This is called pessimistic concurrency.</span></span> <span data-ttu-id="9e5c1-115">在應用程式讀取要更新的資料庫資料列前，它會先要求鎖定。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-115">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="9e5c1-116">針對更新存取鎖定資料列後，在第一個鎖定解除前，其他使用者都無法鎖定該資料列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-116">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="9e5c1-117">管理鎖定有幾個缺點。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-117">Managing locks has disadvantages.</span></span> <span data-ttu-id="9e5c1-118">這種程式可能會相當複雜，且可能會隨著使用者數量的增加而造成效能問題。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-118">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="9e5c1-119">Entity Framework Core 不提供封閉式平行存取的內建支援。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-119">Entity Framework Core provides no built-in support for pessimistic concurrency.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="9e5c1-120">開放式並行存取</span><span class="sxs-lookup"><span data-stu-id="9e5c1-120">Optimistic concurrency</span></span>

<span data-ttu-id="9e5c1-121">開放式並行存取允許並行衝突發生，並會在衝突發生時適當的作出反應。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-121">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="9e5c1-122">例如，Jane 造訪了 Department 編輯頁面，然後將英文部門的預算從美金 $350,000.00 元調整到美金 $0.00 元。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-122">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![將預算變更為 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="9e5c1-124">在 Jane 按一下 [儲存] 前，John 造訪了相同的頁面並將 [開始日期] 欄位從 2007/9/1 變更為 2013/9/1。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-124">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![將開始日期變更為 2013 年](concurrency/_static/change-date30.png)

<span data-ttu-id="9e5c1-126">Jane 先按了一下 [儲存] 並看到她所做的變更生效，因為瀏覽器顯示 Budget 金額為零的 Index 頁。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-126">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="9e5c1-127">John 在仍然顯示預算為美金 $350,000.00 的 [編輯] 頁面上按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-127">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="9e5c1-128">接下來將發生情況是由您處理並行衝突的方式決定：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-128">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="9e5c1-129">追蹤使用者已修改的屬性，並只更新資料庫中對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-129">Keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="9e5c1-130">在此案例中，您將不會遺失任何資料。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-130">In the scenario, no data would be lost.</span></span> <span data-ttu-id="9e5c1-131">兩位使用者更新了不同的屬性。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-131">Different properties were updated by the two users.</span></span> <span data-ttu-id="9e5c1-132">下一次當有人瀏覽英文部門時，他們便會同時看到 Jane 和 John 所作出的變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-132">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="9e5c1-133">這種更新方法可以減少可能會導致資料遺失的衝突數目。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-133">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="9e5c1-134">此方法有一些缺點：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-134">This approach has some disadvantages:</span></span>

  * <span data-ttu-id="9e5c1-135">無法在使用者更新相同屬性時避免資料遺失。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-135">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="9e5c1-136">表示通常在 Web 應用程式中不實用。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-136">Is generally not practical in a web app.</span></span> <span data-ttu-id="9e5c1-137">它必須維持大量的狀態來追蹤所有擷取的值和新的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-137">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="9e5c1-138">維持大量的狀態可能會影響應用程式的效能。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-138">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="9e5c1-139">與實體上的並行偵測相較之下，可能會增加應用程式的複雜度。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-139">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="9e5c1-140">讓 John 的變更覆寫 Jane 的變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-140">Let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="9e5c1-141">下一次當有人瀏覽英文部門時，他們便會看到開始日期為 2013/9/1，以及擷取的美金 $350,000.00 元預算金額。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-141">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="9e5c1-142">這稱之為「用戶端獲勝 (Client Wins)」或「最後寫入為準 (Last in Wins)」案例。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-142">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="9e5c1-143">用戶端的所有值會優先于資料存放區中的內容。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-143">All values from the client take precedence over what's in the data store.</span></span> <span data-ttu-id="9e5c1-144">Scaffold 程式碼不會執行並行處理，用戶端會自動發生。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-144">The scaffolded code does no concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="9e5c1-145">避免在資料庫中更新 John 的變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-145">Prevent John's change from being updated in the database.</span></span> <span data-ttu-id="9e5c1-146">一般而言，應用程式會：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-146">Typically, the app would:</span></span>

  * <span data-ttu-id="9e5c1-147">顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-147">Display an error message.</span></span>
  * <span data-ttu-id="9e5c1-148">顯示資料的目前狀態。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-148">Show the current state of the data.</span></span>
  * <span data-ttu-id="9e5c1-149">允許使用者重新套用變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-149">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="9e5c1-150">這稱為「存放區獲勝 (Store Wins)」案例。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-150">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="9e5c1-151">資料存放區的值會優先于用戶端所提交的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-151">The data-store values take precedence over the values submitted by the client.</span></span> <span data-ttu-id="9e5c1-152">本教學課程中會使用 Store 獲勝案例。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-152">The Store Wins scenario is used in this tutorial.</span></span> <span data-ttu-id="9e5c1-153">這個方法可確保沒有任何變更會在使用者收到警示前遭到覆寫。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-153">This method ensures that no changes are overwritten without a user being alerted.</span></span>

<a name="concurrency"></a>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="9e5c1-154">EF Core 中的衝突偵測</span><span class="sxs-lookup"><span data-stu-id="9e5c1-154">Conflict detection in EF Core</span></span>

<span data-ttu-id="9e5c1-155">設定為 [並行權杖](/ef/core/modeling/concurrency) 的屬性會用來執行開放式並行存取控制。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-155">Properties configured as [concurrency tokens](/ef/core/modeling/concurrency) are used to implement optimistic concurrency control.</span></span> <span data-ttu-id="9e5c1-156">當或觸發更新或刪除作業時  <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> ，會將資料庫中的並行存取權杖值與 EF Core 所讀取的原始值進行比較：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-156">When an update or delete operation is triggered by  <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> or <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>, the value of the concurrency token in the database is compared against the original value read by EF Core:</span></span>

* <span data-ttu-id="9e5c1-157">如果值相符，作業便能完成。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-157">If the values match, the operation can complete.</span></span>
* <span data-ttu-id="9e5c1-158">如果值不相符，EF Core 會假設另一位使用者已執行衝突的作業、中止目前的交易，並擲回  <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-158">If the values do not match, EF Core assumes that another user has performed a conflicting operation, aborts the current transaction, and throws a  <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException>.</span></span>

<span data-ttu-id="9e5c1-159">另一個執行與目前作業發生衝突之作業的使用者或進程，就是所謂的 *並行衝突*。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-159">Another user or process performing an operation that conflicts with the current operation is known as *concurrency conflict*.</span></span>

<span data-ttu-id="9e5c1-160">在關係資料庫上 EF Core 在和語句的子句中檢查並行存取權杖的值， `WHERE` `UPDATE` 以偵測 `DELETE` 並行衝突。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-160">On relational databases EF Core checks for the value of the concurrency token in the `WHERE` clause of `UPDATE` and `DELETE` statements to detect a concurrency conflict.</span></span>

<span data-ttu-id="9e5c1-161">資料模型必須設定為啟用衝突偵測，方法是包含追蹤資料行，可用來判斷資料列的變更時間。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-161">The data model must be configured to enable conflict detection by include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="9e5c1-162">EF 提供兩種 [並行存取權杖](/ef/core/modeling/concurrency)方法：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-162">EF provides two approaches for [concurrency tokens](/ef/core/modeling/concurrency):</span></span>

  * <span data-ttu-id="9e5c1-163">將 [`[ConcurrencyCheck]`](xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute) 或套用 <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken> 至模型上的屬性。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-163">Applying [`[ConcurrencyCheck]`](xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute) or <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken> to a property on the model.</span></span> <span data-ttu-id="9e5c1-164">不建議使用此方法。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-164">This approach is not recommended.</span></span> <span data-ttu-id="9e5c1-165">如需詳細資訊，請參閱 [EF Core 中的並行標記](/ef/core/modeling/concurrency)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-165">For more information, see [Concurrency Tokens in EF Core](/ef/core/modeling/concurrency).</span></span>

  * <span data-ttu-id="9e5c1-166">將 <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 或套用 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsRowVersion%2A> 至模型中的並行標記。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-166">Applying <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> or <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsRowVersion%2A> to a concurrency token in the model.</span></span> <span data-ttu-id="9e5c1-167">這是本教學課程中使用的方法。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-167">This is the approach used in this tutorial.</span></span>

<span data-ttu-id="9e5c1-168">SQL Server 方法和 SQLite 的執行詳細資料會稍有不同。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-168">The  SQL Server approach and SQLite implementation details are slightly different.</span></span> <span data-ttu-id="9e5c1-169">差異檔案稍後會在列出差異的教學課程中顯示。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-169">A difference file is shown later in the tutorial listing the differences.</span></span> <span data-ttu-id="9e5c1-170">[Visual Studio] 索引標籤會顯示 SQL Server 方法。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-170">The Visual Studio tab shows the SQL Server approach.</span></span> <span data-ttu-id="9e5c1-171">[Visual Studio Code] 索引標籤會顯示非 SQL Server 資料庫的方法，例如 SQLite。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-171">The Visual Studio Code tab shows the approach for non-SQL Server databases, such as SQLite.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-172">Visual Studio</span></span>](#tab/visual-studio)

  * <span data-ttu-id="9e5c1-173">在模型中，包含用來判斷資料列變更時間的追蹤資料行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-173">In the model, include a tracking column that is used to determine when a row has been changed.</span></span>
  * <span data-ttu-id="9e5c1-174">將套用 <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 至 concurrency 屬性。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-174">Apply the <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> to the concurrency property.</span></span>

  <span data-ttu-id="9e5c1-175">以下列醒目提示的程式碼，更新 *模型/部門 .cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-175">Update the *Models/Department.cs* file with the following highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippet3&highlight=27,28)]

<span data-ttu-id="9e5c1-176">會將資料 <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> 行識別為並行追蹤資料行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-176">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="9e5c1-177">Fluent API 是指定追蹤屬性的另外一種方式：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-177">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("ConcurrencyToken")
  .IsRowVersion();
```

<span data-ttu-id="9e5c1-178">[`[Timestamp]`](xref:System.ComponentModel.DataAnnotations.TimestampAttribute)實體屬性（attribute）上的屬性（attribute）會在方法中產生下列程式碼 <xref:Microsoft.EntityFrameworkCore.ModelBuilder> ：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-178">The [`[Timestamp]`](xref:System.ComponentModel.DataAnnotations.TimestampAttribute) attribute on an entity property generates the following code in the <xref:Microsoft.EntityFrameworkCore.ModelBuilder> method:</span></span>

```csharp
 b.Property<byte[]>("ConcurrencyToken")
     .IsConcurrencyToken()
     .ValueGeneratedOnAddOrUpdate()
     .HasColumnType("rowversion");
```

<span data-ttu-id="9e5c1-179">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-179">The preceding code:</span></span>

* <span data-ttu-id="9e5c1-180">將屬性型別設定 `ConcurrencyToken` 為位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-180">Sets the property type `ConcurrencyToken` to byte array.</span></span> <span data-ttu-id="9e5c1-181">`byte[]` 這是 SQL Server 的必要型別。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-181">`byte[]` is the required type for SQL Server.</span></span>
* <span data-ttu-id="9e5c1-182">呼叫 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsConcurrencyToken%2A>。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-182">Calls <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.IsConcurrencyToken%2A>.</span></span>  <span data-ttu-id="9e5c1-183">`IsConcurrencyToken` 將屬性設定為並行標記。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-183">`IsConcurrencyToken` configures the property as a concurrency token.</span></span> <span data-ttu-id="9e5c1-184">在更新時，資料庫中的並行標記值會與原始值進行比較，以確保自資料庫從資料庫抓取以來，它沒有變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-184">On updates, the concurrency token value in the database is compared to the original value when to ensure it has not changed since the instance was retrieved from the database.</span></span> <span data-ttu-id="9e5c1-185">如果已變更，則會擲回， <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> 且不會套用變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-185">If it has changed, a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> is thrown and changes are not applied.</span></span>
* <span data-ttu-id="9e5c1-186">呼叫 <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedOnAddOrUpdate%2A> ，這會將 `ConcurrencyToken` 屬性設定為在新增或更新實體時自動產生值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-186">Calls <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedOnAddOrUpdate%2A>, which configures the `ConcurrencyToken` property to have a value automatically generated when adding or updating an entity.</span></span>
* <span data-ttu-id="9e5c1-187">`HasColumnType("rowversion")` 將 SQL Server 資料庫中的資料行類型設定為 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-187">`HasColumnType("rowversion")` sets the column type in the SQL Server database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="9e5c1-188">下列程式碼顯示更新名稱時，EF Core 所產生的 T-sql 部分 `Department` ：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-188">The following code shows a portion of the T-SQL generated by EF Core when the `Department` name is updated:</span></span>

[!code-sql[](intro/samples/cu50snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="9e5c1-189">上述醒目提示程式碼顯示 `WHERE` 子句中包含 `ConcurrencyToken`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-189">The preceding highlighted code shows the `WHERE` clause containing `ConcurrencyToken`.</span></span> <span data-ttu-id="9e5c1-190">如果資料庫 `ConcurrencyToken` 不等於 `ConcurrencyToken` 參數，則 `@p2` 不會更新任何資料列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-190">If the database `ConcurrencyToken` doesn't equal the `ConcurrencyToken` parameter `@p2`, no rows are updated.</span></span>

<span data-ttu-id="9e5c1-191">下列醒目提示程式碼顯示驗證確實有一個資料列獲得更新的 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-191">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu50snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="9e5c1-192">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 傳回最後一個語句所影響的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-192">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="9e5c1-193">若沒有更新任何資料列，則 EF Core 會擲回 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-193">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

  * <span data-ttu-id="9e5c1-195">在模型中，包含追蹤資料行，可用來判斷資料列的變更時間。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-195">In the model, include a tracking column that can be used to determine when a row has been changed.</span></span> <span data-ttu-id="9e5c1-196">在此範例中， `GUID` 會使用。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-196">In this sample, a `GUID` is used.</span></span> <span data-ttu-id="9e5c1-197">以下列醒目提示的程式碼，更新 *模型/部門 .cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-197">Update the *Models/Department.cs* file with the following highlighted code:</span></span>

    [!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippetSL&highlight=27)]

  * <span data-ttu-id="9e5c1-198">藉由在屬性上呼叫來更新 *Data/SchoolCoNtext .cs* 檔案 <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken> `Department.ConcurrencyToken` ：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-198">Update the *Data/SchoolContext.cs* file by calling <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty.IsConcurrencyToken> on the `Department.ConcurrencyToken` property:</span></span>

    [!code-csharp[](intro/samples/cu50/Data/SchoolContext.cs?name=snippet_SQLite&highlight=21-23)]

    <span data-ttu-id="9e5c1-199">`IsConcurrencyToken` 將屬性設定為並行標記。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-199">`IsConcurrencyToken` configures the property as a concurrency token.</span></span> <span data-ttu-id="9e5c1-200">在更新時，資料庫中的並行標記值會與原始值進行比較，以確保自資料庫從資料庫抓取以來，它沒有變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-200">On updates, the concurrency token value in the database is compared to the original value when to ensure it has not changed since the instance was retrieved from the database.</span></span> <span data-ttu-id="9e5c1-201">如果已變更，則會擲回， <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> 且不會套用變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-201">If it has changed, a <xref:Microsoft.EntityFrameworkCore.DbUpdateConcurrencyException> is thrown and changes are not applied.</span></span>

  * <span data-ttu-id="9e5c1-202">在更新實體的程式碼中，更新並行存取權杖的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-202">In the code that updates the entity, update the value of the concurrency token.</span></span> <span data-ttu-id="9e5c1-203">在此範例中，更新實體的方法會使用下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-203">In this sample, the method that updates the entity uses the following code.</span></span> <span data-ttu-id="9e5c1-204">本逐步解說將在稍後的教學課程中完成。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-204">This step will be done later in the tutorial.</span></span>

    ```csharp
    departmentToUpdate.ConcurrencyToken = Guid.NewGuid();
    ```

  <span data-ttu-id="9e5c1-205">在 update 方法中更新並行存取權杖的替代方式是設定觸發程式，以便資料庫自動更新。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-205">An alternative to updating the concurrency token in the update method is to set up a trigger so it's automatically updated by the database.</span></span> <span data-ttu-id="9e5c1-206">如需詳細資訊，請參閱 [SQLite 和 EF Core 並行存取權杖](https://www.bricelam.net/2020/08/07/sqlite-and-efcore-concurrency-tokens.html)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-206">For more information, see [SQLite and EF Core Concurrency Tokens](https://www.bricelam.net/2020/08/07/sqlite-and-efcore-concurrency-tokens.html).</span></span>
  <!--* Call <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> to set the `rowVersion` value to the value from the entity when it was read. The `OriginalValue` update  code is shown in [the concurrency updates](#c-up) section of this document. This step will be done later in the tutorial. -->

---

## <a name="add-a-migration"></a><span data-ttu-id="9e5c1-207">新增移轉</span><span class="sxs-lookup"><span data-stu-id="9e5c1-207">Add a migration</span></span>

<span data-ttu-id="9e5c1-208">新增 `ConcurrencyToken` 屬性會變更資料模型，因此您將需要進行移轉。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-208">Adding the `ConcurrencyToken` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="9e5c1-209">建置專案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-209">Build the project.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-210">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9e5c1-211">在 PMC 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-211">Run the following commands in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  Update-Database
    
  ```

<span data-ttu-id="9e5c1-212">上述命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-212">The preceding commands:</span></span>

* <span data-ttu-id="9e5c1-213">建立 *Migrations/{time stamp}_RowVersion.cs* 移轉檔案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-213">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="9e5c1-214">更新 *Migrations/SchoolContextModelSnapshot.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-214">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="9e5c1-215">此更新會將下列程式碼加入至 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-215">The update adds the following code to the `BuildModel` method:</span></span>

```csharp
 b.Property<byte[]>("ConcurrencyToken")
     .IsConcurrencyToken()
     .ValueGeneratedOnAddOrUpdate()
     .HasColumnType("rowversion");
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9e5c1-217">在終端機中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-217">Run the following commands in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  dotnet ef database update
  ```

<span data-ttu-id="9e5c1-218">上述命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-218">The preceding commands:</span></span>

* <span data-ttu-id="9e5c1-219">建立 *Migrations/{time stamp}_RowVersion.cs* 移轉檔案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-219">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="9e5c1-220">更新 *Migrations/SchoolContextModelSnapshot.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-220">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="9e5c1-221">更新會將下列醒目提示程式碼新增至 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-221">The update adds the following highlighted code to the `BuildModel` method:</span></span>

```csharp
b.Property<Guid>("ConcurrencyToken")
    .IsConcurrencyToken()
    .HasColumnType("TEXT");
```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="9e5c1-222">Scaffold Department 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-222">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-223">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9e5c1-224">遵循 [Scaffold Student 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-224">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>
  * <span data-ttu-id="9e5c1-225">建立 *Pages/Departments* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-225">Create a *Pages/Departments* folder.</span></span>  
  * <span data-ttu-id="9e5c1-226">使用 `Department` 作為模型類別。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-226">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="9e5c1-227">使用現有內容類別，而非建立新的類別。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-227">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9e5c1-229">建立 *Pages/Departments* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-229">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="9e5c1-230">執行下列命令來 Scaffold Department 頁面。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-230">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="9e5c1-231">**在 Windows 上：**</span><span class="sxs-lookup"><span data-stu-id="9e5c1-231">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="9e5c1-232">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="9e5c1-232">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

### <a name="add-a-utility-class"></a><span data-ttu-id="9e5c1-233">新增公用程式類別</span><span class="sxs-lookup"><span data-stu-id="9e5c1-233">Add a utility class</span></span>

<span data-ttu-id="9e5c1-234">使用下列程式碼來新增名為的類別 `Utility` ：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-234">Add a class named `Utility` with the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-235">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Utility.cs?name=snippet)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Utility.cs?name=snippet_sl)]

---

<span data-ttu-id="9e5c1-237">`Utility`類別會提供 `GetLastChars` 用來顯示並行權杖最後幾個字元的方法。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-237">The `Utility` class provides the `GetLastChars` method used to display the last few characters of the concurrency token.</span></span> <span data-ttu-id="9e5c1-238">下列程式碼顯示適用于 SQLite ad SQL Server 的程式碼：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-238">The following code shows the code that works with both SQLite ad SQL Server:</span></span>

[!code-csharp[](intro/samples/cu50/Utility.cs?name=snippet_full)]

<span data-ttu-id="9e5c1-239">`#if SQLiteVersion`[預處理器](/dotnet/csharp/language-reference/preprocessor-directives/)指示詞會隔離 SQLite 和 SQL Server 版本的差異，並協助：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-239">The `#if SQLiteVersion` [preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/) isolates the differences in the SQLite and SQL Server versions and helps:</span></span>

* <span data-ttu-id="9e5c1-240">作者針對兩個版本都維持一個程式碼基底。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-240">The author maintain one code base for both versions.</span></span>
* <span data-ttu-id="9e5c1-241">SQLite 開發人員將應用程式部署至 Azure，並使用 SQL Azure。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-241">SQLite developers deploy the app to Azure and use SQL Azure.</span></span>

<span data-ttu-id="9e5c1-242">建置專案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-242">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="9e5c1-243">更新 Index 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-243">Update the Index page</span></span>

<span data-ttu-id="9e5c1-244">Scaffolding 工具會為 Index 頁面建立 `ConcurrencyToken` 資料行，但該欄位不會在生產應用程式中顯示。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-244">The scaffolding tool created a `ConcurrencyToken` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="9e5c1-245">在本教學課程中，會顯示的最後一個部分， `ConcurrencyToken` 以協助顯示並行處理的運作方式。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-245">In this tutorial, the last portion of the `ConcurrencyToken` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="9e5c1-246">最後一個部分不一定是唯一的。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-246">The last portion isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="9e5c1-247">更新 *Pages\Departments\Index.cshtml* 頁面：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-247">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="9e5c1-248">使用 Departments 取代 Index。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-248">Replace Index with Departments.</span></span>
* <span data-ttu-id="9e5c1-249">變更包含的程式碼 `ConcurrencyToken` ，只顯示最後幾個字元。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-249">Change the code containing `ConcurrencyToken` to show just the last few characters.</span></span>
* <span data-ttu-id="9e5c1-250">將 `FirstMidName` 取代為 `FullName`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-250">Replace `FirstMidName` with `FullName`.</span></span>

<span data-ttu-id="9e5c1-251">下列程式碼會顯示更新後的頁面：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-251">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="9e5c1-252">更新 [編輯] 頁面模型</span><span class="sxs-lookup"><span data-stu-id="9e5c1-252">Update the Edit page model</span></span>

<span data-ttu-id="9e5c1-253">使用下列程式碼更新 *Pages\Departments\Edit.cshtml.cs* ：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-253">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-254">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_All_ss)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-255">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-255">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

---

<a name="c-up"></a>

### <a name="the-concurrency-updates"></a><span data-ttu-id="9e5c1-256">平行存取更新</span><span class="sxs-lookup"><span data-stu-id="9e5c1-256">The concurrency updates</span></span>

<span data-ttu-id="9e5c1-257"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue>`ConcurrencyToken`在方法中提取時，會以實體中的值更新 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-257"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `ConcurrencyToken` value from the entity when it was fetched in the `OnGetAsync` method.</span></span> <span data-ttu-id="9e5c1-258">EF Core 會 `SQL UPDATE` 使用 `WHERE` 包含原始值的子句產生命令 `ConcurrencyToken` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-258">EF Core generates a `SQL UPDATE` command with a `WHERE` clause containing the original `ConcurrencyToken` value.</span></span> <span data-ttu-id="9e5c1-259">如果命令未影響任何資料列 `UPDATE` ， `DbUpdateConcurrencyException` 則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-259">If no rows are affected by the `UPDATE` command, a `DbUpdateConcurrencyException` exception is thrown.</span></span> <span data-ttu-id="9e5c1-260">`UPDATE`沒有任何資料列具有原始值時，命令不會影響任何資料列 `ConcurrencyToken` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-260">No rows are affected by the `UPDATE` command when no rows have the original `ConcurrencyToken` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-261">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=19-99)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-262">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-262">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion_sl&highlight=21-99)]

---

<span data-ttu-id="9e5c1-263">在上述醒目提示的程式碼中：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-263">In the preceding highlighted code:</span></span>

* <span data-ttu-id="9e5c1-264">中的值 `Department.ConcurrencyToken` 是在頁面的要求中提取實體時的值 `Get` `Edit` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-264">The value in `Department.ConcurrencyToken` is the value when the entity was fetched in the `Get` request for the `Edit` page.</span></span> <span data-ttu-id="9e5c1-265">此值會 `OnPost` 由頁面中隱藏的欄位提供給方法，該欄位 Razor 會顯示要編輯的實體。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-265">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="9e5c1-266">隱藏欄位的值會由模型繫結器複製到 `Department.ConcurrencyToken`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-266">The hidden field value is copied to `Department.ConcurrencyToken` by the model binder.</span></span>
* <span data-ttu-id="9e5c1-267">`OriginalValue` EF Core 在子句中使用的 `WHERE` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-267">`OriginalValue` is what EF Core uses in the `WHERE` clause.</span></span> <span data-ttu-id="9e5c1-268">在反白顯示的程式程式碼執行之前：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-268">Before the highlighted line of code executes:</span></span>
  * <span data-ttu-id="9e5c1-269">`OriginalValue` 具有在 `FirstOrDefaultAsync` 此方法中呼叫時的資料庫值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-269">`OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method.</span></span>
  * <span data-ttu-id="9e5c1-270">此值可能與編輯頁面上顯示的值不同。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-270">This value  might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="9e5c1-271">醒目提示的程式碼可確保 EF Core 使用 `ConcurrencyToken` `Department` SQL 語句子句中所顯示實體的原始值 `UPDATE` `WHERE` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-271">The highlighted code makes sure that EF Core uses the original `ConcurrencyToken`  value from the displayed `Department` entity in the SQL `UPDATE` statement's `WHERE` clause.</span></span>

<span data-ttu-id="9e5c1-272">下列程式碼顯示 `Department` 模型。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-272">The following code shows the `Department` model.</span></span> <span data-ttu-id="9e5c1-273">`Department` 在中初始化：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-273">`Department` is initialized in the:</span></span>

  * <span data-ttu-id="9e5c1-274">`OnGetAsync` EF 查詢的方法。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-274">`OnGetAsync` method by the EF query.</span></span>
  * <span data-ttu-id="9e5c1-275">`OnPostAsync`使用模型系結之頁面中的隱藏欄位方法 Razor ： [](xref:mvc/models/model-binding)</span><span class="sxs-lookup"><span data-stu-id="9e5c1-275">`OnPostAsync` method by the hidden field in the Razor page using [model binding](xref:mvc/models/model-binding):</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-276">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-276">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_mb&highlight=10-11,17-20,50-99)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_mb_ss&highlight=10-11,17-20,55-99)]

---

<span data-ttu-id="9e5c1-278">上述程式碼顯示 `ConcurrencyToken` `Department` 要求中的實體值 `HTTP POST` 設定為 `ConcurrencyToken` 要求中的值 `HTTP GET` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-278">The preceding code shows the `ConcurrencyToken` value  of the `Department` entity from the `HTTP POST` request is set to the `ConcurrencyToken` value from the `HTTP GET` request.</span></span>

<span data-ttu-id="9e5c1-279">發生並行錯誤時，下列醒目提示的程式碼會取得用戶端值 (POST 到此方法的值) 及資料庫值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-279">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="9e5c1-280">下列程式碼會為每個其資料庫中值與 POST 到 `OnPostAsync` 值的不同資料行新增一個自訂錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-280">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="9e5c1-281">下列醒目提示的程式碼會將 `ConcurrencyToken` 值設為從資料庫所擷取新值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-281">The following highlighted code sets the `ConcurrencyToken` value to the new value retrieved from the database.</span></span> <span data-ttu-id="9e5c1-282">下一次當使用者按一下 [儲存] 時，只有在上一次顯示 [編輯] 頁面之後發生的並行錯誤會被捕捉到。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-282">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="9e5c1-283">[`ModelState.Remove`](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.Remove%2A)因為 `ModelState` 有先前的值，所以需要語句 `ConcurrencyToken` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-283">The [`ModelState.Remove`](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.Remove%2A) statement is required because `ModelState` has the previous `ConcurrencyToken` value.</span></span> <span data-ttu-id="9e5c1-284">在 Razor 頁面中， `ModelState` 當兩個欄位都存在時，欄位的值會優先于模型屬性值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-284">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="sql-server-vs-sqlite-code-differences"></a><span data-ttu-id="9e5c1-285">SQL Server vs SQLite 程式碼差異</span><span class="sxs-lookup"><span data-stu-id="9e5c1-285">SQL Server vs SQLite code differences</span></span>

<span data-ttu-id="9e5c1-286">以下顯示 SQL Server 和 SQLite 版本之間的差異：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-286">The following shows the differences between the SQL Server and SQLite versions:</span></span>

```diff
+ using System;    // For GUID on SQLite

+ departmentToUpdate.ConcurrencyToken = Guid.NewGuid();

 _context.Entry(departmentToUpdate)
    .Property(d => d.ConcurrencyToken).OriginalValue = Department.ConcurrencyToken;

- Department.ConcurrencyToken = (byte[])dbValues.ConcurrencyToken;
+ Department.ConcurrencyToken = dbValues.ConcurrencyToken;
```

### <a name="update-the-edit-razor-page"></a><span data-ttu-id="9e5c1-287">更新 [編輯] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-287">Update the Edit Razor page</span></span>

<span data-ttu-id="9e5c1-288">使用下列程式碼更新 *Pages/Departments/Edit.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-288">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="9e5c1-289">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-289">The preceding code:</span></span>

* <span data-ttu-id="9e5c1-290">將 `page` 指示詞從 `@page` 更新為 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-290">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9e5c1-291">新增一個隱藏的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-291">Adds a hidden row version.</span></span> <span data-ttu-id="9e5c1-292">`ConcurrencyToken` 必須加入，以便回傳系結值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-292">`ConcurrencyToken` must be added so postback binds the value.</span></span>
* <span data-ttu-id="9e5c1-293">顯示 `ConcurrencyToken` 的最後一個位元組，作為偵錯用途。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-293">Displays the last byte of `ConcurrencyToken` for debugging purposes.</span></span>
* <span data-ttu-id="9e5c1-294">使用強型別的 `InstructorNameSL` 取代 `ViewData`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-294">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="9e5c1-295">使用 [編輯] 頁面測試並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-295">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="9e5c1-296">開啟兩個英文部門上 [編輯] 頁面的瀏覽器執行個體：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-296">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="9e5c1-297">執行應用程式並選取 Departments。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-297">Run the app and select Departments.</span></span>
* <span data-ttu-id="9e5c1-298">以滑鼠右鍵按一下英文部門的 **編輯** 超連結，然後選取 [Open in new tab] (在新索引標籤中開啟)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-298">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="9e5c1-299">在第一個索引標籤中，按一下英文部門的 **編輯** 超連結。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-299">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="9e5c1-300">兩個瀏覽器索引標籤會顯示相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-300">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9e5c1-301">在第一個瀏覽器索引標籤中變更名稱，然後按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-301">Change the name in the first browser tab and click **Save**.</span></span>

![變更之後的 Department [編輯] 頁面 1](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="9e5c1-303">瀏覽器會顯示 [索引] 頁面，其中包含已變更的值和更新的 `ConcurrencyToken` 指標。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-303">The browser shows the Index page with the changed value and updated `ConcurrencyToken`indicator.</span></span> <span data-ttu-id="9e5c1-304">請注意更新的 `ConcurrencyToken` 指標，它會顯示在第二個回傳的 [其他] 索引標籤中。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-304">Note the updated `ConcurrencyToken`indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9e5c1-305">在第二個瀏覽器索引標籤中變更不同欄位。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-305">Change a different field in the second browser tab.</span></span>

![變更之後的 Department [編輯] 頁面 2](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="9e5c1-307">按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-307">Click **Save**.</span></span> <span data-ttu-id="9e5c1-308">您會看到所有不符合資料庫值欄位的錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-308">You see error messages for all fields that don't match the database values:</span></span>

![Department [編輯] 頁面錯誤訊息](concurrency/_static/edit-error30.png)

<span data-ttu-id="9e5c1-310">此瀏覽器視窗並未嘗試變更 [名稱] 欄位。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-310">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="9e5c1-311">複製並將目前的值 (語言 (Language)) 貼上 [名稱] 欄位。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-311">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="9e5c1-312">Tab 鍵移出。用戶端驗證會移除錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-312">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="9e5c1-313">再按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-313">Click **Save** again.</span></span> <span data-ttu-id="9e5c1-314">您在第二個瀏覽器索引標籤中輸入的值已儲存。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-314">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="9e5c1-315">您會在 [索引] 頁面中看到儲存的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-315">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="9e5c1-316">更新刪除頁面模型</span><span class="sxs-lookup"><span data-stu-id="9e5c1-316">Update the Delete page model</span></span>

<span data-ttu-id="9e5c1-317">使用下列程式碼更新 *ges/Departments/Delete.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-317">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu50/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="9e5c1-318">[刪除] 頁面會在實體擷取之後發生變更時偵測並行衝突。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-318">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="9e5c1-319">`Department.ConcurrencyToken` 是擷取實體時的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-319">`Department.ConcurrencyToken` is the row version when the entity was fetched.</span></span> <span data-ttu-id="9e5c1-320">當 EF Core 建立 `SQL DELETE` 命令時，它會包含 WHERE 子句和 `ConcurrencyToken` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-320">When EF Core creates the `SQL DELETE` command, it includes a WHERE clause with `ConcurrencyToken`.</span></span> <span data-ttu-id="9e5c1-321">如果 `SQL DELETE` 命令導致零個數據列受到影響：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-321">If the `SQL DELETE` command results in zero rows affected:</span></span>

* <span data-ttu-id="9e5c1-322">`ConcurrencyToken`命令中的 `SQL DELETE` 不符合 `ConcurrencyToken` 資料庫中的。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-322">The `ConcurrencyToken` in the `SQL DELETE` command doesn't match `ConcurrencyToken` in the database.</span></span>
* <span data-ttu-id="9e5c1-323">擲回 `DbUpdateConcurrencyException` 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-323">A `DbUpdateConcurrencyException` exception is thrown.</span></span>
* <span data-ttu-id="9e5c1-324">使用 `concurrencyError` 呼叫 `OnGetAsync`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-324">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="9e5c1-325">更新 [刪除] Razor 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-325">Update the Delete Razor page</span></span>

<span data-ttu-id="9e5c1-326">使用下列程式碼更新 *ges/Departments/Delete.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-326">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu50/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="9e5c1-327">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-327">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="9e5c1-328">將 `page` 指示詞從 `@page` 更新為 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-328">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9e5c1-329">新增錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-329">Adds an error message.</span></span>
* <span data-ttu-id="9e5c1-330">在 [系統管理員] 欄位中將 FirstMidName 取代為 FullName。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-330">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="9e5c1-331">變更 `ConcurrencyToken` 以顯示最後一個位元組。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-331">Changes `ConcurrencyToken` to display the last byte.</span></span>
* <span data-ttu-id="9e5c1-332">新增一個隱藏的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-332">Adds a hidden row version.</span></span> <span data-ttu-id="9e5c1-333">`ConcurrencyToken` 必須加入，以便回傳系結值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-333">`ConcurrencyToken` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="9e5c1-334">測試並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-334">Test concurrency conflicts</span></span>

<span data-ttu-id="9e5c1-335">建立一個測試部門。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-335">Create a test department.</span></span>

<span data-ttu-id="9e5c1-336">開啟兩個測試部門上 [刪除] 頁面的瀏覽器執行個體：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-336">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="9e5c1-337">執行應用程式並選取 Departments。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-337">Run the app and select Departments.</span></span>
* <span data-ttu-id="9e5c1-338">以滑鼠右鍵按一下測試部門的 **刪除** 超連結，然後選取 [Open in new tab] (在新索引標籤中開啟)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-338">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="9e5c1-339">按一下測試部門的 **編輯** 超連結。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-339">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="9e5c1-340">兩個瀏覽器索引標籤會顯示相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-340">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9e5c1-341">在第一個瀏覽器索引標籤中變更預算，然後按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-341">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="9e5c1-342">瀏覽器會顯示 [索引] 頁面，其中包含已變更的值和更新的 `ConcurrencyToken` 指標。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-342">The browser shows the Index page with the changed value and updated `ConcurrencyToken`indicator.</span></span> <span data-ttu-id="9e5c1-343">請注意更新的 `ConcurrencyToken` 指標，它會顯示在第二個回傳的 [其他] 索引標籤中。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-343">Note the updated `ConcurrencyToken`indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9e5c1-344">從第二個索引標籤中刪除測試部門。並行錯誤會與資料庫中目前的值一起顯示。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-344">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="9e5c1-345">除非已更新，否則按一下 [ **刪除** ] 會刪除實體 `ConcurrencyToken` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-345">Clicking **Delete** deletes the entity, unless `ConcurrencyToken` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e5c1-346">其他資源</span><span class="sxs-lookup"><span data-stu-id="9e5c1-346">Additional resources</span></span>

* [<span data-ttu-id="9e5c1-347">EF Core 中的並行權杖</span><span class="sxs-lookup"><span data-stu-id="9e5c1-347">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="9e5c1-348">在 EF Core 中處理並行</span><span class="sxs-lookup"><span data-stu-id="9e5c1-348">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="9e5c1-349">偵錯 ASP.NET Core 2.x 原始檔</span><span class="sxs-lookup"><span data-stu-id="9e5c1-349">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="9e5c1-350">下一步</span><span class="sxs-lookup"><span data-stu-id="9e5c1-350">Next steps</span></span>

<span data-ttu-id="9e5c1-351">這是一系列教學課程的最後一個教學課程。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-351">This is the last tutorial in the series.</span></span> <span data-ttu-id="9e5c1-352">[本教學課程系列的 MVC 版本](xref:data/ef-mvc/index)涵蓋了其他主題。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-352">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9e5c1-353">上一個教學課程</span><span class="sxs-lookup"><span data-stu-id="9e5c1-353">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="9e5c1-354">本教學課程會顯示如何在多位使用者同時並行更新實體時處理衝突。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-354">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="9e5c1-355">並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-355">Concurrency conflicts</span></span>

<span data-ttu-id="9e5c1-356">並行衝突發生的時機：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-356">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="9e5c1-357">使用者巡覽至實體的編輯頁面。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-357">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="9e5c1-358">另一位使用者在第一位使用者的變更寫入到資料庫前更新相同實體。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-358">Another user updates the same entity before the first user's change is written to the database.</span></span>

<span data-ttu-id="9e5c1-359">若未啟用並行偵測，則最後更新資料庫的任何使用者所作出變更會覆寫前一位使用者所作出變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-359">If concurrency detection isn't enabled, whoever updates the database last overwrites the other user's changes.</span></span> <span data-ttu-id="9e5c1-360">若您可以接受這種風險，則並行程式設計所帶來的成本便可能會超過其效益。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-360">If this risk is acceptable, the cost of programming for concurrency might outweigh the benefit.</span></span>

### <a name="pessimistic-concurrency-locking"></a><span data-ttu-id="9e5c1-361">封閉式並行存取 (鎖定)</span><span class="sxs-lookup"><span data-stu-id="9e5c1-361">Pessimistic concurrency (locking)</span></span>

<span data-ttu-id="9e5c1-362">其中一種避免並行衝突的方式是使用資料庫鎖定。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-362">One way to prevent concurrency conflicts is to use database locks.</span></span> <span data-ttu-id="9e5c1-363">這稱之為封閉式並行存取。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-363">This is called pessimistic concurrency.</span></span> <span data-ttu-id="9e5c1-364">在應用程式讀取要更新的資料庫資料列前，它會先要求鎖定。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-364">Before the app reads a database row that it intends to update, it requests a lock.</span></span> <span data-ttu-id="9e5c1-365">針對更新存取鎖定資料列後，在第一個鎖定解除前，其他使用者都無法鎖定該資料列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-365">Once a row is locked for update access, no other users are allowed to lock the row until the first lock is released.</span></span>

<span data-ttu-id="9e5c1-366">管理鎖定有幾個缺點。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-366">Managing locks has disadvantages.</span></span> <span data-ttu-id="9e5c1-367">這種程式可能會相當複雜，且可能會隨著使用者數量的增加而造成效能問題。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-367">It can be complex to program and can cause performance problems as the number of users increases.</span></span> <span data-ttu-id="9e5c1-368">Entity Framework Core 沒有提供這種功能的內建支援，本教學課程也不會示範如何進行實作。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-368">Entity Framework Core provides no built-in support for it, and this tutorial doesn't show how to implement it.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="9e5c1-369">開放式並行存取</span><span class="sxs-lookup"><span data-stu-id="9e5c1-369">Optimistic concurrency</span></span>

<span data-ttu-id="9e5c1-370">開放式並行存取允許並行衝突發生，並會在衝突發生時適當的作出反應。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-370">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="9e5c1-371">例如，Jane 造訪了 Department 編輯頁面，然後將英文部門的預算從美金 $350,000.00 元調整到美金 $0.00 元。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-371">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![將預算變更為 0](concurrency/_static/change-budget30.png)

<span data-ttu-id="9e5c1-373">在 Jane 按一下 [儲存] 前，John 造訪了相同的頁面並將 [開始日期] 欄位從 2007/9/1 變更為 2013/9/1。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-373">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![將開始日期變更為 2013 年](concurrency/_static/change-date30.png)

<span data-ttu-id="9e5c1-375">Jane 先按了一下 [儲存] 並看到她所做的變更生效，因為瀏覽器顯示 Budget 金額為零的 Index 頁。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-375">Jane clicks **Save** first and sees her change take effect, since the browser displays the Index page with zero as the Budget amount.</span></span>

<span data-ttu-id="9e5c1-376">John 在仍然顯示預算為美金 $350,000.00 的 [編輯] 頁面上按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-376">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="9e5c1-377">接下來將發生情況是由您處理並行衝突的方式決定：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-377">What happens next is determined by how you handle concurrency conflicts:</span></span>

* <span data-ttu-id="9e5c1-378">您可以追蹤使用者修改的屬性，然後僅在資料庫中更新相對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-378">You can keep track of which property a user has modified and update only the corresponding columns in the database.</span></span>

  <span data-ttu-id="9e5c1-379">在此案例中，您將不會遺失任何資料。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-379">In the scenario, no data would be lost.</span></span> <span data-ttu-id="9e5c1-380">兩位使用者更新了不同的屬性。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-380">Different properties were updated by the two users.</span></span> <span data-ttu-id="9e5c1-381">下一次當有人瀏覽英文部門時，他們便會同時看到 Jane 和 John 所作出的變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-381">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="9e5c1-382">這種更新方法可以減少可能會導致資料遺失的衝突數目。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-382">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="9e5c1-383">此方法有一些缺點：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-383">This approach has some disadvantages:</span></span>
 
  * <span data-ttu-id="9e5c1-384">無法在使用者更新相同屬性時避免資料遺失。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-384">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="9e5c1-385">表示通常在 Web 應用程式中不實用。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-385">Is generally not practical in a web app.</span></span> <span data-ttu-id="9e5c1-386">它必須維持大量的狀態來追蹤所有擷取的值和新的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-386">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="9e5c1-387">維持大量的狀態可能會影響應用程式的效能。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-387">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="9e5c1-388">與實體上的並行偵測相較之下，可能會增加應用程式的複雜度。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-388">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="9e5c1-389">您可以讓 John 的變更覆寫 Jane 的變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-389">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="9e5c1-390">下一次當有人瀏覽英文部門時，他們便會看到開始日期為 2013/9/1，以及擷取的美金 $350,000.00 元預算金額。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-390">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="9e5c1-391">這稱之為「用戶端獲勝 (Client Wins)」或「最後寫入為準 (Last in Wins)」案例。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-391">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="9e5c1-392"> (用戶端的所有值都優先于資料存放區中的內容。 ) 如果您沒有針對並行處理進行任何程式碼撰寫，用戶端會自動進行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-392">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="9e5c1-393">您可以防止 John 的變更更新到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-393">You can prevent John's change from being updated in the database.</span></span> <span data-ttu-id="9e5c1-394">一般而言，應用程式會：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-394">Typically, the app would:</span></span>

  * <span data-ttu-id="9e5c1-395">顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-395">Display an error message.</span></span>
  * <span data-ttu-id="9e5c1-396">顯示資料的目前狀態。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-396">Show the current state of the data.</span></span>
  * <span data-ttu-id="9e5c1-397">允許使用者重新套用變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-397">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="9e5c1-398">這稱為「存放區獲勝 (Store Wins)」案例。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-398">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="9e5c1-399"> (資料存放區值的優先順序高於用戶端所提交的值。 ) 在本教學課程中，您將會執行 Store 獲勝案例。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-399">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="9e5c1-400">這個方法可確保沒有任何變更會在使用者收到警示前遭到覆寫。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-400">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="conflict-detection-in-ef-core"></a><span data-ttu-id="9e5c1-401">EF Core 中的衝突偵測</span><span class="sxs-lookup"><span data-stu-id="9e5c1-401">Conflict detection in EF Core</span></span>

<span data-ttu-id="9e5c1-402">EF Core 會在偵測到衝突時擲回 `DbConcurrencyException` 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-402">EF Core throws `DbConcurrencyException` exceptions when it detects conflicts.</span></span> <span data-ttu-id="9e5c1-403">資料模型必須進行設定才能啟用衝突偵測。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-403">The data model has to be configured to enable conflict detection.</span></span> <span data-ttu-id="9e5c1-404">啟用衝突偵測的選項包括下列項目：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-404">Options for enabling conflict detection include the following:</span></span>

* <span data-ttu-id="9e5c1-405">設定 EF Core，在 Update 和 Delete 命令的 Where 子句中將資料行的原始值設為[並行權杖](/ef/core/modeling/concurrency)並包含在其中。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-405">Configure EF Core to include the original values of columns configured as [concurrency tokens](/ef/core/modeling/concurrency) in the Where clause of Update and Delete commands.</span></span>

  <span data-ttu-id="9e5c1-406">當 `SaveChanges` 呼叫時，Where 子句會尋找以屬性標注之任何屬性的原始值 <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-406">When `SaveChanges` is called, the Where clause looks for the original values of any properties annotated with the <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> attribute.</span></span> <span data-ttu-id="9e5c1-407">若從第一次讀取資料列後有任何的並行權杖屬性產生變更，更新陳述式便不會尋找要更新的資料列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-407">The update statement won't find a row to update if any of the concurrency token properties changed since the row was first read.</span></span> <span data-ttu-id="9e5c1-408">EF Core 會將其解譯為並行衝突。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-408">EF Core interprets that as a concurrency conflict.</span></span> <span data-ttu-id="9e5c1-409">針對擁有許多資料行的資料庫資料表，這種方法可能導致非常龐大的 Where 子句，且可能會需要維持龐大數量的狀態。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-409">For database tables that have many columns, this approach can result in very large Where clauses, and can require large amounts of state.</span></span> <span data-ttu-id="9e5c1-410">因此通常不建議使用這種方法，並且這種方法也不是此教學課程中所使用的方法。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-410">Therefore this approach is generally not recommended, and it isn't the method used in this tutorial.</span></span>

* <span data-ttu-id="9e5c1-411">在資料庫資料表中，包含一個追蹤資料行，該資料行可用於決定資料列發生變更的時機。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-411">In the database table, include a tracking column that can be used to determine when a row has been changed.</span></span>

  <span data-ttu-id="9e5c1-412">在 SQL Server 資料庫中，追蹤資料行的資料型別是 `rowversion`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-412">In a SQL Server database, the data type of the tracking column is `rowversion`.</span></span> <span data-ttu-id="9e5c1-413">`rowversion` 的值是一個循序號碼，每一次資料列更新時都會遞增。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-413">The `rowversion` value is a sequential number that's incremented each time the row is updated.</span></span> <span data-ttu-id="9e5c1-414">在 Update 或 Delete 命令中，Where 子句會包含追蹤資料行的原始值 (原始資料列版本號碼)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-414">In an Update or Delete command, the Where clause includes the original value of the tracking column (the original row version number).</span></span> <span data-ttu-id="9e5c1-415">若正在更新的資料列已由另一位使用者進行變更，則 `rowversion` 資料行中的值便會和原始值不同。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-415">If the row being updated has been changed by another user, the value in the `rowversion` column is different than the original value.</span></span> <span data-ttu-id="9e5c1-416">在這種情況下，Update 或 Delete 陳述式便會因為 Where 子句而無法找到要更新的資料列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-416">In that case, the Update or Delete statement can't find the row to update because of the Where clause.</span></span> <span data-ttu-id="9e5c1-417">EF Core 會在 Update 或 Delete 命令沒有影響任何資料列時擲回並行例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-417">EF Core throws a concurrency exception when no rows are affected by an Update or Delete command.</span></span>

## <a name="add-a-tracking-property"></a><span data-ttu-id="9e5c1-418">新增追蹤屬性</span><span class="sxs-lookup"><span data-stu-id="9e5c1-418">Add a tracking property</span></span>

<span data-ttu-id="9e5c1-419">在 *Models/Department.cs* 中，新增一個名為 RowVersion 的追蹤屬性：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-419">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<span data-ttu-id="9e5c1-420"><xref:System.ComponentModel.DataAnnotations.TimestampAttribute>屬性會將資料行識別為並行追蹤資料行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-420">The <xref:System.ComponentModel.DataAnnotations.TimestampAttribute> attribute is what identifies the column as a concurrency tracking column.</span></span> <span data-ttu-id="9e5c1-421">Fluent API 是指定追蹤屬性的另外一種方式：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-421">The fluent API is an alternative way to specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-422">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9e5c1-423">針對 SQL Server 資料庫，實體屬性上的 `[Timestamp]` 屬性會定義為位元組陣列：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-423">For a SQL Server database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="9e5c1-424">使資料行包含在 DELETE 和 UPDATE WHERE 子句中。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-424">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="9e5c1-425">將資料庫中的資料行型別設為 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-425">Sets the column type in the database to [rowversion](/sql/t-sql/data-types/rowversion-transact-sql).</span></span>

<span data-ttu-id="9e5c1-426">資料庫會產生循序的資料列版本號碼，會在每一次更新資料列時遞增。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-426">The database generates a sequential row version number that's incremented each time the row is updated.</span></span> <span data-ttu-id="9e5c1-427">在 `Update` 或 `Delete` 命令中，`Where` 子句會包含擷取到的資料列版本值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-427">In an `Update` or `Delete` command, the `Where` clause includes the fetched row version value.</span></span> <span data-ttu-id="9e5c1-428">若正在更新的資料列從擷取之後已產生變更：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-428">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="9e5c1-429">目前的資料列版本值會與所擷取值不相符。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-429">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="9e5c1-430">`Update` 或 `Delete` 命令會因為 `Where` 子句尋找的是所擷取資料列版本值，而找不到資料列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-430">The `Update` or `Delete` commands don't find a row because the `Where` clause looks for the fetched row version value.</span></span>
* <span data-ttu-id="9e5c1-431">於是便會擲回 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-431">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="9e5c1-432">下列程式碼顯示了當 Department 名稱更新時，由 EF Core 產生的一部分 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-432">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

<span data-ttu-id="9e5c1-433">上述醒目提示程式碼顯示 `WHERE` 子句中包含 `RowVersion`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-433">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="9e5c1-434">若資料庫 `RowVersion` 與 `RowVersion` 參數 (`@p2`) 不相同，便不會更新任何資料列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-434">If the database `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="9e5c1-435">下列醒目提示程式碼顯示驗證確實有一個資料列獲得更新的 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-435">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

<span data-ttu-id="9e5c1-436">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 傳回最後一個語句所影響的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-436">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="9e5c1-437">若沒有更新任何資料列，則 EF Core 會擲回 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-437">If no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9e5c1-439">針對 SQLite 資料庫，實體屬性上的 `[Timestamp]` 屬性會定義為位元組陣列：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-439">For a SQLite database, the `[Timestamp]` attribute on an entity property defined as byte array:</span></span>

* <span data-ttu-id="9e5c1-440">使資料行包含在 DELETE 和 UPDATE WHERE 子句中。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-440">Causes the column to be included in DELETE and UPDATE WHERE clauses.</span></span>
* <span data-ttu-id="9e5c1-441">對應到 BLOB 資料行型別。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-441">Maps to a BLOB column type.</span></span>

<span data-ttu-id="9e5c1-442">資料庫觸發程序會在每次資料列更新時，使用新的隨機位元組陣列更新 RowVersion 資料行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-442">Database triggers update the RowVersion column with a new random byte array whenever a row is updated.</span></span> <span data-ttu-id="9e5c1-443">在 `Update` 或 `Delete` 命令中，`Where` 子句會包含 RowVersion 資料行的擷取值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-443">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of the RowVersion column.</span></span> <span data-ttu-id="9e5c1-444">若正在更新的資料列從擷取之後已產生變更：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-444">If the row being updated has changed since it was fetched:</span></span>

* <span data-ttu-id="9e5c1-445">目前的資料列版本值會與所擷取值不相符。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-445">The current row version value doesn't match the fetched value.</span></span>
* <span data-ttu-id="9e5c1-446">`Update` 或 `Delete` 命令會因為 `Where` 子句尋找的是原始資料列版本值而找不到資料列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-446">The `Update` or `Delete` command doesn't find a row because the `Where` clause looks for the original row version value.</span></span>
* <span data-ttu-id="9e5c1-447">於是便會擲回 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-447">A `DbUpdateConcurrencyException` is thrown.</span></span>

---

### <a name="update-the-database"></a><span data-ttu-id="9e5c1-448">更新資料庫</span><span class="sxs-lookup"><span data-stu-id="9e5c1-448">Update the database</span></span>

<span data-ttu-id="9e5c1-449">新增 `RowVersion` 屬性會變更資料模型，因此您將需要進行移轉。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-449">Adding the `RowVersion` property changes the data model, which requires a migration.</span></span>

<span data-ttu-id="9e5c1-450">建置專案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-450">Build the project.</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-451">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-451">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9e5c1-452">在 PMC 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-452">Run the following command in the PMC:</span></span>

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-453">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-453">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9e5c1-454">在終端機中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-454">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

<span data-ttu-id="9e5c1-455">此命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-455">This command:</span></span>

* <span data-ttu-id="9e5c1-456">建立 *Migrations/{time stamp}_RowVersion.cs* 移轉檔案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-456">Creates the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="9e5c1-457">更新 *Migrations/SchoolContextModelSnapshot.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-457">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="9e5c1-458">更新會將下列醒目提示程式碼新增至 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-458">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-459">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-459">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9e5c1-460">在 PMC 中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-460">Run the following command in the PMC:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-461">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-461">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9e5c1-462">開啟 `Migrations/<timestamp>_RowVersion.cs` 檔案並新增醒目提示的程式碼：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-462">Open the `Migrations/<timestamp>_RowVersion.cs` file and add the highlighted code:</span></span>

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  <span data-ttu-id="9e5c1-463">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-463">The preceding code:</span></span>

  * <span data-ttu-id="9e5c1-464">使用隨機 Blob 值更新現有資料列。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-464">Updates existing rows with random blob values.</span></span>
  * <span data-ttu-id="9e5c1-465">新增資料庫觸發程序，在每次資料列更新時將 RowVersion 資料行設為隨機 Blob 值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-465">Adds database triggers that set the RowVersion column to a random blob value whenever a row is updated.</span></span>

* <span data-ttu-id="9e5c1-466">在終端機中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-466">Run the following command in a terminal:</span></span>

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a><span data-ttu-id="9e5c1-467">Scaffold Department 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-467">Scaffold Department pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-468">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-468">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="9e5c1-469">遵循 [Scaffold Student 頁面](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，下列部分除外：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-469">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

* <span data-ttu-id="9e5c1-470">建立 *Pages/Departments* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-470">Create a *Pages/Departments* folder.</span></span>  
* <span data-ttu-id="9e5c1-471">使用 `Department` 作為模型類別。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-471">Use `Department` for the model class.</span></span>
  * <span data-ttu-id="9e5c1-472">使用現有內容類別，而非建立新的類別。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-472">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-473">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-473">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9e5c1-474">建立 *Pages/Departments* 資料夾。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-474">Create a *Pages/Departments* folder.</span></span>

* <span data-ttu-id="9e5c1-475">執行下列命令來 Scaffold Department 頁面。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-475">Run the following command to scaffold the Department pages.</span></span>

  <span data-ttu-id="9e5c1-476">**在 Windows 上：**</span><span class="sxs-lookup"><span data-stu-id="9e5c1-476">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  <span data-ttu-id="9e5c1-477">**在 Linux 或 macOS 上：**</span><span class="sxs-lookup"><span data-stu-id="9e5c1-477">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="9e5c1-478">建置專案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-478">Build the project.</span></span>

## <a name="update-the-index-page"></a><span data-ttu-id="9e5c1-479">更新 Index 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-479">Update the Index page</span></span>

<span data-ttu-id="9e5c1-480">Scaffolding 工具會為 Index 頁面建立 `RowVersion` 資料行，但該欄位不會在生產應用程式中顯示。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-480">The scaffolding tool created a `RowVersion` column for the Index page, but that field wouldn't be displayed in a production app.</span></span> <span data-ttu-id="9e5c1-481">在本教學課程中，會顯示 `RowVersion` 的最後一個位元組，來協助示範並行處理的運作方式。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-481">In this tutorial, the last byte of the `RowVersion` is displayed to help show how concurrency handling works.</span></span> <span data-ttu-id="9e5c1-482">最後一個位元組本身不一定是唯一的。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-482">The last byte isn't guaranteed to be unique by itself.</span></span>

<span data-ttu-id="9e5c1-483">更新 *Pages\Departments\Index.cshtml* 頁面：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-483">Update *Pages\Departments\Index.cshtml* page:</span></span>

* <span data-ttu-id="9e5c1-484">使用 Departments 取代 Index。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-484">Replace Index with Departments.</span></span>
* <span data-ttu-id="9e5c1-485">變更包含 `RowVersion` 的程式碼，僅顯示位元組陣列的最後一個位元組。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-485">Change the code containing `RowVersion` to show just the last byte of the byte array.</span></span>
* <span data-ttu-id="9e5c1-486">使用 FullName 取代 FirstMidName。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-486">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="9e5c1-487">下列程式碼會顯示更新後的頁面：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-487">The following code shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a><span data-ttu-id="9e5c1-488">更新 [編輯] 頁面模型</span><span class="sxs-lookup"><span data-stu-id="9e5c1-488">Update the Edit page model</span></span>

<span data-ttu-id="9e5c1-489">使用下列程式碼更新 *Pages\Departments\Edit.cshtml.cs* ：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-489">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<span data-ttu-id="9e5c1-490">在 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> `rowVersion` 方法中提取時，會以實體中的值更新 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-490">The <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> is updated with the `rowVersion` value from the entity when it was fetched in the `OnGetAsync` method.</span></span> <span data-ttu-id="9e5c1-491">EF Core 會產生一個帶有包含了原始 `RowVersion` 值 WHERE 子句的 SQL UPDATE 命令。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-491">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="9e5c1-492">若 UPDATE 命令並未影響任何資料列 (即沒有任何資料列具有原始的 `RowVersion` 值)，則便會擲回 `DbUpdateConcurrencyException` 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-492">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

<span data-ttu-id="9e5c1-493">在上述醒目提示的程式碼中：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-493">In the preceding highlighted code:</span></span>

* <span data-ttu-id="9e5c1-494">`Department.RowVersion` 中的值是原先在 Edit 頁面 Get 要求中所擷取實體中的內容。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-494">The value in `Department.RowVersion` is what was in the entity when it was originally fetched in the Get request for the Edit page.</span></span> <span data-ttu-id="9e5c1-495">此值會 `OnPost` 由頁面中隱藏的欄位提供給方法，該欄位 Razor 會顯示要編輯的實體。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-495">The value is provided to the `OnPost` method by a hidden field in the Razor page that displays the entity to be edited.</span></span> <span data-ttu-id="9e5c1-496">隱藏欄位的值會由模型繫結器複製到 `Department.RowVersion`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-496">The hidden field value is copied to `Department.RowVersion` by the model binder.</span></span>
* <span data-ttu-id="9e5c1-497">`OriginalValue` 是 EF Core 將在 Where 子句中使用的內容。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-497">`OriginalValue` is what EF Core will use in the Where clause.</span></span> <span data-ttu-id="9e5c1-498">在執行醒目提示的程式碼區段前，`OriginalValue` 會擁有在此方法中呼叫 `FirstOrDefaultAsync` 時原先在資料庫內的值，而該值可能會和 Edit 頁面上顯示的內容不同。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-498">Before the highlighted line of code executes, `OriginalValue` has the value that was in the database when `FirstOrDefaultAsync` was called in this method, which might be different from what was displayed on the Edit page.</span></span>
* <span data-ttu-id="9e5c1-499">醒目提示的程式碼會確保 EF Core 使用 SQL UPDATE 陳述式 WHERE 子句中所顯示 `Department` 實體原始 `RowVersion` 值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-499">The highlighted code makes sure that EF Core uses the original `RowVersion` value from the displayed `Department` entity in the SQL UPDATE statement's Where clause.</span></span>

<span data-ttu-id="9e5c1-500">發生並行錯誤時，下列醒目提示的程式碼會取得用戶端值 (POST 到此方法的值) 及資料庫值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-500">When a concurrency error happens, the following highlighted code gets the client values (the values posted to this method) and the database values.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

<span data-ttu-id="9e5c1-501">下列程式碼會為每個其資料庫中值與 POST 到 `OnPostAsync` 值的不同資料行新增一個自訂錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-501">The following code adds a custom error message for each column that has database values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

<span data-ttu-id="9e5c1-502">下列醒目提示的程式碼會將 `RowVersion` 值設為從資料庫所擷取新值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-502">The following highlighted code sets the `RowVersion` value to the new value retrieved from the database.</span></span> <span data-ttu-id="9e5c1-503">下一次當使用者按一下 [儲存] 時，只有在上一次顯示 [編輯] 頁面之後發生的並行錯誤會被捕捉到。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-503">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

<span data-ttu-id="9e5c1-504">`ModelState.Remove` 陳述式是必須的，因為 `ModelState` 具有舊的 `RowVersion` 值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-504">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="9e5c1-505">在 Razor 頁面中， `ModelState` 當兩個欄位都存在時，欄位的值會優先于模型屬性值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-505">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

### <a name="update-the-edit-page"></a><span data-ttu-id="9e5c1-506">更新 [編輯] 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-506">Update the Edit page</span></span>

<span data-ttu-id="9e5c1-507">使用下列程式碼更新 *Pages/Departments/Edit.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-507">Update *Pages/Departments/Edit.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="9e5c1-508">上述程式碼：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-508">The preceding code:</span></span>

* <span data-ttu-id="9e5c1-509">將 `page` 指示詞從 `@page` 更新為 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-509">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9e5c1-510">新增一個隱藏的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-510">Adds a hidden row version.</span></span> <span data-ttu-id="9e5c1-511">`RowVersion` 必須加入，以便回傳系結值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-511">`RowVersion` must be added so postback binds the value.</span></span>
* <span data-ttu-id="9e5c1-512">顯示 `RowVersion` 的最後一個位元組，作為偵錯用途。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-512">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="9e5c1-513">使用強型別的 `InstructorNameSL` 取代 `ViewData`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-513">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

### <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="9e5c1-514">使用 [編輯] 頁面測試並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-514">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="9e5c1-515">開啟兩個英文部門上 [編輯] 頁面的瀏覽器執行個體：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-515">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="9e5c1-516">執行應用程式並選取 Departments。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-516">Run the app and select Departments.</span></span>
* <span data-ttu-id="9e5c1-517">以滑鼠右鍵按一下英文部門的 **編輯** 超連結，然後選取 [Open in new tab] (在新索引標籤中開啟)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-517">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="9e5c1-518">在第一個索引標籤中，按一下英文部門的 **編輯** 超連結。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-518">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="9e5c1-519">兩個瀏覽器索引標籤會顯示相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-519">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9e5c1-520">在第一個瀏覽器索引標籤中變更名稱，然後按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-520">Change the name in the first browser tab and click **Save**.</span></span>

![變更之後的 Department [編輯] 頁面 1](concurrency/_static/edit-after-change-130.png)

<span data-ttu-id="9e5c1-522">瀏覽器會顯示 [索引] 頁面，當中包含了變更之後的值和更新後的 rowVersion 指標。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-522">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="9e5c1-523">請注意更新後的 rowVersion 指標。它會顯示在另一個索引標籤中的第二個回傳上。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-523">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9e5c1-524">在第二個瀏覽器索引標籤中變更不同欄位。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-524">Change a different field in the second browser tab.</span></span>

![變更之後的 Department [編輯] 頁面 2](concurrency/_static/edit-after-change-230.png)

<span data-ttu-id="9e5c1-526">按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-526">Click **Save**.</span></span> <span data-ttu-id="9e5c1-527">您會看到所有不符合資料庫值欄位的錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-527">You see error messages for all fields that don't match the database values:</span></span>

![Department [編輯] 頁面錯誤訊息](concurrency/_static/edit-error30.png)

<span data-ttu-id="9e5c1-529">此瀏覽器視窗並未嘗試變更 [名稱] 欄位。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-529">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="9e5c1-530">複製並將目前的值 (語言 (Language)) 貼上 [名稱] 欄位。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-530">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="9e5c1-531">Tab 鍵移出。用戶端驗證會移除錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-531">Tab out. Client-side validation removes the error message.</span></span>

<span data-ttu-id="9e5c1-532">再按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-532">Click **Save** again.</span></span> <span data-ttu-id="9e5c1-533">您在第二個瀏覽器索引標籤中輸入的值已儲存。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-533">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="9e5c1-534">您會在 [索引] 頁面中看到儲存的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-534">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page-model"></a><span data-ttu-id="9e5c1-535">更新刪除頁面模型</span><span class="sxs-lookup"><span data-stu-id="9e5c1-535">Update the Delete page model</span></span>

<span data-ttu-id="9e5c1-536">使用下列程式碼更新 *ges/Departments/Delete.cshtml.cs*：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-536">Update *Pages/Departments/Delete.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="9e5c1-537">[刪除] 頁面會在實體擷取之後發生變更時偵測並行衝突。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-537">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="9e5c1-538">`Department.RowVersion` 是擷取實體時的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-538">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="9e5c1-539">當 EF Core 建立 SQL DELETE 命令時，它會包含一個帶有 `RowVersion` 的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-539">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="9e5c1-540">若 SQL DELETE 命令影響的資料列數目為零：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-540">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="9e5c1-541">SQL DELETE 命令中 `RowVersion` 與資料庫中 `RowVersion` 不相符。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-541">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the database.</span></span>
* <span data-ttu-id="9e5c1-542">擲回 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-542">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="9e5c1-543">使用 `concurrencyError` 呼叫 `OnGetAsync`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-543">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="9e5c1-544">更新 [刪除] 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-544">Update the Delete page</span></span>

<span data-ttu-id="9e5c1-545">使用下列程式碼更新 *ges/Departments/Delete.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-545">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

<span data-ttu-id="9e5c1-546">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-546">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="9e5c1-547">將 `page` 指示詞從 `@page` 更新為 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-547">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9e5c1-548">新增錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-548">Adds an error message.</span></span>
* <span data-ttu-id="9e5c1-549">在 [系統管理員] 欄位中將 FirstMidName 取代為 FullName。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-549">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="9e5c1-550">變更 `RowVersion` 以顯示最後一個位元組。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-550">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="9e5c1-551">新增一個隱藏的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-551">Adds a hidden row version.</span></span> <span data-ttu-id="9e5c1-552">`RowVersion` 必須加入，以便回傳系結值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-552">`RowVersion` must be added so postback binds the value.</span></span>

### <a name="test-concurrency-conflicts"></a><span data-ttu-id="9e5c1-553">測試並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-553">Test concurrency conflicts</span></span>

<span data-ttu-id="9e5c1-554">建立一個測試部門。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-554">Create a test department.</span></span>

<span data-ttu-id="9e5c1-555">開啟兩個測試部門上 [刪除] 頁面的瀏覽器執行個體：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-555">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="9e5c1-556">執行應用程式並選取 Departments。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-556">Run the app and select Departments.</span></span>
* <span data-ttu-id="9e5c1-557">以滑鼠右鍵按一下測試部門的 **刪除** 超連結，然後選取 [Open in new tab] (在新索引標籤中開啟)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-557">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="9e5c1-558">按一下測試部門的 **編輯** 超連結。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-558">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="9e5c1-559">兩個瀏覽器索引標籤會顯示相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-559">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9e5c1-560">在第一個瀏覽器索引標籤中變更預算，然後按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-560">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="9e5c1-561">瀏覽器會顯示 [索引] 頁面，當中包含了變更之後的值和更新後的 rowVersion 指標。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-561">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="9e5c1-562">請注意更新後的 rowVersion 指標。它會顯示在另一個索引標籤中的第二個回傳上。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-562">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9e5c1-563">從第二個索引標籤中刪除測試部門。並行錯誤會與資料庫中目前的值一起顯示。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-563">Delete the test department from the second tab. A concurrency error is display with the current values from the database.</span></span> <span data-ttu-id="9e5c1-564">除非已更新，否則按一下 [ **刪除** ] 會刪除實體 `RowVersion` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-564">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e5c1-565">其他資源</span><span class="sxs-lookup"><span data-stu-id="9e5c1-565">Additional resources</span></span>

* [<span data-ttu-id="9e5c1-566">EF Core 中的並行權杖</span><span class="sxs-lookup"><span data-stu-id="9e5c1-566">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="9e5c1-567">在 EF Core 中處理並行</span><span class="sxs-lookup"><span data-stu-id="9e5c1-567">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="9e5c1-568">偵錯 ASP.NET Core 2.x 原始檔</span><span class="sxs-lookup"><span data-stu-id="9e5c1-568">Debugging ASP.NET Core 2.x source</span></span>](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a><span data-ttu-id="9e5c1-569">下一步</span><span class="sxs-lookup"><span data-stu-id="9e5c1-569">Next steps</span></span>

<span data-ttu-id="9e5c1-570">這是一系列教學課程的最後一個教學課程。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-570">This is the last tutorial in the series.</span></span> <span data-ttu-id="9e5c1-571">[本教學課程系列的 MVC 版本](xref:data/ef-mvc/index)涵蓋了其他主題。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-571">Additional topics are covered in the [MVC version of this tutorial series](xref:data/ef-mvc/index).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9e5c1-572">上一個教學課程</span><span class="sxs-lookup"><span data-stu-id="9e5c1-572">Previous tutorial</span></span>](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9e5c1-573">本教學課程會顯示如何在多位使用者同時並行更新實體時處理衝突。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-573">This tutorial shows how to handle conflicts when multiple users update an entity concurrently (at the same time).</span></span> <span data-ttu-id="9e5c1-574">若您遇到無法解決的問題，請[下載或檢視完整應用程式。](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)</span><span class="sxs-lookup"><span data-stu-id="9e5c1-574">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="9e5c1-575">[下載指示](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-575">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="concurrency-conflicts"></a><span data-ttu-id="9e5c1-576">並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-576">Concurrency conflicts</span></span>

<span data-ttu-id="9e5c1-577">並行衝突發生的時機：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-577">A concurrency conflict occurs when:</span></span>

* <span data-ttu-id="9e5c1-578">使用者巡覽至實體的編輯頁面。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-578">A user navigates to the edit page for an entity.</span></span>
* <span data-ttu-id="9e5c1-579">另一個使用者在第一個使用者的變更寫入到資料庫前更新了相同的實體。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-579">Another user updates the same entity before the first user's change is written to the DB.</span></span>

<span data-ttu-id="9e5c1-580">當未啟用並行偵測時卻發生並行存取時：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-580">If concurrency detection isn't enabled, when concurrent updates occur:</span></span>

* <span data-ttu-id="9e5c1-581">最後作出的更新會成功。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-581">The last update wins.</span></span> <span data-ttu-id="9e5c1-582">亦即，最後更新的值會儲存到資料庫。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-582">That is, the last update values are saved to the DB.</span></span>
* <span data-ttu-id="9e5c1-583">第一個作出的更新會遺失。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-583">The first of the current updates are lost.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="9e5c1-584">開放式並行存取</span><span class="sxs-lookup"><span data-stu-id="9e5c1-584">Optimistic concurrency</span></span>

<span data-ttu-id="9e5c1-585">開放式並行存取允許並行衝突發生，並會在衝突發生時適當的作出反應。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-585">Optimistic concurrency allows concurrency conflicts to happen, and then reacts appropriately when they do.</span></span> <span data-ttu-id="9e5c1-586">例如，Jane 造訪了 Department 編輯頁面，然後將英文部門的預算從美金 $350,000.00 元調整到美金 $0.00 元。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-586">For example, Jane visits the Department edit page and changes the budget for the English department from $350,000.00 to $0.00.</span></span>

![將預算變更為 0](concurrency/_static/change-budget.png)

<span data-ttu-id="9e5c1-588">在 Jane 按一下 [儲存] 前，John 造訪了相同的頁面並將 [開始日期] 欄位從 2007/9/1 變更為 2013/9/1。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-588">Before Jane clicks **Save**, John visits the same page and changes the Start Date field from 9/1/2007 to 9/1/2013.</span></span>

![將開始日期變更為 2013 年](concurrency/_static/change-date.png)

<span data-ttu-id="9e5c1-590">Jane 先按了一下 [儲存]，並且在瀏覽器顯示 [索引] 頁面時看到她作出的變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-590">Jane clicks **Save** first and sees her change when the browser displays the Index page.</span></span>

![預算已變更為 0](concurrency/_static/budget-zero.png)

<span data-ttu-id="9e5c1-592">John 在仍然顯示預算為美金 $350,000.00 的 [編輯] 頁面上按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-592">John clicks **Save** on an Edit page that still shows a budget of $350,000.00.</span></span> <span data-ttu-id="9e5c1-593">接下來發生的情況便是由您處理並行衝突的方式決定。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-593">What happens next is determined by how you handle concurrency conflicts.</span></span>

<span data-ttu-id="9e5c1-594">開放式並行存取包含下列選項：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-594">Optimistic concurrency includes the following options:</span></span>

* <span data-ttu-id="9e5c1-595">您可以追蹤使用者修改的屬性，然後僅在資料庫中更新相對應的資料行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-595">You can keep track of which property a user has modified and update only the corresponding columns in the DB.</span></span>

  <span data-ttu-id="9e5c1-596">在此案例中，您將不會遺失任何資料。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-596">In the scenario, no data would be lost.</span></span> <span data-ttu-id="9e5c1-597">兩位使用者更新了不同的屬性。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-597">Different properties were updated by the two users.</span></span> <span data-ttu-id="9e5c1-598">下一次當有人瀏覽英文部門時，他們便會同時看到 Jane 和 John 所作出的變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-598">The next time someone browses the English department, they will see both Jane's and John's changes.</span></span> <span data-ttu-id="9e5c1-599">這種更新方法可以減少可能會導致資料遺失的衝突數目。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-599">This method of updating can reduce the number of conflicts that could result in data loss.</span></span> <span data-ttu-id="9e5c1-600">這種方法：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-600">This approach:</span></span>
 
  * <span data-ttu-id="9e5c1-601">無法在使用者更新相同屬性時避免資料遺失。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-601">Can't avoid data loss if competing changes are made to the same property.</span></span>
  * <span data-ttu-id="9e5c1-602">表示通常在 Web 應用程式中不實用。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-602">Is generally not practical in a web app.</span></span> <span data-ttu-id="9e5c1-603">它必須維持大量的狀態來追蹤所有擷取的值和新的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-603">It requires maintaining significant state in order to keep track of all fetched values and new values.</span></span> <span data-ttu-id="9e5c1-604">維持大量的狀態可能會影響應用程式的效能。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-604">Maintaining large amounts of state can affect app performance.</span></span>
  * <span data-ttu-id="9e5c1-605">與實體上的並行偵測相較之下，可能會增加應用程式的複雜度。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-605">Can increase app complexity compared to concurrency detection on an entity.</span></span>

* <span data-ttu-id="9e5c1-606">您可以讓 John 的變更覆寫 Jane 的變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-606">You can let John's change overwrite Jane's change.</span></span>

  <span data-ttu-id="9e5c1-607">下一次當有人瀏覽英文部門時，他們便會看到開始日期為 2013/9/1，以及擷取的美金 $350,000.00 元預算金額。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-607">The next time someone browses the English department, they will see 9/1/2013 and the fetched $350,000.00 value.</span></span> <span data-ttu-id="9e5c1-608">這稱之為「用戶端獲勝 (Client Wins)」或「最後寫入為準 (Last in Wins)」案例。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-608">This approach is called a *Client Wins* or *Last in Wins* scenario.</span></span> <span data-ttu-id="9e5c1-609"> (用戶端的所有值都優先于資料存放區中的內容。 ) 如果您沒有針對並行處理進行任何程式碼撰寫，用戶端會自動進行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-609">(All values from the client take precedence over what's in the data store.) If you don't do any coding for concurrency handling, Client Wins happens automatically.</span></span>

* <span data-ttu-id="9e5c1-610">您可以防止 John 的變更更新到資料庫中。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-610">You can prevent John's change from being updated in the DB.</span></span> <span data-ttu-id="9e5c1-611">一般而言，應用程式會：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-611">Typically, the app would:</span></span>

  * <span data-ttu-id="9e5c1-612">顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-612">Display an error message.</span></span>
  * <span data-ttu-id="9e5c1-613">顯示資料的目前狀態。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-613">Show the current state of the data.</span></span>
  * <span data-ttu-id="9e5c1-614">允許使用者重新套用變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-614">Allow the user to reapply the changes.</span></span>

  <span data-ttu-id="9e5c1-615">這稱為「存放區獲勝 (Store Wins)」案例。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-615">This is called a *Store Wins* scenario.</span></span> <span data-ttu-id="9e5c1-616"> (資料存放區值的優先順序高於用戶端所提交的值。 ) 在本教學課程中，您將會執行 Store 獲勝案例。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-616">(The data-store values take precedence over the values submitted by the client.) You implement the Store Wins scenario in this tutorial.</span></span> <span data-ttu-id="9e5c1-617">這個方法可確保沒有任何變更會在使用者收到警示前遭到覆寫。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-617">This method ensures that no changes are overwritten without a user being alerted.</span></span>

## <a name="handling-concurrency"></a><span data-ttu-id="9e5c1-618">處理並行</span><span class="sxs-lookup"><span data-stu-id="9e5c1-618">Handling concurrency</span></span>

<span data-ttu-id="9e5c1-619">當屬性已設定為[並行權杖](/ef/core/modeling/concurrency)時：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-619">When a property is configured as a [concurrency token](/ef/core/modeling/concurrency):</span></span>

* <span data-ttu-id="9e5c1-620">EF Core 會驗證屬性在擷取之後尚未被修改。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-620">EF Core verifies that property has not been modified after it was fetched.</span></span> <span data-ttu-id="9e5c1-621">該檢查會在呼叫 [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) 或 [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) 時發生。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-621">The check occurs when [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) or [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span>
* <span data-ttu-id="9e5c1-622">若屬性在擷取之後已遭修改，則系統便會擲回 [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-622">If the property has been changed after it was fetched, a [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) is thrown.</span></span> 

<span data-ttu-id="9e5c1-623">資料庫和資料模型必須設定為支援擲回 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-623">The DB and data model must be configured to support throwing `DbUpdateConcurrencyException`.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-property"></a><span data-ttu-id="9e5c1-624">在屬性上偵測並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-624">Detecting concurrency conflicts on a property</span></span>

<span data-ttu-id="9e5c1-625">您可以使用 [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) 屬性來在屬性層級上偵測並行衝突。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-625">Concurrency conflicts can be detected at the property level with the [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) attribute.</span></span> <span data-ttu-id="9e5c1-626">屬性可套用至模型上的多個屬性。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-626">The attribute can be applied to multiple properties on the model.</span></span> <span data-ttu-id="9e5c1-627">如需詳細資訊，請參閱[資料註解 - ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-627">For more information, see [Data Annotations-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).</span></span>

<span data-ttu-id="9e5c1-628">此教學課程中不會使用 `[ConcurrencyCheck]` 屬性。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-628">The `[ConcurrencyCheck]` attribute isn't used in this tutorial.</span></span>

### <a name="detecting-concurrency-conflicts-on-a-row"></a><span data-ttu-id="9e5c1-629">在資料列上偵測並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-629">Detecting concurrency conflicts on a row</span></span>

<span data-ttu-id="9e5c1-630">為了偵測並行衝突，一個 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 追蹤資料行會新增到模型中。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-630">To detect concurrency conflicts, a [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) tracking column is added to the model.</span></span>  <span data-ttu-id="9e5c1-631">`rowversion` :</span><span class="sxs-lookup"><span data-stu-id="9e5c1-631">`rowversion` :</span></span>

* <span data-ttu-id="9e5c1-632">是 SQL Server 的特定功能。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-632">Is SQL Server specific.</span></span> <span data-ttu-id="9e5c1-633">其他資料庫可能不會提供類似的功能。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-633">Other databases may not provide a similar feature.</span></span>
* <span data-ttu-id="9e5c1-634">是用來判斷實體在從資料庫擷取之後是否有變更。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-634">Is used to determine that an entity has not been changed since it was fetched from the DB.</span></span> 

<span data-ttu-id="9e5c1-635">資料庫會產生一個循序 `rowversion` 數字，每一次資料列更新時該數字都會遞增。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-635">The DB generates a sequential `rowversion` number that's incremented each time the row is updated.</span></span> <span data-ttu-id="9e5c1-636">在 `Update` 或 `Delete` 命令中，`Where` 子句會包含 `rowversion` 的擷取值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-636">In an `Update` or `Delete` command, the `Where` clause includes the fetched value of `rowversion`.</span></span> <span data-ttu-id="9e5c1-637">如果更新的資料列已變更：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-637">If the row being updated has changed:</span></span>

* <span data-ttu-id="9e5c1-638">`rowversion` 便不會符合擷取的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-638">`rowversion` doesn't match the fetched value.</span></span>
* <span data-ttu-id="9e5c1-639">`Update` 或 `Delete` 命令便會找不到資料列，因為 `Where` 子句包含了擷取的 `rowversion`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-639">The `Update` or `Delete` commands don't find a row because the `Where` clause includes the fetched `rowversion`.</span></span>
* <span data-ttu-id="9e5c1-640">於是便會擲回 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-640">A `DbUpdateConcurrencyException` is thrown.</span></span>

<span data-ttu-id="9e5c1-641">在 EF Core 中，當 `Update` 或 `Delete` 命令沒有更新任何資料列時，系統便會擲回並行例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-641">In EF Core, when no rows have been updated by an `Update` or `Delete` command, a concurrency exception is thrown.</span></span>

### <a name="add-a-tracking-property-to-the-department-entity"></a><span data-ttu-id="9e5c1-642">將追蹤屬性新增到 Department 實體</span><span class="sxs-lookup"><span data-stu-id="9e5c1-642">Add a tracking property to the Department entity</span></span>

<span data-ttu-id="9e5c1-643">在 *Models/Department.cs* 中，新增一個名為 RowVersion 的追蹤屬性：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-643">In *Models/Department.cs*, add a tracking property named RowVersion:</span></span>

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

<span data-ttu-id="9e5c1-644">[Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) 屬性表示此資料行會包含在 `Update` 和 `Delete` 命令的 `Where` 子句中。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-644">The [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) attribute specifies that this column is included in the `Where` clause of `Update` and `Delete` commands.</span></span> <span data-ttu-id="9e5c1-645">該屬性稱為 `Timestamp`，因為先前版本的 SQL Server 在以 SQL `rowversion` 類型取代之前使用了 SQL `timestamp` 資料類型。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-645">The attribute is called `Timestamp` because previous versions of SQL Server used a SQL `timestamp` data type before the SQL `rowversion` type replaced it.</span></span>

<span data-ttu-id="9e5c1-646">Fluent API 也可以指定追蹤屬性：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-646">The fluent API can also specify the tracking property:</span></span>

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

<span data-ttu-id="9e5c1-647">下列程式碼顯示了當 Department 名稱更新時，由 EF Core 產生的一部分 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-647">The following code shows a portion of the T-SQL generated by EF Core when the Department name is updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

<span data-ttu-id="9e5c1-648">上述醒目提示程式碼顯示 `WHERE` 子句中包含 `RowVersion`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-648">The preceding highlighted code shows the `WHERE` clause containing `RowVersion`.</span></span> <span data-ttu-id="9e5c1-649">若資料庫 `RowVersion` 不等於 `RowVersion` 參數 (`@p2`)，則沒有任何資料列會獲得更新。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-649">If the DB `RowVersion` doesn't equal the `RowVersion` parameter (`@p2`), no rows are updated.</span></span>

<span data-ttu-id="9e5c1-650">下列醒目提示程式碼顯示驗證確實有一個資料列獲得更新的 T-SQL：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-650">The following highlighted code shows the T-SQL that verifies exactly one row was updated:</span></span>

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

<span data-ttu-id="9e5c1-651">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) 傳回最後一個語句所影響的資料列數目。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-651">[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) returns the number of rows affected by the last statement.</span></span> <span data-ttu-id="9e5c1-652">當沒有更新任何資料列時，EF Core 便會擲回 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-652">In no rows are updated, EF Core throws a `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="9e5c1-653">您可以在 Visual Studio 的輸出視窗中看到 EF Core 產生的 T-SQL。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-653">You can see the T-SQL EF Core generates in the output window of Visual Studio.</span></span>

### <a name="update-the-db"></a><span data-ttu-id="9e5c1-654">更新資料庫</span><span class="sxs-lookup"><span data-stu-id="9e5c1-654">Update the DB</span></span>

<span data-ttu-id="9e5c1-655">新增 `RowVersion` 屬性會變更資料庫模型，因此您將需要進行移轉。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-655">Adding the `RowVersion` property changes the DB model, which requires a migration.</span></span>

<span data-ttu-id="9e5c1-656">建置專案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-656">Build the project.</span></span> <span data-ttu-id="9e5c1-657">在命令視窗中輸入下列命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-657">Enter the following in a command window:</span></span>

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

<span data-ttu-id="9e5c1-658">上述命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-658">The preceding commands:</span></span>

* <span data-ttu-id="9e5c1-659">新增一個 *Migrations/{time stamp}_RowVersion.cs* 移轉檔案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-659">Adds the *Migrations/{time stamp}_RowVersion.cs* migration file.</span></span>
* <span data-ttu-id="9e5c1-660">更新 *Migrations/SchoolContextModelSnapshot.cs* 檔案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-660">Updates the *Migrations/SchoolContextModelSnapshot.cs* file.</span></span> <span data-ttu-id="9e5c1-661">更新會將下列醒目提示程式碼新增至 `BuildModel` 方法：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-661">The update adds the following highlighted code to the `BuildModel` method:</span></span>

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* <span data-ttu-id="9e5c1-662">執行移轉，以更新資料庫。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-662">Runs migrations to update the DB.</span></span>

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a><span data-ttu-id="9e5c1-663">Scaffold Departments 模型</span><span class="sxs-lookup"><span data-stu-id="9e5c1-663">Scaffold the Departments model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9e5c1-664">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9e5c1-664">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="9e5c1-665">請遵循[建立學生結構模型](xref:data/ef-rp/intro#scaffold-student-pages)中的指示，並為模型類別使用 `Department`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-665">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-student-pages) and use `Department` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9e5c1-666">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9e5c1-666">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="9e5c1-667">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-667">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

<span data-ttu-id="9e5c1-668">上述命令會 Scaffold `Department` 模型。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-668">The preceding command scaffolds the `Department` model.</span></span> <span data-ttu-id="9e5c1-669">在 Visual Studio 中開啟專案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-669">Open the project in Visual Studio.</span></span>

<span data-ttu-id="9e5c1-670">建置專案。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-670">Build the project.</span></span>

### <a name="update-the-departments-index-page"></a><span data-ttu-id="9e5c1-671">更新 Departments [索引] 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-671">Update the Departments Index page</span></span>

<span data-ttu-id="9e5c1-672">Scaffolding 引擎會在 [索引] 頁面中建立 `RowVersion` 資料行，但該欄位不應該顯示出來。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-672">The scaffolding engine created a `RowVersion` column for the Index page, but that field shouldn't be displayed.</span></span> <span data-ttu-id="9e5c1-673">在本教學課程中，`RowVersion` 的最後一個位元組會顯示出來，以協助您了解並行。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-673">In this tutorial, the last byte of the `RowVersion` is displayed to help understand concurrency.</span></span> <span data-ttu-id="9e5c1-674">最後一個位元組不一定是唯一的。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-674">The last byte isn't guaranteed to be unique.</span></span> <span data-ttu-id="9e5c1-675">實際的應用程式不會顯示 `RowVersion` 或 `RowVersion` 的最後一個位元組。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-675">A real app wouldn't display `RowVersion` or the last byte of `RowVersion`.</span></span>

<span data-ttu-id="9e5c1-676">更新 [索引] 頁面：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-676">Update the Index page:</span></span>

* <span data-ttu-id="9e5c1-677">使用 Departments 取代 Index。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-677">Replace Index with Departments.</span></span>
* <span data-ttu-id="9e5c1-678">使用 `RowVersion` 的最後一個位元組取代包含 `RowVersion` 的標記。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-678">Replace the markup containing `RowVersion` with the last byte of `RowVersion`.</span></span>
* <span data-ttu-id="9e5c1-679">使用 FullName 取代 FirstMidName。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-679">Replace FirstMidName with FullName.</span></span>

<span data-ttu-id="9e5c1-680">下列標記顯示了更新後的頁面：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-680">The following markup shows the updated page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a><span data-ttu-id="9e5c1-681">更新 [編輯] 頁面模型</span><span class="sxs-lookup"><span data-stu-id="9e5c1-681">Update the Edit page model</span></span>

<span data-ttu-id="9e5c1-682">使用下列程式碼更新 *Pages\Departments\Edit.cshtml.cs* ：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-682">Update *Pages\Departments\Edit.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

<span data-ttu-id="9e5c1-683">為了偵測並行問題，系統會使用擷取實體時的 `rowVersion` 值更新 [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-683">To detect a concurrency issue, the [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) is updated with the `rowVersion` value from the entity it was fetched.</span></span> <span data-ttu-id="9e5c1-684">EF Core 會產生一個帶有包含了原始 `RowVersion` 值 WHERE 子句的 SQL UPDATE 命令。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-684">EF Core generates a SQL UPDATE command with a WHERE clause containing the original `RowVersion` value.</span></span> <span data-ttu-id="9e5c1-685">若 UPDATE 命令並未影響任何資料列 (即沒有任何資料列具有原始的 `RowVersion` 值)，則便會擲回 `DbUpdateConcurrencyException` 例外狀況。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-685">If no rows are affected by the UPDATE command (no rows have the original `RowVersion` value), a `DbUpdateConcurrencyException` exception is thrown.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

<span data-ttu-id="9e5c1-686">在上述程式碼中，`Department.RowVersion` 的值為擷取實體時的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-686">In the preceding code, `Department.RowVersion` is the value when the entity was fetched.</span></span> <span data-ttu-id="9e5c1-687">`OriginalValue` 的值為此方法呼叫 `FirstOrDefaultAsync` 時在資料庫中的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-687">`OriginalValue` is the value in the DB when `FirstOrDefaultAsync` was called in this method.</span></span>

<span data-ttu-id="9e5c1-688">下列程式碼會取得用戶端的值 (POST 到此方法的值) 以及資料庫的值：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-688">The following code gets the client values (the values posted to this method) and the DB values:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

<span data-ttu-id="9e5c1-689">下列程式碼會為每個資料庫中的值與發佈到 `OnPostAsync` 的值不同的資料行新增一個自訂錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-689">The following code adds a custom error message for each column that has DB values different from what was posted to `OnPostAsync`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

<span data-ttu-id="9e5c1-690">下列醒目提示程式碼會將 `RowVersion` 的值設為從資料庫取得的新值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-690">The following highlighted code sets the `RowVersion` value to the new value retrieved from the DB.</span></span> <span data-ttu-id="9e5c1-691">下一次當使用者按一下 [儲存] 時，只有在上一次顯示 [編輯] 頁面之後發生的並行錯誤會被捕捉到。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-691">The next time the user clicks **Save**, only concurrency errors that happen since the last display of the Edit page will be caught.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

<span data-ttu-id="9e5c1-692">`ModelState.Remove` 陳述式是必須的，因為 `ModelState` 具有舊的 `RowVersion` 值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-692">The `ModelState.Remove` statement is required because `ModelState` has the old `RowVersion` value.</span></span> <span data-ttu-id="9e5c1-693">在 Razor 頁面中， `ModelState` 當兩個欄位都存在時，欄位的值會優先于模型屬性值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-693">In the Razor Page, the `ModelState` value for a field takes precedence over the model property values when both are present.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="9e5c1-694">更新 [編輯] 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-694">Update the Edit page</span></span>

<span data-ttu-id="9e5c1-695">以下列標記更新 *Pages/Departments/Edit.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-695">Update *Pages/Departments/Edit.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

<span data-ttu-id="9e5c1-696">上述標記：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-696">The preceding markup:</span></span>

* <span data-ttu-id="9e5c1-697">將 `page` 指示詞從 `@page` 更新為 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-697">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9e5c1-698">新增一個隱藏的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-698">Adds a hidden row version.</span></span> <span data-ttu-id="9e5c1-699">您必須新增 `RowVersion`，以讓回傳繫結值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-699">`RowVersion` must be added so post back binds the value.</span></span>
* <span data-ttu-id="9e5c1-700">顯示 `RowVersion` 的最後一個位元組，作為偵錯用途。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-700">Displays the last byte of `RowVersion` for debugging purposes.</span></span>
* <span data-ttu-id="9e5c1-701">使用強型別的 `InstructorNameSL` 取代 `ViewData`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-701">Replaces `ViewData` with the strongly-typed `InstructorNameSL`.</span></span>

## <a name="test-concurrency-conflicts-with-the-edit-page"></a><span data-ttu-id="9e5c1-702">使用 [編輯] 頁面測試並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-702">Test concurrency conflicts with the Edit page</span></span>

<span data-ttu-id="9e5c1-703">開啟兩個英文部門上 [編輯] 頁面的瀏覽器執行個體：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-703">Open two browsers instances of Edit on the English department:</span></span>

* <span data-ttu-id="9e5c1-704">執行應用程式並選取 Departments。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-704">Run the app and select Departments.</span></span>
* <span data-ttu-id="9e5c1-705">以滑鼠右鍵按一下英文部門的 **編輯** 超連結，然後選取 [Open in new tab] (在新索引標籤中開啟)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-705">Right-click the **Edit** hyperlink for the English department and select **Open in new tab**.</span></span>
* <span data-ttu-id="9e5c1-706">在第一個索引標籤中，按一下英文部門的 **編輯** 超連結。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-706">In the first tab, click the **Edit** hyperlink for the English department.</span></span>

<span data-ttu-id="9e5c1-707">兩個瀏覽器索引標籤會顯示相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-707">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9e5c1-708">在第一個瀏覽器索引標籤中變更名稱，然後按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-708">Change the name in the first browser tab and click **Save**.</span></span>

![變更之後的 Department [編輯] 頁面 1](concurrency/_static/edit-after-change-1.png)

<span data-ttu-id="9e5c1-710">瀏覽器會顯示 [索引] 頁面，當中包含了變更之後的值和更新後的 rowVersion 指標。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-710">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="9e5c1-711">請注意更新後的 rowVersion 指標。它會顯示在另一個索引標籤中的第二個回傳上。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-711">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9e5c1-712">在第二個瀏覽器索引標籤中變更不同欄位。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-712">Change a different field in the second browser tab.</span></span>

![變更之後的 Department [編輯] 頁面 2](concurrency/_static/edit-after-change-2.png)

<span data-ttu-id="9e5c1-714">按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-714">Click **Save**.</span></span> <span data-ttu-id="9e5c1-715">您會看到所有不符合資料庫值之欄位的錯誤訊息：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-715">You see error messages for all fields that don't match the DB values:</span></span>

![部門編輯分頁錯誤訊息1](concurrency/_static/edit-error.png)

<span data-ttu-id="9e5c1-717">此瀏覽器視窗並未嘗試變更 [名稱] 欄位。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-717">This browser window didn't intend to change the Name field.</span></span> <span data-ttu-id="9e5c1-718">複製並將目前的值 (語言 (Language)) 貼上 [名稱] 欄位。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-718">Copy and paste the current value (Languages) into the Name field.</span></span> <span data-ttu-id="9e5c1-719">Tab 鍵移出。用戶端驗證會移除錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-719">Tab out. Client-side validation removes the error message.</span></span>

![部門編輯分頁錯誤訊息2](concurrency/_static/cv.png)

<span data-ttu-id="9e5c1-721">再按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-721">Click **Save** again.</span></span> <span data-ttu-id="9e5c1-722">您在第二個瀏覽器索引標籤中輸入的值已儲存。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-722">The value you entered in the second browser tab is saved.</span></span> <span data-ttu-id="9e5c1-723">您會在 [索引] 頁面中看到儲存的值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-723">You see the saved values in the Index page.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="9e5c1-724">更新 [刪除] 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-724">Update the Delete page</span></span>

<span data-ttu-id="9e5c1-725">以下列程式碼更新 [刪除] 頁面模型：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-725">Update the Delete page model with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

<span data-ttu-id="9e5c1-726">[刪除] 頁面會在實體擷取之後發生變更時偵測並行衝突。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-726">The Delete page detects concurrency conflicts when the entity has changed after it was fetched.</span></span> <span data-ttu-id="9e5c1-727">`Department.RowVersion` 是擷取實體時的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-727">`Department.RowVersion` is the row version when the entity was fetched.</span></span> <span data-ttu-id="9e5c1-728">當 EF Core 建立 SQL DELETE 命令時，它會包含一個帶有 `RowVersion` 的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-728">When EF Core creates the SQL DELETE command, it includes a WHERE clause with `RowVersion`.</span></span> <span data-ttu-id="9e5c1-729">若 SQL DELETE 命令影響的資料列數目為零：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-729">If the SQL DELETE command results in zero rows affected:</span></span>

* <span data-ttu-id="9e5c1-730">表示 SQL DELETE 命令中的 `RowVersion` 不符合資料庫中的 `RowVersion`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-730">The `RowVersion` in the SQL DELETE command doesn't match `RowVersion` in the DB.</span></span>
* <span data-ttu-id="9e5c1-731">擲回 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-731">A DbUpdateConcurrencyException exception is thrown.</span></span>
* <span data-ttu-id="9e5c1-732">使用 `concurrencyError` 呼叫 `OnGetAsync`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-732">`OnGetAsync` is called with the `concurrencyError`.</span></span>

### <a name="update-the-delete-page"></a><span data-ttu-id="9e5c1-733">更新 [刪除] 頁面</span><span class="sxs-lookup"><span data-stu-id="9e5c1-733">Update the Delete page</span></span>

<span data-ttu-id="9e5c1-734">使用下列程式碼更新 *ges/Departments/Delete.cshtml*：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-734">Update *Pages/Departments/Delete.cshtml* with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

<span data-ttu-id="9e5c1-735">上述程式碼會進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-735">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="9e5c1-736">將 `page` 指示詞從 `@page` 更新為 `@page "{id:int}"`。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-736">Updates the `page` directive from `@page` to `@page "{id:int}"`.</span></span>
* <span data-ttu-id="9e5c1-737">新增錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-737">Adds an error message.</span></span>
* <span data-ttu-id="9e5c1-738">在 [系統管理員] 欄位中將 FirstMidName 取代為 FullName。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-738">Replaces FirstMidName with FullName in the **Administrator** field.</span></span>
* <span data-ttu-id="9e5c1-739">變更 `RowVersion` 以顯示最後一個位元組。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-739">Changes `RowVersion` to display the last byte.</span></span>
* <span data-ttu-id="9e5c1-740">新增一個隱藏的資料列版本。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-740">Adds a hidden row version.</span></span> <span data-ttu-id="9e5c1-741">您必須新增 `RowVersion`，以讓回傳繫結值。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-741">`RowVersion` must be added so post back binds the value.</span></span>

### <a name="test-concurrency-conflicts-with-the-delete-page"></a><span data-ttu-id="9e5c1-742">使用 [刪除] 頁面測試並行衝突</span><span class="sxs-lookup"><span data-stu-id="9e5c1-742">Test concurrency conflicts with the Delete page</span></span>

<span data-ttu-id="9e5c1-743">建立一個測試部門。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-743">Create a test department.</span></span>

<span data-ttu-id="9e5c1-744">開啟兩個測試部門上 [刪除] 頁面的瀏覽器執行個體：</span><span class="sxs-lookup"><span data-stu-id="9e5c1-744">Open two browsers instances of Delete on the test department:</span></span>

* <span data-ttu-id="9e5c1-745">執行應用程式並選取 Departments。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-745">Run the app and select Departments.</span></span>
* <span data-ttu-id="9e5c1-746">以滑鼠右鍵按一下測試部門的 **刪除** 超連結，然後選取 [Open in new tab] (在新索引標籤中開啟)。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-746">Right-click the **Delete** hyperlink for the test department and select **Open in new tab**.</span></span>
* <span data-ttu-id="9e5c1-747">按一下測試部門的 **編輯** 超連結。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-747">Click the **Edit** hyperlink for the test department.</span></span>

<span data-ttu-id="9e5c1-748">兩個瀏覽器索引標籤會顯示相同的資訊。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-748">The two browser tabs display the same information.</span></span>

<span data-ttu-id="9e5c1-749">在第一個瀏覽器索引標籤中變更預算，然後按一下 [儲存]。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-749">Change the budget in the first browser tab and click **Save**.</span></span>

<span data-ttu-id="9e5c1-750">瀏覽器會顯示 [索引] 頁面，當中包含了變更之後的值和更新後的 rowVersion 指標。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-750">The browser shows the Index page with the changed value and updated rowVersion indicator.</span></span> <span data-ttu-id="9e5c1-751">請注意更新後的 rowVersion 指標。它會顯示在另一個索引標籤中的第二個回傳上。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-751">Note the updated rowVersion indicator, it's displayed on the second postback in the other tab.</span></span>

<span data-ttu-id="9e5c1-752">從第二個索引標籤中刪除測試部門。並行錯誤會與資料庫目前的值一起顯示。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-752">Delete the test department from the second tab. A concurrency error is display with the current values from the DB.</span></span> <span data-ttu-id="9e5c1-753">除非已更新，否則按一下 [ **刪除** ] 會刪除實體 `RowVersion` 。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-753">Clicking **Delete** deletes the entity, unless `RowVersion` has been updated.</span></span>

<span data-ttu-id="9e5c1-754">請參閱[繼承](xref:data/ef-mvc/inheritance)以了解如何繼承資料模型。</span><span class="sxs-lookup"><span data-stu-id="9e5c1-754">See [Inheritance](xref:data/ef-mvc/inheritance) on how to inherit a data model.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="9e5c1-755">其他資源</span><span class="sxs-lookup"><span data-stu-id="9e5c1-755">Additional resources</span></span>

* [<span data-ttu-id="9e5c1-756">EF Core 中的並行權杖</span><span class="sxs-lookup"><span data-stu-id="9e5c1-756">Concurrency Tokens in EF Core</span></span>](/ef/core/modeling/concurrency)
* [<span data-ttu-id="9e5c1-757">在 EF Core 中處理並行</span><span class="sxs-lookup"><span data-stu-id="9e5c1-757">Handle concurrency in EF Core</span></span>](/ef/core/saving/concurrency)
* [<span data-ttu-id="9e5c1-758">這個教學課程的 YouTube 版本 (處理並行存取衝突)</span><span class="sxs-lookup"><span data-stu-id="9e5c1-758">YouTube version of this tutorial(Handling Concurrency Conflicts)</span></span>](https://youtu.be/EosxHTFgYps)
* [<span data-ttu-id="9e5c1-759">這個教學課程的 YouTube 版本 (第 2 部分)</span><span class="sxs-lookup"><span data-stu-id="9e5c1-759">YouTube version of this tutorial(Part 2)</span></span>](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [<span data-ttu-id="9e5c1-760">這個教學課程的 YouTube 版本 (第 3 部分)</span><span class="sxs-lookup"><span data-stu-id="9e5c1-760">YouTube version of this tutorial(Part 3)</span></span>](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> <span data-ttu-id="9e5c1-761">[[上一步]](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="9e5c1-761">[Previous](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
