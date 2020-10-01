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
# <a name="aspnet-core-no-locblazor-file-uploads"></a>ASP.NET Core 檔案上 Blazor 傳

依 [Daniel Roth](https://github.com/danroth27) 和 [Pranav Krishnamoorthy](https://github.com/pranavkm)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

使用 `InputFile` 元件可將瀏覽器檔案資料讀取至 .net 程式碼，包括檔案上傳。

> [!WARNING]
> 一律遵循檔案上傳安全性最佳作法。 如需詳細資訊，請參閱<xref:mvc/models/file-uploads#security-considerations>。

## <a name="inputfile-component"></a>`InputFile` 元件

元件會轉譯 `InputFile` 為型別的 HTML 輸入 `file` 。

依預設，使用者會選取單一檔案。 加入 `multiple` 屬性，以允許使用者一次上傳多個檔案。 當使用者選取一個或多個檔案時，該 `InputFile` 元件會引發 `OnChange` 事件並傳入，以 `InputFileChangeEventArgs` 提供所選檔案清單的存取權，以及每個檔案的詳細資料。

若要從使用者選取的檔案讀取資料：

* `OpenReadStream`在檔案上呼叫，並從傳回的資料流程讀取。 如需詳細資訊，請參閱檔案 [資料流程](#file-streams) 一節。
* 使用 `ReadAsync`。 根據預設， `ReadAsync` 只允許讀取小於 524288 KB (512 kb) 大小的檔案。 這項限制是為了避免開發人員不小心將大型檔案讀取到記憶體中。 如果必須支援較大的檔案，請針對預期的最大檔案大小指定合理的近似值。 避免直接將傳入檔案資料流程讀入記憶體中。 例如，請勿將檔案位元組複製到 <xref:System.IO.MemoryStream> 或讀取為位元組陣列。 這些方法可能會導致效能和安全性問題，尤其是在中 Blazor Server 。 相反地，請考慮將檔案位元組複製到外部存放區，例如 blob 或磁片上的檔案。

接收影像檔案的元件可以呼叫檔案 `RequestImageFileAsync` 上的便利方法，在將影像串流至應用程式之前，先調整瀏覽器的 JavaScript 執行時間中影像資料的大小。

下列範例示範如何在元件中上傳多個影像檔案。 `InputFileChangeEventArgs.GetMultipleFiles` 允許讀取多個檔案。 指定您預期要讀取的檔案數目上限，以防止惡意使用者上傳超過應用程式所預期的檔案數目。 `InputFileChangeEventArgs.File` 如果檔案上傳不支援多個檔案，則允許讀取第一個和唯一的檔案。

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

`IBrowserFile` 傳回 [瀏覽器公開](https://developer.mozilla.org/docs/Web/API/File#Instance_properties) 的中繼資料做為屬性。 這種中繼資料對於初步驗證很有用。 例如，請參閱[ `FileUpload.razor` 和 `FilePreview.razor` 範例元件](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/file-uploads/samples/)。

## <a name="file-streams"></a>檔案資料流程

在 Blazor WebAssembly 應用程式中，資料會直接串流至瀏覽器中的 .net 程式碼。

在 Blazor Server 應用程式中，檔案資料會透過 SignalR 連接串流至伺服器上的 .net 程式碼，因為該檔案是從資料流程讀取的。 [`Forms.RemoteBrowserFileStreamOptions`](https://github.com/dotnet/aspnetcore/blob/master/src/Components/Web/src/Forms/InputFile/RemoteBrowserFileStreamOptions.cs) 允許設定檔案上傳特性 Blazor Server 。

## <a name="additional-resources"></a>其他資源

* <xref:mvc/models/file-uploads#security-considerations>
* <xref:blazor/forms-validation>
