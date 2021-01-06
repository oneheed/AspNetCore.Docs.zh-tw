---
title: ASP.NET Core 檔案上 Blazor 傳
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
ms.date: 10/27/2020
uid: blazor/file-uploads
ms.openlocfilehash: 77c2874eef788b8083758c087913a7a04c55fa2b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "94691166"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="f4d38-103">ASP.NET Core 檔案上 Blazor 傳</span><span class="sxs-lookup"><span data-stu-id="f4d38-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="f4d38-104">依 [Daniel Roth](https://github.com/danroth27) 和 [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="f4d38-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="f4d38-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="f4d38-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f4d38-106">使用 `InputFile` 元件可將瀏覽器檔案資料讀取至 .net 程式碼，包括檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="f4d38-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="f4d38-107">一律遵循檔案上傳安全性最佳作法。</span><span class="sxs-lookup"><span data-stu-id="f4d38-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="f4d38-108">如需詳細資訊，請參閱 <xref:mvc/models/file-uploads#security-considerations> 。</span><span class="sxs-lookup"><span data-stu-id="f4d38-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="f4d38-109">`InputFile` 元件</span><span class="sxs-lookup"><span data-stu-id="f4d38-109">`InputFile` component</span></span>

<span data-ttu-id="f4d38-110">元件會轉譯 `InputFile` 為型別的 HTML 輸入 `file` 。</span><span class="sxs-lookup"><span data-stu-id="f4d38-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="f4d38-111">依預設，使用者會選取單一檔案。</span><span class="sxs-lookup"><span data-stu-id="f4d38-111">By default, the user selects single files.</span></span> <span data-ttu-id="f4d38-112">加入 `multiple` 屬性，以允許使用者一次上傳多個檔案。</span><span class="sxs-lookup"><span data-stu-id="f4d38-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="f4d38-113">當使用者選取一個或多個檔案時，該 `InputFile` 元件會引發 `OnChange` 事件並傳入，以 `InputFileChangeEventArgs` 提供所選檔案清單的存取權，以及每個檔案的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="f4d38-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="f4d38-114">若要從使用者選取的檔案讀取資料：</span><span class="sxs-lookup"><span data-stu-id="f4d38-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="f4d38-115">`Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream`在檔案上呼叫，並從傳回的資料流程讀取。</span><span class="sxs-lookup"><span data-stu-id="f4d38-115">Call `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="f4d38-116">如需詳細資訊，請參閱檔案 [資料流程](#file-streams) 一節。</span><span class="sxs-lookup"><span data-stu-id="f4d38-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="f4d38-117"><xref:System.IO.Stream>傳回的會 `OpenReadStream` 強制讀取的大小上限（以位元組為單位） `Stream` 。</span><span class="sxs-lookup"><span data-stu-id="f4d38-117">The <xref:System.IO.Stream> returned by `OpenReadStream` enforces a maximum size in bytes of the `Stream` being read.</span></span> <span data-ttu-id="f4d38-118">根據預設，在任何進一步讀取會導致例外狀況之前，允許讀取不超過512000個位元組 (500 KB) 的檔案。</span><span class="sxs-lookup"><span data-stu-id="f4d38-118">By default, files no larger than 512,000 bytes (500 KB) in size are allowed to be read before any further reads would result in an exception.</span></span> <span data-ttu-id="f4d38-119">這項限制是為了避免開發人員不小心將大型檔案讀取到記憶體中。</span><span class="sxs-lookup"><span data-stu-id="f4d38-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="f4d38-120">`maxAllowedSize` `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` 如有需要，可以使用上的參數來指定較大的大小。</span><span class="sxs-lookup"><span data-stu-id="f4d38-120">The `maxAllowedSize` parameter on `Microsoft.AspNetCore.Components.Forms.IBrowserFile.OpenReadStream` can be used to specify a larger size if required.</span></span>
* <span data-ttu-id="f4d38-121">避免直接將傳入檔案資料流程讀入記憶體中。</span><span class="sxs-lookup"><span data-stu-id="f4d38-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="f4d38-122">例如，請勿將檔案位元組複製到 <xref:System.IO.MemoryStream> 或讀取為位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="f4d38-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="f4d38-123">這些方法可能會導致效能和安全性問題，尤其是在中 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="f4d38-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="f4d38-124">相反地，請考慮將檔案位元組複製到外部存放區，例如 blob 或磁片上的檔案。</span><span class="sxs-lookup"><span data-stu-id="f4d38-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="f4d38-125">接收影像檔案的元件可以呼叫檔案 `RequestImageFileAsync` 上的便利方法，在將影像串流至應用程式之前，先調整瀏覽器的 JavaScript 執行時間中影像資料的大小。</span><span class="sxs-lookup"><span data-stu-id="f4d38-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="f4d38-126">下列範例示範如何在元件中上傳多個影像檔案。</span><span class="sxs-lookup"><span data-stu-id="f4d38-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="f4d38-127">`InputFileChangeEventArgs.GetMultipleFiles` 允許讀取多個檔案。</span><span class="sxs-lookup"><span data-stu-id="f4d38-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="f4d38-128">指定您預期要讀取的檔案數目上限，以防止惡意使用者上傳超過應用程式所預期的檔案數目。</span><span class="sxs-lookup"><span data-stu-id="f4d38-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="f4d38-129">`InputFileChangeEventArgs.File` 如果檔案上傳不支援多個檔案，則允許讀取第一個和唯一的檔案。</span><span class="sxs-lookup"><span data-stu-id="f4d38-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

> [!NOTE]
> <span data-ttu-id="f4d38-130"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> 位於 <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> 命名空間中，這通常是應用程式檔案中的其中一個命名空間 `_Imports.razor` 。</span><span class="sxs-lookup"><span data-stu-id="f4d38-130"><xref:Microsoft.AspNetCore.Components.Forms.InputFileChangeEventArgs> is in the <xref:Microsoft.AspNetCore.Components.Forms?displayProperty=fullName> namespace, which is typically one of the namespaces in the app's `_Imports.razor` file.</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h4>Images</h4>

    <div class="card" style="width:30rem;">
        <div class="card-body">
            @foreach (var imageDataUrl in imageDataUrls)
            {
                <img class="rounded m-1" src="@imageDataUrl" />
            }
        </div>
    </div>
}

@code {
    private IList<string> imageDataUrls = new List<string>();

    private async Task OnInputFileChange(InputFileChangeEventArgs e)
    {
        var maxAllowedFiles = 3;
        var format = "image/png";

        foreach (var imageFile in e.GetMultipleFiles(maxAllowedFiles))
        {
            var resizedImageFile = await imageFile.RequestImageFileAsync(format, 
                100, 100);
            var buffer = new byte[resizedImageFile.Size];
            await resizedImageFile.OpenReadStream().ReadAsync(buffer);
            var imageDataUrl = 
                $"data:{format};base64,{Convert.ToBase64String(buffer)}";
            imageDataUrls.Add(imageDataUrl);
        }
    }
}
```

