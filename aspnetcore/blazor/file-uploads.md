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
# <a name="aspnet-core-blazor-file-uploads"></a><span data-ttu-id="f291f-103">ASP.NET 核心檔案上 Blazor 傳</span><span class="sxs-lookup"><span data-stu-id="f291f-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="f291f-104">使用 `InputFile` 元件可將瀏覽器檔案資料讀取至 .net 程式碼，包括檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="f291f-104">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="f291f-105">一律遵循檔案上傳安全性最佳作法。</span><span class="sxs-lookup"><span data-stu-id="f291f-105">Always follow file upload security best practices.</span></span> <span data-ttu-id="f291f-106">如需詳細資訊，請參閱<xref:mvc/models/file-uploads#security-considerations>。</span><span class="sxs-lookup"><span data-stu-id="f291f-106">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="f291f-107">`InputFile` 元件</span><span class="sxs-lookup"><span data-stu-id="f291f-107">`InputFile` component</span></span>

<span data-ttu-id="f291f-108">元件會轉譯 `InputFile` 為型別的 HTML 輸入 `file` 。</span><span class="sxs-lookup"><span data-stu-id="f291f-108">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="f291f-109">依預設，使用者會選取單一檔案。</span><span class="sxs-lookup"><span data-stu-id="f291f-109">By default, the user selects single files.</span></span> <span data-ttu-id="f291f-110">加入 `multiple` 屬性，以允許使用者一次上傳多個檔案。</span><span class="sxs-lookup"><span data-stu-id="f291f-110">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="f291f-111">當使用者選取一個或多個檔案時，該 `InputFile` 元件會引發 `OnChange` 事件並傳入，以 `InputFileChangeEventArgs` 提供所選檔案清單的存取權，以及每個檔案的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="f291f-111">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="f291f-112">若要從使用者選取的檔案讀取資料：</span><span class="sxs-lookup"><span data-stu-id="f291f-112">To read data from a user-selected file:</span></span>

