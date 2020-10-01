---
title: ASP.NET Core 檔案上 Blazor 傳
author: guardrex
description: 瞭解如何 Blazor 使用 InputFile 元件上傳檔案。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
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
uid: blazor/file-uploads
ms.openlocfilehash: 06d1464cb731a8008362fc911f463e4ff8a37b6b
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606655"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="3eb00-103">ASP.NET Core 檔案上 Blazor 傳</span><span class="sxs-lookup"><span data-stu-id="3eb00-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="3eb00-104">依 [Daniel Roth](https://github.com/danroth27) 和 [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="3eb00-104">By [Daniel Roth](https://github.com/danroth27) and [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="3eb00-105">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="3eb00-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3eb00-106">使用 `InputFile` 元件可將瀏覽器檔案資料讀取至 .net 程式碼，包括檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="3eb00-106">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span>

> [!WARNING]
> <span data-ttu-id="3eb00-107">一律遵循檔案上傳安全性最佳作法。</span><span class="sxs-lookup"><span data-stu-id="3eb00-107">Always follow file upload security best practices.</span></span> <span data-ttu-id="3eb00-108">如需詳細資訊，請參閱<xref:mvc/models/file-uploads#security-considerations>。</span><span class="sxs-lookup"><span data-stu-id="3eb00-108">For more information, see <xref:mvc/models/file-uploads#security-considerations>.</span></span>

## <a name="inputfile-component"></a><span data-ttu-id="3eb00-109">`InputFile` 元件</span><span class="sxs-lookup"><span data-stu-id="3eb00-109">`InputFile` component</span></span>

<span data-ttu-id="3eb00-110">元件會轉譯 `InputFile` 為型別的 HTML 輸入 `file` 。</span><span class="sxs-lookup"><span data-stu-id="3eb00-110">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="3eb00-111">依預設，使用者會選取單一檔案。</span><span class="sxs-lookup"><span data-stu-id="3eb00-111">By default, the user selects single files.</span></span> <span data-ttu-id="3eb00-112">加入 `multiple` 屬性，以允許使用者一次上傳多個檔案。</span><span class="sxs-lookup"><span data-stu-id="3eb00-112">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="3eb00-113">當使用者選取一個或多個檔案時，該 `InputFile` 元件會引發 `OnChange` 事件並傳入，以 `InputFileChangeEventArgs` 提供所選檔案清單的存取權，以及每個檔案的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="3eb00-113">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="3eb00-114">若要從使用者選取的檔案讀取資料：</span><span class="sxs-lookup"><span data-stu-id="3eb00-114">To read data from a user-selected file:</span></span>

* <span data-ttu-id="3eb00-115">`OpenReadStream`在檔案上呼叫，並從傳回的資料流程讀取。</span><span class="sxs-lookup"><span data-stu-id="3eb00-115">Call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="3eb00-116">如需詳細資訊，請參閱檔案 [資料流程](#file-streams) 一節。</span><span class="sxs-lookup"><span data-stu-id="3eb00-116">For more information, see the [File streams](#file-streams) section.</span></span>
* <span data-ttu-id="3eb00-117">使用 `ReadAsync`。</span><span class="sxs-lookup"><span data-stu-id="3eb00-117">Use `ReadAsync`.</span></span> <span data-ttu-id="3eb00-118">根據預設， `ReadAsync` 只允許讀取小於 524288 KB (512 kb) 大小的檔案。</span><span class="sxs-lookup"><span data-stu-id="3eb00-118">By default, `ReadAsync` only allows reading a file smaller than 524,288 KB (512 KB) in size.</span></span> <span data-ttu-id="3eb00-119">這項限制是為了避免開發人員不小心將大型檔案讀取到記憶體中。</span><span class="sxs-lookup"><span data-stu-id="3eb00-119">This limit is present to prevent developers from accidentally reading large files in to memory.</span></span> <span data-ttu-id="3eb00-120">如果必須支援較大的檔案，請針對預期的最大檔案大小指定合理的近似值。</span><span class="sxs-lookup"><span data-stu-id="3eb00-120">Specify a reasonable approximation for the maximum expected file size if larger files must be supported.</span></span> <span data-ttu-id="3eb00-121">避免直接將傳入檔案資料流程讀入記憶體中。</span><span class="sxs-lookup"><span data-stu-id="3eb00-121">Avoid reading the incoming file stream directly into memory.</span></span> <span data-ttu-id="3eb00-122">例如，請勿將檔案位元組複製到 <xref:System.IO.MemoryStream> 或讀取為位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="3eb00-122">For example, don't copy file bytes into a <xref:System.IO.MemoryStream> or read as a byte array.</span></span> <span data-ttu-id="3eb00-123">這些方法可能會導致效能和安全性問題，尤其是在中 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="3eb00-123">These approaches can result in performance and security problems, especially in Blazor Server.</span></span> <span data-ttu-id="3eb00-124">相反地，請考慮將檔案位元組複製到外部存放區，例如 blob 或磁片上的檔案。</span><span class="sxs-lookup"><span data-stu-id="3eb00-124">Instead, consider copying file bytes to an external store, such as a a blob or a file on disk.</span></span>

<span data-ttu-id="3eb00-125">接收影像檔案的元件可以呼叫檔案 `RequestImageFileAsync` 上的便利方法，在將影像串流至應用程式之前，先調整瀏覽器的 JavaScript 執行時間中影像資料的大小。</span><span class="sxs-lookup"><span data-stu-id="3eb00-125">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="3eb00-126">下列範例示範如何在元件中上傳多個影像檔案。</span><span class="sxs-lookup"><span data-stu-id="3eb00-126">The following example demonstrates multiple image file upload in a component.</span></span> <span data-ttu-id="3eb00-127">`InputFileChangeEventArgs.GetMultipleFiles` 允許讀取多個檔案。</span><span class="sxs-lookup"><span data-stu-id="3eb00-127">`InputFileChangeEventArgs.GetMultipleFiles` allows reading multiple files.</span></span> <span data-ttu-id="3eb00-128">指定您預期要讀取的檔案數目上限，以防止惡意使用者上傳超過應用程式所預期的檔案數目。</span><span class="sxs-lookup"><span data-stu-id="3eb00-128">Specify the maximum number of files you expect to read to prevent a malicious user from uploading a larger number of files than the app expects.</span></span> <span data-ttu-id="3eb00-129">`InputFileChangeEventArgs.File` 如果檔案上傳不支援多個檔案，則允許讀取第一個和唯一的檔案。</span><span class="sxs-lookup"><span data-stu-id="3eb00-129">`InputFileChangeEventArgs.File` allows reading the first and only file if the file upload does not support multiple files.</span></span>

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
    IList<string> imageDataUrls = new List<string>();

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

<span data-ttu-id="3eb00-130">`IBrowserFile` 傳回 [瀏覽器公開](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) 的中繼資料做為屬性。</span><span class="sxs-lookup"><span data-stu-id="3eb00-130">`IBrowserFile` returns metadata [exposed by the browser](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) as properties.</span></span> <span data-ttu-id="3eb00-131">這種中繼資料對於初步驗證很有用。</span><span class="sxs-lookup"><span data-stu-id="3eb00-131">This metadata can be useful to preliminary validation.</span></span> <span data-ttu-id="3eb00-132">例如，請參閱[ `FileUpload.razor` 和 `FilePreview.razor` 範例元件](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)。</span><span class="sxs-lookup"><span data-stu-id="3eb00-132">For example, see the [`FileUpload.razor` and `FilePreview.razor` sample components](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/).</span></span>

## <a name="file-streams"></a><span data-ttu-id="3eb00-133">檔案資料流程</span><span class="sxs-lookup"><span data-stu-id="3eb00-133">File streams</span></span>

<span data-ttu-id="3eb00-134">在 Blazor WebAssembly 應用程式中，資料會直接串流至瀏覽器中的 .net 程式碼。</span><span class="sxs-lookup"><span data-stu-id="3eb00-134">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span>

<span data-ttu-id="3eb00-135">在 Blazor Server 應用程式中，檔案資料會透過 SignalR 連接串流至伺服器上的 .net 程式碼，因為該檔案是從資料流程讀取的。</span><span class="sxs-lookup"><span data-stu-id="3eb00-135">In a Blazor Server app, the file data is streamed over the SignalR connection into .NET code on the server as the file is read from the stream.</span></span> <span data-ttu-id="3eb00-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) 允許設定檔案上傳特性 Blazor Server 。</span><span class="sxs-lookup"><span data-stu-id="3eb00-136">[`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) allows configuring file upload characteristics for Blazor Server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3eb00-137">其他資源</span><span class="sxs-lookup"><span data-stu-id="3eb00-137">Additional resources</span></span>

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