<span data-ttu-id="f4d38-131">`IBrowserFile` 傳回 [瀏覽器公開](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) 的中繼資料做為屬性。</span><span class="sxs-lookup"><span data-stu-id="f4d38-131">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="f4d38-132">這種中繼資料對於初步驗證很有用。</span><span class="sxs-lookup"><span data-stu-id="f4d38-132">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="f4d38-133">例如，請參閱[ `FileUpload.razor` 和 `FilePreview.razor` 範例元件](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)。</span><span class="sxs-lookup"><span data-stu-id="f4d38-133">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="f4d38-134">檔案資料流程</span><span class="sxs-lookup"><span data-stu-id="f4d38-134">File streams</span></span>

<span data-ttu-id="f4d38-135">在 Blazor WebAssembly 應用程式中，資料會直接串流至瀏覽器中的 .net 程式碼。</span><span class="sxs-lookup"><span data-stu-id="f4d38-135">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="f4d38-136">在 Blazor Server 應用程式中，檔案資料會透過 SignalR 連接串流至伺服器上的 .net 程式碼，因為該檔案是從資料流程讀取的。</span><span class="sxs-lookup"><span data-stu-id="f4d38-136">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="f4d38-137">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) 允許設定檔案上傳特性 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="f4d38-137">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f4d38-138">其他資源</span><span class="sxs-lookup"><span data-stu-id="f4d38-138">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