* <span data-ttu-id="f291f-113">`Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`在檔案上呼叫，並從傳回的資料流程讀取。</span><span class="sxs-lookup"><span data-stu-id="f291f-113">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="f291f-114">如需詳細資訊，請參閱檔案 [資料流程](#file-streams) 一節。</span><span class="sxs-lookup"><span data-stu-id="f291f-114">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="f291f-115"><xref:System.IO.Stream>傳回的會 `OpenReadStream` 強制讀取的大小上限（以位元組為單位） `Stream` 。</span><span class="sxs-lookup"><span data-stu-id="f291f-115">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="f291f-116">根據預設，在任何進一步讀取會導致例外狀況之前，允許讀取不超過512000個位元組 (500 KB) 的檔案。</span><span class="sxs-lookup"><span data-stu-id="f291f-116">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="f291f-117">這項限制是為了避免開發人員不小心將大型檔案讀取到記憶體中。</span><span class="sxs-lookup"><span data-stu-id="f291f-117">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="f291f-118">`maxAllowedSize` `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` 如有需要，可以使用上的參數來指定較大的大小。</span><span class="sxs-lookup"><span data-stu-id="f291f-118">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="f291f-119">避免直接將傳入檔案資料流程讀入記憶體中。</span><span class="sxs-lookup"><span data-stu-id="f291f-119">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="f291f-120">例如，請勿將檔案位元組複製到 <xref:System.IO.MemoryStream> 或讀取為位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="f291f-120">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="f291f-121">這些方法可能會導致效能和安全性問題，尤其是在中 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="f291f-121">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="f291f-122">相反地，請考慮將檔案位元組複製到外部存放區，例如 blob 或磁片上的檔案。</span><span class="sxs-lookup"><span data-stu-id="f291f-122">Instead, consider copying file bytes to an external store, such as a blob or a file on disk.</span></span>

<span data-ttu-id="f291f-123">接收影像檔案的元件可以呼叫檔案 `RequestImageFileAsync` 上的便利方法，在將影像串流至應用程式之前，先調整瀏覽器的 JavaScript 執行時間中影像資料的大小。</span><span class="sxs-lookup"><span data-stu-id="f291f-123">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="f291f-124">下列範例示範元件中的多個檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="f291f-124">The following example demonstrates multiple file upload in a component.</span></span> <span data-ttu-id="f291f-125">`InputFileChangeEventArgs.GetMultipleFiles` 允許讀取多個檔案。</span><span class="sxs-lookup"><span data-stu-id="f291f-125">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="f291f-126">指定您預期要讀取的檔案數目上限，以防止惡意使用者上傳超過應用程式所預期的檔案數目。</span><span class="sxs-lookup"><span data-stu-id="f291f-126">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="f291f-127">`InputFileChangeEventArgs.File` 如果檔案上傳不支援多個檔案，則允許讀取第一個和唯一的檔案。</span><span class="sxs-lookup"><span data-stu-id="f291f-127">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload doesn't support multiple files.</span></span>

> [!NOTE]
> <span data-ttu-id="f291f-128"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> 位於 <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> 命名空間中，這通常是應用程式檔案中的其中一個命名空間 `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="f291f-128"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> is in the <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> namespace, which is typically one of the namespaces in the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="f291f-129">`Pages/UploadFiles.razor`:</span><span class="sxs-lookup"><span data-stu-id="f291f-129">`Pages/UploadFiles.razor`:</span></span>

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

<span data-ttu-id="f291f-130">`IBrowserFile` 傳回 [瀏覽器公開](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) 的中繼資料做為屬性。</span><span class="sxs-lookup"><span data-stu-id="f291f-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="f291f-131">此中繼資料適用于初步驗證。</span><span class="sxs-lookup"><span data-stu-id="f291f-131">This metadata can be useful for preliminary validation.</span></span>

## <a name="upload-files-to-a-server"></a><span data-ttu-id="f291f-132">將檔案上傳至伺服器</span><span class="sxs-lookup"><span data-stu-id="f291f-132">Upload files to a server</span></span>

<span data-ttu-id="f291f-133">下列範例示範如何使用託管的方案將檔案上傳至伺服器 Blazor WebAssembly 。</span><span class="sxs-lookup"><span data-stu-id="f291f-133">The following example demonstrates uploading files to a server using a hosted Blazor WebAssembly solution.</span></span>

> [!WARNING]
> <span data-ttu-id="f291f-134">提供使用者將檔案上傳至伺服器的功能時，請特別小心。</span><span class="sxs-lookup"><span data-stu-id="f291f-134">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="f291f-135">如需詳細資訊，請參閱<xref:mvc/models/file-uploads#security-considerations>。</span><span class="sxs-lookup"><span data-stu-id="f291f-135">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

<span data-ttu-id="f291f-136">`UploadResult`專案中的下列類別會 **`Shared`** 維護上傳檔案的結果。</span><span class="sxs-lookup"><span data-stu-id="f291f-136">The following `UploadResult` class in the **`Shared`** project maintains the result of an uploaded file.</span></span> <span data-ttu-id="f291f-137">當檔案無法在伺服器上傳時，會在中傳回錯誤碼以 `ErrorCode` 顯示給使用者。</span><span class="sxs-lookup"><span data-stu-id="f291f-137">When a file fails to upload on the server, an error code is returned in `ErrorCode` for display to the user.</span></span> <span data-ttu-id="f291f-138">每個檔案都會在伺服器上產生安全的預存檔案名，並傳回中的用戶端以 `StoredFileName` 供顯示。</span><span class="sxs-lookup"><span data-stu-id="f291f-138">A safe stored file name is generated on the server for each file and returned to the client in `StoredFileName` for display.</span></span> <span data-ttu-id="f291f-139">在中使用不安全/不受信任的檔案名，在用戶端與伺服器之間為檔案命名 `FileName` 。</span><span class="sxs-lookup"><span data-stu-id="f291f-139">Files are keyed between the client and server using the unsafe/untrusted file name in `FileName`.</span></span>

<span data-ttu-id="f291f-140">`UploadResult.cs`:</span><span class="sxs-lookup"><span data-stu-id="f291f-140">`UploadResult.cs`:</span></span>

```csharp
public class UploadResult
{
    public bool Uploaded { get; set; }

    public string FileName { get; set; }

    public string StoredFileName { get; set; }

    public int ErrorCode { get; set; }
}
```

<span data-ttu-id="f291f-141">`UploadFiles`專案中的下列元件 **`Client`** ：</span><span class="sxs-lookup"><span data-stu-id="f291f-141">The following `UploadFiles` component in the **`Client`** project:</span></span>

* <span data-ttu-id="f291f-142">允許使用者從用戶端上傳檔案。</span><span class="sxs-lookup"><span data-stu-id="f291f-142">Permits users to upload files from the client.</span></span>
* <span data-ttu-id="f291f-143">在 UI 中顯示用戶端提供的不受信任/不安全檔案名，因為 Razor 會自動以 HTML 編碼字串。</span><span class="sxs-lookup"><span data-stu-id="f291f-143">Displays the untrusted/unsafe file name provided by the client in the UI because Razor automatically HTML-encodes strings.</span></span> <span data-ttu-id="f291f-144">**在其他情況下，請勿信任用戶端所提供的檔案名。**</span><span class="sxs-lookup"><span data-stu-id="f291f-144">**Don't trust file names supplied by clients in other scenarios.**</span></span>

<span data-ttu-id="f291f-145">`Pages/UploadFiles.razor`:</span><span class="sxs-lookup"><span data-stu-id="f291f-145">`Pages/UploadFiles.razor`:</span></span>

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

<span data-ttu-id="f291f-146">若要使用下列程式碼，請 `Development/unsafe_uploads` 在專案的根目錄建立資料夾 **`Server`** 。</span><span class="sxs-lookup"><span data-stu-id="f291f-146">To use the following code, create a `Development/unsafe_uploads` folder at the root of the **`Server`** project.</span></span>

<span data-ttu-id="f291f-147">專案中的下列控制器會儲存 `FilesaveController` **`Server`** 用戶端上傳的檔案。</span><span class="sxs-lookup"><span data-stu-id="f291f-147">The following `FilesaveController` controller in the **`Server`** project saves uploaded files from the client.</span></span>

> [!WARNING]
> <span data-ttu-id="f291f-148">此範例會儲存檔案，而不會掃描其內容。</span><span class="sxs-lookup"><span data-stu-id="f291f-148">The example saves files without scanning their contents.</span></span> <span data-ttu-id="f291f-149">在大部分的生產案例中，會在檔案上使用防毒軟體/反惡意程式碼掃描器 API，然後讓它們可供下載或供其他系統使用。</span><span class="sxs-lookup"><span data-stu-id="f291f-149">In most production scenarios, an anti-virus/anti-malware scanner API is used on files before making them available for download or for use by other systems.</span></span> <span data-ttu-id="f291f-150">如需將檔案上傳到伺服器時的安全性考慮詳細資訊，請參閱 <xref:mvc/models/file-uploads#security-considerations> 。</span><span class="sxs-lookup"><span data-stu-id="f291f-150">For more information on security considerations when uploading files to a server, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

<span data-ttu-id="f291f-151">`Controllers/FilesaveController.cs`:</span><span class="sxs-lookup"><span data-stu-id="f291f-151">`Controllers/FilesaveController.cs`:</span></span>

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

## <a name="file-streams"></a><span data-ttu-id="f291f-152">檔案資料流程</span><span class="sxs-lookup"><span data-stu-id="f291f-152">File streams</span></span>

<span data-ttu-id="f291f-153">在中 Blazor WebAssembly ，檔案資料會直接串流至瀏覽器中的 .net 程式碼。</span><span class="sxs-lookup"><span data-stu-id="f291f-153">In Blazor WebAssembly, file data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="f291f-154">在中 Blazor Server ，檔案資料會透過 SignalR 連接串流至伺服器上的 .net 程式碼，因為該檔案是從資料流程讀取的。</span><span class="sxs-lookup"><span data-stu-id="f291f-154">In Blazor Server, file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="f291f-155">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) 允許設定檔案上傳特性 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="f291f-155">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f291f-156">其他資源</span><span class="sxs-lookup"><span data-stu-id="f291f-156">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
