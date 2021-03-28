---
title: ASP.NET Core 中的資料繫結
author: rick-anderson
description: 了解 ASP.NET Core 中的模型繫結如何運作，以及如何自訂其行為。
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 5b3892a13da5c72f9ac76428febc73afaaa9fc06
ms.sourcegitcommit: 7b6781051d341a1daaf46c6a4368fa8a5701db81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2021
ms.locfileid: "105638727"
---
# <a name="model-binding-in-aspnet-core"></a>ASP.NET Core 中的資料繫結

::: moniker range=">= aspnetcore-3.0"

本文會說明何謂模型繫結、其運作方式，以及如何自訂其行為。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([如何下載](xref:index#how-to-download-a-sample))。

## <a name="what-is-model-binding"></a>何謂模型繫結

控制器和 Razor 頁面會處理來自 HTTP 要求的資料。 例如，路由資料可能會提供記錄索引鍵，而已張貼的表單欄位可能會提供模型屬性的值。 撰寫程式碼來擷取這些值的每一個並將它們從字串轉換成 .NET 類型，不但繁瑣又容易發生錯誤。 模型繫結會自動化此程序。 模型繫結系統：

* 從各種來源（例如，路由資料、表單欄位和查詢字串）抓取資料。
* 提供資料給 Razor 方法參數和公用屬性中的控制器和頁面。
* 將字串資料轉換成 .NET 類型。
* 更新複雜類型的屬性。

## <a name="example"></a>範例

假設您有下列動作方法：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

而應用程式收到具有此 URL 的要求：

```
http://contoso.com/api/pets/2?DogsOnly=true
```

在路由系統選取動作方法之後，模型系結會經歷下列步驟：

* 尋找第一個參數 `GetByID`，它是名為 `id` 的整數。
* 查看 HTTP 要求中所有可用的來源，在路由資料中找到 `id` = "2"。
* 將字串 "2" 轉換成整數 2。
* 尋找的下一個參數 `GetByID` ，也就是名為的布林值 `dogsOnly` 。
* 查看來源，在查詢字串中找到 "DogsOnly=true"。 名稱比對不區分大小寫。
* 將字串 "true" 轉換成布林值 `true` 。

架構接著會呼叫 `GetById` 方法，針對 `id` 參數傳送 2、`dogsOnly` 參數傳送 `true`。

在上例中，模型繫結目標都是簡單型別的方法參數。 目標也可以是複雜類型的屬性。 成功系結每個屬性之後，就會針對該屬性進行 [模型驗證](xref:mvc/models/validation) 。 哪些資料繫結至模型，以及任何繫結或驗證錯誤的記錄，都會儲存在 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 或 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)。 為了解此程序是否成功，應用程式會檢查 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 旗標。

## <a name="targets"></a>目標

模型繫結會嘗試尋找下列幾種目標的值：

* 要求路由目標的控制器動作方法參數。
* Razor傳送要求的頁面處理常式方法的參數。 
* 控制站的公用屬性或 `PageModel` 類別，如由屬性指定。

### <a name="bindproperty-attribute"></a>[BindProperty] 屬性

可以套用至控制器或類別的公用屬性 `PageModel` ，使模型系結成為該屬性的目標：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a>[BindProperties] 屬性

可在 ASP.NET Core 2.1 和更新版本中使用。  可以套用至控制器或 `PageModel` 類別，以告知模型系結將目標設為類別的所有公用屬性：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>適用於 HTTP GET 要求的模型繫結

根據預設，屬性不會針對 HTTP GET 要求繫結。 一般而言，GET 要求只需要記錄識別碼參數。 此記錄識別碼用來查詢資料庫中的項目。 因此，不需要系結保存模型實例的屬性。 在您要從 GET 要求將屬性繫結至資料的案例中，請將 `SupportsGet` 屬性設為 `true`：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>來源

根據預設，模型繫結會從下列 HTTP 要求的來源中，取得索引鍵/值組形式的資料：

1. 表單欄位
1. [具有 [ApiController] 屬性之控制器](xref:web-api/index#binding-source-parameter-inference)的要求主體 (。 ) 
1. 路由傳送資料
1. 查詢字串參數
1. 已上傳的檔案

針對每個目標參數或屬性，系統會依照上述清單中指出的順序掃描來源。 但也有一些例外：

* 路由資料和查詢字串值只用於簡單型別。
* 上傳的檔案只會系結至執行或的目標型別 `IFormFile` `IEnumerable<IFormFile>` 。

如果預設來源不正確，請使用下列其中一個屬性來指定來源：

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -取得查詢字串中的值。 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -從路由資料取得值。
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -從張貼的表單欄位取得值。
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -從要求主體取得值。
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -取得 HTTP 標頭的值。

這些屬性：

* 會個別新增至模型屬性 (不是模型類別)，如下列範例所示：

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* （選擇性）在函式中接受模型名稱值。 如果屬性名稱與要求中的值不符，就會提供這個選項。 例如，要求中的值可能是名稱中有連字號的標頭，如下列範例所示：

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody] 屬性

將 `[FromBody]` 屬性套用至參數，以從 HTTP 要求的主體填入其屬性。 ASP.NET Core 執行時間會將讀取主體的責任委派給輸入格式器。 [本文稍後](#input-formatters)會說明輸入格式器。

當套用 `[FromBody]` 至複雜型別參數時，會忽略套用至其屬性的任何系結來源屬性。 例如，下列 `Create` 動作會指定其 `pet` 參數已從主體填入：

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

`Pet`類別 `Breed` 會指定從查詢字串參數填入其屬性：

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

在上述範例中：

* `[FromQuery]`忽略屬性。
* `Breed`屬性不會從查詢字串參數填入。 

輸入格式器只會讀取本文，而不會瞭解系結來源屬性。 如果在主體中找到適當的值，則會使用該值來填入 `Breed` 屬性。

請勿套用 `[FromBody]` 至每個動作方法的一個以上參數。 一旦輸入格式器讀取要求資料流程之後，就無法再讀取它來系結其他 `[FromBody]` 參數。

### <a name="additional-sources"></a>其他來源

*值提供* 者會將來源資料提供給模型系結系統。 您可以撰寫並註冊自訂值提供者，其從其他來源取得模型繫結資料。 例如，您可能會想要來自 cookie s 或會話狀態的資料。 若要從新的來源取得資料：

* 建立會實作 `IValueProvider` 的類別。
* 建立會實作 `IValueProviderFactory` 的類別。
* 在 `Startup.ConfigureServices` 中註冊 Factory 類別。

範例應用程式包含 [值提供者](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) 和 [factory](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) 範例，可取得 s 的值 cookie 。 以下是 `Startup.ConfigureServices` 中的註冊碼：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

顯示的程式碼會將自訂值提供者放在所有內建值提供者的後面。  若要讓它成為清單中的第一個，請呼叫， `Insert(0, new CookieValueProviderFactory())` 而不是 `Add` 。

## <a name="no-source-for-a-model-property"></a>無模型屬性的來源

依預設，如果找不到模型屬性的值，則不會建立模型狀態錯誤。 屬性設定為 null 或預設值：

* 可為 null 的簡單類型會設定為 `null` 。
* 不可為 Null 的實值型別會設為 `default(T)`。 例如，參數 `int id` 設為 0。
* 針對複雜類型，模型系結會使用預設的函式來建立實例，而不需要設定屬性。
* 陣列設為 `Array.Empty<T>()`，但 `byte[]` 陣列設為 `null`。

如果模型狀態在模型屬性的表單欄位中找不到任何專案時，應該會失效，請使用 [`[BindRequired]`](#bindrequired-attribute) 屬性。

請注意，此 `[BindRequired]` 行為適用於來自已張貼表單資料的模型繫結，不適合要求本文中的 JSON 或 XML 資料。 要求主體資料是由 [輸入](#input-formatters)格式器處理。

## <a name="type-conversion-errors"></a>類型轉換錯誤

如果找到來源但無法轉換成目標型別，則會將模型狀態標示為無效。 目標參數或屬性會設為 null 或預設值，如上一節中所述。

在具有 `[ApiController]` 屬性的 API 控制器中，無效的模型狀態會導致自動 HTTP 400 回應。

在 Razor 頁面中，重新顯示包含錯誤訊息的頁面：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

用戶端驗證會攔截將會提交至頁面表單的最不正確資料 Razor 。 此驗證會讓您更難觸發上述醒目提示的程式碼。 範例應用程式包含 [ **提交** 日期] 不正確 [提交日期] 按鈕，會將錯誤的資料放入 [ **雇用日期** ] 欄位並提交表單。 此按鈕會顯示當資料轉換錯誤發生時，重新顯示頁面的程式碼如何運作。

當先前的程式碼重新顯示頁面時，表單欄位中不會顯示不正確輸入。 這是因為模型屬性已設為 null 或預設值。 無效的輸入確實會出現在錯誤訊息中。 但是，如果您想要在表單欄位中重新顯示不正確的資料，請考慮讓模型屬性成為字串，以手動方式執行資料轉換。

如果您不想要類型轉換錯誤導致模型狀態錯誤，則建議使用相同的策略。 在這種情況下，請將 model 屬性設為字串。

## <a name="simple-types"></a>簡單型別

模型繫結器可將來源字串轉換成的簡單型別包括：

* [布林值](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [十進位](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [列舉](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)
* [Uri](xref:System.UriTypeConverter)
* [版本](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>複雜類型

複雜型別必須具有公用預設的函式和可系結的公用可寫入屬性。 發生模型繫結時，類別會使用公用預設建構函式具現化。 

針對複雜類型的每個屬性，模型繫結會查看名稱模式 *prefix.property_name* 的來源。 如果找不到，它會只尋找沒有前置詞的 *property_name*。

若要繫結至參數，則前置詞是參數名稱。 若要繫結至 `PageModel` 公用屬性，則前置詞為公用屬性名稱。 某些屬性 (Attribute) 具有 `Prefix` 屬性 (Property)，其可讓您覆寫參數或屬性名稱的預設使用方法。

例如，假設複雜類型是下列 `Instructor` 類別：

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>前置詞 = 參數名稱

如果要繫結的模型是名為 `instructorToUpdate` 的參數：

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

模型繫結從查看索引鍵 `instructorToUpdate.ID` 的來源開始。 如果找不到，則會尋找 `ID` 沒有前置詞的。

### <a name="prefix--property-name"></a>前置詞 = 屬性名稱

如果要繫結的模型是控制器或 `PageModel` 類別名為 `Instructor` 的屬性：

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

模型繫結從查看索引鍵 `Instructor.ID` 的來源開始。 如果找不到，則會尋找 `ID` 沒有前置詞的。

### <a name="custom-prefix"></a>自訂前置詞

如果要繫結的模型是名為 `instructorToUpdate` 的參數，且 `Bind` 屬性指定 `Instructor` 為前置詞：

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

模型繫結從查看索引鍵 `Instructor.ID` 的來源開始。 如果找不到，則會尋找 `ID` 沒有前置詞的。

### <a name="attributes-for-complex-type-targets"></a>複雜類型目標的屬性

有數個內建屬性可用來控制複雜類型的模型系結：

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> 當張貼的表單資料為值來源時，這些屬性會影響模型繫結。 它們 ***不*** 會影響輸入格式器，其會處理張貼的 JSON 和 XML 要求主體。 [本文稍後](#input-formatters)會說明輸入格式器。

### <a name="bind-attribute"></a>[Bind] 屬性

可以套用至類別或方法參數。 指定模型繫結應包含哪些模型屬性。 `[Bind]` 不 ***會影響輸入*** 格式器。

在下列範例中，當呼叫任何處理常式或動作方法時，只會繫結 `Instructor` 模型的指定屬性：

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

在下列範例中，當呼叫 `OnPost` 方法時，只會繫結 `Instructor` 模型的指定屬性：

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

`[Bind]` 屬性可用來防止「建立」案例中的大量指派。 因為排除的屬性是設為 null 或預設值，而不是保持不變，所以在編輯案例中無法正常運作。 建議使用檢視模型而非 `[Bind]` 屬性來防禦大量指派。 如需詳細資訊，請參閱 [關於大量指派的安全性注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)。

### <a name="modelbinder-attribute"></a>[ModelBinder] 屬性

<xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> 可以套用至類型、屬性或參數。 它可讓您指定用來系結特定實例或型別的模型系結器類型。 例如：

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

屬性（ `[ModelBinder]` attribute）也可以用來在模型系結時，變更屬性或參數的名稱：

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a>[BindRequired] 屬性

只能套用至模型屬性，不能套用到方法參數。 如果模型的屬性不能發生繫結，則會造成模型繫結新增模型狀態錯誤。 以下為範例：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

另請參閱 `[Required]` [模型驗證](xref:mvc/models/validation#required-attribute)中的屬性討論。

### <a name="bindnever-attribute"></a>[BindNever] 屬性

只能套用至模型屬性，不能套用到方法參數。 避免模型繫結設定模型的屬性。 以下為範例：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a>集合

針對簡單型別集合的目標，模型繫結會尋找符合 *parameter_name* 或 *property_name* 的項目。 如果找不到相符項目，它會尋找其中一種沒有前置詞的受支援格式。 例如：

* 假設要繫結的參數是名為 `selectedCourses` 的陣列：

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* 表單或查詢字串資料可以是下列其中一種格式：
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* 只有表單資料支援下列格式：

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* 針對前列所有範例格式，模型繫結會將有兩個項目的陣列傳遞至 `selectedCourses` 參數：

  * selectedCourses [0] = 1050
  * selectedCourses [1] = 2000

  使用注標編號 ( 的資料格式 .。。[0] .。。[1] ... ) 必須確保從零開始依序編號。 下標編號中如有任何間距，則會忽略間隔後的所有項目。 例如，如果下標是 0 和 2，而不是 0 和 1，則忽略第二個項目。

## <a name="dictionaries"></a>字典

針對 `Dictionary` 目標，模型系結會尋找對 *parameter_name* 或 *property_name* 的相符專案。 如果找不到相符項目，它會尋找其中一種沒有前置詞的受支援格式。 例如：

* 假設目標參數是 `Dictionary<int, string>` 名為的 `selectedCourses` ：

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* 已張貼的表單或查詢字串資料看起來會像下列其中一個範例：

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* 針對前列所有範例格式，模型繫結會將有兩個項目的字典傳遞至 `selectedCourses` 參數：

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses ["2000"] = "經濟效益"
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a>函數系結和記錄類型

模型系結需要複雜類型具有無參數的函式。 和型輸入格式子都支援還原序列化 `System.Text.Json` `Newtonsoft.Json` 沒有無參數函式的類別。 

C # 9 引進了一種記錄類型，這是在網路上簡潔表示資料的絕佳方式。 ASP.NET Core 使用單一的函式加入模型系結和驗證記錄類型的支援：

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age, [BindNever] int Id);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

`Person/Index.cshtml`:

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

驗證記錄類型時，執行時間會特別針對參數（而不是在屬性上）搜尋系結和驗證中繼資料。

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>模型系結路由資料和查詢字串的全球化行為

ASP.NET Core 路由值提供者和查詢字串值提供者：

* 將值視為不變的文化特性。
* 預期 Url 的文化特性不變。

相反地，來自表單資料的值會進行區分文化特性的轉換。 這是設計的，因此可以跨地區設定共用 Url。

若要讓 ASP.NET Core 路由值提供者和查詢字串值提供者進行區分文化特性的轉換：

* 繼承自 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>
* 從[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs)或[RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)複製程式碼
* 使用[CultureInfo CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)取代傳遞給值提供者函式的[文化特性值](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)。
* 以您的新值取代 MVC 選項中的預設值提供者 factory：

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>特殊資料類型

模型繫結可以處理某些特殊資料類型。

### <a name="iformfile-and-iformfilecollection"></a>IFormFile 和 IFormFileCollection

HTTP 要求包含上傳的檔案。  也支援多個檔案的 `IEnumerable<IFormFile>`。

### <a name="cancellationtoken"></a>CancellationToken

動作可以選擇性地系結 `CancellationToken` 做為參數。 <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted>當 HTTP 要求的基礎連接中止時，此系結會發出信號。 動作可以使用此參數來取消長時間執行的非同步作業，而這些作業會做為控制器動作的一部分執行。

### <a name="formcollection"></a>FormCollection

用來擷取已張貼表單資料中的所有值。

## <a name="input-formatters"></a>輸入格式器

要求主體中的資料可以是 JSON、XML 或其他格式。 模型繫結會使用設定處理特定內容類型的「輸入格式器」，來剖析此資料。 根據預設，ASP.NET Core 包含以 JSON 為基礎的輸入格式器，用來處理 JSON 資料。 您可以新增其他內容類型的格式器。

ASP.NET Core 選取以 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 屬性為基礎的輸入格式器。 若無任何屬性，則它會使用 [Content-Type 標頭](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)。

使用內建的 XML 輸入格式器：

* 安裝 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 套件。

* 在 `Startup.ConfigureServices` 中，呼叫 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 或 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>。

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* 將 `Consumes` 屬性套用至要求本文應為 XML 的控制器類別或動作方法。

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  如需詳細資訊，請參閱 [XML 序列化簡介](/dotnet/standard/serialization/introducing-xml-serialization)。

### <a name="customize-model-binding-with-input-formatters"></a>使用輸入格式子自訂模型系結

輸入格式器完全負責從要求主體讀取資料。 若要自訂此程式，請設定輸入格式器所使用的 Api。 本節說明如何自訂 `System.Text.Json` 型輸入格式器，以瞭解名為的自訂型別 `ObjectId` 。 

請考慮下列模型，其中包含名為的自訂 `ObjectId` 屬性 `Id` ：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

若要在使用時自訂模型系結處理常式 `System.Text.Json` ，請建立衍生自的類別 <xref:System.Text.Json.Serialization.JsonConverter%601> ：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

若要使用自訂轉換器，請將 <xref:System.Text.Json.Serialization.JsonConverterAttribute> 屬性套用至類型。 在下列範例中， `ObjectId` 會將類型設定 `ObjectIdConverter` 為其自訂轉換器：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

如需詳細資訊，請參閱 [如何撰寫自訂轉換器](/dotnet/standard/serialization/system-text-json-converters-how-to)。

## <a name="exclude-specified-types-from-model-binding"></a>排除模型繫結中的指定類型

模型繫結和驗證系統的行為是由 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) 所驅動。 您可以將詳細資料提供者新增至 [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)，藉以自訂 `ModelMetadata`。 內建的詳細資料提供者可用於停用模型繫結或驗證所指定類型。

若要停用指定類型之所有模型的模型繫結，請在 `Startup.ConfigureServices` 中新增 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>。 例如，若要對類型為 `System.Version` 的所有模型停用模型繫結：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

若要停用指定類型屬性的驗證，請在 `Startup.ConfigureServices` 中新增 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>。 例如，若要針對類型為 `System.Guid` 的屬性停用驗證：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a>自訂模型繫結器

您可以撰寫自訂的模型繫結器來擴充模型繫結，並使用 `[ModelBinder]` 屬性以針對特定目標來選取它。 深入了解[自訂模型繫結](xref:mvc/advanced/custom-model-binding)。

## <a name="manual-model-binding"></a>手動模型系結 

使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 方法即可手動叫用模型繫結。 此方法已於 `ControllerBase` 和 `PageModel` 類別中定義。 方法多載可讓您指定要使用的前置詞和值提供者。 如果模型繫結失敗，此方法會傳回 `false`。 以下為範例：

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  使用值提供者從表單主體、查詢字串和路由資料取得資料。 `TryUpdateModelAsync` 通常是： 

* 搭配 Razor 使用控制器和 views 的頁面和 MVC 應用程式來防止過度張貼。
* 除非從表單資料、查詢字串和路由資料使用，否則不會與 web API 搭配使用。 使用 JSON 的 Web API 端點會使用 [輸入](#input-formatters) 格式子將要求本文還原序列化為物件。

如需詳細資訊，請參閱 [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync)。

## <a name="fromservices-attribute"></a>[FromServices] 屬性

這個屬性的名稱會遵循指定資料來源之模型系結屬性的模式。 但是，它並不是從值提供者系結資料。 它從[相依性插入](xref:fundamentals/dependency-injection)容器取得類型的執行個體。 其目的是只有在呼叫特定方法時，才在您需要服務時提供建構函式插入的替代項目。

## <a name="additional-resources"></a>其他資源

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本文會說明何謂模型繫結、其運作方式，以及如何自訂其行為。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/models/model-binding/samples) ([如何下載](xref:index#how-to-download-a-sample))。

## <a name="what-is-model-binding"></a>何謂模型繫結

控制器和 Razor 頁面會處理來自 HTTP 要求的資料。 例如，路由資料可能會提供記錄索引鍵，而已張貼的表單欄位可能會提供模型屬性的值。 撰寫程式碼來擷取這些值的每一個並將它們從字串轉換成 .NET 類型，不但繁瑣又容易發生錯誤。 模型繫結會自動化此程序。 模型繫結系統：

* 從各種來源（例如，路由資料、表單欄位和查詢字串）抓取資料。
* 提供資料給 Razor 方法參數和公用屬性中的控制器和頁面。
* 將字串資料轉換成 .NET 類型。
* 更新複雜類型的屬性。

## <a name="example"></a>範例

假設您有下列動作方法：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

而應用程式收到具有此 URL 的要求：

```
http://contoso.com/api/pets/2?DogsOnly=true
```

在路由系統選取動作方法之後，模型系結會經歷下列步驟：

* 尋找第一個參數 `GetByID`，它是名為 `id` 的整數。
* 查看 HTTP 要求中所有可用的來源，在路由資料中找到 `id` = "2"。
* 將字串 "2" 轉換成整數 2。
* 尋找的下一個參數 `GetByID` ，也就是名為的布林值 `dogsOnly` 。
* 查看來源，在查詢字串中找到 "DogsOnly=true"。 名稱比對不區分大小寫。
* 將字串 "true" 轉換成布林值 `true` 。

架構接著會呼叫 `GetById` 方法，針對 `id` 參數傳送 2、`dogsOnly` 參數傳送 `true`。

在上例中，模型繫結目標都是簡單型別的方法參數。 目標也可以是複雜類型的屬性。 成功系結每個屬性之後，就會針對該屬性進行 [模型驗證](xref:mvc/models/validation) 。 哪些資料繫結至模型，以及任何繫結或驗證錯誤的記錄，都會儲存在 [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) 或 [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)。 為了解此程序是否成功，應用程式會檢查 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 旗標。

## <a name="targets"></a>目標

模型繫結會嘗試尋找下列幾種目標的值：

* 要求路由目標的控制器動作方法參數。
* Razor傳送要求的頁面處理常式方法的參數。 
* 控制站的公用屬性或 `PageModel` 類別，如由屬性指定。

### <a name="bindproperty-attribute"></a>[BindProperty] 屬性

可以套用至控制器或類別的公用屬性 `PageModel` ，使模型系結成為該屬性的目標：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a>[BindProperties] 屬性

可在 ASP.NET Core 2.1 和更新版本中使用。  可以套用至控制器或 `PageModel` 類別，以告知模型系結將目標設為類別的所有公用屬性：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a>適用於 HTTP GET 要求的模型繫結

根據預設，屬性不會針對 HTTP GET 要求繫結。 一般而言，GET 要求只需要記錄識別碼參數。 此記錄識別碼用來查詢資料庫中的項目。 因此，不需要系結保存模型實例的屬性。 在您要從 GET 要求將屬性繫結至資料的案例中，請將 `SupportsGet` 屬性設為 `true`：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a>來源

根據預設，模型繫結會從下列 HTTP 要求的來源中，取得索引鍵/值組形式的資料：

1. 表單欄位
1. [具有 [ApiController] 屬性之控制器](xref:web-api/index#binding-source-parameter-inference)的要求主體 (。 ) 
1. 路由傳送資料
1. 查詢字串參數
1. 已上傳的檔案

針對每個目標參數或屬性，系統會依照上述清單中指出的順序掃描來源。 但也有一些例外：

* 路由資料和查詢字串值只用於簡單型別。
* 上傳的檔案只會系結至執行或的目標型別 `IFormFile` `IEnumerable<IFormFile>` 。

如果預設來源不正確，請使用下列其中一個屬性來指定來源：

* [`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -取得查詢字串中的值。 
* [`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -從路由資料取得值。
* [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -從張貼的表單欄位取得值。
* [`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -從要求主體取得值。
* [`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -取得 HTTP 標頭的值。

這些屬性：

* 會個別新增至模型屬性 (不是模型類別)，如下列範例所示：

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* （選擇性）在函式中接受模型名稱值。 如果屬性名稱與要求中的值不符，就會提供這個選項。 例如，要求中的值可能是名稱中有連字號的標頭，如下列範例所示：

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a>[FromBody] 屬性

將 `[FromBody]` 屬性套用至參數，以從 HTTP 要求的主體填入其屬性。 ASP.NET Core 執行時間會將讀取主體的責任委派給輸入格式器。 [本文稍後](#input-formatters)會說明輸入格式器。

當套用 `[FromBody]` 至複雜型別參數時，會忽略套用至其屬性的任何系結來源屬性。 例如，下列 `Create` 動作會指定其 `pet` 參數已從主體填入：

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

`Pet`類別 `Breed` 會指定從查詢字串參數填入其屬性：

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

在上述範例中：

* `[FromQuery]`忽略屬性。
* `Breed`屬性不會從查詢字串參數填入。 

輸入格式器只會讀取本文，而不會瞭解系結來源屬性。 如果在主體中找到適當的值，則會使用該值來填入 `Breed` 屬性。

請勿套用 `[FromBody]` 至每個動作方法的一個以上參數。 一旦輸入格式器讀取要求資料流程之後，就無法再讀取它來系結其他 `[FromBody]` 參數。

### <a name="additional-sources"></a>其他來源

*值提供* 者會將來源資料提供給模型系結系統。 您可以撰寫並註冊自訂值提供者，其從其他來源取得模型繫結資料。 例如，您可能會想要來自 cookie s 或會話狀態的資料。 若要從新的來源取得資料：

* 建立會實作 `IValueProvider` 的類別。
* 建立會實作 `IValueProviderFactory` 的類別。
* 在 `Startup.ConfigureServices` 中註冊 Factory 類別。

範例應用程式包含 [值提供者](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) 和 [factory](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) 範例，可取得 s 的值 cookie 。 以下是 `Startup.ConfigureServices` 中的註冊碼：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

顯示的程式碼會將自訂值提供者放在所有內建值提供者的後面。  若要讓它成為清單中的第一個，請呼叫， `Insert(0, new CookieValueProviderFactory())` 而不是 `Add` 。

## <a name="no-source-for-a-model-property"></a>無模型屬性的來源

依預設，如果找不到模型屬性的值，則不會建立模型狀態錯誤。 屬性設定為 null 或預設值：

* 可為 null 的簡單類型會設定為 `null` 。
* 不可為 Null 的實值型別會設為 `default(T)`。 例如，參數 `int id` 設為 0。
* 針對複雜類型，模型系結會使用預設的函式來建立實例，而不需要設定屬性。
* 陣列設為 `Array.Empty<T>()`，但 `byte[]` 陣列設為 `null`。

如果模型狀態在模型屬性的表單欄位中找不到任何專案時，應該會失效，請使用 [`[BindRequired]`](#bindrequired-attribute) 屬性。

請注意，此 `[BindRequired]` 行為適用於來自已張貼表單資料的模型繫結，不適合要求本文中的 JSON 或 XML 資料。 要求主體資料是由 [輸入](#input-formatters)格式器處理。

## <a name="type-conversion-errors"></a>類型轉換錯誤

如果找到來源但無法轉換成目標型別，則會將模型狀態標示為無效。 目標參數或屬性會設為 null 或預設值，如上一節中所述。

在具有 `[ApiController]` 屬性的 API 控制器中，無效的模型狀態會導致自動 HTTP 400 回應。

在 Razor 頁面中，重新顯示包含錯誤訊息的頁面：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

用戶端驗證會攔截將會提交至頁面表單的最不正確資料 Razor 。 此驗證會讓您更難觸發上述醒目提示的程式碼。 範例應用程式包含 [ **提交** 日期] 不正確 [提交日期] 按鈕，會將錯誤的資料放入 [ **雇用日期** ] 欄位並提交表單。 此按鈕會顯示當資料轉換錯誤發生時，重新顯示頁面的程式碼如何運作。

當先前的程式碼重新顯示頁面時，表單欄位中不會顯示不正確輸入。 這是因為模型屬性已設為 null 或預設值。 無效的輸入確實會出現在錯誤訊息中。 但是，如果您想要在表單欄位中重新顯示不正確的資料，請考慮讓模型屬性成為字串，以手動方式執行資料轉換。

如果您不想要類型轉換錯誤導致模型狀態錯誤，則建議使用相同的策略。 在這種情況下，請將 model 屬性設為字串。

## <a name="simple-types"></a>簡單型別

模型繫結器可將來源字串轉換成的簡單型別包括：

* [布林值](xref:System.ComponentModel.BooleanConverter)
* [Byte](xref:System.ComponentModel.ByteConverter)、[SByte](xref:System.ComponentModel.SByteConverter)
* [Char](xref:System.ComponentModel.CharConverter)
* [DateTime](xref:System.ComponentModel.DateTimeConverter)
* [DateTimeOffset](xref:System.ComponentModel.DateTimeOffsetConverter)
* [十進位](xref:System.ComponentModel.DecimalConverter)
* [Double](xref:System.ComponentModel.DoubleConverter)
* [列舉](xref:System.ComponentModel.EnumConverter)
* [Guid](xref:System.ComponentModel.GuidConverter)
* [Int16](xref:System.ComponentModel.Int16Converter)、[Int32](xref:System.ComponentModel.Int32Converter)、[Int64](xref:System.ComponentModel.Int64Converter)
* [Single](xref:System.ComponentModel.SingleConverter)
* [TimeSpan](xref:System.ComponentModel.TimeSpanConverter)
* [UInt16](xref:System.ComponentModel.UInt16Converter)、[UInt32](xref:System.ComponentModel.UInt32Converter)、[UInt64](xref:System.ComponentModel.UInt64Converter)
* [Uri](xref:System.UriTypeConverter)
* [版本](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a>複雜類型

複雜型別必須具有公用預設的函式和可系結的公用可寫入屬性。 發生模型繫結時，類別會使用公用預設建構函式具現化。 

針對複雜類型的每個屬性，模型繫結會查看名稱模式 *prefix.property_name* 的來源。 如果找不到，它會只尋找沒有前置詞的 *property_name*。

若要繫結至參數，則前置詞是參數名稱。 若要繫結至 `PageModel` 公用屬性，則前置詞為公用屬性名稱。 某些屬性 (Attribute) 具有 `Prefix` 屬性 (Property)，其可讓您覆寫參數或屬性名稱的預設使用方法。

例如，假設複雜類型是下列 `Instructor` 類別：

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a>前置詞 = 參數名稱

如果要繫結的模型是名為 `instructorToUpdate` 的參數：

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

模型繫結從查看索引鍵 `instructorToUpdate.ID` 的來源開始。 如果找不到，則會尋找 `ID` 沒有前置詞的。

### <a name="prefix--property-name"></a>前置詞 = 屬性名稱

如果要繫結的模型是控制器或 `PageModel` 類別名為 `Instructor` 的屬性：

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

模型繫結從查看索引鍵 `Instructor.ID` 的來源開始。 如果找不到，則會尋找 `ID` 沒有前置詞的。

### <a name="custom-prefix"></a>自訂前置詞

如果要繫結的模型是名為 `instructorToUpdate` 的參數，且 `Bind` 屬性指定 `Instructor` 為前置詞：

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

模型繫結從查看索引鍵 `Instructor.ID` 的來源開始。 如果找不到，則會尋找 `ID` 沒有前置詞的。

### <a name="attributes-for-complex-type-targets"></a>複雜類型目標的屬性

有數個內建屬性可用來控制複雜類型的模型系結：

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> 當張貼的表單資料為值來源時，這些屬性會影響模型繫結。 它們不會影響處理已張貼 JSON 和 XML 要求本文的輸入格式器。 [本文稍後](#input-formatters)會說明輸入格式器。
>
> 另請參閱 `[Required]` [模型驗證](xref:mvc/models/validation#required-attribute)中的屬性討論。

### <a name="bindrequired-attribute"></a>[BindRequired] 屬性

只能套用至模型屬性，不能套用到方法參數。 如果模型的屬性不能發生繫結，則會造成模型繫結新增模型狀態錯誤。 以下為範例：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a>[BindNever] 屬性

只能套用至模型屬性，不能套用到方法參數。 避免模型繫結設定模型的屬性。 以下為範例：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a>[Bind] 屬性

可以套用至類別或方法參數。 指定模型繫結應包含哪些模型屬性。

在下列範例中，當呼叫任何處理常式或動作方法時，只會繫結 `Instructor` 模型的指定屬性：

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

在下列範例中，當呼叫 `OnPost` 方法時，只會繫結 `Instructor` 模型的指定屬性：

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

`[Bind]` 屬性可用來防止「建立」案例中的大量指派。 因為排除的屬性是設為 null 或預設值，而不是保持不變，所以在編輯案例中無法正常運作。 建議使用檢視模型而非 `[Bind]` 屬性來防禦大量指派。 如需詳細資訊，請參閱 [關於大量指派的安全性注意事項](xref:data/ef-mvc/crud#security-note-about-overposting)。

## <a name="collections"></a>集合

針對簡單型別集合的目標，模型繫結會尋找符合 *parameter_name* 或 *property_name* 的項目。 如果找不到相符項目，它會尋找其中一種沒有前置詞的受支援格式。 例如：

* 假設要繫結的參數是名為 `selectedCourses` 的陣列：

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* 表單或查詢字串資料可以是下列其中一種格式：
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* 只有表單資料支援下列格式：

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* 針對前列所有範例格式，模型繫結會將有兩個項目的陣列傳遞至 `selectedCourses` 參數：

  * selectedCourses [0] = 1050
  * selectedCourses [1] = 2000

  使用注標編號 ( 的資料格式 .。。[0] .。。[1] ... ) 必須確保從零開始依序編號。 下標編號中如有任何間距，則會忽略間隔後的所有項目。 例如，如果下標是 0 和 2，而不是 0 和 1，則忽略第二個項目。

## <a name="dictionaries"></a>字典

針對 `Dictionary` 目標，模型系結會尋找對 *parameter_name* 或 *property_name* 的相符專案。 如果找不到相符項目，它會尋找其中一種沒有前置詞的受支援格式。 例如：

* 假設目標參數是 `Dictionary<int, string>` 名為的 `selectedCourses` ：

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* 已張貼的表單或查詢字串資料看起來會像下列其中一個範例：

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* 針對前列所有範例格式，模型繫結會將有兩個項目的字典傳遞至 `selectedCourses` 參數：

  * selectedCourses["1050"]="Chemistry"
  * selectedCourses ["2000"] = "經濟效益"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a>模型系結路由資料和查詢字串的全球化行為

ASP.NET Core 路由值提供者和查詢字串值提供者：

* 將值視為不變的文化特性。
* 預期 Url 的文化特性不變。

相反地，來自表單資料的值會進行區分文化特性的轉換。 這是設計的，因此可以跨地區設定共用 Url。

若要讓 ASP.NET Core 路由值提供者和查詢字串值提供者進行區分文化特性的轉換：

* 繼承自 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>
* 從[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs)或[RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/main/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)複製程式碼
* 使用[CultureInfo CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)取代傳遞給值提供者函式的[文化特性值](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30)。
* 以您的新值取代 MVC 選項中的預設值提供者 factory：

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a>特殊資料類型

模型繫結可以處理某些特殊資料類型。

### <a name="iformfile-and-iformfilecollection"></a>IFormFile 和 IFormFileCollection

HTTP 要求包含上傳的檔案。  也支援多個檔案的 `IEnumerable<IFormFile>`。

### <a name="cancellationtoken"></a>CancellationToken

用來取消非同步控制器中的活動。

### <a name="formcollection"></a>FormCollection

用來擷取已張貼表單資料中的所有值。

## <a name="input-formatters"></a>輸入格式器

要求主體中的資料可以是 JSON、XML 或其他格式。 模型繫結會使用設定處理特定內容類型的「輸入格式器」，來剖析此資料。 根據預設，ASP.NET Core 包含以 JSON 為基礎的輸入格式器，用來處理 JSON 資料。 您可以新增其他內容類型的格式器。

ASP.NET Core 選取以 [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) 屬性為基礎的輸入格式器。 若無任何屬性，則它會使用 [Content-Type 標頭](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)。

使用內建的 XML 輸入格式器：

* 安裝 `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet 套件。

* 在 `Startup.ConfigureServices` 中，呼叫 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> 或 <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>。

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* 將 `Consumes` 屬性套用至要求本文應為 XML 的控制器類別或動作方法。

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  如需詳細資訊，請參閱 [XML 序列化簡介](/dotnet/standard/serialization/introducing-xml-serialization)。

## <a name="exclude-specified-types-from-model-binding"></a>排除模型繫結中的指定類型

模型繫結和驗證系統的行為是由 [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) 所驅動。 您可以將詳細資料提供者新增至 [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)，藉以自訂 `ModelMetadata`。 內建的詳細資料提供者可用於停用模型繫結或驗證所指定類型。

若要停用指定類型之所有模型的模型繫結，請在 `Startup.ConfigureServices` 中新增 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider>。 例如，若要對類型為 `System.Version` 的所有模型停用模型繫結：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

若要停用指定類型屬性的驗證，請在 `Startup.ConfigureServices` 中新增 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider>。 例如，若要針對類型為 `System.Guid` 的屬性停用驗證：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a>自訂模型繫結器

您可以撰寫自訂的模型繫結器來擴充模型繫結，並使用 `[ModelBinder]` 屬性以針對特定目標來選取它。 深入了解[自訂模型繫結](xref:mvc/advanced/custom-model-binding)。

## <a name="manual-model-binding"></a>手動模型系結

使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> 方法即可手動叫用模型繫結。 此方法已於 `ControllerBase` 和 `PageModel` 類別中定義。 方法多載可讓您指定要使用的前置詞和值提供者。 如果模型繫結失敗，此方法會傳回 `false`。 以下為範例：

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a>[FromServices] 屬性

這個屬性的名稱會遵循指定資料來源之模型系結屬性的模式。 但是，它並不是從值提供者系結資料。 它從[相依性插入](xref:fundamentals/dependency-injection)容器取得類型的執行個體。 其目的是只有在呼叫特定方法時，才在您需要服務時提供建構函式插入的替代項目。

## <a name="additional-resources"></a>其他資源

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
