---
title: ASP.NET 核心檔案上 Blazor 傳
author: guardrex
description: 瞭解如何 Blazor 使用 InputFile 元件上傳檔案。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
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
ms.date: 02/18/2021
uid: blazor/file-uploads
ms.openlocfilehash: 26dada3a749a114fc27da89897701076378eef11
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109763"
---
# <a name="aspnet-core-blazor-file-uploads"></a>ASP.NET 核心檔案上 Blazor 傳

使用 `InputFile` 元件可將瀏覽器檔案資料讀取至 .net 程式碼，包括檔案上傳。

> [!WARNING]
> 一律遵循檔案上傳安全性最佳作法。 如需詳細資訊，請參閱<xref:mvc/models/file-uploads#security-considerations>。

## <a name="inputfile-component"></a>`InputFile` 元件

元件會轉譯 `InputFile` 為型別的 HTML 輸入 `file` 。

依預設，使用者會選取單一檔案。 加入 `multiple` 屬性，以允許使用者一次上傳多個檔案。 當使用者選取一個或多個檔案時，該 `InputFile` 元件會引發 `OnChange` 事件並傳入，以 `InputFileChangeEventArgs` 提供所選檔案清單的存取權，以及每個檔案的詳細資料。

若要從使用者選取的檔案讀取資料：

