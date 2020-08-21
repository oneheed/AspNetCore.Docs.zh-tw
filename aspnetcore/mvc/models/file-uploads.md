---
title: 在 ASP.NET Core 上傳檔案
author: rick-anderson
description: 如何使用模型繫結和資料流在 ASP.NET Core MVC 上傳檔案。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
no-loc:
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
uid: mvc/models/file-uploads
ms.openlocfilehash: 6ff78b26e8e2363cf6c54ebb2a392f390fb2995c
ms.sourcegitcommit: cd412a44f26cb416ceb348fc0a1ccc9a6e9ca73e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2020
ms.locfileid: "88720275"
---
# <a name="upload-files-in-aspnet-core"></a>在 ASP.NET Core 上傳檔案

[Steve Smith](https://ardalis.com/)和[Rutger 風暴](https://github.com/rutix)

::: moniker range=">= aspnetcore-5.0"

ASP.NET Core 支援針對較小的檔案使用緩衝的模型系結上傳一或多個檔案，並針對較大的檔案上傳未緩衝

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="security-considerations"></a>安全性考量

提供使用者將檔案上傳至伺服器的功能時，請特別小心。 攻擊者可能會嘗試：

* 執行 [拒絕服務的](/windows-hardware/drivers/ifs/denial-of-service) 攻擊。
* 上傳病毒或惡意程式碼。
* 以其他方式危害網路和伺服器。

降低成功攻擊可能性的安全性步驟如下：

* 將檔案上傳到專用檔案上傳區域，最好是非系統磁片磁碟機。 專用位置可讓您更輕鬆地對上傳的檔案強加安全性限制。 停用檔案上傳位置的執行許可權。&dagger;
* 請勿將上傳的 **檔案保存在** 與應用程式相同的目錄樹狀結構中。&dagger;
* 使用應用程式所決定的安全檔案名。 請勿使用使用者提供的檔案名或上傳檔案的不受信任檔案名。 &dagger; HTML 會在顯示不受信任的檔案名時進行編碼。 例如，記錄檔案名或在 UI 中顯示 (Razor 會自動以 HTML 編碼輸出) 。
* 只允許應用程式設計規格的核准副檔名。&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* 確認已在伺服器上執行用戶端檢查。 &dagger; 用戶端檢查很容易規避。
* 檢查上傳檔案的大小。 設定大小上限以防止大上傳。&dagger;
* 使用相同名稱的已上傳檔案覆寫檔案時，請先檢查資料庫或實體儲存體的檔案名，再上傳檔案。
* **在儲存檔案之前，在上傳的內容上執行病毒/惡意程式碼掃描器。**

&dagger;範例應用程式會示範符合準則的方法。

> [!WARNING]
> 將惡意程式碼上傳至系統經常是執行程式碼的第一步，該程式碼可能：
>
> * 完全掌握系統的控制權。
> * 使用系統損毀的結果來多載系統。
> * 洩漏使用者或系統資料。
> * 將刻套用至公用 UI。
>
> 如需在接受來自使用者的檔案時減少攻擊介面區的資訊，請參閱下列資源：
>
> * [不受限制的檔案上傳](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure 安全性：請確保在接受來自使用者的檔案時，適當的控制項已就緒](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

如需有關實施安全性措施的詳細資訊，包括範例應用程式中的範例，請參閱 [驗證](#validation) 一節。

## <a name="storage-scenarios"></a>儲存體案例

檔案的一般儲存選項包括：

* 資料庫

  * 若為小型檔案上傳，資料庫通常會比實體儲存體 (檔案系統或網路共用) 選項更快。
  * 資料庫通常比實體儲存體選項更方便，因為抓取使用者資料的資料庫記錄可以同時提供檔案內容 (例如，) 的圖片。
  * 資料庫的成本可能比使用資料儲存體服務還便宜。

* 實體儲存體 (檔案系統或網路共用) 

  * 針對大型檔案上傳：
    * 資料庫限制可能會限制上傳的大小。
    * 實體儲存體通常比資料庫中的儲存體便宜。
  * 實體儲存體的成本可能比使用資料儲存體服務更便宜。
  * 應用程式的進程必須具有儲存位置的讀取和寫入權限。 **絕不授與 execute 許可權。**

* 資料儲存體服務 (例如 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)) 

  * 服務通常會在內部部署解決方案中提供改良的擴充性和復原能力，這些解決方案通常會受限於單一失敗點。
  * 在大型儲存體基礎結構案例中，服務可能會降低成本。

  如需詳細資訊，請參閱 [快速入門：使用 .net 在物件儲存體中建立 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。

## <a name="file-upload-scenarios"></a>檔案上傳案例

上傳檔案的兩個一般方法是緩衝處理和串流處理。

**緩衝處理**

系統會將整個檔案讀入 <xref:Microsoft.AspNetCore.Http.IFormFile> ，這是用來處理或儲存檔案之檔案的 c # 標記法。

檔案上傳所使用的資源 (磁片、記憶體) 取決於並行檔案上傳的數目和大小。 如果應用程式嘗試緩衝過多上傳，則當網站的記憶體或磁碟空間不足時，就會損毀。 如果檔案上傳的大小或頻率是耗盡應用程式資源，請使用串流處理。

> [!NOTE]
> 任何超過 64 KB 的緩衝檔案都會從記憶體移至磁片上的暫存檔案。

本主題的下列各節涵蓋了小型檔案的緩衝：

* [實體儲存體](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Database](#upload-small-files-with-buffered-model-binding-to-a-database)

**串流**

從多部分要求接收檔案，並由應用程式直接處理或儲存。 串流無法大幅改善效能。 串流處理會在上傳檔案時減少記憶體或磁碟空間的需求。

[上傳含有串流的大型](#upload-large-files-with-streaming)檔案一節中涵蓋了串流大型檔案。

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>將具有緩衝模型系結的小型檔案上傳至實體儲存體

若要上傳小型檔案，請使用多部分形式的表單，或使用 JavaScript 來建立 POST 要求。

下列範例將示範 Razor 如何使用頁面表單，在範例應用程式) 中上傳單一檔案 (*Pages/BufferedSingleFileUploadPhysical. cshtml* ：

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

下列範例類似于先前的範例，不同之處在于：

* JavaScript 的 ([FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 用來提交表單的資料。
* 沒有任何驗證。

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

若要針對 [不支援 FETCH API](https://caniuse.com/#feat=fetch)的用戶端，在 JavaScript 中執行表單貼文，請使用下列其中一種方法：

* 使用 Fetch Polyfill (例如， [Polyfill (github/fetch) ](https://github.com/github/fetch)) 。
* 請使用 `XMLHttpRequest`。 例如：

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

為了支援檔案上傳，HTML 表單必須指定 (`enctype`) 的編碼類型 `multipart/form-data` 。

`files`若要讓輸入元素支援上傳多個檔案，請 `multiple` 在元素上提供屬性 `<input>` ：

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

您可以使用，透過 [模型](xref:mvc/models/model-binding) 系結來存取上傳至伺服器的個別檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 。 範例應用程式會示範針對資料庫和實體儲存案例的多個緩衝檔案上傳。

<a name="filename"></a>

> [!WARNING]
> 請勿 **使用的** `FileName` 屬性做 <xref:Microsoft.AspNetCore.Http.IFormFile> 為顯示和記錄。 顯示或記錄時，HTML 會將檔案名編碼。 攻擊者可以提供惡意檔案名，包括完整路徑或相對路徑。 應用程式應該：
>
> * 從使用者提供的檔案名中移除路徑。
> * 針對 UI 或記錄，儲存 HTML 編碼、路徑移除的檔案名。
> * 為儲存體產生新的隨機檔案名。
>
> 下列程式碼會將路徑從檔案名中移除：
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> 目前為止所提供的範例不考慮安全性考慮。 下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：
>
> * [安全性考量](#security-considerations)
> * [驗證](#validation)

使用模型系結和來上傳檔案時 <xref:Microsoft.AspNetCore.Http.IFormFile> ，動作方法可以接受：

* 單一 <xref:Microsoft.AspNetCore.Http.IFormFile> 。
* 下列任何代表數個檔案的集合：
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [清單](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> 系結符合依名稱的表單檔案。 例如，中的 HTML `name` 值 `<input type="file" name="formFile">` 必須符合 () 系結的 c # 參數/屬性 `FormFile` 。 如需詳細資訊，請參閱 [比 [對名稱] 屬性值與 POST 方法的參數名稱](#match-name-attribute-value-to-parameter-name-of-post-method) 一節。

下列範例將：

* 迴圈一或多個上傳的檔案。
* 使用 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 傳回檔案的完整路徑，包括檔案名。 
* 使用應用程式所產生的檔案名，將檔案儲存到本機檔案系統。
* 傳回上傳檔案的總數和大小。

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

用 `Path.GetRandomFileName` 來產生不含路徑的檔案名。 在下列範例中，路徑是從 configuration 取得：

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

傳遞至的路徑 <xref:System.IO.FileStream> *必須* 包含檔案名。 如果未提供檔案名， <xref:System.UnauthorizedAccessException> 則會在執行時間擲回。

使用此技術所上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 會在處理之前，在記憶體或伺服器的磁片上進行緩衝處理。 在動作方法內， <xref:Microsoft.AspNetCore.Http.IFormFile> 內容可作為進行存取 <xref:System.IO.Stream> 。 除了本機檔案系統之外，檔案也可以儲存到網路共用或檔案儲存體服務（例如 [Azure Blob 儲存體](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)）。

如需在多個要上傳的檔案上進行迴圈，並使用安全檔案名的另一個範例，請參閱範例應用程式中的*Pages/BufferedMultipleFileUploadPhysical。*

> [!WARNING]
> [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 會擲回， <xref:System.IO.IOException> 如果在不刪除先前的暫存檔的情況下建立65535個以上的檔案，則擲回。 65535檔案的限制為每個伺服器的限制。 如需 Windows OS 上這項限制的詳細資訊，請參閱下列主題中的備註：
>
> * [GetTempFileNameA 函式](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>將具有緩衝模型系結的小型檔案上傳至資料庫

若要使用 [Entity Framework](/ef/core/index)將二進位檔案資料儲存在資料庫中，請 <xref:System.Byte> 在實體上定義陣列屬性：

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

針對包含下列各類別的類別，指定頁面模型屬性 <xref:Microsoft.AspNetCore.Http.IFormFile> ：

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接當做動作方法參數使用，或作為系結模型屬性。 先前的範例會使用系結模型屬性。

在 `FileUpload` Razor 頁面表單中使用：

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

當表單張貼至伺服器時，將複製 <xref:Microsoft.AspNetCore.Http.IFormFile> 到資料流程，並將它儲存為資料庫中的位元組陣列。 在下列範例中，會 `_dbContext` 儲存應用程式的資料庫內容：

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

上述範例類似于範例應用程式中所示範的案例：

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb，.cs*

> [!WARNING]
> 將二進位資料儲存至關聯式資料庫時請小心，因為它可能會對效能造成不良影響。
>
> 請勿依賴或信任 `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> 不含驗證的屬性。 `FileName`屬性只能用於顯示目的，而且只適用于 HTML 編碼。
>
> 提供的範例不會考慮安全性考慮。 下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：
>
> * [安全性考量](#security-considerations)
> * [驗證](#validation)

### <a name="upload-large-files-with-streaming"></a>使用串流上傳大型檔案

下列範例示範如何使用 JavaScript 將檔案串流至控制器動作。 檔案的 antiforgery token 是使用自訂篩選屬性所產生，並且傳遞至用戶端 HTTP 標頭，而不是在要求主體中。 因為動作方法會直接處理已上傳的資料，所以其他自訂篩選會停用表單模型系結。 在動作內，會使用 `MultipartReader` 來讀取表單內容，以讀取每個個別 `MultipartSection`、處理檔案，或視需要儲存內容。 讀取多部分區段之後，動作會執行它自己的模型系結。

初始頁面回應會載入表單，並透過屬性) 將 antiforgery token 儲存在 cookie (中 `GenerateAntiforgeryTokenCookieAttribute` 。 屬性使用 ASP.NET Core 的內建 [antiforgery 支援](xref:security/anti-request-forgery) 來設定 cookie 具有要求權杖的。

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`用來停用模型系結：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

在範例應用程式中 `GenerateAntiforgeryTokenCookieAttribute` ， `DisableFormValueModelBindingAttribute` 會 `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 頁面慣例](xref:razor-pages/razor-pages-conventions)，並在和的頁面應用程式模型中套用為篩選：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

由於模型系結不會讀取表單，因此從表單系結的參數不會系結 (查詢、路由和標頭會繼續運作) 。 動作方法會直接與屬性搭配使用 `Request` 。 `MultipartReader` 是用來讀取每個區段。 索引鍵/值資料儲存在中 `KeyValueAccumulator` 。 讀取多部分區段之後，的內容 `KeyValueAccumulator` 會用來將表單資料系結至模型類型。

`StreamingController.UploadDatabase`使用 EF Core 串流至資料庫的完整方法：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*公用程式/MultipartRequestHelper .cs*) ：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

`StreamingController.UploadPhysical`串流至實體位置的完整方法：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

在範例應用程式中，驗證檢查是由處理 `FileHelpers.ProcessStreamedFile` 。

## <a name="validation"></a>驗證

範例應用程式的類別會示範經過緩衝處理和串流處理檔案上 `FileHelpers` 傳的數個檢查 <xref:Microsoft.AspNetCore.Http.IFormFile> 。 若要 <xref:Microsoft.AspNetCore.Http.IFormFile> 在範例應用程式中處理緩衝的檔案上傳，請參閱 `ProcessFormFile` *公用程式/FileHelpers .cs* 檔案中的方法。 如需處理資料流程檔，請參閱 `ProcessStreamedFile` 相同檔案中的方法。

> [!WARNING]
> 範例應用程式中所示範的驗證處理方法，不會掃描所上傳檔案的內容。 在大部分的生產案例中，會在檔案上使用病毒/惡意程式碼掃描器 API，然後將檔案提供給使用者或其他系統。
>
> 雖然本主題範例提供驗證技術的實用範例，但 `FileHelpers` 除非您執行下列作業，否則請勿在生產應用程式中執行類別：
>
> * 充分瞭解執行。
> * 針對應用程式的環境和規格，適當地修改執行。
>
> **絕對不要在應用程式中執行安全程式碼，而不需滿足這些需求。**

### <a name="content-validation"></a>內容驗證

**在上傳的內容上使用協力廠商病毒/惡意程式碼掃描 API。**

掃描檔案對大量案例中的伺服器資源有需求。 如果要求處理效能因為檔案掃描而降低，請考慮將掃描工作卸載至 [背景服務](xref:fundamentals/host/hosted-services)，可能是在與應用程式伺服器不同的伺服器上執行的服務。 上傳的檔案通常會保留在隔離區域中，直到背景病毒掃描程式檢查它們為止。 當檔案通過時，檔案會移至一般檔案儲存位置。 這些步驟通常會與指出檔案掃描狀態的資料庫記錄一起執行。 藉由使用這種方法，應用程式和應用程式伺服器仍會專注于回應要求。

### <a name="file-extension-validation"></a>副檔名驗證

所上傳檔案的副檔名應針對允許的延伸模組清單進行檢查。 例如：

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>檔案簽章驗證

檔案的簽章是由檔案開頭的前幾個位元組所決定。 您可以使用這些位元組來指出延伸模組是否符合檔案的內容。 範例應用程式會檢查檔案簽章中有幾個常見的檔案類型。 在下列範例中，會針對檔案檢查 JPEG 影像的檔案簽章：

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

若要取得其他檔案簽章，請參閱檔案簽章 [資料庫](https://www.filesignatures.net/) 與官方檔案規格。

### <a name="file-name-security"></a>檔案名安全性

絕對不要使用用戶端提供的檔案名將檔案儲存至實體儲存體。 使用 [GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 建立檔案的安全檔案名，以建立完整路徑 (包括暫存儲存體的檔案名) 。

Razor 自動為顯示的 HTML 編碼屬性值。 下列程式碼可安全地使用：

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Razor從開始，一律 <xref:System.Net.WebUtility.HtmlEncode*> 是使用者要求的檔案名內容。

許多執行必須包含檢查檔案是否存在;否則，檔案會覆寫相同名稱的檔案。 提供其他邏輯以符合您應用程式的規格。

### <a name="size-validation"></a>大小驗證

限制上傳檔案的大小。

在範例應用程式中，檔案大小限制為 2 MB (以位元組) 表示。 這項限制是透過[Configuration](xref:fundamentals/configuration/index)檔案*appsettings.js*的設定提供：

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`會插入至 `PageModel` 類別：

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

當檔案大小超過限制時，會拒絕檔案：

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>將名稱屬性值與 POST 方法的參數名稱相符

在 Razor 張貼表單資料或直接使用 JavaScript 的非表單中 `FormData` ，在表單的元素中指定的名稱或 `FormData` 必須符合控制器動作中參數的名稱。

在下例中︰

* 使用專案時 `<input>` ， `name` 屬性會設定為值 `battlePlans` ：

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* `FormData`在 JavaScript 中使用時，名稱會設定為下列值 `battlePlans` ：

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

針對 c # 方法 () 的參數使用相符的名稱 `battlePlans` ：

* 針對名為的 Razor 頁面頁面處理常式方法 `Upload` ：

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* 針對 MVC POST 控制器動作方法：

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>伺服器和應用程式設定

### <a name="multipart-body-length-limit"></a>多部分主體長度限制

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 設定每個多部分主體長度的限制。 超過此限制的表單區段會 <xref:System.IO.InvalidDataException> 在剖析時擲回。 預設值為 134217728 (128 MB) 。 使用中的設定來自訂限制 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 用來設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 單一頁面或動作的。

在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面模型或動作方法：

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel 要求主體大小上限

針對 Kestrel 所裝載的應用程式，預設的要求主體大小上限為30000000個位元組，大約是 28.6 MB。 使用 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 伺服器選項自訂限制：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 用來設定單一頁面或動作的 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) 。

在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面處理常式類別或動作方法：

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`也可以使用指示詞套用 [`@attribute`](xref:mvc/views/razor#attribute) Razor ：

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>其他 Kestrel 限制

其他 Kestrel 限制可能適用于 Kestrel 所裝載的應用程式：

* [用戶端連線數目上限](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [要求和回應資料速率](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

預設的要求限制 (`maxAllowedContentLength`) 為30000000個位元組，大約 28.6 MB。 自訂檔案中的限制 `web.config` 。 在下列範例中，此限制設定為 50 MB (52428800 bytes) ：

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

此 `maxAllowedContentLength` 設定僅適用于 IIS。 如需詳細資訊，請參閱[要求限制 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。

## <a name="troubleshoot"></a>疑難排解

以下是使用上傳檔案和其可能解決方案時發現的一些常見問題。

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>部署至 IIS 伺服器時找不到錯誤

下列錯誤表示上傳的檔案超過伺服器設定的內容長度：

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

如需詳細資訊，請參閱 [IIS](#iis) 一節。

### <a name="connection-failure"></a>連線失敗

連接錯誤和重設伺服器連接可能表示上傳的檔案超過 Kestrel 的要求主體大小上限。 如需詳細資訊，請參閱 [Kestrel 的要求主體大小上限](#kestrel-maximum-request-body-size) 一節。 Kestrel 用戶端連接限制也可能需要調整。

### <a name="null-reference-exception-with-iformfile"></a>IFormFile 的 Null 參考例外狀況

如果控制器使用已上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> ，但值為 `null` ，請確認 HTML 表單指定 `enctype` 的值 `multipart/form-data` 。 如果未在專案上設定此屬性 `<form>` ，則不會進行檔案上傳，而且任何系結的 <xref:Microsoft.AspNetCore.Http.IFormFile> 引數都是 `null` 。 也請確認 [表單資料中的上傳命名符合應用程式的命名](#match-name-attribute-value-to-parameter-name-of-post-method)。

### <a name="stream-was-too-long"></a>資料流程太長

本主題中的範例會依賴 <xref:System.IO.MemoryStream> 保存已上傳檔案的內容。 的大小限制 `MemoryStream` 為 `int.MaxValue` 。 如果應用程式的檔案上傳案例需要保存大於 50 MB 的檔案內容，請使用另一種方法，而不依賴單一 `MemoryStream` 的方法來保存上傳的檔案內容。

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

ASP.NET Core 支援針對較小的檔案使用緩衝的模型系結上傳一或多個檔案，並針對較大的檔案上傳未緩衝

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="security-considerations"></a>安全性考量

提供使用者將檔案上傳至伺服器的功能時，請特別小心。 攻擊者可能會嘗試：

* 執行 [拒絕服務的](/windows-hardware/drivers/ifs/denial-of-service) 攻擊。
* 上傳病毒或惡意程式碼。
* 以其他方式危害網路和伺服器。

降低成功攻擊可能性的安全性步驟如下：

* 將檔案上傳到專用檔案上傳區域，最好是非系統磁片磁碟機。 專用位置可讓您更輕鬆地對上傳的檔案強加安全性限制。 停用檔案上傳位置的執行許可權。&dagger;
* 請勿將上傳的 **檔案保存在** 與應用程式相同的目錄樹狀結構中。&dagger;
* 使用應用程式所決定的安全檔案名。 請勿使用使用者提供的檔案名或上傳檔案的不受信任檔案名。 &dagger; HTML 會在顯示不受信任的檔案名時進行編碼。 例如，記錄檔案名或在 UI 中顯示 (Razor 會自動以 HTML 編碼輸出) 。
* 只允許應用程式設計規格的核准副檔名。&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* 確認已在伺服器上執行用戶端檢查。 &dagger; 用戶端檢查很容易規避。
* 檢查上傳檔案的大小。 設定大小上限以防止大上傳。&dagger;
* 使用相同名稱的已上傳檔案覆寫檔案時，請先檢查資料庫或實體儲存體的檔案名，再上傳檔案。
* **在儲存檔案之前，在上傳的內容上執行病毒/惡意程式碼掃描器。**

&dagger;範例應用程式會示範符合準則的方法。

> [!WARNING]
> 將惡意程式碼上傳至系統經常是執行程式碼的第一步，該程式碼可能：
>
> * 完全掌握系統的控制權。
> * 使用系統損毀的結果來多載系統。
> * 洩漏使用者或系統資料。
> * 將刻套用至公用 UI。
>
> 如需在接受來自使用者的檔案時減少攻擊介面區的資訊，請參閱下列資源：
>
> * [不受限制的檔案上傳](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure 安全性：請確保在接受來自使用者的檔案時，適當的控制項已就緒](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

如需有關實施安全性措施的詳細資訊，包括範例應用程式中的範例，請參閱 [驗證](#validation) 一節。

## <a name="storage-scenarios"></a>儲存體案例

檔案的一般儲存選項包括：

* 資料庫

  * 若為小型檔案上傳，資料庫通常會比實體儲存體 (檔案系統或網路共用) 選項更快。
  * 資料庫通常比實體儲存體選項更方便，因為抓取使用者資料的資料庫記錄可以同時提供檔案內容 (例如，) 的圖片。
  * 資料庫的成本可能比使用資料儲存體服務還便宜。

* 實體儲存體 (檔案系統或網路共用) 

  * 針對大型檔案上傳：
    * 資料庫限制可能會限制上傳的大小。
    * 實體儲存體通常比資料庫中的儲存體便宜。
  * 實體儲存體的成本可能比使用資料儲存體服務更便宜。
  * 應用程式的進程必須具有儲存位置的讀取和寫入權限。 **絕不授與 execute 許可權。**

* 資料儲存體服務 (例如 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)) 

  * 服務通常會在內部部署解決方案中提供改良的擴充性和復原能力，這些解決方案通常會受限於單一失敗點。
  * 在大型儲存體基礎結構案例中，服務可能會降低成本。

  如需詳細資訊，請參閱 [快速入門：使用 .net 在物件儲存體中建立 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。

## <a name="file-upload-scenarios"></a>檔案上傳案例

上傳檔案的兩個一般方法是緩衝處理和串流處理。

**緩衝處理**

系統會將整個檔案讀入 <xref:Microsoft.AspNetCore.Http.IFormFile> ，這是用來處理或儲存檔案之檔案的 c # 標記法。

檔案上傳所使用的資源 (磁片、記憶體) 取決於並行檔案上傳的數目和大小。 如果應用程式嘗試緩衝過多上傳，則當網站的記憶體或磁碟空間不足時，就會損毀。 如果檔案上傳的大小或頻率是耗盡應用程式資源，請使用串流處理。

> [!NOTE]
> 任何超過 64 KB 的緩衝檔案都會從記憶體移至磁片上的暫存檔案。

本主題的下列各節涵蓋了小型檔案的緩衝：

* [實體儲存體](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Database](#upload-small-files-with-buffered-model-binding-to-a-database)

**串流**

從多部分要求接收檔案，並由應用程式直接處理或儲存。 串流無法大幅改善效能。 串流處理會在上傳檔案時減少記憶體或磁碟空間的需求。

[上傳含有串流的大型](#upload-large-files-with-streaming)檔案一節中涵蓋了串流大型檔案。

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>將具有緩衝模型系結的小型檔案上傳至實體儲存體

若要上傳小型檔案，請使用多部分形式的表單，或使用 JavaScript 來建立 POST 要求。

下列範例將示範 Razor 如何使用頁面表單，在範例應用程式) 中上傳單一檔案 (*Pages/BufferedSingleFileUploadPhysical. cshtml* ：

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

下列範例類似于先前的範例，不同之處在于：

* JavaScript 的 ([FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 用來提交表單的資料。
* 沒有任何驗證。

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

若要針對 [不支援 FETCH API](https://caniuse.com/#feat=fetch)的用戶端，在 JavaScript 中執行表單貼文，請使用下列其中一種方法：

* 使用 Fetch Polyfill (例如， [Polyfill (github/fetch) ](https://github.com/github/fetch)) 。
* 請使用 `XMLHttpRequest`。 例如：

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

為了支援檔案上傳，HTML 表單必須指定 (`enctype`) 的編碼類型 `multipart/form-data` 。

`files`若要讓輸入元素支援上傳多個檔案，請 `multiple` 在元素上提供屬性 `<input>` ：

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

您可以使用，透過 [模型](xref:mvc/models/model-binding) 系結來存取上傳至伺服器的個別檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 。 範例應用程式會示範針對資料庫和實體儲存案例的多個緩衝檔案上傳。

<a name="filename"></a>

> [!WARNING]
> 請勿 **使用的** `FileName` 屬性做 <xref:Microsoft.AspNetCore.Http.IFormFile> 為顯示和記錄。 顯示或記錄時，HTML 會將檔案名編碼。 攻擊者可以提供惡意檔案名，包括完整路徑或相對路徑。 應用程式應該：
>
> * 從使用者提供的檔案名中移除路徑。
> * 針對 UI 或記錄，儲存 HTML 編碼、路徑移除的檔案名。
> * 為儲存體產生新的隨機檔案名。
>
> 下列程式碼會將路徑從檔案名中移除：
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> 目前為止所提供的範例不考慮安全性考慮。 下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：
>
> * [安全性考量](#security-considerations)
> * [驗證](#validation)

使用模型系結和來上傳檔案時 <xref:Microsoft.AspNetCore.Http.IFormFile> ，動作方法可以接受：

* 單一 <xref:Microsoft.AspNetCore.Http.IFormFile> 。
* 下列任何代表數個檔案的集合：
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [清單](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> 系結符合依名稱的表單檔案。 例如，中的 HTML `name` 值 `<input type="file" name="formFile">` 必須符合 () 系結的 c # 參數/屬性 `FormFile` 。 如需詳細資訊，請參閱 [比 [對名稱] 屬性值與 POST 方法的參數名稱](#match-name-attribute-value-to-parameter-name-of-post-method) 一節。

下列範例將：

* 迴圈一或多個上傳的檔案。
* 使用 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 傳回檔案的完整路徑，包括檔案名。 
* 使用應用程式所產生的檔案名，將檔案儲存到本機檔案系統。
* 傳回上傳檔案的總數和大小。

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

用 `Path.GetRandomFileName` 來產生不含路徑的檔案名。 在下列範例中，路徑是從 configuration 取得：

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

傳遞至的路徑 <xref:System.IO.FileStream> *必須* 包含檔案名。 如果未提供檔案名， <xref:System.UnauthorizedAccessException> 則會在執行時間擲回。

使用此技術所上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 會在處理之前，在記憶體或伺服器的磁片上進行緩衝處理。 在動作方法內， <xref:Microsoft.AspNetCore.Http.IFormFile> 內容可作為進行存取 <xref:System.IO.Stream> 。 除了本機檔案系統之外，檔案也可以儲存到網路共用或檔案儲存體服務（例如 [Azure Blob 儲存體](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)）。

如需在多個要上傳的檔案上進行迴圈，並使用安全檔案名的另一個範例，請參閱範例應用程式中的*Pages/BufferedMultipleFileUploadPhysical。*

> [!WARNING]
> [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 會擲回， <xref:System.IO.IOException> 如果在不刪除先前的暫存檔的情況下建立65535個以上的檔案，則擲回。 65535檔案的限制為每個伺服器的限制。 如需 Windows OS 上這項限制的詳細資訊，請參閱下列主題中的備註：
>
> * [GetTempFileNameA 函式](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>將具有緩衝模型系結的小型檔案上傳至資料庫

若要使用 [Entity Framework](/ef/core/index)將二進位檔案資料儲存在資料庫中，請 <xref:System.Byte> 在實體上定義陣列屬性：

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

針對包含下列各類別的類別，指定頁面模型屬性 <xref:Microsoft.AspNetCore.Http.IFormFile> ：

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接當做動作方法參數使用，或作為系結模型屬性。 先前的範例會使用系結模型屬性。

在 `FileUpload` Razor 頁面表單中使用：

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

當表單張貼至伺服器時，將複製 <xref:Microsoft.AspNetCore.Http.IFormFile> 到資料流程，並將它儲存為資料庫中的位元組陣列。 在下列範例中，會 `_dbContext` 儲存應用程式的資料庫內容：

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

上述範例類似于範例應用程式中所示範的案例：

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb，.cs*

> [!WARNING]
> 將二進位資料儲存至關聯式資料庫時請小心，因為它可能會對效能造成不良影響。
>
> 請勿依賴或信任 `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> 不含驗證的屬性。 `FileName`屬性只能用於顯示目的，而且只適用于 HTML 編碼。
>
> 提供的範例不會考慮安全性考慮。 下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：
>
> * [安全性考量](#security-considerations)
> * [驗證](#validation)

### <a name="upload-large-files-with-streaming"></a>使用串流上傳大型檔案

下列範例示範如何使用 JavaScript 將檔案串流至控制器動作。 檔案的 antiforgery token 是使用自訂篩選屬性所產生，並且傳遞至用戶端 HTTP 標頭，而不是在要求主體中。 因為動作方法會直接處理已上傳的資料，所以其他自訂篩選會停用表單模型系結。 在動作內，會使用 `MultipartReader` 來讀取表單內容，以讀取每個個別 `MultipartSection`、處理檔案，或視需要儲存內容。 讀取多部分區段之後，動作會執行它自己的模型系結。

初始頁面回應會載入表單，並透過屬性) 將 antiforgery token 儲存在 cookie (中 `GenerateAntiforgeryTokenCookieAttribute` 。 屬性使用 ASP.NET Core 的內建 [antiforgery 支援](xref:security/anti-request-forgery) 來設定 cookie 具有要求權杖的。

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`用來停用模型系結：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

在範例應用程式中 `GenerateAntiforgeryTokenCookieAttribute` ， `DisableFormValueModelBindingAttribute` 會 `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 頁面慣例](xref:razor-pages/razor-pages-conventions)，並在和的頁面應用程式模型中套用為篩選：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

由於模型系結不會讀取表單，因此從表單系結的參數不會系結 (查詢、路由和標頭會繼續運作) 。 動作方法會直接與屬性搭配使用 `Request` 。 `MultipartReader` 是用來讀取每個區段。 索引鍵/值資料儲存在中 `KeyValueAccumulator` 。 讀取多部分區段之後，的內容 `KeyValueAccumulator` 會用來將表單資料系結至模型類型。

`StreamingController.UploadDatabase`使用 EF Core 串流至資料庫的完整方法：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*公用程式/MultipartRequestHelper .cs*) ：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

`StreamingController.UploadPhysical`串流至實體位置的完整方法：

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

在範例應用程式中，驗證檢查是由處理 `FileHelpers.ProcessStreamedFile` 。

## <a name="validation"></a>驗證

範例應用程式的類別會示範經過緩衝處理和串流處理檔案上 `FileHelpers` 傳的數個檢查 <xref:Microsoft.AspNetCore.Http.IFormFile> 。 若要 <xref:Microsoft.AspNetCore.Http.IFormFile> 在範例應用程式中處理緩衝的檔案上傳，請參閱 `ProcessFormFile` *公用程式/FileHelpers .cs* 檔案中的方法。 如需處理資料流程檔，請參閱 `ProcessStreamedFile` 相同檔案中的方法。

> [!WARNING]
> 範例應用程式中所示範的驗證處理方法，不會掃描所上傳檔案的內容。 在大部分的生產案例中，會在檔案上使用病毒/惡意程式碼掃描器 API，然後將檔案提供給使用者或其他系統。
>
> 雖然本主題範例提供驗證技術的實用範例，但 `FileHelpers` 除非您執行下列作業，否則請勿在生產應用程式中執行類別：
>
> * 充分瞭解執行。
> * 針對應用程式的環境和規格，適當地修改執行。
>
> **絕對不要在應用程式中執行安全程式碼，而不需滿足這些需求。**

### <a name="content-validation"></a>內容驗證

**在上傳的內容上使用協力廠商病毒/惡意程式碼掃描 API。**

掃描檔案對大量案例中的伺服器資源有需求。 如果要求處理效能因為檔案掃描而降低，請考慮將掃描工作卸載至 [背景服務](xref:fundamentals/host/hosted-services)，可能是在與應用程式伺服器不同的伺服器上執行的服務。 上傳的檔案通常會保留在隔離區域中，直到背景病毒掃描程式檢查它們為止。 當檔案通過時，檔案會移至一般檔案儲存位置。 這些步驟通常會與指出檔案掃描狀態的資料庫記錄一起執行。 藉由使用這種方法，應用程式和應用程式伺服器仍會專注于回應要求。

### <a name="file-extension-validation"></a>副檔名驗證

所上傳檔案的副檔名應針對允許的延伸模組清單進行檢查。 例如：

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>檔案簽章驗證

檔案的簽章是由檔案開頭的前幾個位元組所決定。 您可以使用這些位元組來指出延伸模組是否符合檔案的內容。 範例應用程式會檢查檔案簽章中有幾個常見的檔案類型。 在下列範例中，會針對檔案檢查 JPEG 影像的檔案簽章：

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

若要取得其他檔案簽章，請參閱檔案簽章 [資料庫](https://www.filesignatures.net/) 與官方檔案規格。

### <a name="file-name-security"></a>檔案名安全性

絕對不要使用用戶端提供的檔案名將檔案儲存至實體儲存體。 使用 [GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 建立檔案的安全檔案名，以建立完整路徑 (包括暫存儲存體的檔案名) 。

Razor 自動為顯示的 HTML 編碼屬性值。 下列程式碼可安全地使用：

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Razor從開始，一律 <xref:System.Net.WebUtility.HtmlEncode*> 是使用者要求的檔案名內容。

許多執行必須包含檢查檔案是否存在;否則，檔案會覆寫相同名稱的檔案。 提供其他邏輯以符合您應用程式的規格。

### <a name="size-validation"></a>大小驗證

限制上傳檔案的大小。

在範例應用程式中，檔案大小限制為 2 MB (以位元組) 表示。 這項限制是透過[Configuration](xref:fundamentals/configuration/index)檔案*appsettings.js*的設定提供：

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`會插入至 `PageModel` 類別：

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

當檔案大小超過限制時，會拒絕檔案：

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>將名稱屬性值與 POST 方法的參數名稱相符

在 Razor 張貼表單資料或直接使用 JavaScript 的非表單中 `FormData` ，在表單的元素中指定的名稱或 `FormData` 必須符合控制器動作中參數的名稱。

在下例中︰

* 使用專案時 `<input>` ， `name` 屬性會設定為值 `battlePlans` ：

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* `FormData`在 JavaScript 中使用時，名稱會設定為下列值 `battlePlans` ：

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

針對 c # 方法 () 的參數使用相符的名稱 `battlePlans` ：

* 針對名為的 Razor 頁面頁面處理常式方法 `Upload` ：

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* 針對 MVC POST 控制器動作方法：

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>伺服器和應用程式設定

### <a name="multipart-body-length-limit"></a>多部分主體長度限制

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 設定每個多部分主體長度的限制。 超過此限制的表單區段會 <xref:System.IO.InvalidDataException> 在剖析時擲回。 預設值為 134217728 (128 MB) 。 使用中的設定來自訂限制 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 用來設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 單一頁面或動作的。

在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model.Filters.Add(
                new RequestFormLimitsAttribute()
                {
                    // Set the limit to 256 MB
                    MultipartBodyLengthLimit = 268435456
                });
});
```

在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面模型或動作方法：

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel 要求主體大小上限

針對 Kestrel 所裝載的應用程式，預設的要求主體大小上限為30000000個位元組，大約是 28.6 MB。 使用 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 伺服器選項自訂限制：

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel((context, options) =>
            {
                // Handle requests up to 50 MB
                options.Limits.MaxRequestBodySize = 52428800;
            })
            .UseStartup<Startup>();
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 用來設定單一頁面或動作的 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) 。

在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：

```csharp
services.AddRazorPages(options =>
{
    options.Conventions
        .AddPageApplicationModelConvention("/FileUploadPage",
            model =>
            {
                // Handle requests up to 50 MB
                model.Filters.Add(
                    new RequestSizeLimitAttribute(52428800));
            });
});
```

在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面處理常式類別或動作方法：

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

`RequestSizeLimitAttribute`也可以使用指示詞套用 [`@attribute`](xref:mvc/views/razor#attribute) Razor ：

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a>其他 Kestrel 限制

其他 Kestrel 限制可能適用于 Kestrel 所裝載的應用程式：

* [用戶端連線數目上限](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [要求和回應資料速率](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

預設的要求限制 (`maxAllowedContentLength`) 為30000000個位元組，大約 28.6 MB。 自訂檔案中的限制 `web.config` 。 在下列範例中，此限制設定為 50 MB (52428800 bytes) ：

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

此 `maxAllowedContentLength` 設定僅適用于 IIS。 如需詳細資訊，請參閱[要求限制 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。

在中設定，以增加 HTTP 要求的要求主體大小 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> 上限 `Startup.ConfigureServices` 。 在下列範例中，此限制設定為 50 MB (52428800 bytes) ：

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

如需詳細資訊，請參閱<xref:host-and-deploy/iis/index#iis-options>。

## <a name="troubleshoot"></a>疑難排解

以下是使用上傳檔案和其可能解決方案時發現的一些常見問題。

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>部署至 IIS 伺服器時找不到錯誤

下列錯誤表示上傳的檔案超過伺服器設定的內容長度：

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

如需詳細資訊，請參閱 [IIS](#iis) 一節。

### <a name="connection-failure"></a>連線失敗

連接錯誤和重設伺服器連接可能表示上傳的檔案超過 Kestrel 的要求主體大小上限。 如需詳細資訊，請參閱 [Kestrel 的要求主體大小上限](#kestrel-maximum-request-body-size) 一節。 Kestrel 用戶端連接限制也可能需要調整。

### <a name="null-reference-exception-with-iformfile"></a>IFormFile 的 Null 參考例外狀況

如果控制器使用已上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> ，但值為 `null` ，請確認 HTML 表單指定 `enctype` 的值 `multipart/form-data` 。 如果未在專案上設定此屬性 `<form>` ，則不會進行檔案上傳，而且任何系結的 <xref:Microsoft.AspNetCore.Http.IFormFile> 引數都是 `null` 。 也請確認 [表單資料中的上傳命名符合應用程式的命名](#match-name-attribute-value-to-parameter-name-of-post-method)。

### <a name="stream-was-too-long"></a>資料流程太長

本主題中的範例會依賴 <xref:System.IO.MemoryStream> 保存已上傳檔案的內容。 的大小限制 `MemoryStream` 為 `int.MaxValue` 。 如果應用程式的檔案上傳案例需要保存大於 50 MB 的檔案內容，請使用另一種方法，而不依賴單一 `MemoryStream` 的方法來保存上傳的檔案內容。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 支援針對較小的檔案使用緩衝的模型系結上傳一或多個檔案，並針對較大的檔案上傳未緩衝

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="security-considerations"></a>安全性考量

提供使用者將檔案上傳至伺服器的功能時，請特別小心。 攻擊者可能會嘗試：

* 執行 [拒絕服務的](/windows-hardware/drivers/ifs/denial-of-service) 攻擊。
* 上傳病毒或惡意程式碼。
* 以其他方式危害網路和伺服器。

降低成功攻擊可能性的安全性步驟如下：

* 將檔案上傳到專用檔案上傳區域，最好是非系統磁片磁碟機。 專用位置可讓您更輕鬆地對上傳的檔案強加安全性限制。 停用檔案上傳位置的執行許可權。&dagger;
* 請勿將上傳的 **檔案保存在** 與應用程式相同的目錄樹狀結構中。&dagger;
* 使用應用程式所決定的安全檔案名。 請勿使用使用者提供的檔案名或上傳檔案的不受信任檔案名。 &dagger; HTML 會在顯示不受信任的檔案名時進行編碼。 例如，記錄檔案名或在 UI 中顯示 (Razor 會自動以 HTML 編碼輸出) 。
* 只允許應用程式設計規格的核准副檔名。&dagger; <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* 確認已在伺服器上執行用戶端檢查。 &dagger; 用戶端檢查很容易規避。
* 檢查上傳檔案的大小。 設定大小上限以防止大上傳。&dagger;
* 使用相同名稱的已上傳檔案覆寫檔案時，請先檢查資料庫或實體儲存體的檔案名，再上傳檔案。
* **在儲存檔案之前，在上傳的內容上執行病毒/惡意程式碼掃描器。**

&dagger;範例應用程式會示範符合準則的方法。

> [!WARNING]
> 將惡意程式碼上傳至系統經常是執行程式碼的第一步，該程式碼可能：
>
> * 完全掌握系統的控制權。
> * 使用系統損毀的結果來多載系統。
> * 洩漏使用者或系統資料。
> * 將刻套用至公用 UI。
>
> 如需在接受來自使用者的檔案時減少攻擊介面區的資訊，請參閱下列資源：
>
> * [不受限制的檔案上傳](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [Azure 安全性：請確保在接受來自使用者的檔案時，適當的控制項已就緒](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

如需有關實施安全性措施的詳細資訊，包括範例應用程式中的範例，請參閱 [驗證](#validation) 一節。

## <a name="storage-scenarios"></a>儲存體案例

檔案的一般儲存選項包括：

* 資料庫

  * 若為小型檔案上傳，資料庫通常會比實體儲存體 (檔案系統或網路共用) 選項更快。
  * 資料庫通常比實體儲存體選項更方便，因為抓取使用者資料的資料庫記錄可以同時提供檔案內容 (例如，) 的圖片。
  * 資料庫的成本可能比使用資料儲存體服務還便宜。

* 實體儲存體 (檔案系統或網路共用) 

  * 針對大型檔案上傳：
    * 資料庫限制可能會限制上傳的大小。
    * 實體儲存體通常比資料庫中的儲存體便宜。
  * 實體儲存體的成本可能比使用資料儲存體服務更便宜。
  * 應用程式的進程必須具有儲存位置的讀取和寫入權限。 **絕不授與 execute 許可權。**

* 資料儲存體服務 (例如 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)) 

  * 服務通常會在內部部署解決方案中提供改良的擴充性和復原能力，這些解決方案通常會受限於單一失敗點。
  * 在大型儲存體基礎結構案例中，服務可能會降低成本。

  如需詳細資訊，請參閱 [快速入門：使用 .net 在物件儲存體中建立 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。 本主題將示範 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> ，但 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> 在使用時，可以用來儲存 <xref:System.IO.FileStream> 至 blob 儲存體 <xref:System.IO.Stream> 。

## <a name="file-upload-scenarios"></a>檔案上傳案例

上傳檔案的兩個一般方法是緩衝處理和串流處理。

**緩衝處理**

系統會將整個檔案讀入 <xref:Microsoft.AspNetCore.Http.IFormFile> ，這是用來處理或儲存檔案之檔案的 c # 標記法。

檔案上傳所使用的資源 (磁片、記憶體) 取決於並行檔案上傳的數目和大小。 如果應用程式嘗試緩衝過多上傳，則當網站的記憶體或磁碟空間不足時，就會損毀。 如果檔案上傳的大小或頻率是耗盡應用程式資源，請使用串流處理。

> [!NOTE]
> 任何超過 64 KB 的緩衝檔案都會從記憶體移至磁片上的暫存檔案。

本主題的下列各節涵蓋了小型檔案的緩衝：

* [實體儲存體](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [Database](#upload-small-files-with-buffered-model-binding-to-a-database)

**串流**

從多部分要求接收檔案，並由應用程式直接處理或儲存。 串流無法大幅改善效能。 串流處理會在上傳檔案時減少記憶體或磁碟空間的需求。

[上傳含有串流的大型](#upload-large-files-with-streaming)檔案一節中涵蓋了串流大型檔案。

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a>將具有緩衝模型系結的小型檔案上傳至實體儲存體

若要上傳小型檔案，請使用多部分形式的表單，或使用 JavaScript 來建立 POST 要求。

下列範例將示範 Razor 如何使用頁面表單，在範例應用程式) 中上傳單一檔案 (*Pages/BufferedSingleFileUploadPhysical. cshtml* ：

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
            <span asp-validation-for="FileUpload.FormFile"></span>
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload" />
</form>
```

下列範例類似于先前的範例，不同之處在于：

* JavaScript 的 ([FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 用來提交表單的資料。
* 沒有任何驗證。

```cshtml
<form action="BufferedSingleFileUploadPhysical/?handler=Upload" 
      enctype="multipart/form-data" onsubmit="AJAXSubmit(this);return false;" 
      method="post">
    <dl>
        <dt>
            <label for="FileUpload_FormFile">File</label>
        </dt>
        <dd>
            <input id="FileUpload_FormFile" type="file" 
                name="FileUpload.FormFile" />
        </dd>
    </dl>

    <input class="btn" type="submit" value="Upload" />

    <div style="margin-top:15px">
        <output name="result"></output>
    </div>
</form>

<script>
  async function AJAXSubmit (oFormElement) {
    var resultElement = oFormElement.elements.namedItem("result");
    const formData = new FormData(oFormElement);

    try {
    const response = await fetch(oFormElement.action, {
      method: 'POST',
      body: formData
    });

    if (response.ok) {
      window.location.href = '/';
    }

    resultElement.value = 'Result: ' + response.status + ' ' + 
      response.statusText;
    } catch (error) {
      console.error('Error:', error);
    }
  }
</script>
```

若要針對 [不支援 FETCH API](https://caniuse.com/#feat=fetch)的用戶端，在 JavaScript 中執行表單貼文，請使用下列其中一種方法：

* 使用 Fetch Polyfill (例如， [Polyfill (github/fetch) ](https://github.com/github/fetch)) 。
* 請使用 `XMLHttpRequest`。 例如：

  ```javascript
  <script>
    "use strict";

    function AJAXSubmit (oFormElement) {
      var oReq = new XMLHttpRequest();
      oReq.onload = function(e) { 
      oFormElement.elements.namedItem("result").value = 
        'Result: ' + this.status + ' ' + this.statusText;
      };
      oReq.open("post", oFormElement.action);
      oReq.send(new FormData(oFormElement));
    }
  </script>
  ```

為了支援檔案上傳，HTML 表單必須指定 (`enctype`) 的編碼類型 `multipart/form-data` 。

`files`若要讓輸入元素支援上傳多個檔案，請 `multiple` 在元素上提供屬性 `<input>` ：

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

您可以使用，透過 [模型](xref:mvc/models/model-binding) 系結來存取上傳至伺服器的個別檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 。 範例應用程式會示範針對資料庫和實體儲存案例的多個緩衝檔案上傳。

<a name="filename2"></a>

> [!WARNING]
> 請勿 **使用的** `FileName` 屬性做 <xref:Microsoft.AspNetCore.Http.IFormFile> 為顯示和記錄。 顯示或記錄時，HTML 會將檔案名編碼。 攻擊者可以提供惡意檔案名，包括完整路徑或相對路徑。 應用程式應該：
>
> * 從使用者提供的檔案名中移除路徑。
> * 針對 UI 或記錄，儲存 HTML 編碼、路徑移除的檔案名。
> * 為儲存體產生新的隨機檔案名。
>
> 下列程式碼會將路徑從檔案名中移除：
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> 目前為止所提供的範例不考慮安全性考慮。 下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：
>
> * [安全性考量](#security-considerations)
> * [驗證](#validation)

使用模型系結和來上傳檔案時 <xref:Microsoft.AspNetCore.Http.IFormFile> ，動作方法可以接受：

* 單一 <xref:Microsoft.AspNetCore.Http.IFormFile> 。
* 下列任何代表數個檔案的集合：
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * [清單](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>>

> [!NOTE]
> 系結符合依名稱的表單檔案。 例如，中的 HTML `name` 值 `<input type="file" name="formFile">` 必須符合 () 系結的 c # 參數/屬性 `FormFile` 。 如需詳細資訊，請參閱 [比 [對名稱] 屬性值與 POST 方法的參數名稱](#match-name-attribute-value-to-parameter-name-of-post-method) 一節。

下列範例將：

* 迴圈一或多個上傳的檔案。
* 使用 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 傳回檔案的完整路徑，包括檔案名。 
* 使用應用程式所產生的檔案名，將檔案儲存到本機檔案系統。
* 傳回上傳檔案的總數和大小。

```csharp
public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> files)
{
    long size = files.Sum(f => f.Length);

    foreach (var formFile in files)
    {
        if (formFile.Length > 0)
        {
            var filePath = Path.GetTempFileName();

            using (var stream = System.IO.File.Create(filePath))
            {
                await formFile.CopyToAsync(stream);
            }
        }
    }

    // Process uploaded files
    // Don't rely on or trust the FileName property without validation.

    return Ok(new { count = files.Count, size });
}
```

用 `Path.GetRandomFileName` 來產生不含路徑的檔案名。 在下列範例中，路徑是從 configuration 取得：

```csharp
foreach (var formFile in files)
{
    if (formFile.Length > 0)
    {
        var filePath = Path.Combine(_config["StoredFilesPath"], 
            Path.GetRandomFileName());

        using (var stream = System.IO.File.Create(filePath))
        {
            await formFile.CopyToAsync(stream);
        }
    }
}
```

傳遞至的路徑 <xref:System.IO.FileStream> *必須* 包含檔案名。 如果未提供檔案名， <xref:System.UnauthorizedAccessException> 則會在執行時間擲回。

使用此技術所上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 會在處理之前，在記憶體或伺服器的磁片上進行緩衝處理。 在動作方法內， <xref:Microsoft.AspNetCore.Http.IFormFile> 內容可作為進行存取 <xref:System.IO.Stream> 。 除了本機檔案系統之外，檔案也可以儲存到網路共用或檔案儲存體服務（例如 [Azure Blob 儲存體](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)）。

如需在多個要上傳的檔案上進行迴圈，並使用安全檔案名的另一個範例，請參閱範例應用程式中的*Pages/BufferedMultipleFileUploadPhysical。*

> [!WARNING]
> [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 會擲回， <xref:System.IO.IOException> 如果在不刪除先前的暫存檔的情況下建立65535個以上的檔案，則擲回。 65535檔案的限制為每個伺服器的限制。 如需 Windows OS 上這項限制的詳細資訊，請參閱下列主題中的備註：
>
> * [GetTempFileNameA 函式](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a>將具有緩衝模型系結的小型檔案上傳至資料庫

若要使用 [Entity Framework](/ef/core/index)將二進位檔案資料儲存在資料庫中，請 <xref:System.Byte> 在實體上定義陣列屬性：

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

針對包含下列各類別的類別，指定頁面模型屬性 <xref:Microsoft.AspNetCore.Http.IFormFile> ：

```csharp
public class BufferedSingleFileUploadDbModel : PageModel
{
    ...

    [BindProperty]
    public BufferedSingleFileUploadDb FileUpload { get; set; }

    ...
}

public class BufferedSingleFileUploadDb
{
    [Required]
    [Display(Name="File")]
    public IFormFile FormFile { get; set; }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接當做動作方法參數使用，或作為系結模型屬性。 先前的範例會使用系結模型屬性。

在 `FileUpload` Razor 頁面表單中使用：

```cshtml
<form enctype="multipart/form-data" method="post">
    <dl>
        <dt>
            <label asp-for="FileUpload.FormFile"></label>
        </dt>
        <dd>
            <input asp-for="FileUpload.FormFile" type="file">
        </dd>
    </dl>
    <input asp-page-handler="Upload" class="btn" type="submit" value="Upload">
</form>
```

當表單張貼至伺服器時，將複製 <xref:Microsoft.AspNetCore.Http.IFormFile> 到資料流程，並將它儲存為資料庫中的位元組陣列。 在下列範例中，會 `_dbContext` 儲存應用程式的資料庫內容：

```csharp
public async Task<IActionResult> OnPostUploadAsync()
{
    using (var memoryStream = new MemoryStream())
    {
        await FileUpload.FormFile.CopyToAsync(memoryStream);

        // Upload the file if less than 2 MB
        if (memoryStream.Length < 2097152)
        {
            var file = new AppFile()
            {
                Content = memoryStream.ToArray()
            };

            _dbContext.File.Add(file);

            await _dbContext.SaveChangesAsync();
        }
        else
        {
            ModelState.AddModelError("File", "The file is too large.");
        }
    }

    return Page();
}
```

上述範例類似于範例應用程式中所示範的案例：

* *Pages/BufferedSingleFileUploadDb. cshtml*
* *Pages/BufferedSingleFileUploadDb，.cs*

> [!WARNING]
> 將二進位資料儲存至關聯式資料庫時請小心，因為它可能會對效能造成不良影響。
>
> 請勿依賴或信任 `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> 不含驗證的屬性。 `FileName`屬性只能用於顯示目的，而且只適用于 HTML 編碼。
>
> 提供的範例不會考慮安全性考慮。 下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：
>
> * [安全性考量](#security-considerations)
> * [驗證](#validation)

### <a name="upload-large-files-with-streaming"></a>使用串流上傳大型檔案

下列範例示範如何使用 JavaScript 將檔案串流至控制器動作。 檔案的 antiforgery token 是使用自訂篩選屬性所產生，並且傳遞至用戶端 HTTP 標頭，而不是在要求主體中。 因為動作方法會直接處理已上傳的資料，所以其他自訂篩選會停用表單模型系結。 在動作內，會使用 `MultipartReader` 來讀取表單內容，以讀取每個個別 `MultipartSection`、處理檔案，或視需要儲存內容。 讀取多部分區段之後，動作會執行它自己的模型系結。

初始頁面回應會載入表單，並透過屬性) 將 antiforgery token 儲存在 cookie (中 `GenerateAntiforgeryTokenCookieAttribute` 。 屬性使用 ASP.NET Core 的內建 [antiforgery 支援](xref:security/anti-request-forgery) 來設定 cookie 具有要求權杖的。

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

`DisableFormValueModelBindingAttribute`用來停用模型系結：

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

在範例應用程式中 `GenerateAntiforgeryTokenCookieAttribute` ， `DisableFormValueModelBindingAttribute` 會 `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 頁面慣例](xref:razor-pages/razor-pages-conventions)，並在和的頁面應用程式模型中套用為篩選：

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

由於模型系結不會讀取表單，因此從表單系結的參數不會系結 (查詢、路由和標頭會繼續運作) 。 動作方法會直接與屬性搭配使用 `Request` 。 `MultipartReader` 是用來讀取每個區段。 索引鍵/值資料儲存在中 `KeyValueAccumulator` 。 讀取多部分區段之後，的內容 `KeyValueAccumulator` 會用來將表單資料系結至模型類型。

`StreamingController.UploadDatabase`使用 EF Core 串流至資料庫的完整方法：

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

`MultipartRequestHelper` (*公用程式/MultipartRequestHelper .cs*) ：

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

`StreamingController.UploadPhysical`串流至實體位置的完整方法：

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

在範例應用程式中，驗證檢查是由處理 `FileHelpers.ProcessStreamedFile` 。

## <a name="validation"></a>驗證

範例應用程式的類別會示範經過緩衝處理和串流處理檔案上 `FileHelpers` 傳的數個檢查 <xref:Microsoft.AspNetCore.Http.IFormFile> 。 若要 <xref:Microsoft.AspNetCore.Http.IFormFile> 在範例應用程式中處理緩衝的檔案上傳，請參閱 `ProcessFormFile` *公用程式/FileHelpers .cs* 檔案中的方法。 如需處理資料流程檔，請參閱 `ProcessStreamedFile` 相同檔案中的方法。

> [!WARNING]
> 範例應用程式中所示範的驗證處理方法，不會掃描所上傳檔案的內容。 在大部分的生產案例中，會在檔案上使用病毒/惡意程式碼掃描器 API，然後將檔案提供給使用者或其他系統。
>
> 雖然本主題範例提供驗證技術的實用範例，但 `FileHelpers` 除非您執行下列作業，否則請勿在生產應用程式中執行類別：
>
> * 充分瞭解執行。
> * 針對應用程式的環境和規格，適當地修改執行。
>
> **絕對不要在應用程式中執行安全程式碼，而不需滿足這些需求。**

### <a name="content-validation"></a>內容驗證

**在上傳的內容上使用協力廠商病毒/惡意程式碼掃描 API。**

掃描檔案對大量案例中的伺服器資源有需求。 如果要求處理效能因為檔案掃描而降低，請考慮將掃描工作卸載至 [背景服務](xref:fundamentals/host/hosted-services)，可能是在與應用程式伺服器不同的伺服器上執行的服務。 上傳的檔案通常會保留在隔離區域中，直到背景病毒掃描程式檢查它們為止。 當檔案通過時，檔案會移至一般檔案儲存位置。 這些步驟通常會與指出檔案掃描狀態的資料庫記錄一起執行。 藉由使用這種方法，應用程式和應用程式伺服器仍會專注于回應要求。

### <a name="file-extension-validation"></a>副檔名驗證

所上傳檔案的副檔名應針對允許的延伸模組清單進行檢查。 例如：

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a>檔案簽章驗證

檔案的簽章是由檔案開頭的前幾個位元組所決定。 您可以使用這些位元組來指出延伸模組是否符合檔案的內容。 範例應用程式會檢查檔案簽章中有幾個常見的檔案類型。 在下列範例中，會針對檔案檢查 JPEG 影像的檔案簽章：

```csharp
private static readonly Dictionary<string, List<byte[]>> _fileSignature = 
    new Dictionary<string, List<byte[]>>
{
    { ".jpeg", new List<byte[]>
        {
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 },
        }
    },
};

using (var reader = new BinaryReader(uploadedFileData))
{
    var signatures = _fileSignature[ext];
    var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));
    
    return signatures.Any(signature => 
        headerBytes.Take(signature.Length).SequenceEqual(signature));
}
```

若要取得其他檔案簽章，請參閱檔案簽章 [資料庫](https://www.filesignatures.net/) 與官方檔案規格。

### <a name="file-name-security"></a>檔案名安全性

絕對不要使用用戶端提供的檔案名將檔案儲存至實體儲存體。 使用 [GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 建立檔案的安全檔案名，以建立完整路徑 (包括暫存儲存體的檔案名) 。

Razor 自動為顯示的 HTML 編碼屬性值。 下列程式碼可安全地使用：

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

Razor從開始，一律 <xref:System.Net.WebUtility.HtmlEncode*> 是使用者要求的檔案名內容。

許多執行必須包含檢查檔案是否存在;否則，檔案會覆寫相同名稱的檔案。 提供其他邏輯以符合您應用程式的規格。

### <a name="size-validation"></a>大小驗證

限制上傳檔案的大小。

在範例應用程式中，檔案大小限制為 2 MB (以位元組) 表示。 這項限制是透過[Configuration](xref:fundamentals/configuration/index)檔案*appsettings.js*的設定提供：

```json
{
  "FileSizeLimit": 2097152
}
```

`FileSizeLimit`會插入至 `PageModel` 類別：

```csharp
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    private readonly long _fileSizeLimit;

    public BufferedSingleFileUploadPhysicalModel(IConfiguration config)
    {
        _fileSizeLimit = config.GetValue<long>("FileSizeLimit");
    }

    ...
}
```

當檔案大小超過限制時，會拒絕檔案：

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a>將名稱屬性值與 POST 方法的參數名稱相符

在 Razor 張貼表單資料或直接使用 JavaScript 的非表單中 `FormData` ，在表單的元素中指定的名稱或 `FormData` 必須符合控制器動作中參數的名稱。

在下例中︰

* 使用專案時 `<input>` ， `name` 屬性會設定為值 `battlePlans` ：

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* `FormData`在 JavaScript 中使用時，名稱會設定為下列值 `battlePlans` ：

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

針對 c # 方法 () 的參數使用相符的名稱 `battlePlans` ：

* 針對名為的 Razor 頁面頁面處理常式方法 `Upload` ：

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* 針對 MVC POST 控制器動作方法：

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a>伺服器和應用程式設定

### <a name="multipart-body-length-limit"></a>多部分主體長度限制

<xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 設定每個多部分主體長度的限制。 超過此限制的表單區段會 <xref:System.IO.InvalidDataException> 在剖析時擲回。 預設值為 134217728 (128 MB) 。 使用中的設定來自訂限制 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices` ：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<FormOptions>(options =>
    {
        // Set the limit to 256 MB
        options.MultipartBodyLengthLimit = 268435456;
    });
}
```

<xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 用來設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 單一頁面或動作的。

在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model.Filters.Add(
                    new RequestFormLimitsAttribute()
                    {
                        // Set the limit to 256 MB
                        MultipartBodyLengthLimit = 268435456
                    });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面模型或動作方法：

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a>Kestrel 要求主體大小上限

針對 Kestrel 所裝載的應用程式，預設的要求主體大小上限為30000000個位元組，大約是 28.6 MB。 使用 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 伺服器選項自訂限制：

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Handle requests up to 50 MB
            options.Limits.MaxRequestBodySize = 52428800;
        });
```

<xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 用來設定單一頁面或動作的 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) 。

在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.Conventions
            .AddPageApplicationModelConvention("/FileUploadPage",
                model =>
                {
                    // Handle requests up to 50 MB
                    model.Filters.Add(
                        new RequestSizeLimitAttribute(52428800));
                });
    })
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面處理常式類別或動作方法：

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a>其他 Kestrel 限制

其他 Kestrel 限制可能適用于 Kestrel 所裝載的應用程式：

* [用戶端連線數目上限](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [要求和回應資料速率](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a>IIS

預設的要求限制 (`maxAllowedContentLength`) 為30000000個位元組，大約 28.6 MB。 自訂檔案中的限制 `web.config` 。 在下列範例中，此限制設定為 50 MB (52428800 bytes) ：

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

此 `maxAllowedContentLength` 設定僅適用于 IIS。 如需詳細資訊，請參閱[要求限制 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。

在中設定，以增加 HTTP 要求的要求主體大小 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> 上限 `Startup.ConfigureServices` 。 在下列範例中，此限制設定為 50 MB (52428800 bytes) ：

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

如需詳細資訊，請參閱<xref:host-and-deploy/iis/index#iis-options>。

## <a name="troubleshoot"></a>疑難排解

以下是使用上傳檔案和其可能解決方案時發現的一些常見問題。

### <a name="not-found-error-when-deployed-to-an-iis-server"></a>部署至 IIS 伺服器時找不到錯誤

下列錯誤表示上傳的檔案超過伺服器設定的內容長度：

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

如需詳細資訊，請參閱 [IIS](#iis) 一節。

### <a name="connection-failure"></a>連線失敗

連接錯誤和重設伺服器連接可能表示上傳的檔案超過 Kestrel 的要求主體大小上限。 如需詳細資訊，請參閱 [Kestrel 的要求主體大小上限](#kestrel-maximum-request-body-size) 一節。 Kestrel 用戶端連接限制也可能需要調整。

### <a name="null-reference-exception-with-iformfile"></a>IFormFile 的 Null 參考例外狀況

如果控制器使用已上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> ，但值為 `null` ，請確認 HTML 表單指定 `enctype` 的值 `multipart/form-data` 。 如果未在專案上設定此屬性 `<form>` ，則不會進行檔案上傳，而且任何系結的 <xref:Microsoft.AspNetCore.Http.IFormFile> 引數都是 `null` 。 也請確認 [表單資料中的上傳命名符合應用程式的命名](#match-name-attribute-value-to-parameter-name-of-post-method)。

### <a name="stream-was-too-long"></a>資料流程太長

本主題中的範例會依賴 <xref:System.IO.MemoryStream> 保存已上傳檔案的內容。 的大小限制 `MemoryStream` 為 `int.MaxValue` 。 如果應用程式的檔案上傳案例需要保存大於 50 MB 的檔案內容，請使用另一種方法，而不依賴單一 `MemoryStream` 的方法來保存上傳的檔案內容。

::: moniker-end


## <a name="additional-resources"></a>其他資源

* [HTTP 連接要求清空](xref:fundamentals/servers/kestrel#http11-request-draining)
* [不受限制的檔案上傳](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [Azure 安全性：安全性框架：輸入驗證 |措施](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [Azure 雲端設計模式： Valet 金鑰模式](/azure/architecture/patterns/valet-key)
