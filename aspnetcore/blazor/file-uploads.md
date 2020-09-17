---
title: ASP.NET Core 檔案上 Blazor 傳
author: guardrex
description: 瞭解如何 Blazor 使用 InputFile 元件上傳檔案。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/17/2020
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
ms.openlocfilehash: de4654f2efc401143e066628b096052efa65d7a0
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722972"
---
# <a name="aspnet-core-no-locblazor-file-uploads"></a><span data-ttu-id="52c52-103">ASP.NET Core 檔案上 Blazor 傳</span><span class="sxs-lookup"><span data-stu-id="52c52-103">ASP.NET Core Blazor file uploads</span></span>

<span data-ttu-id="52c52-104">依 [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="52c52-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="52c52-105">使用 `InputFile` 元件可將瀏覽器檔案資料讀取至 .net 程式碼，包括檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="52c52-105">Use the `InputFile` component to read browser file data into .NET code, including for file uploads.</span></span> <span data-ttu-id="52c52-106">元件會轉譯 `InputFile` 為型別的 HTML 輸入 `file` 。</span><span class="sxs-lookup"><span data-stu-id="52c52-106">The `InputFile` component renders as an HTML input of type `file`.</span></span>

<span data-ttu-id="52c52-107">依預設，使用者會選取單一檔案。</span><span class="sxs-lookup"><span data-stu-id="52c52-107">By default, the user selects single files.</span></span> <span data-ttu-id="52c52-108">加入 `multiple` 屬性，以允許使用者一次上傳多個檔案。</span><span class="sxs-lookup"><span data-stu-id="52c52-108">Add the `multiple` attribute to permit the user to upload multiple files at once.</span></span> <span data-ttu-id="52c52-109">當使用者選取一個或多個檔案時，該 `InputFile` 元件會引發 `OnChange` 事件並傳入，以 `InputFileChangeEventArgs` 提供所選檔案清單的存取權，以及每個檔案的詳細資料。</span><span class="sxs-lookup"><span data-stu-id="52c52-109">When one or more files is selected by the user, the `InputFile` component fires an `OnChange` event and passes in an `InputFileChangeEventArgs` that provides access to the selected file list and details about each file.</span></span>

<span data-ttu-id="52c52-110">接收影像檔案的元件可以呼叫檔案 `RequestImageFileAsync` 上的便利方法，在將影像串流至應用程式之前，先調整瀏覽器的 JavaScript 執行時間中影像資料的大小。</span><span class="sxs-lookup"><span data-stu-id="52c52-110">A component that receives an image file can call the `RequestImageFileAsync` convenience method on the file to resize the image data within the browser's JavaScript runtime before the image is streamed into the app.</span></span>

<span data-ttu-id="52c52-111">下列範例示範元件中的多個影像檔上傳：</span><span class="sxs-lookup"><span data-stu-id="52c52-111">The following example demonstrates multiple image file upload in a component:</span></span>

```razor
<h3>Upload PNG images</h3>

<p>
    <InputFile OnChange="@OnInputFileChange" multiple />
</p>

@if (imageDataUrls.Count > 0)
{
    <h3>Images</h3>

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
        var imageFiles = e.GetMultipleFiles();
        var format = "image/png";

        foreach (var imageFile in imageFiles)
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

<span data-ttu-id="52c52-112">若要從使用者選取的檔案讀取資料，請 `OpenReadStream` 在檔案上呼叫，並從傳回的資料流程中讀取。</span><span class="sxs-lookup"><span data-stu-id="52c52-112">To read data from a user-selected file, call `OpenReadStream` on the file and read from the returned stream.</span></span> <span data-ttu-id="52c52-113">在 Blazor WebAssembly 應用程式中，資料會直接串流至瀏覽器中的 .net 程式碼。</span><span class="sxs-lookup"><span data-stu-id="52c52-113">In a Blazor WebAssembly app, the data is streamed directly into the .NET code within the browser.</span></span> <span data-ttu-id="52c52-114">在 Blazor Server 應用程式中，檔案資料會在伺服器上串流至 .net 程式碼，因為該檔案是從資料流程讀取的。</span><span class="sxs-lookup"><span data-stu-id="52c52-114">In a Blazor Server app, the file data is streamed into .NET code on the server as the file is read from the stream.</span></span> 