* `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`在檔案上呼叫，並從傳回的資料流程讀取。 如需詳細資訊，請參閱檔案 [資料流程](#file-streams) 一節。
* <xref:System.IO.Stream>傳回的會 `OpenReadStream` 強制讀取的大小上限（以位元組為單位） `Stream` 。 根據預設，在任何進一步讀取會導致例外狀況之前，允許讀取不超過512000個位元組 (500 KB) 的檔案。 這項限制是為了避免開發人員不小心將大型檔案讀取到記憶體中。 `maxAllowedSize` `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` 如有需要，可以使用上的參數來指定較大的大小。
* 避免直接將傳入檔案資料流程讀入記憶體中。 例如，請勿將檔案位元組複製到 <xref:System.IO.MemoryStream> 或讀取為位元組陣列。 這些方法可能會導致效能和安全性問題，尤其是在中 Blazor Server 。 相反地，請考慮將檔案位元組複製到外部存放區，例如 blob 或磁片上的檔案。

接收影像檔案的元件可以呼叫檔案 `RequestImageFileAsync` 上的便利方法，在將影像串流至應用程式之前，先調整瀏覽器的 JavaScript 執行時間中影像資料的大小。

下列範例示範元件中的多個檔案上傳。 `InputFileChangeEventArgs.GetMultipleFiles` 允許讀取多個檔案。 指定您預期要讀取的檔案數目上限，以防止惡意使用者上傳超過應用程式所預期的檔案數目。 `InputFileChangeEventArgs.File` 如果檔案上傳不支援多個檔案，則允許讀取第一個和唯一的檔案。

> [!NOTE]
> <xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> 位於 <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> 命名空間中，這通常是應用程式檔案中的其中一個命名空間 `_Imports.razor` 。

`Pages/UploadFiles.razor`:

```razor
@page "/upload-files"
@using System.IO

<h3>Upload Files</h3>

<p>
    <label>
        Max file size:
        <input type="number" @bind="maxFileSize" />
    </label>
</p>

<p>
    <label>
        Max allowed files:
        <input type="number" @bind="maxAllowedFiles" />
    </label>
</p>

<p>
    <label>
        Upload up to @maxAllowedFiles files of up to @maxFileSize bytes each:
        <InputFile OnChange="@LoadFiles" multiple />
    </label>
</p>

<p>@exceptionMessage</p>

@if (isLoading)
{
    <p>Loading...</p>
}

<ul>
    @foreach (var (file, content) in loadedFiles)
    {
        <li>
            <ul>
                <li>Name: @file.Name</li>
                <li>Last modified: @file.LastModified.ToString()</li>
                <li>Size (bytes): @file.Size</li>
                <li>Content type: @file.ContentType</li>
                <li>Content: @content</li>
            </ul>
        </li>
    }
</ul>

@code {
    private Dictionary<IBrowserFile, string> loadedFiles =
        new Dictionary<IBrowserFile, string>();
    private long maxFileSize = 1024 * 15;
    private int maxAllowedFiles = 3;
    private bool isLoading;
    string exceptionMessage;

    async Task LoadFiles(InputFileChangeEventArgs e)
    {
        isLoading = true;
        loadedFiles.Clear();
        exceptionMessage = string.Empty;

        try
        {
            foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
            {
                using var reader = 
                    new StreamReader(file.OpenReadStream(maxFileSize));

                loadedFiles.Add(file, await reader.ReadToEndAsync());
            }
        }
        catch (Exception ex)
        {
            exceptionMessage = ex.Message;
        }

        isLoading = false;
    }
}
```

`IBrowserFile` 傳回 [瀏覽器公開](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) 的中繼資料做為屬性。 此中繼資料適用于初步驗證。

## <a name="upload-files-to-a-server"></a>將檔案上傳至伺服器

下列範例示範如何使用託管的方案將檔案上傳至伺服器 Blazor WebAssembly 。

> [!WARNING]
> 提供使用者將檔案上傳至伺服器的功能時，請特別小心。 如需詳細資訊，請參閱<xref:mvc/models/file-uploads#security-considerations>。

`UploadResult`專案中的下列類別會 **`Shared`** 維護上傳檔案的結果。 當檔案無法在伺服器上傳時，會在中傳回錯誤碼以 `ErrorCode` 顯示給使用者。 每個檔案都會在伺服器上產生安全的預存檔案名，並傳回中的用戶端以 `StoredFileName` 供顯示。 在中使用不安全/不受信任的檔案名，在用戶端與伺服器之間為檔案命名 `FileName` 。

`UploadResult.cs`:

```csharp
public class UploadResult
{
    public bool Uploaded { get; set; }

    public string FileName { get; set; }

    public string StoredFileName { get; set; }

    public int ErrorCode { get; set; }
}
```

`UploadFiles`專案中的下列元件 **`Client`** ：

* 允許使用者從用戶端上傳檔案。
* 在 UI 中顯示用戶端提供的不受信任/不安全檔案名，因為 Razor 會自動以 HTML 編碼字串。 **在其他情況下，請勿信任用戶端所提供的檔案名。**

`Pages/UploadFiles.razor`:

```razor
@page "/upload-files"
@using System.Linq
@using Microsoft.Extensions.Logging
@inject HttpClient Http
@inject ILogger<UploadFiles> logger

<h1>Upload Files</h1>

<p>
    <label>
        Upload up to @maxAllowedFiles files:
        <InputFile OnChange="@OnInputFileChange" multiple />
    </label>
</p>

@if (files.Count > 0)
{
    <div class="card">
        <div class="card-body">
            <ul>
                @foreach (var file in files)
                {
                    <li>
                        File: @file.Name
                        <br>
                        @if (FileUpload(uploadResults, file.Name, logger,
                            out var result))
                        {
                            <span>
                                Stored File Name: @result.StoredFileName
                            </span>
                        }
                        else
                        {
                            <span>
                                There was an error uploading the file
                                (Error: @result.ErrorCode).
                            </span>
                        }
                    </li>
                }
            </ul>
        </div>
    </div>
}

@code {
    private IList<File> files = new List<File>();
    private IList<UploadResult> uploadResults = new List<UploadResult>();
    private int maxAllowedFiles = 3;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        shouldRender = false;
        long maxFileSize = 1024 * 1024 * 15;
        var upload = false;

        using var content = new MultipartFormDataContent();

        foreach (var file in e.GetMultipleFiles(maxAllowedFiles))
        {
            if (uploadResults.SingleOrDefault(
                f => f.FileName == file.Name) is null)
            {
                var fileContent = new StreamContent(file.OpenReadStream());

                files.Add(
                    new File()
                    {
                        Name = file.Name,
                    });

                if (file.Size < maxFileSize)
                {
                    content.Add(
                        content: fileContent,
                        name: "\"files\"",
                        fileName: file.Name);

                    upload = true;
                }
                else
                {
                    logger.LogInformation("{FileName} not uploaded", file.Name);

                    uploadResults.Add(
                        new UploadResult()
                        {
                            FileName = file.Name,
                            ErrorCode = 6,
                            Uploaded = false,
                        });
                }
            }
        }

        if (upload)
        {
            var response = await Http.PostAsync("/Filesave", content);

            var newUploadResults = await response.Content
                .ReadFromJsonAsync<IList<UploadResult>>();

            uploadResults = uploadResults.Concat(newUploadResults).ToList();
        }

        shouldRender = true;
    }

    private static bool FileUpload(IList<UploadResult> uploadResults,
        string fileName, ILogger<UploadFiles> logger, out UploadResult result)
    {
        result = uploadResults.SingleOrDefault(f => f.FileName == fileName);

        if (result is null)
        {
            logger.LogInformation("{FileName} not uploaded", fileName);
            result = new UploadResult();
            result.ErrorCode = 5;
        }

        return result.Uploaded;
    }

    private class File
    {
        public string Name { get; set; }
    }
}
```

若要使用下列程式碼，請 `Development/unsafe_uploads` 在專案的根目錄建立資料夾 **`Server`** 。

專案中的下列控制器會儲存 `FilesaveController` **`Server`** 用戶端上傳的檔案。

> [!WARNING]
> 此範例會儲存檔案，而不會掃描其內容。 在大部分的生產案例中，會在檔案上使用防毒軟體/反惡意程式碼掃描器 API，然後讓它們可供下載或供其他系統使用。 如需將檔案上傳到伺服器時的安全性考慮詳細資訊，請參閱 <xref:mvc/models/file-uploads#security-considerations> 。

`Controllers/FilesaveController.cs`:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

[ApiController]
[Route("[controller]")]
public class FilesaveController : ControllerBase
{
    private readonly IWebHostEnvironment env;
    private readonly ILogger<FilesaveController> logger;

    public FilesaveController(IWebHostEnvironment env, 
        ILogger<FilesaveController> logger)
    {
        this.env = env;
        this.logger = logger;
    }

    [HttpPost]
    public async Task<ActionResult<IList<UploadResult>>> PostFile(
        [FromForm] IEnumerable<IFormFile> files)
    {
        var maxAllowedFiles = 3;
        long maxFileSize = 1024 * 1024 * 15;
        var filesProcessed = 0;
        var resourcePath = new Uri($"{Request.Scheme}://{Request.Host}/");
        IList<UploadResult> uploadResults = new List<UploadResult>();

        foreach (var file in files)
        {
            var uploadResult = new UploadResult();
            string trustedFileNameForFileStorage;
            var untrustedFileName = file.FileName;
            uploadResult.FileName = untrustedFileName;
            var trustedFileNameForDisplay = 
                WebUtility.HtmlEncode(untrustedFileName);

            if (filesProcessed < maxAllowedFiles)
            {
                if (file.Length == 0)
                {
                    logger.LogInformation("{FileName} length is 0", 
                        trustedFileNameForDisplay);
                    uploadResult.ErrorCode = 1;
                }
                else if (file.Length > maxFileSize)
                {
                    logger.LogInformation("{FileName} of {Length} bytes is " +
                        "larger than the limit of {Limit} bytes", 
                        trustedFileNameForDisplay, file.Length, maxFileSize);
                    uploadResult.ErrorCode = 2;
                }
                else
                {
                    try
                    {
                        trustedFileNameForFileStorage = Path.GetRandomFileName();
                        var path = Path.Combine(env.ContentRootPath, 
                            env.EnvironmentName, "unsafe_uploads", 
                            trustedFileNameForFileStorage);
                        using MemoryStream ms = new();
                        await file.CopyToAsync(ms);
                        await System.IO.File.WriteAllBytesAsync(path, ms.ToArray());
                        logger.LogInformation("{FileName} saved at {Path}", 
                            trustedFileNameForDisplay, path);
                        uploadResult.Uploaded = true;
                        uploadResult.StoredFileName = trustedFileNameForFileStorage;
                    }
                    catch (IOException ex)
                    {
                        logger.LogError("{FileName} error on upload: {Message}", 
                            trustedFileNameForDisplay, ex.Message);
                        uploadResult.ErrorCode = 3;
                    }
                }

                filesProcessed++;
            }
            else
            {
                logger.LogInformation("{FileName} not uploaded because the " +
                    "request exceeded the allowed {Count} of files", 
                    trustedFileNameForDisplay, maxAllowedFiles);
                uploadResult.ErrorCode = 4;
            }

            uploadResults.Add(uploadResult);
        }

        return new CreatedResult(resourcePath, uploadResults);
    }
}
```

## <a name="file-streams"></a>檔案資料流程

在中 Blazor WebAssembly ，檔案資料會直接串流至瀏覽器中的 .net 程式碼。

在中 Blazor Server ，檔案資料會透過 SignalR 連接串流至伺服器上的 .net 程式碼，因為該檔案是從資料流程讀取的。 [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) 允許設定檔案上傳特性 Blazor Server 。

## <a name="additional-resources"></a>其他資源

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
