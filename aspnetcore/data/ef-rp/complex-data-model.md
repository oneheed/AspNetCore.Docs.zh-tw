---
title: 第5部分： Razor ASP.NET Core 資料模型中有 EF Core 的頁面
author: rick-anderson
description: 第 5 Razor 頁和 Entity Framework 教學課程系列。
ms.author: riande
ms.custom: mvc
ms.date: 3/3/2021
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
uid: data/ef-rp/complex-data-model
ms.openlocfilehash: 23ac7fa83d6ee313e6695e3829a50ae94c967572
ms.sourcegitcommit: 4864500b0f43e33dc0445ce632bfbe1e43e79320
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2021
ms.locfileid: "105994152"
---
<!-- Removed from V 5.0, CourseAssignment -->

# <a name="part-5-razor-pages-with-ef-core-in-aspnet-core---data-model"></a>第5部分： Razor ASP.NET Core 資料模型中有 EF Core 的頁面

由 [Tom Dykstra](https://github.com/tdykstra)、 [Jeremy Likness](https://twitter.com/jeremylikness)和 [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-5.0"

先前的教學課程建立了基本的資料模型，該模型由三個實體組成。 在本教學課程中：

* 新增更多實體和關聯性。
* 藉由指定格式、驗證和資料庫對應規則來自訂資料模型。

已完成的資料模型如下圖所示：

![實體圖表](complex-data-model/_static/EF.png)

以下是使用 [Dataedo](https://dataedo.com/)建立的資料庫關係圖：

<!--Image added in https://github.com/dotnet/AspNetCore.Docs/pull/21922  -->

![Dataedo 圖](intro/_static/5/AzureSQL_DB_ERD_madeIn_Dataedo.png)

若要使用 Dataedo 建立資料庫關係圖：

* [將應用程式部署至 Azure](/azure/app-service/tutorial-dotnetcore-sqldb-app)
* 在您的電腦上下載並安裝 [Dataedo](https://dataedo.com/) 。
* [在5分鐘內依照指示產生 Azure SQL Database 的檔](https://dataedo.com/tutorials/generate-documentation-for-azure-sql-database)

在上述的 Dataedo 圖中， `CourseInstructor` 是 Entity Framework 所建立的聯結資料表。 如需詳細資訊，請參閱 [多對多](/ef/core/modeling/relationships#many-to-many)

## <a name="the-student-entity"></a>Student 實體

將 *Models/Student.cs* 中的程式碼替換成下列程式碼：

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

上述程式碼會新增 `FullName` 屬性，並將下列屬性新增到現有屬性：

* [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)
* [`[DisplayFormat]`](xref:System.ComponentModel.DataAnnotations.DisplayFormatAttribute)
* [`[StringLength]`](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)
* [`[Column]`](xref:System.ComponentModel.DataAnnotations.Schema.ColumnAttribute)
* [`[Required]`](xref:System.ComponentModel.DataAnnotations.RequiredAttribute)
* [`[Display]`](xref:System.ComponentModel.DataAnnotations.DisplayAttribute)

### <a name="the-fullname-calculated-property"></a>FullName 計算屬性

`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。 您無法設定 `FullName`，因此它只會有 get 存取子。 資料庫中不會建立 `FullName` 資料行。

### <a name="the-datatype-attribute"></a>DataType 屬性

```csharp
[DataType(DataType.Date)]
```

針對學生註冊日期，所有頁面目前都會同時顯示日期和一天當中的時間，雖然只要日期才是重要項目。 透過使用資料註解屬性，您便可以只透過單一程式碼變更來修正每個顯示資料頁面中的顯示格式。 

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 屬性會指定一個比資料庫內建類型更明確的資料類型。 在此情況下，該欄位應該只顯示日期，而不會同時顯示日期和時間。 [DataType 列舉](/dotnet/api/system.componentmodel.dataannotations.datatype)可提供許多資料類型，例如 Date、Time、PhoneNumber、Currency、EmailAddress 等。`DataType`屬性也可以讓應用程式自動提供型別特有的功能。 例如：

* `DataType.EmailAddress` 會自動建立 `mailto:` 連結。
* `DataType.Date` 在大多數的瀏覽器中都會提供日期選取器。

`DataType` 屬性會發出 HTML5 `data-` (發音為 data dash) 屬性。 `DataType` 屬性不會提供驗證。

### <a name="the-displayformat-attribute"></a>DisplayFormat 屬性

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date` 未指定顯示日期的格式。 根據預設，日期欄位會依照根據伺服器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 為基礎的預設格式顯示。

`DisplayFormat` 屬性會用來明確指定日期格式。 `ApplyFormatInEditMode` 設定會指定格式也應套用在編輯 UI。 某些欄位不應該使用 `ApplyFormatInEditMode`。 例如，貨幣符號通常不應顯示在編輯文字方塊中。

`DisplayFormat` 屬性可由自身使用。 通常使用 `DataType` 屬性搭配 `DisplayFormat` 屬性是一個不錯的做法。 `DataType` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。 `DataType` 屬性提供了下列優點，並且這些優點在 `DisplayFormat` 中無法使用：

* 瀏覽器可以啟用 HTML5 功能。 例如，顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結和用戶端輸入驗證。
* 根據預設，瀏覽器將根據地區設定，使用正確的格式呈現資料。

如需詳細資訊，請參閱[ \<input> Tag Helper 檔](xref:mvc/views/working-with-forms#the-input-tag-helper)。

### <a name="the-stringlength-attribute"></a>StringLength 屬性

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

您可使用屬性指定資料驗證規則和驗證錯誤訊息。 [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 屬性指定了在資料欄位中允許的最小及最大字元長度。 此處所顯示的程式碼會限制名稱不得超過 50 個字元。 設定字串長度下限的範例會在[稍後](#the-required-attribute)顯示。

`StringLength` 屬性同時也提供了用戶端和伺服器端的驗證。 最小值對資料庫結構描述不會造成任何影響。

`StringLength` 屬性不會防止使用者在名稱中輸入空白字元。 [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 屬性可用來將限制套用到輸入。 例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 [SQL Server 物件總管] (SSOX) 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。

![移轉之前於 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-before-migration.png)

上述影像顯示了 `Student` 資料表的結構描述。 名稱欄位的型別為 `nvarchar(MAX)`。 在本教學課程稍後建立移轉並套用時，名稱欄位會因為字串長度屬性而變更為 `nvarchar(50)`。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

使用 SQLite 工具，檢查資料表的資料行定義 `Student` 。 名稱欄位的型別為 `Text`。 請注意，第一個名稱欄位稱為 `FirstMidName`。 在下一節中， `FirstMidName` 會變更為 `FirstName` 。

---

### <a name="the-column-attribute"></a>Column 屬性

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

可控制類別和屬性如何對應到資料庫的屬性。 在 `Student` 模型中，`Column` 屬性會用來將 `FirstMidName` 屬性名稱對應到資料庫中的 "FirstName"。

建立資料庫時，模型上的屬性名稱會用來作為資料行名稱 (使用 `Column` 屬性時除外)。 `Student` 模型針對名字欄位使用 `FirstMidName`，因為欄位中可能也會包含中間名。

透過 `[Column]` 屬性，資料模型中 `Student.FirstMidName` 會對應到 `Student` 資料表的 `FirstName` 資料行。 新增 `Column` 屬性會變更支援 `SchoolContext` 的模型。 支援 `SchoolContext` 的模型不再符合資料庫。 這種不一致會透過在本教學課程中稍後部分新增移轉來解決。

### <a name="the-required-attribute"></a>Required 屬性

```csharp
[Required]
```

`Required` 屬性會讓名稱屬性成為必要欄位。 針對不可為 Null 型別的實值型別 (例如 `DateTime`、`int` 和 `double`)，`Required` 屬性並非必要項目。 不可為 Null 的類型會自動視為必要欄位。

`Required` 屬性必須搭配 `MinimumLength` 使用，才能強制執行 `MinimumLength`。

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength` 與 `Required` 允許空白字元以滿足驗證。 使用 `RegularExpression` 屬性來完全控制字串。

### <a name="the-display-attribute"></a>Display 屬性

```csharp
[Display(Name = "Last Name")]
```

`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」。 預設標題中沒有使用空白分隔文字，例如「姓氏」。

### <a name="create-a-migration"></a>建立移轉

執行應用程式並移至 Students 頁面。 隨即擲回例外狀況。 `[Column]` 屬性會造成 EF 預期尋找名為 `FirstName` 的資料行，但資料庫中的資料行名稱仍是 `FirstMidName`。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

錯誤訊息會與下列範例相似：

```
SqlException: Invalid column name 'FirstName'.
There are pending model changes
Pending model changes are detected in the following:

SchoolContext
```

* 在 PMC 中，輸入下列命令來建立新的移轉並更新資料庫：

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
   
  ```

  這些命令中的第一個命令會產生下列警告訊息：

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  產生警告的理由是名稱欄位現在限制長度為 50 個字元。 若資料庫中的名稱超過 50 個字元，則第 51 到最後一個字元便會遺失。

* 在 SSOX 中開啟 Student 資料表：

  ![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

  在套用移轉前，名稱資料行的型別為 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。 名稱資料行現在為 `nvarchar(50)`。 資料行的名稱已從 `FirstMidName` 變更為 `FirstName`。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

錯誤訊息會與下列範例相似：

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* 在專案資料夾中開啟命令視窗。 輸入下列命令來建立新的移轉並更新資料庫：

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  資料庫更新命令會顯示與下列範例相似的錯誤：

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

針對本教學課程，通過此錯誤的方法是刪除和重新建立初始移轉。 如需詳細資訊，請參閱[移轉教學課程](xref:data/ef-rp/migrations)頂端的 SQLite 警告附註。

* 刪除 *Migrations* 資料夾。
* 執行下列命令來卸除資料庫、建立新的初始移轉，然後套用移轉：

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
   
  ```

* 使用 SQLite 工具檢查 Student 資料表。 目前的資料行 `FirstMidName` `FirstName` 。

---

* 執行應用程式並移至 Students 頁面。
* 請注意時間並未輸入或和日期一同顯示。
* 選取 [新建] 然後嘗試輸入超過 50 個字元的名稱。

> [!Note]
> 在下列各節中，在某些階段建置應用程式會產生編譯器錯誤。 指令會指定何時應建置應用程式。

## <a name="the-instructor-entity"></a>Instructor 實體

<!-- no longer using PJT
![Instructor entity](complex-data-model/_static/instructor-entity.png) -->

使用下列程式碼建立 *Models/Instructor.cs*：

[!code-csharp[](intro/samples/cu50/Models/Instructor.cs?name=snippet_BeforeInheritance)]

在同一行上可以有多個屬性。 `HireDate` 屬性可以下列方式撰寫：

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>導覽屬性

`Courses` 和 `OfficeAssignment` 屬性為導覽屬性。

由於講師可以教授任何數量的課程，因此 `Courses` 已定義為一個集合。

```csharp
public ICollection<Course> Courses { get; set; }
```

講師最多只能擁有一間辦公室，因此 `OfficeAssignment` 屬性會保存單一 `OfficeAssignment` 實體。 若沒有指派任何辦公室，則 `OfficeAssignment` 為 Null。

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>OfficeAssignment 實體

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

使用下列程式碼建立 *Models/OfficeAssignment.cs*：

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Key 屬性

屬性 [`[Key]`](xref:System.ComponentModel.DataAnnotations.KeyAttribute) （attribute）會用來將屬性（attribute）識別為 primary key (PK) 當屬性名稱不是 `classnameID` 或時 `ID` 。

在 `Instructor` 和 `OfficeAssignment` 實體之間有一對零或一關聯性。 辦公室指派只存在於與其受指派的講師關聯中。 `OfficeAssignment` PK 同時也是其連結到 `Instructor` 實體的外部索引鍵 (FK)。 當某個資料表中的 PK 同時為 PK 和另一個資料表中的 FK 時，就會發生一到零或一種關聯性。

EF Core 無法將 `InstructorID` 自動識別為 `OfficeAssignment` 的主索引鍵，因為 `InstructorID` 並未遵循識別碼或 classnameID 命名慣例。 因此，必須使用 `Key` 屬性將 `InstructorID` 識別為 PK：

```csharp
[Key]
public int InstructorID { get; set; }
```

根據預設，EF Core 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。 如需詳細資訊，請參閱 [EF 金鑰](/ef/core/modeling/keys)。

### <a name="the-instructor-navigation-property"></a>Instructor 導覽屬性

`Instructor.OfficeAssignment` 導覽屬性可以是 Null，因為指定講師可能不會有 `OfficeAssignment` 資料列。 講師可能沒有辦公室指派。

`OfficeAssignment.Instructor` 導覽屬性一律會擁有一個講師實體，因為外部索引鍵 `InstructorID` 的型別是 `int`，其為不可為 Null 的實值型別。 辦公室指派無法獨立於講師之外存在。

當 `Instructor` 實體有相關的 `OfficeAssignment` 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。

## <a name="the-course-entity"></a>Course 實體

使用下列程式碼更新 *Models/Course.cs*：

[!code-csharp[](intro/samples/cu50/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

`Course` 實體具有外部索引鍵 (FK) 屬性`DepartmentID`。 `DepartmentID` 會指向相關的 `Department` 實體。 `Course` 實體具有一個 `Department` 導覽屬性。

若模型針對相關實體已有導覽屬性，則 EF Core 針對資料模型便不需要外部索引鍵屬性。 EF Core 會自動在資料庫中需要的任何地方建立 FK。 EF Core 會為了自動建立的 FK 建立[陰影屬性](/ef/core/modeling/shadow-properties)。 但是，在資料模型中明確包含 FK (外部索引鍵) 可讓更新更簡單且更有效率。 例如，假設有一個模型，當中「不」包含 `DepartmentID` FK 屬性。 當擷取課程實體以進行編輯時：

* `Department` `null` 如果未明確載入，則為屬性。
* 若要更新課程實體，必須先擷取 `Department` 實體。

當 FK 屬性 `DepartmentID` 包含在資料模型中時，便不需要在更新前擷取 `Department` 實體。

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 屬性

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 屬性會指定 PK 是由應用程式提供的，而非資料庫產生的。

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

根據預設，EF Core 會假設 PK (主索引鍵) 的值是由資料庫所產生。 由資料庫產生主索引鍵通常是最佳做法。 針對 `Course` 實體，使用者指定了 PK。 例如，課程號碼 1000 系列表示數學部門的課程，2000 系列則為英文部門的課程。

`DatabaseGenerated` 屬性也可用於產生預設值。 例如，資料庫可以自動產生日期欄位來記錄建立或更新資料列的日期。 如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。

### <a name="foreign-key-and-navigation-properties"></a>外部索引鍵及導覽屬性

`Course` 實體中的外部索引鍵 (FK) 屬性和導覽屬性反映了下列關聯性：

由於一個課程已指派給了一個部門，因此當中具有 `DepartmentID` FK 和 `Department` 導覽屬性。

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

課程可由多個講師進行教授，因此 `Instructors` 導覽屬性為一個集合：

```csharp
        public ICollection<Instructor> Instructors { get; set; }
```

## <a name="the-department-entity"></a>Department 實體

使用下列程式碼建立 *Models/Department.cs*：

[!code-csharp[](intro/samples/cu50/Models/Department.cs?name=snippet1)]

### <a name="the-column-attribute"></a>Column 屬性

先前，`Column` 屬性主要用於變更資料行的名稱對應。 在 `Department` 實體的程式碼中，`Column` 屬性則用於變更 SQL 資料類型對應。 `Budget` 資料行在資料庫中是使用 SQL Server 的 money 型別來定義：

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

通常您不需要資料行對應。 EF Core 會根據屬性的 CLR 型別來選擇適當 SQL Server 資料型別。 CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。 由於 `Budget` 是貨幣，因此金額資料類型會比較適合貨幣。

### <a name="foreign-key-and-navigation-properties"></a>外部索引鍵及導覽屬性

FK 及導覽屬性反映了下列關聯性：

* 部門可能有或可能沒有系統管理員。
* 系統管理員一律為講師。 因此，`InstructorID` 已作為 FK 包含在 `Instructor` 實體中。

導覽屬性已命名為 `Administrator`，但其中保留了一個 `Instructor` 實體：

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

`?`上述程式碼中的會指定屬性為可為 null。

部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：

```csharp
public ICollection<Course> Courses { get; set; }
```

根據慣例，EF Core 會為不可為 Null 的 FK 和多對多關聯性啟用串聯刪除。 此預設行為可能會導致循環串聯刪除規則。 循環串聯刪除規則會在新增移轉時造成例外狀況。

例如，若 `Department.InstructorID` 屬性已定義成不可為 Null，EF Core 便會設定串聯刪除規則。 在這種情況下，若指派為部門管理員的講師遭到刪除，則會同時刪除部門。 在這種情況下，限制規則會更有意義。 下列 [流暢的 API](#fluent-api-alternative-to-attributes) 會設定限制規則，並停用串聯刪除。

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

<!-- todo review: Why update, just go with this from the start 
## The Enrollment entity

An enrollment record is for one course taken by one student.

![Enrollment entity](complex-data-model/_static/enrollment-entity.png)

Update *Models/Enrollment.cs* with the following code:

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

-->

### <a name="the-enrollment-foreign-key-and-navigation-properties"></a>註冊外鍵和導覽屬性

註冊記錄是某位學生參加的一門課程。

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png) 

使用下列程式碼更新 *Models/Enrollment.cs*：

[!code-csharp[](intro/samples/cu50/Models/Enrollment.cs?highlight=1,15)]

FK 屬性及導覽屬性反映了下列關聯性：

註冊記錄乃針對一個課程，因此當中包含了一個 `CourseID` FK 屬性及一個 `Course` 導覽屬性：

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

註冊記錄乃針對一位學生，因此當中包含了一個 `StudentID` FK 屬性及一個 `Student` 導覽屬性：

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Many-to-Many Relationships

在 `Student` 和 `Course` 實體之間存在一個多對多關聯性。 `Enrollment`實體會在資料庫中做為多對多聯結資料表 ***與** 裝載 _。 _ *_With_* 裝載 * 表示 `Enrollment` 除了 fk 聯結資料表以外，資料表還包含額外的資料。 在 `Enrollment` 實體中，fk 以外的其他資料是 PK 和 `Grade` 。

下列圖例展示了在實體圖表中這些關聯性的樣子。 (此圖表是使用 EF 6.x 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 產生的。 建立圖表並不是此教學課程的一部分)。

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (*)」，顯示其為一對多關聯性。

如果 `Enrollment` 資料表不包含等級資訊，則只需要包含這兩個 fk `CourseID` 和 `StudentID` 。 沒有承載的多對多聯結資料表有時候也稱為「純聯結資料表 (PJT)」。

`Instructor`和 `Course` 實體具有多對多關聯性，並使用 PJT。

<!--
## The CourseAssignment entity

![CourseAssignment entity](complex-data-model/_static/courseassignment-entity.png)

Create *Models/CourseAssignment.cs* with the following code:

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

The Instructor-to-Courses many-to-many relationship requires a join table, and the entity for that join table is CourseAssignment.

![Instructor-to-Courses m:M](complex-data-model/_static/courseassignment.png)

It's common to name a join entity `EntityName1EntityName2`. For example, the Instructor-to-Courses join table using this pattern would be `CourseInstructor`. However, we recommend using a name that describes the relationship.

Data models start out simple and grow. Join tables without payload (PJTs) frequently evolve to include payload. By starting with a descriptive entity name, the name doesn't need to change when the join table changes. Ideally, the join entity would have its own natural (possibly single word) name in the business domain. For example, Books and Customers could be linked with a join entity called Ratings. For the Instructor-to-Courses many-to-many relationship, `CourseAssignment` is preferred over `CourseInstructor`.


### Composite key

The two FKs in `CourseAssignment` (`InstructorID` and `CourseID`) together uniquely identify each row of the `CourseAssignment` table. `CourseAssignment` doesn't require a dedicated PK. The `InstructorID` and `CourseID` properties function as a composite PK. The only way to specify composite PKs to EF Core is with the *fluent API*. The next section shows how to configure the composite PK.

The composite key ensures that:

* Multiple rows are allowed for one course.
* Multiple rows are allowed for one instructor.
* Multiple rows aren't allowed for the same instructor and course.

The `Enrollment` join entity defines its own PK, so duplicates of this sort are possible. To prevent such duplicates:

* Add a unique index on the FK fields, or
* Configure `Enrollment` with a primary composite key similar to `CourseAssignment`. For more information, see [Indexes](/ef/core/modeling/indexes).

-->

## <a name="update-the-database-context"></a>更新資料庫內容

使用下列程式碼來更新 *Data/SchoolContext.cs*：

[!code-csharp[](intro/samples/cu50/Data/SchoolContext.cs?name=snippet_SS&highlight=15-17,21-28)]

<!-- TODO review -->
上述程式碼會新增新的實體和：

* 設定和實體之間的多對多關聯性 `Instructor` `Course` 。
* 設定實體的並行存取權杖 `Department` 。 稍後會在本教學課程中討論並行。

## <a name="fluent-api-alternative-to-attributes"></a>屬性的 Fluent API 替代項目

上述程式碼中的 `OnModelCreating` 方法使用了 *Fluent API* 設定 EF Core 行為。 此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式。 [下列程式碼](/ef/core/modeling/#use-fluent-api-to-configure-a-model)為 Fluent API 的其中一個範例：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

在本教學課程中，Fluent API 僅會用於無法使用屬性完成的資料庫對應。 然而，Fluent API 可指定大部分透過屬性可完成的格式、驗證及對應規則。

某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。 `MinimumLength` 不會變更結構描述。它只會套用一項最小長度驗證規則。

有些開發人員偏好以獨佔方式使用流暢的 API，讓它們可以保持實體類別的 *清晰*。 屬性和 Fluent API 可混合使用。 有一些設定只能透過流暢的 API 來完成，例如，指定複合 PK。 有一些設定只能透過屬性完成 (`MinimumLength`)。 使用 Fluent API 或屬性的建議做法為：

* 從這兩種方法中選擇一項。
* 持續且盡量使用您選擇的方法。

本教學課程中使用到的某些屬性主要用於：

* 僅驗證 (例如，`MinimumLength`)。
* 僅 EF Core 組態 (例如，`HasKey`)。
* 驗證及 EF Core 組態 (例如，`[StringLength(50)]`)。

如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。

<!-- 
## Entity diagram

The following illustration shows the diagram that EF Power Tools create for the completed School model.

![Entity diagram](complex-data-model/_static/diagram.png)

The preceding diagram shows:

* Several one-to-many relationship lines (1 to \*).
* The one-to-zero-or-one relationship line (1 to 0..1) between the `Instructor` and `OfficeAssignment` entities.
* The zero-or-one-to-many relationship line (0..1 to *) between the `Instructor` and `Department` entities.
-->

## <a name="seed-the-database"></a>植入資料庫

更新 *Data/DbInitializer.cs* 中的程式碼：

[!code-csharp[](intro/samples/cu50/Data/DbInitializer2.cs?name=snippet)]

上述程式碼為新的實體提供了種子資料。 此程式碼中的大部分主要用於建立新的實體物件並載入範例資料。 範例資料主要用於測試。

<!-- This is beyond the scope of this tutorial too expensive to maintain, so removing
## Add a migration

Build the project.

# [Visual Studio](#tab/visual-studio)

In PMC, run the following command.

```powershell
Add-Migration ComplexDataModel
```

The preceding command displays a warning about possible data loss.

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

If the `database update` command is run, the following error is produced:

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

The next section fixes this error.

# [Visual Studio Code](#tab/visual-studio-code)

If migration is added and the `database update` command is run, the following error is returned:

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

The next section fixes this error.

---

-->

## <a name="apply-the-migration-or-drop-and-re-create"></a>套用移轉或卸除並重新建立

使用現有的資料庫時，有兩種方法可以變更資料庫：

* [卸除並重新建立資料庫](#drop)。 如果使用 SQLite，請選擇此區段。
* [將遷移套用至現有的資料庫](#applyexisting)。 本節中的說明僅適用於 SQL Server，不適用於 ***SQLite***。

這兩種選擇都適用於 SQL Server。 雖然套用移轉方法更複雜且耗時，但它是現實世界生產環境的慣用方法。

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>卸除並重新建立資料庫

強制 EF Core 建立新的資料庫、卸除並更新資料庫：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  * 刪除 *Migrations* 資料夾。
  * 在 **封裝管理員主控台** 中 (PMC) 上，執行下列命令：

  ```powershell
  Drop-Database
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  * 開啟命令視窗並巡覽至專案資料夾。 專案資料夾包含 *ContosoUniversity.csproj* 檔案。
  * 刪除 *Migrations* 資料夾。
  * 執行下列命令：

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

 ---

執行應用程式。 執行應用程式會執行 `DbInitializer.Initialize` 方法。 `DbInitializer.Initialize` 會填入新的資料庫。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 SSOX 中開啟資料庫：

  * 若先前已開啟過 SSOX，按一下 [重新整理] 按鈕。
  * 展開 **Tables** 節點。 建立的資料表便會顯示。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

使用 SQLite 工具來檢查資料庫：

* 新的資料表和資料行。
* 在資料表中植入資料。

---

<!-- Dropped, see previous comment 
<a name="applyexisting"></a>

## Apply the migration

This section is optional. These steps work only for SQL Server LocalDB and only if you skipped the preceding [Drop and re-create the database](#drop) section.

When migrations are run with existing data, there may be FK constraints that are not satisfied with the existing data. With production data, steps must be taken to migrate the existing data. This section provides an example of fixing FK constraint violations. Don't make these code changes without a backup. Don't make these code changes if you completed the preceding [Drop and re-create the database](#drop) section.

The *{timestamp}_ComplexDataModel.cs* file contains the following code:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

The preceding code adds a non-nullable `DepartmentID` FK to the `Course` table. The database from the previous tutorial contains rows in `Course`, so that table cannot be updated by migrations.

To make the `ComplexDataModel` migration work with existing data:

* Change the code to give the new column (`DepartmentID`) a default value.
* Create a fake department named "Temp" to act as the default department.

#### Fix the foreign key constraints

In the `ComplexDataModel` migration class, update the `Up` method:

* Open the *{timestamp}_ComplexDataModel.cs* file.
* Comment out the line of code that adds the `DepartmentID` column to the `Course` table.

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

Add the following highlighted code. The new code goes after the `.CreateTable( name: "Department"` block:

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

With the preceding changes, existing `Course` rows will be related to the "Temp" department after the `ComplexDataModel.Up` method runs.

The way of handling the situation shown here is simplified for this tutorial. A production app would:

* Include code or scripts to add `Department` rows and related `Course` rows to the new `Department` rows.
* Not use the "Temp" department or the default value for `Course.DepartmentID`.

# [Visual Studio](#tab/visual-studio)

* In the **Package Manager Console** (PMC), run the following command:

  ```powershell
  Update-Database
  ```

Because the `DbInitializer.Initialize` method is designed to work only with an empty database, use SSOX to delete all the rows in the Student and Course tables. (Cascade delete will take care of the Enrollment table.)

# [Visual Studio Code](#tab/visual-studio-code)

* If you're using SQL Server LocalDB with Visual Studio Code, run the following command:

  ```dotnetcli
  dotnet ef database update
  ```

---

Run the app. Running the app runs the `DbInitializer.Initialize` method. The `DbInitializer.Initialize` populates the new database.
-->

## <a name="next-steps"></a>下一步

接下來的兩個教學課程會示範如何讀取和更新相關資料。

> [!div class="step-by-step"]
> [上一個教學](xref:data/ef-rp/migrations) 
>  課程[下一個教學](xref:data/ef-rp/read-related-data)課程

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

先前的教學課程建立了基本的資料模型，該模型由三個實體組成。 在本教學課程中：

* 新增更多實體和關聯性。
* 藉由指定格式、驗證和資料庫對應規則來自訂資料模型。

已完成的資料模型如下圖所示：

![實體圖表](complex-data-model/_static/diagram.png)

## <a name="the-student-entity"></a>Student 實體

![Student 實體](complex-data-model/_static/student-entity.png)

將 *Models/Student.cs* 中的程式碼替換成下列程式碼：

[!code-csharp[](intro/samples/cu30/Models/Student.cs)]

上述程式碼會新增 `FullName` 屬性，並將下列屬性新增到現有屬性：

* `[DataType]`
* `[DisplayFormat]`
* `[StringLength]`
* `[Column]`
* `[Required]`
* `[Display]`

### <a name="the-fullname-calculated-property"></a>FullName 計算屬性

`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。 您無法設定 `FullName`，因此它只會有 get 存取子。 資料庫中不會建立 `FullName` 資料行。

### <a name="the-datatype-attribute"></a>DataType 屬性

```csharp
[DataType(DataType.Date)]
```

針對學生註冊日期，所有頁面目前都會同時顯示日期和一天當中的時間，雖然只要日期才是重要項目。 透過使用資料註解屬性，您便可以只透過單一程式碼變更來修正每個顯示資料頁面中的顯示格式。 

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 屬性會指定一個比資料庫內建類型更明確的資料類型。 在此情況下，該欄位應該只顯示日期，而不會同時顯示日期和時間。 [DataType 列舉](/dotnet/api/system.componentmodel.dataannotations.datatype)可提供許多資料類型，例如 Date、Time、PhoneNumber、Currency、EmailAddress 等。`DataType`屬性也可以讓應用程式自動提供型別特有的功能。 例如：

* `DataType.EmailAddress` 會自動建立 `mailto:` 連結。
* `DataType.Date` 在大多數的瀏覽器中都會提供日期選取器。

`DataType` 屬性會發出 HTML5 `data-` (發音為 data dash) 屬性。 `DataType` 屬性不會提供驗證。

### <a name="the-displayformat-attribute"></a>DisplayFormat 屬性

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`DataType.Date` 未指定顯示日期的格式。 根據預設，日期欄位會依照根據伺服器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 為基礎的預設格式顯示。

`DisplayFormat` 屬性會用來明確指定日期格式。 `ApplyFormatInEditMode` 設定會指定格式也應套用在編輯 UI。 某些欄位不應該使用 `ApplyFormatInEditMode`。 例如，貨幣符號通常不應顯示在編輯文字方塊中。

`DisplayFormat` 屬性可由自身使用。 通常使用 `DataType` 屬性搭配 `DisplayFormat` 屬性是一個不錯的做法。 `DataType` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。 `DataType` 屬性提供了下列優點，並且這些優點在 `DisplayFormat` 中無法使用：

* 瀏覽器可以啟用 HTML5 功能。 例如，顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結和用戶端輸入驗證。
* 根據預設，瀏覽器將根據地區設定，使用正確的格式呈現資料。

如需詳細資訊，請參閱[ \<input> Tag Helper 檔](xref:mvc/views/working-with-forms#the-input-tag-helper)。

### <a name="the-stringlength-attribute"></a>StringLength 屬性

```csharp
[StringLength(50, ErrorMessage = "First name cannot be longer than 50 characters.")]
```

您可使用屬性指定資料驗證規則和驗證錯誤訊息。 [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 屬性指定了在資料欄位中允許的最小及最大字元長度。 此處所顯示的程式碼會限制名稱不得超過 50 個字元。 設定字串長度下限的範例會在[稍後](#the-required-attribute)顯示。

`StringLength` 屬性同時也提供了用戶端和伺服器端的驗證。 最小值對資料庫結構描述不會造成任何影響。

`StringLength` 屬性不會防止使用者在名稱中輸入空白字元。 [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 屬性可用來將限制套用到輸入。 例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 [SQL Server 物件總管] (SSOX) 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。

![移轉之前於 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-before-migration.png)

上述影像顯示了 `Student` 資料表的結構描述。 名稱欄位的型別為 `nvarchar(MAX)`。 在本教學課程稍後建立移轉並套用時，名稱欄位會因為字串長度屬性而變更為 `nvarchar(50)`。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

在您的 SQLite 工具中，檢查 `Student` 資料表的資料行定義。 名稱欄位的型別為 `Text`。 請注意，第一個名稱欄位稱為 `FirstMidName`。 在下一節中，您會將該資料行的名稱變更為 `FirstName`。

---

### <a name="the-column-attribute"></a>Column 屬性

```csharp
[Column("FirstName")]
public string FirstMidName { get; set; }
```

可控制類別和屬性如何對應到資料庫的屬性。 在 `Student` 模型中，`Column` 屬性會用來將 `FirstMidName` 屬性名稱對應到資料庫中的 "FirstName"。

建立資料庫時，模型上的屬性名稱會用來作為資料行名稱 (使用 `Column` 屬性時除外)。 `Student` 模型針對名字欄位使用 `FirstMidName`，因為欄位中可能也會包含中間名。

透過 `[Column]` 屬性，資料模型中 `Student.FirstMidName` 會對應到 `Student` 資料表的 `FirstName` 資料行。 新增 `Column` 屬性會變更支援 `SchoolContext` 的模型。 支援 `SchoolContext` 的模型不再符合資料庫。 這種不一致會透過在本教學課程中稍後部分新增移轉來解決。

### <a name="the-required-attribute"></a>Required 屬性

```csharp
[Required]
```

`Required` 屬性會讓名稱屬性成為必要欄位。 針對不可為 Null 型別的實值型別 (例如 `DateTime`、`int` 和 `double`)，`Required` 屬性並非必要項目。 不可為 Null 的類型會自動視為必要欄位。

`Required` 屬性必須搭配 `MinimumLength` 使用，才能強制執行 `MinimumLength`。

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

`MinimumLength` 與 `Required` 允許空白字元以滿足驗證。 使用 `RegularExpression` 屬性來完全控制字串。

### <a name="the-display-attribute"></a>Display 屬性

```csharp
[Display(Name = "Last Name")]
```

`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」。 預設標題中沒有使用空白分隔文字，例如「姓氏」。

### <a name="create-a-migration"></a>建立移轉

執行應用程式並移至 Students 頁面。 隨即擲回例外狀況。 `[Column]` 屬性會造成 EF 預期尋找名為 `FirstName` 的資料行，但資料庫中的資料行名稱仍是 `FirstMidName`。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

錯誤訊息會與下列範例相似：

```
SqlException: Invalid column name 'FirstName'.
```

* 在 PMC 中，輸入下列命令來建立新的移轉並更新資料庫：

  ```powershell
  Add-Migration ColumnFirstName
  Update-Database
  ```

  這些命令中的第一個命令會產生下列警告訊息：

  ```text
  An operation was scaffolded that may result in the loss of data.
  Please review the migration for accuracy.
  ```

  產生警告的理由是名稱欄位現在限制長度為 50 個字元。 若資料庫中的名稱超過 50 個字元，則第 51 到最後一個字元便會遺失。

* 在 SSOX 中開啟 Student 資料表：

  ![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

  在套用移轉前，名稱資料行的型別為 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。 名稱資料行現在為 `nvarchar(50)`。 資料行的名稱已從 `FirstMidName` 變更為 `FirstName`。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

錯誤訊息會與下列範例相似：

```
SqliteException: SQLite Error 1: 'no such column: s.FirstName'.
```

* 在專案資料夾中開啟命令視窗。 輸入下列命令來建立新的移轉並更新資料庫：

  ```dotnetcli
  dotnet ef migrations add ColumnFirstName
  dotnet ef database update
  ```

  資料庫更新命令會顯示與下列範例相似的錯誤：

  ```text
  SQLite does not support this migration operation ('AlterColumnOperation'). For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
  ```

針對本教學課程，通過此錯誤的方法是刪除和重新建立初始移轉。 如需詳細資訊，請參閱[移轉教學課程](xref:data/ef-rp/migrations)頂端的 SQLite 警告附註。

* 刪除 *Migrations* 資料夾。
* 執行下列命令來卸除資料庫、建立新的初始移轉，然後套用移轉：

  ```dotnetcli
  dotnet ef database drop --force
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

* 在您的 SQLite 工具中檢查 Student 資料表。 原先為 FirstMidName 的資料行現在已變更為 FirstName。

---

* 執行應用程式並移至 Students 頁面。
* 請注意時間並未輸入或和日期一同顯示。
* 選取 [新建] 然後嘗試輸入超過 50 個字元的名稱。

> [!Note]
> 在下列各節中，在某些階段建置應用程式會產生編譯器錯誤。 指令會指定何時應建置應用程式。

## <a name="the-instructor-entity"></a>Instructor 實體

![Instructor 實體](complex-data-model/_static/instructor-entity.png)

使用下列程式碼建立 *Models/Instructor.cs*：

[!code-csharp[](intro/samples/cu30/Models/Instructor.cs)]

在同一行上可以有多個屬性。 `HireDate` 屬性可以下列方式撰寫：

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="navigation-properties"></a>導覽屬性

`CourseAssignments` 和 `OfficeAssignment` 屬性為導覽屬性。

由於講師可以教授任何數量的課程，因此 `CourseAssignments` 已定義為一個集合。

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

講師最多只能擁有一間辦公室，因此 `OfficeAssignment` 屬性會保存單一 `OfficeAssignment` 實體。 若沒有指派任何辦公室，則 `OfficeAssignment` 為 Null。

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="the-officeassignment-entity"></a>OfficeAssignment 實體

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

使用下列程式碼建立 *Models/OfficeAssignment.cs*：

[!code-csharp[](intro/samples/cu30/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Key 屬性

`[Key]` 屬性用於將某個屬性名稱不是 classnameID 或 ID 的屬性識別為主索引鍵 (PK)。

在 `Instructor` 和 `OfficeAssignment` 實體之間有一對零或一關聯性。 辦公室指派只存在於與其受指派的講師關聯中。 `OfficeAssignment` PK 同時也是其連結到 `Instructor` 實體的外部索引鍵 (FK)。

EF Core 無法將 `InstructorID` 自動識別為 `OfficeAssignment` 的主索引鍵，因為 `InstructorID` 並未遵循識別碼或 classnameID 命名慣例。 因此，必須使用 `Key` 屬性將 `InstructorID` 識別為 PK：

```csharp
[Key]
public int InstructorID { get; set; }
```

根據預設，EF Core 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。

### <a name="the-instructor-navigation-property"></a>Instructor 導覽屬性

`Instructor.OfficeAssignment` 導覽屬性可以是 Null，因為指定講師可能不會有 `OfficeAssignment` 資料列。 講師可能沒有辦公室指派。

`OfficeAssignment.Instructor` 導覽屬性一律會擁有一個講師實體，因為外部索引鍵 `InstructorID` 的型別是 `int`，其為不可為 Null 的實值型別。 辦公室指派無法獨立於講師之外存在。

當 `Instructor` 實體有相關的 `OfficeAssignment` 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。

## <a name="the-course-entity"></a>Course 實體

![Course 實體](complex-data-model/_static/course-entity.png)

使用下列程式碼更新 *Models/Course.cs*：

[!code-csharp[](intro/samples/cu30/Models/Course.cs?highlight=2,10,13,16,19,21,23)]

`Course` 實體具有外部索引鍵 (FK) 屬性`DepartmentID`。 `DepartmentID` 會指向相關的 `Department` 實體。 `Course` 實體具有一個 `Department` 導覽屬性。

若模型針對相關實體已有導覽屬性，則 EF Core 針對資料模型便不需要外部索引鍵屬性。 EF Core 會自動在資料庫中需要的任何地方建立 FK。 EF Core 會為了自動建立的 FK 建立[陰影屬性](/ef/core/modeling/shadow-properties)。 但是，在資料模型中明確包含 FK (外部索引鍵) 可讓更新更簡單且更有效率。 例如，假設有一個模型，當中「不」包含 `DepartmentID` FK 屬性。 當擷取課程實體以進行編輯時：

* 若 `Department` 屬性未明確載入，則其為 Null。
* 若要更新課程實體，必須先擷取 `Department` 實體。

當 FK 屬性 `DepartmentID` 包含在資料模型中時，便不需要在更新前擷取 `Department` 實體。

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 屬性

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 屬性會指定 PK 是由應用程式提供的，而非資料庫產生的。

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

根據預設，EF Core 會假設 PK (主索引鍵) 的值是由資料庫所產生。 由資料庫產生主索引鍵通常是最佳做法。 針對 `Course` 實體，使用者指定了 PK。 例如，課程號碼 1000 系列表示數學部門的課程，2000 系列則為英文部門的課程。

`DatabaseGenerated` 屬性也可用於產生預設值。 例如，資料庫可以自動產生日期欄位來記錄建立或更新資料列的日期。 如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。

### <a name="foreign-key-and-navigation-properties"></a>外部索引鍵及導覽屬性

`Course` 實體中的外部索引鍵 (FK) 屬性和導覽屬性反映了下列關聯性：

由於一個課程已指派給了一個部門，因此當中具有 `DepartmentID` FK 和 `Department` 導覽屬性。

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

課程可由多個講師進行教授，因此 `CourseAssignments` 導覽屬性為一個集合：

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` 會在[稍後](#many-to-many-relationships)進行解釋。

## <a name="the-department-entity"></a>Department 實體

![部門實體](complex-data-model/_static/department-entity.png)

使用下列程式碼建立 *Models/Department.cs*：

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Models/Department1.cs)]

### <a name="the-column-attribute"></a>Column 屬性

先前，`Column` 屬性主要用於變更資料行的名稱對應。 在 `Department` 實體的程式碼中，`Column` 屬性則用於變更 SQL 資料類型對應。 `Budget` 資料行在資料庫中是使用 SQL Server 的 money 型別來定義：

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

通常您不需要資料行對應。 EF Core 會根據屬性的 CLR 型別來選擇適當 SQL Server 資料型別。 CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。 由於 `Budget` 是貨幣，因此金額資料類型會比較適合貨幣。

### <a name="foreign-key-and-navigation-properties"></a>外部索引鍵及導覽屬性

FK 及導覽屬性反映了下列關聯性：

* 部門可能有或可能沒有系統管理員。
* 系統管理員一律為講師。 因此，`InstructorID` 已作為 FK 包含在 `Instructor` 實體中。

導覽屬性已命名為 `Administrator`，但其中保留了一個 `Instructor` 實體：

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

上述程式碼中的問號 (?) 表示屬性可為 Null。

部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：

```csharp
public ICollection<Course> Courses { get; set; }
```

根據慣例，EF Core 會為不可為 Null 的 FK 和多對多關聯性啟用串聯刪除。 此預設行為可能會導致循環串聯刪除規則。 循環串聯刪除規則會在新增移轉時造成例外狀況。

例如，若 `Department.InstructorID` 屬性已定義成不可為 Null，EF Core 便會設定串聯刪除規則。 在這種情況下，若指派為部門管理員的講師遭到刪除，則會同時刪除部門。 在這種情況下，限制規則會更有意義。 下列 [流暢的 API](#fluent-api-alternative-to-attributes) 會設定限制規則，並停用串聯刪除。

  ```csharp
  modelBuilder.Entity<Department>()
     .HasOne(d => d.Administrator)
     .WithMany()
     .OnDelete(DeleteBehavior.Restrict)
  ```

## <a name="the-enrollment-entity"></a>Enrollment 實體

註冊記錄是某位學生參加的一門課程。

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png)

使用下列程式碼更新 *Models/Enrollment.cs*：

[!code-csharp[](intro/samples/cu30/Models/Enrollment.cs?highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>外部索引鍵及導覽屬性

FK 屬性及導覽屬性反映了下列關聯性：

註冊記錄乃針對一個課程，因此當中包含了一個 `CourseID` FK 屬性及一個 `Course` 導覽屬性：

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

註冊記錄乃針對一位學生，因此當中包含了一個 `StudentID` FK 屬性及一個 `Student` 導覽屬性：

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Many-to-Many Relationships

在 `Student` 和 `Course` 實體之間存在一個多對多關聯性。 `Enrollment` 實體的功能為資料庫中一個「具有承載」的多對多聯結資料表。 「具有承載」表示 `Enrollment` 資料表除了聯結資料表 (在此案例中為 PK 和 `Grade`) 的 FK 之外，還包含了額外的資料。

下列圖例展示了在實體圖表中這些關聯性的樣子。 (此圖表是使用 EF 6.x 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 產生的。 建立圖表並不是此教學課程的一部分)。

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (*)」，顯示其為一對多關聯性。

若 `Enrollment` 資料表並未包含成績資訊，則其便只需要包含兩個 FK (`CourseID` 和 `StudentID`)。 沒有承載的多對多聯結資料表有時候也稱為「純聯結資料表 (PJT)」。

`Instructor` 和 `Course` 實體具有使用了純聯結資料表的多對多關聯性。

注意：EF 6.x 支援多對多關聯性的隱含聯結資料表，但 EF Core 並不支援。 如需詳細資訊，請參閱 [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (EF Core 2.0 中的多對多關聯性)。

## <a name="the-courseassignment-entity"></a>CourseAssignment 實體

![CourseAssignment 實體](complex-data-model/_static/courseassignment-entity.png)

使用下列程式碼建立 *Models/CourseAssignment.cs*：

[!code-csharp[](intro/samples/cu30/Models/CourseAssignment.cs)]

Instructor 對 Courses 的多對多關聯性需要聯結資料表，而該聯結資料表的實體為 CourseAssignment。

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

通常會將聯結實體命名為 `EntityName1EntityName2`。 例如，使用此模式的 Instructor 對 Courses 聯結資料表將會是 `CourseInstructor`。 不過，我們建議使用可描述關聯性的名稱。

資料模型一開始都是簡單的，之後便會持續成長。 不含承載的聯結資料表 (PJT) 常常會演變成包含承載。 藉由在一開始便使用描述性的實體名稱，當聯結資料表變更時便不需要變更名稱。 理想情況下，聯結實體在公司網域中會有自己的自然 (可能為一個單字) 名稱。 例如，「書籍」和「客戶」可連結為一個名為「評分」 的聯結實體。 針對講師-課程多對多關聯性，`CourseAssignment` 會比 `CourseInstructor` 來得好。

### <a name="composite-key"></a>複合索引鍵

`CourseAssignment` (`InstructorID` 及 `CourseID`) 中的兩個 FK 一起搭配使用，便可唯一的識別 `CourseAssignment` 資料表中的每一個資料列。 `CourseAssignment` 並不需要其專屬的 PK。 `InstructorID` 及 `CourseID` 屬性的功能便是複合 PK。 為 EF Core 指定複合 PK 的唯一方法是使用 *Fluent API*。 下節會說明如何設定複合 PK。

複合索引鍵可確保：

* 一個課程可以有多個資料列。
* 一個講師可以有多個資料列。
* 不允許相同講師和課程的多個資料列。

由於 `Enrollment` 聯結實體定義了其自身的 PK，因此這種種類的重複項目是可能的。 若要防止這類重複項目：

* 在 FK 欄位中新增一個唯一的索引，或
* 為 `Enrollment` 設定一個主複合索引鍵，與 `CourseAssignment` 相似。 如需詳細資訊，請參閱[索引](/ef/core/modeling/indexes)。

## <a name="update-the-database-context"></a>更新資料庫內容

使用下列程式碼來更新 *Data/SchoolContext.cs*：

[!code-csharp[](intro/samples/cu30/Data/SchoolContext.cs?highlight=15-18,25-31)]

上述程式碼會新增一個新實體，並設定 `CourseAssignment` 實體的複合 PK。

## <a name="fluent-api-alternative-to-attributes"></a>屬性的 Fluent API 替代項目

上述程式碼中的 `OnModelCreating` 方法使用了 *Fluent API* 設定 EF Core 行為。 此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式。 [下列程式碼](/ef/core/modeling/#use-fluent-api-to-configure-a-model)為 Fluent API 的其中一個範例：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

在本教學課程中，Fluent API 僅會用於無法使用屬性完成的資料庫對應。 然而，Fluent API 可指定大部分透過屬性可完成的格式、驗證及對應規則。

某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。 `MinimumLength` 不會變更結構描述。它只會套用一項最小長度驗證規則。

某些開發人員偏好單獨使用 Fluent API，使其實體類別保持「整潔」。 屬性和 Fluent API 可混合使用。 有一些設定只能透過 Fluent API 完成 (指定複合 PK)。 有一些設定只能透過屬性完成 (`MinimumLength`)。 使用 Fluent API 或屬性的建議做法為：

* 從這兩種方法中選擇一項。
* 持續且盡量使用您選擇的方法。

本教學課程中使用到的某些屬性主要用於：

* 僅驗證 (例如，`MinimumLength`)。
* 僅 EF Core 組態 (例如，`HasKey`)。
* 驗證及 EF Core 組態 (例如，`[StringLength(50)]`)。

如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。

## <a name="entity-diagram"></a>實體圖表

下圖顯示了 EF Power Tools 為完成的 School 模型建立的圖表。

![實體圖表](complex-data-model/_static/diagram.png)

上圖顯示︰

* 數個一對多關聯性線條 (1 對 \*)。
* `Instructor` 和 `OfficeAssignment` 實體之間的一對零或一關聯性線條 (1 對 0..1)。
* `Instructor` 和 `Department` 實體之間的零或一對多關聯性線條 (0..1 對 *)。

## <a name="seed-the-database"></a>植入資料庫

更新 *Data/DbInitializer.cs* 中的程式碼：

[!code-csharp[](intro/samples/cu30/Data/DbInitializer.cs)]

上述程式碼為新的實體提供了種子資料。 此程式碼中的大部分主要用於建立新的實體物件並載入範例資料。 範例資料主要用於測試。 如需如何植入多對多聯結資料表的範例，請參閱 `Enrollments` 和 `CourseAssignments`。

## <a name="add-a-migration"></a>新增移轉

建置專案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 PMC 中，執行下列命令。

```powershell
Add-Migration ComplexDataModel
```

上述命令會顯示關於可能發生資料遺失的警告。

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
To undo this action, use 'ef migrations remove'
```

若執行 `database update` 命令，便會產生下列錯誤：

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

在下一節中，您會看到如何針對此錯誤採取動作。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

若您新增移轉和執行 `database update` 命令，即會產生下列錯誤：

```text
SQLite does not support this migration operation ('DropForeignKeyOperation').
For more information, see http://go.microsoft.com/fwlink/?LinkId=723262.
```

在下一節中，您會了解如何避免此錯誤。

---

## <a name="apply-the-migration-or-drop-and-re-create"></a>套用移轉或卸除並重新建立

現在您已經具有現有的資料庫，您需要思考如何將變更套用到該資料庫。 本教學課程示範兩種替代方法：

* [卸除並重新建立資料庫](#drop)。 若您正在使用 SQLite，請選擇此節。
* [將遷移套用至現有的資料庫](#applyexisting)。 本節中的說明僅適用於 SQL Server，不適用於 **SQLite**。 

這兩種選擇都適用於 SQL Server。 雖然套用移轉方法更複雜且耗時，但它是現實世界生產環境的慣用方法。 

<a name="drop"></a>

## <a name="drop-and-re-create-the-database"></a>卸除並重新建立資料庫

若您正在使用 SQL Server 且想要在下一節中執行套用移轉方法，請[跳過此節](#apply-the-migration)。

強制 EF Core 建立新的資料庫、卸除並更新資料庫：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 [套件管理員主控台] (PMC) 中，執行下列命令：

  ```powershell
  Drop-Database
  ```

* 刪除 *Migrations* 資料夾，然後執行下列命令：

  ```powershell
  Add-Migration InitialCreate
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 開啟命令視窗並巡覽至專案資料夾。 專案資料夾包含 *ContosoUniversity.csproj* 檔案。

* 執行以下命令：

  ```dotnetcli
  dotnet ef database drop --force
  ```

* 刪除 *Migrations* 資料夾，然後執行下列命令：

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

執行應用程式。 執行應用程式會執行 `DbInitializer.Initialize` 方法。 `DbInitializer.Initialize` 會填入新的資料庫。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 SSOX 中開啟資料庫：

* 若先前已開啟過 SSOX，按一下 [重新整理] 按鈕。
* 展開 **Tables** 節點。 建立的資料表便會顯示。

  ![SSOX 中的資料表](complex-data-model/_static/ssox-tables.png)

* 檢查 **CourseAssignment** 資料表：

  * 以滑鼠右鍵按一下 **CourseAssignment** 資料表，然後選取 [檢視資料]。
  * 驗證 **CourseAssignment** 資料表中是否包含資料。

  ![SSOX 中的 CourseAssignment 資料](complex-data-model/_static/ssox-ci-data.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

使用您的 SQLite 工具來檢查資料庫：

* 新的資料表和資料行。
* 在資料表中植入資料，例如 **CourseAssignment** 資料表。

---

<a name="applyexisting"></a>

## <a name="apply-the-migration"></a>套用移轉

此為選擇性區段。 這些步驟僅適用於 SQL Server LocalDB，且只有在您跳過先前的[卸除並重新建立資料庫](#drop)一節時才適用。

當使用現有的資料執行移轉作業時，某些 FK 條件約束可能會無法透過現有資料滿足。 當您使用的是生產資料時，您必須進行幾個步驟才能移轉現有資料。 本節提供了修正 FK 條件約束違規的範例。 請不要在沒有備份的情況下進行這些程式碼變更。 若您已完成先前的[卸除並重新建立資料庫](#drop)一節，請不要變更這些程式碼。

*{timestamp}_ComplexDataModel.cs* 檔案包含了下列程式碼：

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_DepartmentID)]

上述程式碼將一個不可為 Null 的 `DepartmentID` FK 新增至 `Course` 資料表。 先前教學課程中的資料庫在 `Course` 中包含了資料列，因此無法使用移轉來更新資料表。

若要讓 `ComplexDataModel` 與現有資料進行移轉：

* 變更程式碼，以給予新資料行 (`DepartmentID`) 一個新的預設值。
* 建立一個名為 "Temp" 的假部門以作為預設部門之用。

#### <a name="fix-the-foreign-key-constraints"></a>修正外部索引鍵條件約束

在 `ComplexDataModel` 移轉類別中，更新 `Up` 方法：

* 開啟 *{timestamp}_ComplexDataModel.cs* 檔案。
* 將新增 `DepartmentID` 資料行至 `Course` 資料表的程式碼全部標為註解。

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

新增下列醒目提示程式碼。 新的程式碼位於 `.CreateTable( name: "Department"` 區塊後方：

[!code-csharp[](intro/samples/cu30snapshots/5-complex/Migrations/ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=23-31)]

透過上述變更，現有的 `Course` 資料列將會在執行 `ComplexDataModel.Up` 方法後與 "Temp" 部門相關。

此處顯示處理這種情況的方式已針對本教學課程進行簡化。 生產環境的應用程式會：

* 包含將 `Department` 資料列及相關 `Course` 資料列新增到新 `Department` 資料列的程式碼或指令碼。
* 不使用 "Temp" 部門或 `Course.DepartmentID` 的預設值。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 [套件管理員主控台] (PMC) 中，執行下列命令：

  ```powershell
  Update-Database
  ```

因為 `DbInitializer.Initialize` 方法的設計僅適用於空白資料庫，所以請使用 SSOX 刪除 Student 和 Course 資料表中的所有資料列。 (串聯刪除會負責處理 Enrollment 資料表。)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 若您正在搭配 Visual Studio Code 使用 SQL Server LocalDB，請執行下列命令：

  ```dotnetcli
  dotnet ef database update
  ```

---

執行應用程式。 執行應用程式會執行 `DbInitializer.Initialize` 方法。 `DbInitializer.Initialize` 會填入新的資料庫。

## <a name="next-steps"></a>下一步

接下來的兩個教學課程會示範如何讀取和更新相關資料。

> [!div class="step-by-step"]
> [上一個教學](xref:data/ef-rp/migrations) 
>  課程[下一個教學](xref:data/ef-rp/read-related-data)課程

::: moniker-end

::: moniker range="< aspnetcore-3.0"

先前的教學課程建立了基本的資料模型，該模型由三個實體組成。 在本教學課程中：

* 新增更多實體和關聯性。
* 藉由指定格式、驗證和資料庫對應規則來自訂資料模型。

下圖顯示已完成資料模型的實體類別：

![實體圖表](complex-data-model/_static/diagram.png)

若您遇到無法解決的問題，請下載[完整應用程式](
https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/ef-rp/intro/samples)。

## <a name="customize-the-data-model-with-attributes"></a>使用屬性自訂資料模型

在本節中，您會使用屬性自訂資料模型。

### <a name="the-datatype-attribute"></a>DataType 屬性

學生頁面目前顯示了註冊日期的時間。 一般而言，日期欄位只會顯示日期，而非時間。

使用下列醒目提示的程式碼更新 *Models/Student.cs*：

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

[DataType](/dotnet/api/system.componentmodel.dataannotations.datatypeattribute) 屬性會指定一個比資料庫內建類型更明確的資料類型。 在此情況下，該欄位應該只顯示日期，而不會同時顯示日期和時間。 [DataType 列舉](/dotnet/api/system.componentmodel.dataannotations.datatype)可提供許多資料類型，例如 Date、Time、PhoneNumber、Currency、EmailAddress 等。`DataType`屬性也可以讓應用程式自動提供型別特有的功能。 例如：

* `DataType.EmailAddress` 會自動建立 `mailto:` 連結。
* `DataType.Date` 在大多數的瀏覽器中都會提供日期選取器。

`DataType` 屬性會發出 HTML 5 `data-` (發音為 data dash) 屬性，可讓 HTML 5 瀏覽器取用。 `DataType` 屬性不會提供驗證。

`DataType.Date` 未指定顯示日期的格式。 根據預設，日期欄位會依照根據伺服器的 [CultureInfo](xref:fundamentals/localization#provide-localized-resources-for-the-languages-and-cultures-you-support) 為基礎的預設格式顯示。

`DisplayFormat` 屬性用來明確指定日期格式：

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

`ApplyFormatInEditMode` 設定會指定格式也應套用在編輯 UI。 某些欄位不應該使用 `ApplyFormatInEditMode`。 例如，貨幣符號通常不應顯示在編輯文字方塊中。

`DisplayFormat` 屬性可由自身使用。 通常使用 `DataType` 屬性搭配 `DisplayFormat` 屬性是一個不錯的做法。 `DataType` 屬性會將資料的語意以其在螢幕上呈現方式的相反方式傳遞。 `DataType` 屬性提供了下列優點，並且這些優點在 `DisplayFormat` 中無法使用：

* 瀏覽器可以啟用 HTML5 功能。 例如，顯示日曆控制項、適合地區設定的貨幣符號、電子郵件連結、用戶端輸入驗證等。
* 根據預設，瀏覽器將根據地區設定，使用正確的格式呈現資料。

如需詳細資訊，請參閱[ \<input> Tag Helper 檔](xref:mvc/views/working-with-forms#the-input-tag-helper)。

執行應用程式。 巡覽至 Students [索引] 頁面。 時間將不再顯示。 使用 `Student` 模型的每個檢視現在都只會顯示日期，而不會顯示時間。

![顯示日期而不顯示時間的 Students [索引] 頁面](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>StringLength 屬性

您可使用屬性指定資料驗證規則和驗證錯誤訊息。 [StringLength](/dotnet/api/system.componentmodel.dataannotations.stringlengthattribute) 屬性指定了在資料欄位中允許的最小及最大字元長度。 `StringLength` 屬性同時也提供了用戶端和伺服器端的驗證。 最小值對資料庫結構描述不會造成任何影響。

使用下列程式碼更新 `Student` 模型：

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

上述的程式碼會限制名稱不得超過 50 個字元。 `StringLength` 屬性不會防止使用者在名稱中輸入空白字元。 [RegularExpression](/dotnet/api/system.componentmodel.dataannotations.regularexpressionattribute) 屬性可用於對輸入套用限制。 例如，下列程式碼會要求第一個字元必須是大寫，其餘字元則必須是英文字母：

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

執行應用程式：

* 巡覽至 Students 頁面。
* 選取 [新建]，然後輸入超過 50 個字元的名稱。
* 選取 [建立]，用戶端驗證便會顯示錯誤訊息。

![顯示字元長度錯誤的 Students [索引] 頁面](complex-data-model/_static/string-length-errors.png)

在 [SQL Server 物件總管] (SSOX) 中，按兩下 [Student] 資料表來開啟 Student 資料表設計工具。

![移轉之前於 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-before-migration.png)

上述影像顯示了 `Student` 資料表的結構描述。 名稱欄位的類型為 `nvarchar(MAX)`，因為移轉尚未在資料庫中執行。 在本教學課程的稍後執行移轉後，名稱欄位便會成為 `nvarchar(50)`。

### <a name="the-column-attribute"></a>Column 屬性

可控制類別和屬性如何對應到資料庫的屬性。 在本節中，`Column` 屬性會用於將 `FirstMidName` 屬性的名稱對應到資料庫中的 "FirstName"。

當建立資料庫時，模型上的屬性名稱會用於資料行名稱 (除了使用 `Column` 屬性時之外)。

`Student` 模型針對名字欄位使用 `FirstMidName`，因為欄位中可能也會包含中間名。

使用下列醒目提示程式碼更新 *Student.cs* 檔案：

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Column&highlight=4,14)]

經過上述變更之後，應用程式中的 `Student.FirstMidName` 會對應到 `Student` 資料表的 `FirstName` 資料行。

新增 `Column` 屬性會變更支援 `SchoolContext` 的模型。 支援 `SchoolContext` 的模型不再符合資料庫。 若應用程式在套用移轉之前執行，便會產生下列例外狀況：

```
SqlException: Invalid column name 'FirstName'.
```

若要更新資料庫：

* 建置專案。
* 在專案資料夾中開啟命令視窗。 輸入下列命令來建立新的移轉並更新資料庫：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ColumnFirstName
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ColumnFirstName
dotnet ef database update
```

---

`migrations add ColumnFirstName` 命令會產生下列警告訊息：

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
```

產生警告的理由是名稱欄位現在限制長度為 50 個字元。 若在資料庫中有名稱超過 50 個字元，第 51 個字元到最後一個字元便會遺失。

* 測試應用程式。

在 SSOX 中開啟 Student 資料表：

![移轉之後 SSOX 中的 Students 資料表](complex-data-model/_static/ssox-after-migration.png)

在移轉套用之前，名稱資料行的類型為 [nvarchar(MAX)](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)。 名稱資料行現在為 `nvarchar(50)`。 資料行的名稱已從 `FirstMidName` 變更為 `FirstName`。

> [!Note]
> 在下節中，在某些階段建置應用程式會產生編譯錯誤。 指令會指定何時應建置應用程式。

## <a name="student-entity-update"></a>Student 實體更新

![Student 實體](complex-data-model/_static/student-entity.png)

使用下列程式碼更新 *Models/Student.cs*：

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Required 屬性

`Required` 屬性會讓名稱屬性成為必要欄位。 針對不可為 Null 的類型，例如實值型別 (`DateTime`、`int`、`double` 等) 等，`Required` 屬性是不需要的。 不可為 Null 的類型會自動視為必要欄位。

`Required` 屬性可由 `StringLength` 屬性中的最小長度參數取代：

```csharp
[Display(Name = "Last Name")]
[StringLength(50, MinimumLength=1)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Display 屬性

`Display` 屬性指定了文字方塊的標題應為「名字」、「姓氏」、「全名」及「註冊日期」。 預設標題中沒有使用空白分隔文字，例如「姓氏」。

### <a name="the-fullname-calculated-property"></a>FullName 計算屬性

`FullName` 為一個計算屬性，會傳回藉由串連兩個其他屬性而建立的值。 `FullName` 無法進行設定，其只有 get 存取子。 資料庫中不會建立 `FullName` 資料行。

## <a name="create-the-instructor-entity"></a>建立 Instructor 實體

![Instructor 實體](complex-data-model/_static/instructor-entity.png)

使用下列程式碼建立 *Models/Instructor.cs*：

[!code-csharp[](intro/samples/cu21/Models/Instructor.cs)]

在同一行上可以有多個屬性。 `HireDate` 屬性可以下列方式撰寫：

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>CourseAssignments 和 OfficeAssignment 導覽屬性

`CourseAssignments` 和 `OfficeAssignment` 屬性為導覽屬性。

由於講師可以教授任何數量的課程，因此 `CourseAssignments` 已定義為一個集合。

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

若導覽屬性中保留了多個實體：

* 它必須是一種清單類型，可對其中的實體進行新增、刪除和更新。

導覽屬性類型包括：

* `ICollection<T>`
* `List<T>`
* `HashSet<T>`

若已指定 `ICollection<T>`，EF Core 會根據預設建立一個 `HashSet<T>` 集合。

`CourseAssignment` 實體會在本節的多對多關聯性中解釋。

Contoso 大學商務規則訂定了講師最多只能有一間辦公室。 `OfficeAssignment` 屬性保留了一個單一 `OfficeAssignment` 實體。 若沒有指派任何辦公室，則 `OfficeAssignment` 為 Null。

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-the-officeassignment-entity"></a>建立 OfficeAssignment 實體

![OfficeAssignment 實體](complex-data-model/_static/officeassignment-entity.png)

使用下列程式碼建立 *Models/OfficeAssignment.cs*：

[!code-csharp[](intro/samples/cu21/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Key 屬性

`[Key]` 屬性用於將某個屬性名稱不是 classnameID 或 ID 的屬性識別為主索引鍵 (PK)。

在 `Instructor` 和 `OfficeAssignment` 實體之間有一對零或一關聯性。 辦公室指派只存在於與其受指派的講師關聯中。 `OfficeAssignment` PK 同時也是其連結到 `Instructor` 實體的外部索引鍵 (FK)。 EF Core 無法自動將 `InstructorID` 識別為 `OfficeAssignment` 的主索引鍵，因為：

* `InstructorID` 並未遵循 ID 或 classnameID 的命名慣例。

因此，必須使用 `Key` 屬性將 `InstructorID` 識別為 PK：

```csharp
[Key]
public int InstructorID { get; set; }
```

根據預設，EF Core 會將索引鍵作為非資料庫產生的屬性處置，因為該資料行主要用於識別關聯性。

### <a name="the-instructor-navigation-property"></a>Instructor 導覽屬性

`Instructor` 實體的 `OfficeAssignment` 導覽屬性可為 Null，因為：

* 參考型別 (例如類別可為 Null)。
* 講師可能沒有辦公室指派。

`OfficeAssignment` 實體有不可為 Null 的`Instructor` 導覽屬性，因為：

* `InstructorID` 不可為 Null。
* 辦公室指派無法獨立於講師之外存在。

當 `Instructor` 實體有相關的 `OfficeAssignment` 實體時，每一個實體都會在其導覽屬性中包含一個其他實體的參考。

`[Required]` 屬性可套用到 `Instructor` 導覽屬性：

```csharp
[Required]
public Instructor Instructor { get; set; }
```

上述程式碼指定了必須要有相關的講師。 上述程式碼是不必要的，因為 `InstructorID` 外部索引鍵 (同時也是 PK) 不可為 Null。

## <a name="modify-the-course-entity"></a>修改 Course 實體

![Course 實體](complex-data-model/_static/course-entity.png)

使用下列程式碼更新 *Models/Course.cs*：

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

`Course` 實體具有外部索引鍵 (FK) 屬性`DepartmentID`。 `DepartmentID` 會指向相關的 `Department` 實體。 `Course` 實體具有一個 `Department` 導覽屬性。

當資料模型針對相關實體有一個導覽屬性時，EF Core 不需要針對該模型具備 FK 屬性。

EF Core 會自動在資料庫中需要的任何地方建立 FK。 EF Core 會為了自動建立的 FK 建立[陰影屬性](/ef/core/modeling/shadow-properties)。 在資料模型中具備 FK 可讓更新變得更為簡單和有效率。 例如，假設有一個模型，當中「不」包含 `DepartmentID` FK 屬性。 當擷取課程實體以進行編輯時：

* 若沒有明確載入，`Department` 實體將為 Null。
* 若要更新課程實體，必須先擷取 `Department` 實體。

當 FK 屬性 `DepartmentID` 包含在資料模型中時，便不需要在更新前擷取 `Department` 實體。

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 屬性

`[DatabaseGenerated(DatabaseGeneratedOption.None)]` 屬性會指定 PK 是由應用程式提供的，而非資料庫產生的。

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

根據預設，EF Core 會假設 PK 值都是由資料庫產生的。 由資料庫產生 PK 值通常都是最佳做法。 針對 `Course` 實體，使用者指定了 PK。 例如，課程號碼 1000 系列表示數學部門的課程，2000 系列則為英文部門的課程。

`DatabaseGenerated` 屬性也可用於產生預設值。 例如，資料庫會自動產生日期欄位來記錄資料列建立或更新的日期。 如需詳細資訊，請參閱[產生的屬性](/ef/core/modeling/generated-properties)。

### <a name="foreign-key-and-navigation-properties"></a>外部索引鍵及導覽屬性

`Course` 實體中的外部索引鍵 (FK) 屬性和導覽屬性反映了下列關聯性：

由於一個課程已指派給了一個部門，因此當中具有 `DepartmentID` FK 和 `Department` 導覽屬性。

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

由於課程可由任何數量的學生進行註冊，因此 `Enrollments` 導覽屬性為一個集合：

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

課程可由多個講師進行教授，因此 `CourseAssignments` 導覽屬性為一個集合：

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

`CourseAssignment` 會在[稍後](#many-to-many-relationships)進行解釋。

## <a name="create-the-department-entity"></a>建立 Department 實體

![部門實體](complex-data-model/_static/department-entity.png)

使用下列程式碼建立 *Models/Department.cs*：

[!code-csharp[](intro/samples/cu21/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Column 屬性

先前，`Column` 屬性主要用於變更資料行的名稱對應。 在 `Department` 實體的程式碼中，`Column` 屬性則用於變更 SQL 資料類型對應。 `Budget` 資料行則是使用資料庫中的 SQL Server 金額類型定義：

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

通常您不需要資料行對應。 EF Core 通常會根據屬性的 CLR 類型選擇適當的 SQL Server 資料類型。 CLR `decimal` 類型會對應到 SQL Server 的 `decimal` 類型。 由於 `Budget` 是貨幣，因此金額資料類型會比較適合貨幣。

### <a name="foreign-key-and-navigation-properties"></a>外部索引鍵及導覽屬性

FK 及導覽屬性反映了下列關聯性：

* 部門可能有或可能沒有系統管理員。
* 系統管理員一律為講師。 因此，`InstructorID` 已作為 FK 包含在 `Instructor` 實體中。

導覽屬性已命名為 `Administrator`，但其中保留了一個 `Instructor` 實體：

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

上述程式碼中的問號 (?) 表示屬性可為 Null。

部門中可能包含許多課程，因此當中包含了一個 Course 導覽屬性：

```csharp
public ICollection<Course> Courses { get; set; }
```

注意：根據慣例，EF Core 會為不可為 Null 的 FK 和多對多關聯性啟用串聯刪除。 串聯刪除可能會導致循環的串聯刪除規則。 循環串聯刪除規則會在新增移轉時造成例外狀況。

例如，若已將 `Department.InstructorID` 屬性定義為不可為 Null：

* EF Core 會設定串聯刪除規則，以便在刪除講師時刪除部門。
* 在刪除講師時刪除部門並非預期的行為。
* 下列 [流暢的 API](#fluent-api-alternative-to-attributes) 會設定限制規則，而不是 cascade。

   ```csharp
   modelBuilder.Entity<Department>()
      .HasOne(d => d.Administrator)
      .WithMany()
      .OnDelete(DeleteBehavior.Restrict)
  ```

上述的程式碼會在部門-講師關聯性上停用串聯刪除。

## <a name="update-the-enrollment-entity"></a>更新 Enrollment 實體

註冊記錄是某位學生參加的一門課程。

![Enrollment 實體](complex-data-model/_static/enrollment-entity.png)

使用下列程式碼更新 *Models/Enrollment.cs*：

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>外部索引鍵及導覽屬性

FK 屬性及導覽屬性反映了下列關聯性：

註冊記錄乃針對一個課程，因此當中包含了一個 `CourseID` FK 屬性及一個 `Course` 導覽屬性：

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

註冊記錄乃針對一位學生，因此當中包含了一個 `StudentID` FK 屬性及一個 `Student` 導覽屬性：

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>Many-to-Many Relationships

在 `Student` 和 `Course` 實體之間存在一個多對多關聯性。 `Enrollment` 實體的功能為資料庫中一個「具有承載」的多對多聯結資料表。 「具有承載」表示 `Enrollment` 資料表除了聯結資料表 (在此案例中為 PK 和 `Grade`) 的 FK 之外，還包含了額外的資料。

下列圖例展示了在實體圖表中這些關聯性的樣子。 (此圖表是使用 EF 6.x 的 [EF Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) 產生的。 建立圖表並不是此教學課程的一部分)。

![「學生-課程」多對多關聯性](complex-data-model/_static/student-course.png)

每個關聯性線條都在其中一端有一個「1」，並在另外一端有一個「星號 (*)」，顯示其為一對多關聯性。

若 `Enrollment` 資料表並未包含成績資訊，則其便只需要包含兩個 FK (`CourseID` 和 `StudentID`)。 沒有承載的多對多聯結資料表有時候也稱為「純聯結資料表 (PJT)」。

`Instructor` 和 `Course` 實體具有使用了純聯結資料表的多對多關聯性。

注意：EF 6.x 支援多對多關聯性的隱含聯結資料表，但 EF Core 並不支援。 如需詳細資訊，請參閱 [Many-to-many relationships in EF Core 2.0](https://blog.oneunicorn.com/2017/09/25/many-to-many-relationships-in-ef-core-2-0-part-1-the-basics/) (EF Core 2.0 中的多對多關聯性)。

## <a name="the-courseassignment-entity"></a>CourseAssignment 實體

![CourseAssignment 實體](complex-data-model/_static/courseassignment-entity.png)

使用下列程式碼建立 *Models/CourseAssignment.cs*：

[!code-csharp[](intro/samples/cu21/Models/CourseAssignment.cs)]

### <a name="instructor-to-courses"></a>講師-課程

![講師-課程 m:M](complex-data-model/_static/courseassignment.png)

講師-課程多對多關聯性：

* 需要一個由實體集代表的聯結資料表。
* 為一個純聯結資料表 (沒有承載的資料表)。

通常會將聯結實體命名為 `EntityName1EntityName2`。 例如，使用此模式的講師-課程聯結資料表為 `CourseInstructor`。 不過，我們建議使用可描述關聯性的名稱。

資料模型一開始都是簡單的，之後便會持續成長。 無承載聯結 (PJT) 常常會演變為包含承載。 藉由在一開始便使用描述性的實體名稱，當聯結資料表變更時便不需要變更名稱。 理想情況下，聯結實體在公司網域中會有自己的自然 (可能為一個單字) 名稱。 例如，「書籍」和「客戶」可連結為一個名為「評分」 的聯結實體。 針對講師-課程多對多關聯性，`CourseAssignment` 會比 `CourseInstructor` 來得好。

### <a name="composite-key"></a>複合索引鍵

FK 不可為 Null。 `CourseAssignment` (`InstructorID` 及 `CourseID`) 中的兩個 FK 一起搭配使用，便可唯一的識別 `CourseAssignment` 資料表中的每一個資料列。 `CourseAssignment` 並不需要其專屬的 PK。 `InstructorID` 及 `CourseID` 屬性的功能便是複合 PK。 為 EF Core 指定複合 PK 的唯一方法是使用 *Fluent API*。 下節會說明如何設定複合 PK。

複合 PK 可確保：

* 一個課程可以有多個資料列。
* 一個講師可以有多個資料列。
* 相同講師和課程不可有多個資料列。

由於 `Enrollment` 聯結實體定義了其自身的 PK，因此這種種類的重複項目是可能的。 若要防止這類重複項目：

* 在 FK 欄位中新增一個唯一的索引，或
* 為 `Enrollment` 設定一個主複合索引鍵，與 `CourseAssignment` 相似。 如需詳細資訊，請參閱[索引](/ef/core/modeling/indexes)。

## <a name="update-the-db-context"></a>更新資料庫內容

將下列醒目提示的程式碼新增至 *Data/SchoolContext.cs*：

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

上述程式碼會新增一個新實體，並設定 `CourseAssignment` 實體的複合 PK。

## <a name="fluent-api-alternative-to-attributes"></a>屬性的 Fluent API 替代項目

上述程式碼中的 `OnModelCreating` 方法使用了 *Fluent API* 設定 EF Core 行為。 此 API 稱為 "fluent" ，因為其常常會用於將一系列的方法呼叫串在一起，使其成為一個單一陳述式。 [下列程式碼](/ef/core/modeling/#use-fluent-api-to-configure-a-model)為 Fluent API 的其中一個範例：

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

在此教學課程中，Fluent API 僅會用於無法使用屬性完成的資料庫對應。 然而，Fluent API 可指定大部分透過屬性可完成的格式、驗證及對應規則。

某些屬性 (例如 `MinimumLength`) 無法使用 Fluent API 來套用。 `MinimumLength` 不會變更結構描述。它只會套用一項最小長度驗證規則。

某些開發人員偏好單獨使用 Fluent API，使其實體類別保持「整潔」。 屬性和 Fluent API 可混合使用。 有一些設定只能透過 Fluent API 完成 (指定複合 PK)。 有一些設定只能透過屬性完成 (`MinimumLength`)。 使用 Fluent API 或屬性的建議做法為：

* 從這兩種方法中選擇一項。
* 持續且盡量使用您選擇的方法。

此教學課程中使用到的某些屬性主要用於：

* 僅驗證 (例如，`MinimumLength`)。
* 僅 EF Core 組態 (例如，`HasKey`)。
* 驗證及 EF Core 組態 (例如，`[StringLength(50)]`)。

如需屬性與 Fluent API 的詳細資訊，請參閱[組態方法](/ef/core/modeling/)。

## <a name="entity-diagram-showing-relationships"></a>顯示關聯性的實體圖表

下圖顯示了 EF Power Tools 為完成的 School 模型建立的圖表。

![實體圖表](complex-data-model/_static/diagram.png)

上圖顯示︰

* 數個一對多關聯性線條 (1 對 \*)。
* `Instructor` 和 `OfficeAssignment` 實體之間的一對零或一關聯性線條 (1 對 0..1)。
* `Instructor` 和 `Department` 實體之間的零或一對多關聯性線條 (0..1 對 *)。

## <a name="seed-the-db-with-test-data"></a>使用測試資料植入資料庫

更新 *Data/DbInitializer.cs* 中的程式碼：

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Final)]

上述程式碼為新的實體提供了種子資料。 此程式碼中的大部分主要用於建立新的實體物件並載入範例資料。 範例資料主要用於測試。 如需如何植入多對多聯結資料表的範例，請參閱 `Enrollments` 和 `CourseAssignments`。

## <a name="add-a-migration"></a>新增移轉

建置專案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration ComplexDataModel
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

---

上述命令會顯示關於可能發生資料遺失的警告。

```text
An operation was scaffolded that may result in the loss of data.
Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

若執行 `database update` 命令，便會產生下列錯誤：

```text
The ALTER TABLE statement conflicted with the FOREIGN KEY constraint "FK_dbo.Course_dbo.Department_DepartmentID". The conflict occurred in
database "ContosoUniversity", table "dbo.Department", column 'DepartmentID'.
```

## <a name="apply-the-migration"></a>套用移轉

現在您有了現有的資料庫，您需要思考如何對其套用未來變更。 本教學課程示範兩種方法：

* [捨棄並重新建立資料庫](#drop)
* [將遷移套用至現有的資料庫](#applyexisting)。 雖然這個方法更複雜且耗時，卻是實際生產環境的慣用方法。 **請注意**：這是本教學課程的選擇性章節。 您可以執行卸除並重新建立步驟，然後略過本節。 如果您希望遵循本章節中的步驟，請不要執行卸除並重新建立的步驟。 

<a name="drop"></a>

### <a name="drop-and-re-create-the-database"></a>卸除並重新建立資料庫

更新的 `DbInitializer` 中的程式碼會為新的實體新增種子資料。 若要強制 EF Core 建立新的資料庫，請卸除並更新資料庫：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 [套件管理員主控台] (PMC) 中，執行下列命令：

```powershell
Drop-Database
Update-Database
```

從 PMC 執行 `Get-Help about_EntityFrameworkCore` 以取得說明資訊。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

開啟命令視窗並巡覽至專案資料夾。 專案資料夾中包含 *Startup.cs* 檔案。

在命令視窗中輸入下列命令：

```dotnetcli
dotnet ef database drop
dotnet ef database update
```

---

執行應用程式。 執行應用程式會執行 `DbInitializer.Initialize` 方法。 `DbInitializer.Initialize` 會填入新的資料庫。

在 SSOX 中開啟資料庫：

* 若先前已開啟過 SSOX，按一下 [重新整理] 按鈕。
* 展開 **Tables** 節點。 建立的資料表便會顯示。

![SSOX 中的資料表](complex-data-model/_static/ssox-tables.png)

檢查 **CourseAssignment** 資料表：

* 以滑鼠右鍵按一下 **CourseAssignment** 資料表，然後選取 [檢視資料]。
* 驗證 **CourseAssignment** 資料表中是否包含資料。

![SSOX 中的 CourseAssignment 資料](complex-data-model/_static/ssox-ci-data.png)

<a name="applyexisting"></a>

### <a name="apply-the-migration-to-the-existing-database"></a>將移轉套用至現有資料庫

此為選擇性區段。 只有當您略過先前[卸除並重新建立資料庫](#drop)一節，這些步驟才有效。

當使用現有的資料執行移轉作業時，某些 FK 條件約束可能會無法透過現有資料滿足。 當您使用的是生產資料時，您必須進行幾個步驟才能移轉現有資料。 本節提供了修正 FK 條件約束違規的範例。 請不要在沒有備份的情況下進行這些程式碼變更。 若您已完成了先前的章節並已更新資料庫，請不要進行這些程式碼變更。

*{timestamp}_ComplexDataModel.cs* 檔案包含了下列程式碼：

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_DepartmentID)]

上述程式碼將一個不可為 Null 的 `DepartmentID` FK 新增至 `Course` 資料表。 先前教學課程中的資料庫在 `Course` 中包含了資料列，導致資料表無法藉由移轉進行更新。

若要讓 `ComplexDataModel` 與現有資料進行移轉：

* 變更程式碼，以給予新資料行 (`DepartmentID`) 一個新的預設值。
* 建立一個名為 "Temp" 的假部門以作為預設部門之用。

#### <a name="fix-the-foreign-key-constraints"></a>修正外部索引鍵條件約束

更新 `ComplexDataModel` 類別 `Up` 方法：

* 開啟 *{timestamp}_ComplexDataModel.cs* 檔案。
* 將新增 `DepartmentID` 資料行至 `Course` 資料表的程式碼全部標為註解。

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

新增下列醒目提示程式碼。 新的程式碼位於 `.CreateTable( name: "Department"` 區塊後方：

[!code-csharp[](intro/samples/cu/Migrations/20171027005808_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

透過上述變更，現有的 `Course` 資料列將會在執行 `ComplexDataModel` `Up` 方法後與 "Temp" 部門相關。

生產環境的應用程式會：

* 包含將 `Department` 資料列及相關 `Course` 資料列新增到新 `Department` 資料列的程式碼或指令碼。
* 不使用 "Temp" 部門或 `Course.DepartmentID` 的預設值。

下一個教學課程會涵蓋相關資料。

## <a name="additional-resources"></a>其他資源

* [這個教學課程的 YouTube 版本 (第 1 部分)](https://www.youtube.com/watch?v=0n2f0ObgCoA)
* [這個教學課程的 YouTube 版本 (第 2 部分)](https://www.youtube.com/watch?v=Je0Z5K1TNmY)

> [!div class="step-by-step"]
> [上一個](xref:data/ef-rp/migrations) 
> [下一步](xref:data/ef-rp/read-related-data)

::: moniker-end