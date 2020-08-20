---
title: 在 ASP.NET Core 上傳檔案
author: rick-anderson
description: 如何使用模型繫結和資料流在 ASP.NET Core MVC 上傳檔案。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/03/2020
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
ms.openlocfilehash: 93ffa3a5313e63a1e9b98fb5bf9788944254213f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635212"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="a1846-103">在 ASP.NET Core 上傳檔案</span><span class="sxs-lookup"><span data-stu-id="a1846-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="a1846-104">[Steve Smith](https://ardalis.com/)和[Rutger 風暴](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="a1846-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a1846-105">ASP.NET Core 支援針對較小的檔案使用緩衝的模型系結上傳一或多個檔案，並針對較大的檔案上傳未緩衝</span><span class="sxs-lookup"><span data-stu-id="a1846-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="a1846-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a1846-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="a1846-107">安全性考量</span><span class="sxs-lookup"><span data-stu-id="a1846-107">Security considerations</span></span>

<span data-ttu-id="a1846-108">提供使用者將檔案上傳至伺服器的功能時，請特別小心。</span><span class="sxs-lookup"><span data-stu-id="a1846-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="a1846-109">攻擊者可能會嘗試：</span><span class="sxs-lookup"><span data-stu-id="a1846-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="a1846-110">執行 [拒絕服務的](/windows-hardware/drivers/ifs/denial-of-service) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="a1846-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="a1846-111">上傳病毒或惡意程式碼。</span><span class="sxs-lookup"><span data-stu-id="a1846-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="a1846-112">以其他方式危害網路和伺服器。</span><span class="sxs-lookup"><span data-stu-id="a1846-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="a1846-113">降低成功攻擊可能性的安全性步驟如下：</span><span class="sxs-lookup"><span data-stu-id="a1846-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="a1846-114">將檔案上傳到專用檔案上傳區域，最好是非系統磁片磁碟機。</span><span class="sxs-lookup"><span data-stu-id="a1846-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="a1846-115">專用位置可讓您更輕鬆地對上傳的檔案強加安全性限制。</span><span class="sxs-lookup"><span data-stu-id="a1846-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="a1846-116">停用檔案上傳位置的執行許可權。&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1846-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="a1846-117">請勿將上傳的 **檔案保存在** 與應用程式相同的目錄樹狀結構中。&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1846-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="a1846-118">使用應用程式所決定的安全檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="a1846-119">請勿使用使用者提供的檔案名或上傳檔案的不受信任檔案名。 &dagger; HTML 會在顯示不受信任的檔案名時進行編碼。</span><span class="sxs-lookup"><span data-stu-id="a1846-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="a1846-120">例如，記錄檔案名或在 UI 中顯示 (Razor 會自動以 HTML 編碼輸出) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-120">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="a1846-121">只允許應用程式設計規格的核准副檔名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1846-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="a1846-122">確認已在伺服器上執行用戶端檢查。 &dagger; 用戶端檢查很容易規避。</span><span class="sxs-lookup"><span data-stu-id="a1846-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="a1846-123">檢查上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="a1846-124">設定大小上限以防止大上傳。&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1846-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="a1846-125">使用相同名稱的已上傳檔案覆寫檔案時，請先檢查資料庫或實體儲存體的檔案名，再上傳檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="a1846-126">**在儲存檔案之前，在上傳的內容上執行病毒/惡意程式碼掃描器。**</span><span class="sxs-lookup"><span data-stu-id="a1846-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="a1846-127">&dagger;範例應用程式會示範符合準則的方法。</span><span class="sxs-lookup"><span data-stu-id="a1846-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="a1846-128">將惡意程式碼上傳至系統經常是執行程式碼的第一步，該程式碼可能：</span><span class="sxs-lookup"><span data-stu-id="a1846-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="a1846-129">完全掌握系統的控制權。</span><span class="sxs-lookup"><span data-stu-id="a1846-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="a1846-130">使用系統損毀的結果來多載系統。</span><span class="sxs-lookup"><span data-stu-id="a1846-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="a1846-131">洩漏使用者或系統資料。</span><span class="sxs-lookup"><span data-stu-id="a1846-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="a1846-132">將刻套用至公用 UI。</span><span class="sxs-lookup"><span data-stu-id="a1846-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="a1846-133">如需在接受來自使用者的檔案時減少攻擊介面區的資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="a1846-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="a1846-134">不受限制的檔案上傳</span><span class="sxs-lookup"><span data-stu-id="a1846-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="a1846-135">Azure 安全性：請確保在接受來自使用者的檔案時，適當的控制項已就緒</span><span class="sxs-lookup"><span data-stu-id="a1846-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="a1846-136">如需有關實施安全性措施的詳細資訊，包括範例應用程式中的範例，請參閱 [驗證](#validation) 一節。</span><span class="sxs-lookup"><span data-stu-id="a1846-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="a1846-137">儲存體案例</span><span class="sxs-lookup"><span data-stu-id="a1846-137">Storage scenarios</span></span>

<span data-ttu-id="a1846-138">檔案的一般儲存選項包括：</span><span class="sxs-lookup"><span data-stu-id="a1846-138">Common storage options for files include:</span></span>

* <span data-ttu-id="a1846-139">資料庫</span><span class="sxs-lookup"><span data-stu-id="a1846-139">Database</span></span>

  * <span data-ttu-id="a1846-140">若為小型檔案上傳，資料庫通常會比實體儲存體 (檔案系統或網路共用) 選項更快。</span><span class="sxs-lookup"><span data-stu-id="a1846-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="a1846-141">資料庫通常比實體儲存體選項更方便，因為抓取使用者資料的資料庫記錄可以同時提供檔案內容 (例如，) 的圖片。</span><span class="sxs-lookup"><span data-stu-id="a1846-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="a1846-142">資料庫的成本可能比使用資料儲存體服務還便宜。</span><span class="sxs-lookup"><span data-stu-id="a1846-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="a1846-143">實體儲存體 (檔案系統或網路共用) </span><span class="sxs-lookup"><span data-stu-id="a1846-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="a1846-144">針對大型檔案上傳：</span><span class="sxs-lookup"><span data-stu-id="a1846-144">For large file uploads:</span></span>
    * <span data-ttu-id="a1846-145">資料庫限制可能會限制上傳的大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="a1846-146">實體儲存體通常比資料庫中的儲存體便宜。</span><span class="sxs-lookup"><span data-stu-id="a1846-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="a1846-147">實體儲存體的成本可能比使用資料儲存體服務更便宜。</span><span class="sxs-lookup"><span data-stu-id="a1846-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="a1846-148">應用程式的進程必須具有儲存位置的讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="a1846-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="a1846-149">**絕不授與 execute 許可權。**</span><span class="sxs-lookup"><span data-stu-id="a1846-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="a1846-150">資料儲存體服務 (例如 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)) </span><span class="sxs-lookup"><span data-stu-id="a1846-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="a1846-151">服務通常會在內部部署解決方案中提供改良的擴充性和復原能力，這些解決方案通常會受限於單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="a1846-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="a1846-152">在大型儲存體基礎結構案例中，服務可能會降低成本。</span><span class="sxs-lookup"><span data-stu-id="a1846-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="a1846-153">如需詳細資訊，請參閱 [快速入門：使用 .net 在物件儲存體中建立 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="a1846-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="a1846-154">檔案上傳案例</span><span class="sxs-lookup"><span data-stu-id="a1846-154">File upload scenarios</span></span>

<span data-ttu-id="a1846-155">上傳檔案的兩個一般方法是緩衝處理和串流處理。</span><span class="sxs-lookup"><span data-stu-id="a1846-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="a1846-156">**緩衝處理**</span><span class="sxs-lookup"><span data-stu-id="a1846-156">**Buffering**</span></span>

<span data-ttu-id="a1846-157">系統會將整個檔案讀入 <xref:Microsoft.AspNetCore.Http.IFormFile> ，這是用來處理或儲存檔案之檔案的 c # 標記法。</span><span class="sxs-lookup"><span data-stu-id="a1846-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="a1846-158">檔案上傳所使用的資源 (磁片、記憶體) 取決於並行檔案上傳的數目和大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="a1846-159">如果應用程式嘗試緩衝過多上傳，則當網站的記憶體或磁碟空間不足時，就會損毀。</span><span class="sxs-lookup"><span data-stu-id="a1846-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="a1846-160">如果檔案上傳的大小或頻率是耗盡應用程式資源，請使用串流處理。</span><span class="sxs-lookup"><span data-stu-id="a1846-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="a1846-161">任何超過 64 KB 的緩衝檔案都會從記憶體移至磁片上的暫存檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="a1846-162">本主題的下列各節涵蓋了小型檔案的緩衝：</span><span class="sxs-lookup"><span data-stu-id="a1846-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="a1846-163">實體儲存體</span><span class="sxs-lookup"><span data-stu-id="a1846-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="a1846-164">Database</span><span class="sxs-lookup"><span data-stu-id="a1846-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="a1846-165">**串流**</span><span class="sxs-lookup"><span data-stu-id="a1846-165">**Streaming**</span></span>

<span data-ttu-id="a1846-166">從多部分要求接收檔案，並由應用程式直接處理或儲存。</span><span class="sxs-lookup"><span data-stu-id="a1846-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="a1846-167">串流無法大幅改善效能。</span><span class="sxs-lookup"><span data-stu-id="a1846-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="a1846-168">串流處理會在上傳檔案時減少記憶體或磁碟空間的需求。</span><span class="sxs-lookup"><span data-stu-id="a1846-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="a1846-169">[上傳含有串流的大型](#upload-large-files-with-streaming)檔案一節中涵蓋了串流大型檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="a1846-170">將具有緩衝模型系結的小型檔案上傳至實體儲存體</span><span class="sxs-lookup"><span data-stu-id="a1846-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="a1846-171">若要上傳小型檔案，請使用多部分形式的表單，或使用 JavaScript 來建立 POST 要求。</span><span class="sxs-lookup"><span data-stu-id="a1846-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="a1846-172">下列範例將示範 Razor 如何使用頁面表單，在範例應用程式) 中上傳單一檔案 (*Pages/BufferedSingleFileUploadPhysical. cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="a1846-172">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="a1846-173">下列範例類似于先前的範例，不同之處在于：</span><span class="sxs-lookup"><span data-stu-id="a1846-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="a1846-174">JavaScript 的 ([FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 用來提交表單的資料。</span><span class="sxs-lookup"><span data-stu-id="a1846-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="a1846-175">沒有任何驗證。</span><span class="sxs-lookup"><span data-stu-id="a1846-175">There's no validation.</span></span>

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

<span data-ttu-id="a1846-176">若要針對 [不支援 FETCH API](https://caniuse.com/#feat=fetch)的用戶端，在 JavaScript 中執行表單貼文，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="a1846-177">使用 Fetch Polyfill (例如， [Polyfill (github/fetch) ](https://github.com/github/fetch)) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="a1846-178">請使用 `XMLHttpRequest`。</span><span class="sxs-lookup"><span data-stu-id="a1846-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="a1846-179">例如：</span><span class="sxs-lookup"><span data-stu-id="a1846-179">For example:</span></span>

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

<span data-ttu-id="a1846-180">為了支援檔案上傳，HTML 表單必須指定 (`enctype`) 的編碼類型 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="a1846-181">`files`若要讓輸入元素支援上傳多個檔案，請 `multiple` 在元素上提供屬性 `<input>` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="a1846-182">您可以使用，透過 [模型](xref:mvc/models/model-binding) 系結來存取上傳至伺服器的個別檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="a1846-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="a1846-183">範例應用程式會示範針對資料庫和實體儲存案例的多個緩衝檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="a1846-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="a1846-184">請勿 **使用的** `FileName` 屬性做 <xref:Microsoft.AspNetCore.Http.IFormFile> 為顯示和記錄。</span><span class="sxs-lookup"><span data-stu-id="a1846-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="a1846-185">顯示或記錄時，HTML 會將檔案名編碼。</span><span class="sxs-lookup"><span data-stu-id="a1846-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="a1846-186">攻擊者可以提供惡意檔案名，包括完整路徑或相對路徑。</span><span class="sxs-lookup"><span data-stu-id="a1846-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="a1846-187">應用程式應該：</span><span class="sxs-lookup"><span data-stu-id="a1846-187">Applications should:</span></span>
>
> * <span data-ttu-id="a1846-188">從使用者提供的檔案名中移除路徑。</span><span class="sxs-lookup"><span data-stu-id="a1846-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="a1846-189">針對 UI 或記錄，儲存 HTML 編碼、路徑移除的檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="a1846-190">為儲存體產生新的隨機檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="a1846-191">下列程式碼會將路徑從檔案名中移除：</span><span class="sxs-lookup"><span data-stu-id="a1846-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="a1846-192">目前為止所提供的範例不考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="a1846-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="a1846-193">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="a1846-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="a1846-194">安全性考慮</span><span class="sxs-lookup"><span data-stu-id="a1846-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="a1846-195">驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-195">Validation</span></span>](#validation)

<span data-ttu-id="a1846-196">使用模型系結和來上傳檔案時 <xref:Microsoft.AspNetCore.Http.IFormFile> ，動作方法可以接受：</span><span class="sxs-lookup"><span data-stu-id="a1846-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="a1846-197">單一 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="a1846-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="a1846-198">下列任何代表數個檔案的集合：</span><span class="sxs-lookup"><span data-stu-id="a1846-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="a1846-199">[清單](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="a1846-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="a1846-200">系結符合依名稱的表單檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-200">Binding matches form files by name.</span></span> <span data-ttu-id="a1846-201">例如，中的 HTML `name` 值 `<input type="file" name="formFile">` 必須符合 () 系結的 c # 參數/屬性 `FormFile` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="a1846-202">如需詳細資訊，請參閱 [比 [對名稱] 屬性值與 POST 方法的參數名稱](#match-name-attribute-value-to-parameter-name-of-post-method) 一節。</span><span class="sxs-lookup"><span data-stu-id="a1846-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="a1846-203">下列範例將：</span><span class="sxs-lookup"><span data-stu-id="a1846-203">The following example:</span></span>

* <span data-ttu-id="a1846-204">迴圈一或多個上傳的檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="a1846-205">使用 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 傳回檔案的完整路徑，包括檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="a1846-206">使用應用程式所產生的檔案名，將檔案儲存到本機檔案系統。</span><span class="sxs-lookup"><span data-stu-id="a1846-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="a1846-207">傳回上傳檔案的總數和大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="a1846-208">用 `Path.GetRandomFileName` 來產生不含路徑的檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="a1846-209">在下列範例中，路徑是從 configuration 取得：</span><span class="sxs-lookup"><span data-stu-id="a1846-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="a1846-210">傳遞至的路徑 <xref:System.IO.FileStream> *必須* 包含檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="a1846-211">如果未提供檔案名， <xref:System.UnauthorizedAccessException> 則會在執行時間擲回。</span><span class="sxs-lookup"><span data-stu-id="a1846-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="a1846-212">使用此技術所上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 會在處理之前，在記憶體或伺服器的磁片上進行緩衝處理。</span><span class="sxs-lookup"><span data-stu-id="a1846-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="a1846-213">在動作方法內， <xref:Microsoft.AspNetCore.Http.IFormFile> 內容可作為進行存取 <xref:System.IO.Stream> 。</span><span class="sxs-lookup"><span data-stu-id="a1846-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="a1846-214">除了本機檔案系統之外，檔案也可以儲存到網路共用或檔案儲存體服務（例如 [Azure Blob 儲存體](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)）。</span><span class="sxs-lookup"><span data-stu-id="a1846-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="a1846-215">如需在多個要上傳的檔案上進行迴圈，並使用安全檔案名的另一個範例，請參閱範例應用程式中的*Pages/BufferedMultipleFileUploadPhysical。*</span><span class="sxs-lookup"><span data-stu-id="a1846-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="a1846-216">[GetTempFileName](xref:System.IO.Path.GetTempFileName*) 會擲回， <xref:System.IO.IOException> 如果在不刪除先前的暫存檔的情況下建立65535個以上的檔案，則擲回。</span><span class="sxs-lookup"><span data-stu-id="a1846-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="a1846-217">65535檔案的限制為每個伺服器的限制。</span><span class="sxs-lookup"><span data-stu-id="a1846-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="a1846-218">如需 Windows OS 上這項限制的詳細資訊，請參閱下列主題中的備註：</span><span class="sxs-lookup"><span data-stu-id="a1846-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="a1846-219">GetTempFileNameA 函式</span><span class="sxs-lookup"><span data-stu-id="a1846-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="a1846-220">將具有緩衝模型系結的小型檔案上傳至資料庫</span><span class="sxs-lookup"><span data-stu-id="a1846-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="a1846-221">若要使用 [Entity Framework](/ef/core/index)將二進位檔案資料儲存在資料庫中，請 <xref:System.Byte> 在實體上定義陣列屬性：</span><span class="sxs-lookup"><span data-stu-id="a1846-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="a1846-222">針對包含下列各類別的類別，指定頁面模型屬性 <xref:Microsoft.AspNetCore.Http.IFormFile> ：</span><span class="sxs-lookup"><span data-stu-id="a1846-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="a1846-223"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接當做動作方法參數使用，或作為系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="a1846-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="a1846-224">先前的範例會使用系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="a1846-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="a1846-225">在 `FileUpload` Razor 頁面表單中使用：</span><span class="sxs-lookup"><span data-stu-id="a1846-225">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="a1846-226">當表單張貼至伺服器時，將複製 <xref:Microsoft.AspNetCore.Http.IFormFile> 到資料流程，並將它儲存為資料庫中的位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="a1846-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="a1846-227">在下列範例中，會 `_dbContext` 儲存應用程式的資料庫內容：</span><span class="sxs-lookup"><span data-stu-id="a1846-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="a1846-228">上述範例類似于範例應用程式中所示範的案例：</span><span class="sxs-lookup"><span data-stu-id="a1846-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="a1846-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a1846-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="a1846-230">*Pages/BufferedSingleFileUploadDb，.cs*</span><span class="sxs-lookup"><span data-stu-id="a1846-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="a1846-231">將二進位資料儲存至關聯式資料庫時請小心，因為它可能會對效能造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="a1846-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="a1846-232">請勿依賴或信任 `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> 不含驗證的屬性。</span><span class="sxs-lookup"><span data-stu-id="a1846-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="a1846-233">`FileName`屬性只能用於顯示目的，而且只適用于 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="a1846-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="a1846-234">提供的範例不會考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="a1846-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="a1846-235">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="a1846-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="a1846-236">安全性考慮</span><span class="sxs-lookup"><span data-stu-id="a1846-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="a1846-237">驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="a1846-238">使用串流上傳大型檔案</span><span class="sxs-lookup"><span data-stu-id="a1846-238">Upload large files with streaming</span></span>

<span data-ttu-id="a1846-239">下列範例示範如何使用 JavaScript 將檔案串流至控制器動作。</span><span class="sxs-lookup"><span data-stu-id="a1846-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="a1846-240">檔案的 antiforgery token 是使用自訂篩選屬性所產生，並且傳遞至用戶端 HTTP 標頭，而不是在要求主體中。</span><span class="sxs-lookup"><span data-stu-id="a1846-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="a1846-241">因為動作方法會直接處理已上傳的資料，所以其他自訂篩選會停用表單模型系結。</span><span class="sxs-lookup"><span data-stu-id="a1846-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="a1846-242">在動作內，會使用 `MultipartReader` 來讀取表單內容，以讀取每個個別 `MultipartSection`、處理檔案，或視需要儲存內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="a1846-243">讀取多部分區段之後，動作會執行它自己的模型系結。</span><span class="sxs-lookup"><span data-stu-id="a1846-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="a1846-244">初始頁面回應會載入表單，並透過屬性) 將 antiforgery token 儲存在 cookie (中 `GenerateAntiforgeryTokenCookieAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-244">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="a1846-245">屬性使用 ASP.NET Core 的內建 [antiforgery 支援](xref:security/anti-request-forgery) 來設定 cookie 具有要求權杖的。</span><span class="sxs-lookup"><span data-stu-id="a1846-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="a1846-246">`DisableFormValueModelBindingAttribute`用來停用模型系結：</span><span class="sxs-lookup"><span data-stu-id="a1846-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="a1846-247">在範例應用程式中 `GenerateAntiforgeryTokenCookieAttribute` ， `DisableFormValueModelBindingAttribute` 會 `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 頁面慣例](xref:razor-pages/razor-pages-conventions)，並在和的頁面應用程式模型中套用為篩選：</span><span class="sxs-lookup"><span data-stu-id="a1846-247">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_AddRazorPages&highlight=7-10,16-19)]

<span data-ttu-id="a1846-248">由於模型系結不會讀取表單，因此從表單系結的參數不會系結 (查詢、路由和標頭會繼續運作) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="a1846-249">動作方法會直接與屬性搭配使用 `Request` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="a1846-250">`MultipartReader` 是用來讀取每個區段。</span><span class="sxs-lookup"><span data-stu-id="a1846-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="a1846-251">索引鍵/值資料儲存在中 `KeyValueAccumulator` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="a1846-252">讀取多部分區段之後，的內容 `KeyValueAccumulator` 會用來將表單資料系結至模型類型。</span><span class="sxs-lookup"><span data-stu-id="a1846-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="a1846-253">`StreamingController.UploadDatabase`使用 EF Core 串流至資料庫的完整方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="a1846-254">`MultipartRequestHelper` (*公用程式/MultipartRequestHelper .cs*) ：</span><span class="sxs-lookup"><span data-stu-id="a1846-254">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="a1846-255">`StreamingController.UploadPhysical`串流至實體位置的完整方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="a1846-256">在範例應用程式中，驗證檢查是由處理 `FileHelpers.ProcessStreamedFile` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="a1846-257">驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-257">Validation</span></span>

<span data-ttu-id="a1846-258">範例應用程式的類別會示範經過緩衝處理和串流處理檔案上 `FileHelpers` 傳的數個檢查 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="a1846-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="a1846-259">若要 <xref:Microsoft.AspNetCore.Http.IFormFile> 在範例應用程式中處理緩衝的檔案上傳，請參閱 `ProcessFormFile` *公用程式/FileHelpers .cs* 檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="a1846-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="a1846-260">如需處理資料流程檔，請參閱 `ProcessStreamedFile` 相同檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="a1846-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="a1846-261">範例應用程式中所示範的驗證處理方法，不會掃描所上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="a1846-262">在大部分的生產案例中，會在檔案上使用病毒/惡意程式碼掃描器 API，然後將檔案提供給使用者或其他系統。</span><span class="sxs-lookup"><span data-stu-id="a1846-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="a1846-263">雖然本主題範例提供驗證技術的實用範例，但 `FileHelpers` 除非您執行下列作業，否則請勿在生產應用程式中執行類別：</span><span class="sxs-lookup"><span data-stu-id="a1846-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="a1846-264">充分瞭解執行。</span><span class="sxs-lookup"><span data-stu-id="a1846-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="a1846-265">針對應用程式的環境和規格，適當地修改執行。</span><span class="sxs-lookup"><span data-stu-id="a1846-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="a1846-266">**絕對不要在應用程式中執行安全程式碼，而不需滿足這些需求。**</span><span class="sxs-lookup"><span data-stu-id="a1846-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="a1846-267">內容驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-267">Content validation</span></span>

<span data-ttu-id="a1846-268">**在上傳的內容上使用協力廠商病毒/惡意程式碼掃描 API。**</span><span class="sxs-lookup"><span data-stu-id="a1846-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="a1846-269">掃描檔案對大量案例中的伺服器資源有需求。</span><span class="sxs-lookup"><span data-stu-id="a1846-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="a1846-270">如果要求處理效能因為檔案掃描而降低，請考慮將掃描工作卸載至 [背景服務](xref:fundamentals/host/hosted-services)，可能是在與應用程式伺服器不同的伺服器上執行的服務。</span><span class="sxs-lookup"><span data-stu-id="a1846-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="a1846-271">上傳的檔案通常會保留在隔離區域中，直到背景病毒掃描程式檢查它們為止。</span><span class="sxs-lookup"><span data-stu-id="a1846-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="a1846-272">當檔案通過時，檔案會移至一般檔案儲存位置。</span><span class="sxs-lookup"><span data-stu-id="a1846-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="a1846-273">這些步驟通常會與指出檔案掃描狀態的資料庫記錄一起執行。</span><span class="sxs-lookup"><span data-stu-id="a1846-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="a1846-274">藉由使用這種方法，應用程式和應用程式伺服器仍會專注于回應要求。</span><span class="sxs-lookup"><span data-stu-id="a1846-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="a1846-275">副檔名驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-275">File extension validation</span></span>

<span data-ttu-id="a1846-276">所上傳檔案的副檔名應針對允許的延伸模組清單進行檢查。</span><span class="sxs-lookup"><span data-stu-id="a1846-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="a1846-277">例如：</span><span class="sxs-lookup"><span data-stu-id="a1846-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="a1846-278">檔案簽章驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-278">File signature validation</span></span>

<span data-ttu-id="a1846-279">檔案的簽章是由檔案開頭的前幾個位元組所決定。</span><span class="sxs-lookup"><span data-stu-id="a1846-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="a1846-280">您可以使用這些位元組來指出延伸模組是否符合檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="a1846-281">範例應用程式會檢查檔案簽章中有幾個常見的檔案類型。</span><span class="sxs-lookup"><span data-stu-id="a1846-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="a1846-282">在下列範例中，會針對檔案檢查 JPEG 影像的檔案簽章：</span><span class="sxs-lookup"><span data-stu-id="a1846-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="a1846-283">若要取得其他檔案簽章，請參閱檔案簽章 [資料庫](https://www.filesignatures.net/) 與官方檔案規格。</span><span class="sxs-lookup"><span data-stu-id="a1846-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="a1846-284">檔案名安全性</span><span class="sxs-lookup"><span data-stu-id="a1846-284">File name security</span></span>

<span data-ttu-id="a1846-285">絕對不要使用用戶端提供的檔案名將檔案儲存至實體儲存體。</span><span class="sxs-lookup"><span data-stu-id="a1846-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="a1846-286">使用 [GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 建立檔案的安全檔案名，以建立完整路徑 (包括暫存儲存體的檔案名) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="a1846-287">Razor 自動為顯示的 HTML 編碼屬性值。</span><span class="sxs-lookup"><span data-stu-id="a1846-287">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="a1846-288">下列程式碼可安全地使用：</span><span class="sxs-lookup"><span data-stu-id="a1846-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="a1846-289">Razor從開始，一律 <xref:System.Net.WebUtility.HtmlEncode*> 是使用者要求的檔案名內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-289">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="a1846-290">許多執行必須包含檢查檔案是否存在;否則，檔案會覆寫相同名稱的檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="a1846-291">提供其他邏輯以符合您應用程式的規格。</span><span class="sxs-lookup"><span data-stu-id="a1846-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="a1846-292">大小驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-292">Size validation</span></span>

<span data-ttu-id="a1846-293">限制上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="a1846-294">在範例應用程式中，檔案大小限制為 2 MB (以位元組) 表示。</span><span class="sxs-lookup"><span data-stu-id="a1846-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="a1846-295">這項限制是透過[Configuration](xref:fundamentals/configuration/index)檔案*appsettings.js*的設定提供：</span><span class="sxs-lookup"><span data-stu-id="a1846-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="a1846-296">`FileSizeLimit`會插入至 `PageModel` 類別：</span><span class="sxs-lookup"><span data-stu-id="a1846-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="a1846-297">當檔案大小超過限制時，會拒絕檔案：</span><span class="sxs-lookup"><span data-stu-id="a1846-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="a1846-298">將名稱屬性值與 POST 方法的參數名稱相符</span><span class="sxs-lookup"><span data-stu-id="a1846-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="a1846-299">在 Razor 張貼表單資料或直接使用 JavaScript 的非表單中 `FormData` ，在表單的元素中指定的名稱或 `FormData` 必須符合控制器動作中參數的名稱。</span><span class="sxs-lookup"><span data-stu-id="a1846-299">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="a1846-300">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="a1846-300">In the following example:</span></span>

* <span data-ttu-id="a1846-301">使用專案時 `<input>` ， `name` 屬性會設定為值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="a1846-302">`FormData`在 JavaScript 中使用時，名稱會設定為下列值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="a1846-303">針對 c # 方法 () 的參數使用相符的名稱 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="a1846-304">針對名為的 Razor 頁面頁面處理常式方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-304">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="a1846-305">針對 MVC POST 控制器動作方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="a1846-306">伺服器和應用程式設定</span><span class="sxs-lookup"><span data-stu-id="a1846-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="a1846-307">多部分主體長度限制</span><span class="sxs-lookup"><span data-stu-id="a1846-307">Multipart body length limit</span></span>

<span data-ttu-id="a1846-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 設定每個多部分主體長度的限制。</span><span class="sxs-lookup"><span data-stu-id="a1846-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="a1846-309">超過此限制的表單區段會 <xref:System.IO.InvalidDataException> 在剖析時擲回。</span><span class="sxs-lookup"><span data-stu-id="a1846-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="a1846-310">預設值為 134217728 (128 MB) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="a1846-311">使用中的設定來自訂限制 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1846-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 用來設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 單一頁面或動作的。</span><span class="sxs-lookup"><span data-stu-id="a1846-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="a1846-313">在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-313">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1846-314">在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面模型或動作方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-314">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="a1846-315">Kestrel 要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="a1846-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="a1846-316">針對 Kestrel 所裝載的應用程式，預設的要求主體大小上限為30000000個位元組，大約是 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="a1846-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="a1846-317">使用 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 伺服器選項自訂限制：</span><span class="sxs-lookup"><span data-stu-id="a1846-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="a1846-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 用來設定單一頁面或動作的 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="a1846-319">在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-319">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1846-320">在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面處理常式類別或動作方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-320">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="a1846-321">`RequestSizeLimitAttribute`也可以使用指示詞套用 [`@attribute`](xref:mvc/views/razor#attribute) Razor ：</span><span class="sxs-lookup"><span data-stu-id="a1846-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) Razor directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="a1846-322">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="a1846-322">Other Kestrel limits</span></span>

<span data-ttu-id="a1846-323">其他 Kestrel 限制可能適用于 Kestrel 所裝載的應用程式：</span><span class="sxs-lookup"><span data-stu-id="a1846-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="a1846-324">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="a1846-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="a1846-325">要求和回應資料速率</span><span class="sxs-lookup"><span data-stu-id="a1846-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="a1846-326">IIS 內容長度限制</span><span class="sxs-lookup"><span data-stu-id="a1846-326">IIS content length limit</span></span>

<span data-ttu-id="a1846-327">預設的要求限制 (`maxAllowedContentLength`) 為30000000個位元組，大約是 28.6 mb。</span><span class="sxs-lookup"><span data-stu-id="a1846-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="a1846-328">自訂 *web.config* 檔案中的限制：</span><span class="sxs-lookup"><span data-stu-id="a1846-328">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="a1846-329">這個設定只適用於 IIS。</span><span class="sxs-lookup"><span data-stu-id="a1846-329">This setting only applies to IIS.</span></span> <span data-ttu-id="a1846-330">在 Kestrel 上裝載時，預設不會發生此行為。</span><span class="sxs-lookup"><span data-stu-id="a1846-330">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="a1846-331">如需詳細資訊，請參閱[要求限制 \<requestLimits> ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="a1846-331">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="a1846-332">ASP.NET Core 模組中的限制或 IIS 要求篩選模組的存在，可能會將上傳限制為2或 4 GB。</span><span class="sxs-lookup"><span data-stu-id="a1846-332">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="a1846-333">如需詳細資訊，請參閱 [無法上傳大小超過2gb 的檔案 (dotnet/AspNetCore #2711) ](https://github.com/dotnet/AspNetCore/issues/2711)。</span><span class="sxs-lookup"><span data-stu-id="a1846-333">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a1846-334">疑難排解</span><span class="sxs-lookup"><span data-stu-id="a1846-334">Troubleshoot</span></span>

<span data-ttu-id="a1846-335">以下是使用上傳檔案和其可能解決方案時發現的一些常見問題。</span><span class="sxs-lookup"><span data-stu-id="a1846-335">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="a1846-336">部署至 IIS 伺服器時找不到錯誤</span><span class="sxs-lookup"><span data-stu-id="a1846-336">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="a1846-337">下列錯誤表示上傳的檔案超過伺服器設定的內容長度：</span><span class="sxs-lookup"><span data-stu-id="a1846-337">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="a1846-338">如需增加限制的詳細資訊，請參閱 [IIS 內容長度限制](#iis-content-length-limit) 一節。</span><span class="sxs-lookup"><span data-stu-id="a1846-338">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="a1846-339">連線失敗</span><span class="sxs-lookup"><span data-stu-id="a1846-339">Connection failure</span></span>

<span data-ttu-id="a1846-340">連接錯誤和重設伺服器連接可能表示上傳的檔案超過 Kestrel 的要求主體大小上限。</span><span class="sxs-lookup"><span data-stu-id="a1846-340">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="a1846-341">如需詳細資訊，請參閱 [Kestrel 的要求主體大小上限](#kestrel-maximum-request-body-size) 一節。</span><span class="sxs-lookup"><span data-stu-id="a1846-341">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="a1846-342">Kestrel 用戶端連接限制也可能需要調整。</span><span class="sxs-lookup"><span data-stu-id="a1846-342">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="a1846-343">IFormFile 的 Null 參考例外狀況</span><span class="sxs-lookup"><span data-stu-id="a1846-343">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="a1846-344">如果控制器使用已上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> ，但值為 `null` ，請確認 HTML 表單指定 `enctype` 的值 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-344">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="a1846-345">如果未在專案上設定此屬性 `<form>` ，則不會進行檔案上傳，而且任何系結的 <xref:Microsoft.AspNetCore.Http.IFormFile> 引數都是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-345">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="a1846-346">也請確認 [表單資料中的上傳命名符合應用程式的命名](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="a1846-346">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="a1846-347">資料流程太長</span><span class="sxs-lookup"><span data-stu-id="a1846-347">Stream was too long</span></span>

<span data-ttu-id="a1846-348">本主題中的範例會依賴 <xref:System.IO.MemoryStream> 保存已上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-348">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="a1846-349">的大小限制 `MemoryStream` 為 `int.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-349">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="a1846-350">如果應用程式的檔案上傳案例需要保存大於 50 MB 的檔案內容，請使用另一種方法，而不依賴單一 `MemoryStream` 的方法來保存上傳的檔案內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-350">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a1846-351">ASP.NET Core 支援針對較小的檔案使用緩衝的模型系結上傳一或多個檔案，並針對較大的檔案上傳未緩衝</span><span class="sxs-lookup"><span data-stu-id="a1846-351">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="a1846-352">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a1846-352">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="a1846-353">安全性考量</span><span class="sxs-lookup"><span data-stu-id="a1846-353">Security considerations</span></span>

<span data-ttu-id="a1846-354">提供使用者將檔案上傳至伺服器的功能時，請特別小心。</span><span class="sxs-lookup"><span data-stu-id="a1846-354">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="a1846-355">攻擊者可能會嘗試：</span><span class="sxs-lookup"><span data-stu-id="a1846-355">Attackers may attempt to:</span></span>

* <span data-ttu-id="a1846-356">執行 [拒絕服務的](/windows-hardware/drivers/ifs/denial-of-service) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="a1846-356">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="a1846-357">上傳病毒或惡意程式碼。</span><span class="sxs-lookup"><span data-stu-id="a1846-357">Upload viruses or malware.</span></span>
* <span data-ttu-id="a1846-358">以其他方式危害網路和伺服器。</span><span class="sxs-lookup"><span data-stu-id="a1846-358">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="a1846-359">降低成功攻擊可能性的安全性步驟如下：</span><span class="sxs-lookup"><span data-stu-id="a1846-359">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="a1846-360">將檔案上傳到專用檔案上傳區域，最好是非系統磁片磁碟機。</span><span class="sxs-lookup"><span data-stu-id="a1846-360">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="a1846-361">專用位置可讓您更輕鬆地對上傳的檔案強加安全性限制。</span><span class="sxs-lookup"><span data-stu-id="a1846-361">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="a1846-362">停用檔案上傳位置的執行許可權。&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1846-362">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="a1846-363">請勿將上傳的 **檔案保存在** 與應用程式相同的目錄樹狀結構中。&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1846-363">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="a1846-364">使用應用程式所決定的安全檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-364">Use a safe file name determined by the app.</span></span> <span data-ttu-id="a1846-365">請勿使用使用者提供的檔案名或上傳檔案的不受信任檔案名。 &dagger; HTML 會在顯示不受信任的檔案名時進行編碼。</span><span class="sxs-lookup"><span data-stu-id="a1846-365">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="a1846-366">例如，記錄檔案名或在 UI 中顯示 (Razor 會自動以 HTML 編碼輸出) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-366">For example, logging the file name or displaying in UI (Razor automatically HTML encodes output).</span></span>
* <span data-ttu-id="a1846-367">只允許應用程式設計規格的核准副檔名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1846-367">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="a1846-368">確認已在伺服器上執行用戶端檢查。 &dagger; 用戶端檢查很容易規避。</span><span class="sxs-lookup"><span data-stu-id="a1846-368">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="a1846-369">檢查上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-369">Check the size of an uploaded file.</span></span> <span data-ttu-id="a1846-370">設定大小上限以防止大上傳。&dagger;</span><span class="sxs-lookup"><span data-stu-id="a1846-370">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="a1846-371">使用相同名稱的已上傳檔案覆寫檔案時，請先檢查資料庫或實體儲存體的檔案名，再上傳檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-371">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="a1846-372">**在儲存檔案之前，在上傳的內容上執行病毒/惡意程式碼掃描器。**</span><span class="sxs-lookup"><span data-stu-id="a1846-372">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="a1846-373">&dagger;範例應用程式會示範符合準則的方法。</span><span class="sxs-lookup"><span data-stu-id="a1846-373">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="a1846-374">將惡意程式碼上傳至系統經常是執行程式碼的第一步，該程式碼可能：</span><span class="sxs-lookup"><span data-stu-id="a1846-374">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="a1846-375">完全掌握系統的控制權。</span><span class="sxs-lookup"><span data-stu-id="a1846-375">Completely gain control of a system.</span></span>
> * <span data-ttu-id="a1846-376">使用系統損毀的結果來多載系統。</span><span class="sxs-lookup"><span data-stu-id="a1846-376">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="a1846-377">洩漏使用者或系統資料。</span><span class="sxs-lookup"><span data-stu-id="a1846-377">Compromise user or system data.</span></span>
> * <span data-ttu-id="a1846-378">將刻套用至公用 UI。</span><span class="sxs-lookup"><span data-stu-id="a1846-378">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="a1846-379">如需在接受來自使用者的檔案時減少攻擊介面區的資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="a1846-379">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="a1846-380">不受限制的檔案上傳</span><span class="sxs-lookup"><span data-stu-id="a1846-380">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="a1846-381">Azure 安全性：請確保在接受來自使用者的檔案時，適當的控制項已就緒</span><span class="sxs-lookup"><span data-stu-id="a1846-381">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="a1846-382">如需有關實施安全性措施的詳細資訊，包括範例應用程式中的範例，請參閱 [驗證](#validation) 一節。</span><span class="sxs-lookup"><span data-stu-id="a1846-382">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="a1846-383">儲存體案例</span><span class="sxs-lookup"><span data-stu-id="a1846-383">Storage scenarios</span></span>

<span data-ttu-id="a1846-384">檔案的一般儲存選項包括：</span><span class="sxs-lookup"><span data-stu-id="a1846-384">Common storage options for files include:</span></span>

* <span data-ttu-id="a1846-385">資料庫</span><span class="sxs-lookup"><span data-stu-id="a1846-385">Database</span></span>

  * <span data-ttu-id="a1846-386">若為小型檔案上傳，資料庫通常會比實體儲存體 (檔案系統或網路共用) 選項更快。</span><span class="sxs-lookup"><span data-stu-id="a1846-386">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="a1846-387">資料庫通常比實體儲存體選項更方便，因為抓取使用者資料的資料庫記錄可以同時提供檔案內容 (例如，) 的圖片。</span><span class="sxs-lookup"><span data-stu-id="a1846-387">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="a1846-388">資料庫的成本可能比使用資料儲存體服務還便宜。</span><span class="sxs-lookup"><span data-stu-id="a1846-388">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="a1846-389">實體儲存體 (檔案系統或網路共用) </span><span class="sxs-lookup"><span data-stu-id="a1846-389">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="a1846-390">針對大型檔案上傳：</span><span class="sxs-lookup"><span data-stu-id="a1846-390">For large file uploads:</span></span>
    * <span data-ttu-id="a1846-391">資料庫限制可能會限制上傳的大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-391">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="a1846-392">實體儲存體通常比資料庫中的儲存體便宜。</span><span class="sxs-lookup"><span data-stu-id="a1846-392">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="a1846-393">實體儲存體的成本可能比使用資料儲存體服務更便宜。</span><span class="sxs-lookup"><span data-stu-id="a1846-393">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="a1846-394">應用程式的進程必須具有儲存位置的讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="a1846-394">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="a1846-395">**絕不授與 execute 許可權。**</span><span class="sxs-lookup"><span data-stu-id="a1846-395">**Never grant execute permission.**</span></span>

* <span data-ttu-id="a1846-396">資料儲存體服務 (例如 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)) </span><span class="sxs-lookup"><span data-stu-id="a1846-396">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="a1846-397">服務通常會在內部部署解決方案中提供改良的擴充性和復原能力，這些解決方案通常會受限於單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="a1846-397">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="a1846-398">在大型儲存體基礎結構案例中，服務可能會降低成本。</span><span class="sxs-lookup"><span data-stu-id="a1846-398">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="a1846-399">如需詳細資訊，請參閱 [快速入門：使用 .net 在物件儲存體中建立 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="a1846-399">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="a1846-400">本主題將示範 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> ，但 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> 在使用時，可以用來儲存 <xref:System.IO.FileStream> 至 blob 儲存體 <xref:System.IO.Stream> 。</span><span class="sxs-lookup"><span data-stu-id="a1846-400">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="a1846-401">檔案上傳案例</span><span class="sxs-lookup"><span data-stu-id="a1846-401">File upload scenarios</span></span>

<span data-ttu-id="a1846-402">上傳檔案的兩個一般方法是緩衝處理和串流處理。</span><span class="sxs-lookup"><span data-stu-id="a1846-402">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="a1846-403">**緩衝處理**</span><span class="sxs-lookup"><span data-stu-id="a1846-403">**Buffering**</span></span>

<span data-ttu-id="a1846-404">系統會將整個檔案讀入 <xref:Microsoft.AspNetCore.Http.IFormFile> ，這是用來處理或儲存檔案之檔案的 c # 標記法。</span><span class="sxs-lookup"><span data-stu-id="a1846-404">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="a1846-405">檔案上傳所使用的資源 (磁片、記憶體) 取決於並行檔案上傳的數目和大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-405">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="a1846-406">如果應用程式嘗試緩衝過多上傳，則當網站的記憶體或磁碟空間不足時，就會損毀。</span><span class="sxs-lookup"><span data-stu-id="a1846-406">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="a1846-407">如果檔案上傳的大小或頻率是耗盡應用程式資源，請使用串流處理。</span><span class="sxs-lookup"><span data-stu-id="a1846-407">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="a1846-408">任何超過 64 KB 的緩衝檔案都會從記憶體移至磁片上的暫存檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-408">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="a1846-409">本主題的下列各節涵蓋了小型檔案的緩衝：</span><span class="sxs-lookup"><span data-stu-id="a1846-409">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="a1846-410">實體儲存體</span><span class="sxs-lookup"><span data-stu-id="a1846-410">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="a1846-411">Database</span><span class="sxs-lookup"><span data-stu-id="a1846-411">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="a1846-412">**串流**</span><span class="sxs-lookup"><span data-stu-id="a1846-412">**Streaming**</span></span>

<span data-ttu-id="a1846-413">從多部分要求接收檔案，並由應用程式直接處理或儲存。</span><span class="sxs-lookup"><span data-stu-id="a1846-413">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="a1846-414">串流無法大幅改善效能。</span><span class="sxs-lookup"><span data-stu-id="a1846-414">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="a1846-415">串流處理會在上傳檔案時減少記憶體或磁碟空間的需求。</span><span class="sxs-lookup"><span data-stu-id="a1846-415">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="a1846-416">[上傳含有串流的大型](#upload-large-files-with-streaming)檔案一節中涵蓋了串流大型檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-416">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="a1846-417">將具有緩衝模型系結的小型檔案上傳至實體儲存體</span><span class="sxs-lookup"><span data-stu-id="a1846-417">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="a1846-418">若要上傳小型檔案，請使用多部分形式的表單，或使用 JavaScript 來建立 POST 要求。</span><span class="sxs-lookup"><span data-stu-id="a1846-418">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="a1846-419">下列範例將示範 Razor 如何使用頁面表單，在範例應用程式) 中上傳單一檔案 (*Pages/BufferedSingleFileUploadPhysical. cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="a1846-419">The following example demonstrates the use of a Razor Pages form to upload a single file (*Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="a1846-420">下列範例類似于先前的範例，不同之處在于：</span><span class="sxs-lookup"><span data-stu-id="a1846-420">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="a1846-421">JavaScript 的 ([FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 用來提交表單的資料。</span><span class="sxs-lookup"><span data-stu-id="a1846-421">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="a1846-422">沒有任何驗證。</span><span class="sxs-lookup"><span data-stu-id="a1846-422">There's no validation.</span></span>

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

<span data-ttu-id="a1846-423">若要針對 [不支援 FETCH API](https://caniuse.com/#feat=fetch)的用戶端，在 JavaScript 中執行表單貼文，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-423">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="a1846-424">使用 Fetch Polyfill (例如， [Polyfill (github/fetch) ](https://github.com/github/fetch)) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-424">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="a1846-425">請使用 `XMLHttpRequest`。</span><span class="sxs-lookup"><span data-stu-id="a1846-425">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="a1846-426">例如：</span><span class="sxs-lookup"><span data-stu-id="a1846-426">For example:</span></span>

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

<span data-ttu-id="a1846-427">為了支援檔案上傳，HTML 表單必須指定 (`enctype`) 的編碼類型 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-427">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="a1846-428">`files`若要讓輸入元素支援上傳多個檔案，請 `multiple` 在元素上提供屬性 `<input>` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-428">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="a1846-429">您可以使用，透過 [模型](xref:mvc/models/model-binding) 系結來存取上傳至伺服器的個別檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="a1846-429">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="a1846-430">範例應用程式會示範針對資料庫和實體儲存案例的多個緩衝檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="a1846-430">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="a1846-431">請勿 **使用的** `FileName` 屬性做 <xref:Microsoft.AspNetCore.Http.IFormFile> 為顯示和記錄。</span><span class="sxs-lookup"><span data-stu-id="a1846-431">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="a1846-432">顯示或記錄時，HTML 會將檔案名編碼。</span><span class="sxs-lookup"><span data-stu-id="a1846-432">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="a1846-433">攻擊者可以提供惡意檔案名，包括完整路徑或相對路徑。</span><span class="sxs-lookup"><span data-stu-id="a1846-433">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="a1846-434">應用程式應該：</span><span class="sxs-lookup"><span data-stu-id="a1846-434">Applications should:</span></span>
>
> * <span data-ttu-id="a1846-435">從使用者提供的檔案名中移除路徑。</span><span class="sxs-lookup"><span data-stu-id="a1846-435">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="a1846-436">針對 UI 或記錄，儲存 HTML 編碼、路徑移除的檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-436">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="a1846-437">為儲存體產生新的隨機檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-437">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="a1846-438">下列程式碼會將路徑從檔案名中移除：</span><span class="sxs-lookup"><span data-stu-id="a1846-438">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="a1846-439">目前為止所提供的範例不考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="a1846-439">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="a1846-440">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="a1846-440">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="a1846-441">安全性考慮</span><span class="sxs-lookup"><span data-stu-id="a1846-441">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="a1846-442">驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-442">Validation</span></span>](#validation)

<span data-ttu-id="a1846-443">使用模型系結和來上傳檔案時 <xref:Microsoft.AspNetCore.Http.IFormFile> ，動作方法可以接受：</span><span class="sxs-lookup"><span data-stu-id="a1846-443">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="a1846-444">單一 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="a1846-444">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="a1846-445">下列任何代表數個檔案的集合：</span><span class="sxs-lookup"><span data-stu-id="a1846-445">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="a1846-446">[清單](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="a1846-446">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="a1846-447">系結符合依名稱的表單檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-447">Binding matches form files by name.</span></span> <span data-ttu-id="a1846-448">例如，中的 HTML `name` 值 `<input type="file" name="formFile">` 必須符合 () 系結的 c # 參數/屬性 `FormFile` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-448">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="a1846-449">如需詳細資訊，請參閱 [比 [對名稱] 屬性值與 POST 方法的參數名稱](#match-name-attribute-value-to-parameter-name-of-post-method) 一節。</span><span class="sxs-lookup"><span data-stu-id="a1846-449">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="a1846-450">下列範例將：</span><span class="sxs-lookup"><span data-stu-id="a1846-450">The following example:</span></span>

* <span data-ttu-id="a1846-451">迴圈一或多個上傳的檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-451">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="a1846-452">使用 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 傳回檔案的完整路徑，包括檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-452">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="a1846-453">使用應用程式所產生的檔案名，將檔案儲存到本機檔案系統。</span><span class="sxs-lookup"><span data-stu-id="a1846-453">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="a1846-454">傳回上傳檔案的總數和大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-454">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="a1846-455">用 `Path.GetRandomFileName` 來產生不含路徑的檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-455">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="a1846-456">在下列範例中，路徑是從 configuration 取得：</span><span class="sxs-lookup"><span data-stu-id="a1846-456">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="a1846-457">傳遞至的路徑 <xref:System.IO.FileStream> *必須* 包含檔案名。</span><span class="sxs-lookup"><span data-stu-id="a1846-457">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="a1846-458">如果未提供檔案名， <xref:System.UnauthorizedAccessException> 則會在執行時間擲回。</span><span class="sxs-lookup"><span data-stu-id="a1846-458">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="a1846-459">使用此技術所上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 會在處理之前，在記憶體或伺服器的磁片上進行緩衝處理。</span><span class="sxs-lookup"><span data-stu-id="a1846-459">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="a1846-460">在動作方法內， <xref:Microsoft.AspNetCore.Http.IFormFile> 內容可作為進行存取 <xref:System.IO.Stream> 。</span><span class="sxs-lookup"><span data-stu-id="a1846-460">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="a1846-461">除了本機檔案系統之外，檔案也可以儲存到網路共用或檔案儲存體服務（例如 [Azure Blob 儲存體](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)）。</span><span class="sxs-lookup"><span data-stu-id="a1846-461">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="a1846-462">如需在多個要上傳的檔案上進行迴圈，並使用安全檔案名的另一個範例，請參閱範例應用程式中的*Pages/BufferedMultipleFileUploadPhysical。*</span><span class="sxs-lookup"><span data-stu-id="a1846-462">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="a1846-463">[GetTempFileName](xref:System.IO.Path.GetTempFileName*) 會擲回， <xref:System.IO.IOException> 如果在不刪除先前的暫存檔的情況下建立65535個以上的檔案，則擲回。</span><span class="sxs-lookup"><span data-stu-id="a1846-463">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="a1846-464">65535檔案的限制為每個伺服器的限制。</span><span class="sxs-lookup"><span data-stu-id="a1846-464">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="a1846-465">如需 Windows OS 上這項限制的詳細資訊，請參閱下列主題中的備註：</span><span class="sxs-lookup"><span data-stu-id="a1846-465">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="a1846-466">GetTempFileNameA 函式</span><span class="sxs-lookup"><span data-stu-id="a1846-466">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="a1846-467">將具有緩衝模型系結的小型檔案上傳至資料庫</span><span class="sxs-lookup"><span data-stu-id="a1846-467">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="a1846-468">若要使用 [Entity Framework](/ef/core/index)將二進位檔案資料儲存在資料庫中，請 <xref:System.Byte> 在實體上定義陣列屬性：</span><span class="sxs-lookup"><span data-stu-id="a1846-468">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="a1846-469">針對包含下列各類別的類別，指定頁面模型屬性 <xref:Microsoft.AspNetCore.Http.IFormFile> ：</span><span class="sxs-lookup"><span data-stu-id="a1846-469">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="a1846-470"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接當做動作方法參數使用，或作為系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="a1846-470"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="a1846-471">先前的範例會使用系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="a1846-471">The prior example uses a bound model property.</span></span>

<span data-ttu-id="a1846-472">在 `FileUpload` Razor 頁面表單中使用：</span><span class="sxs-lookup"><span data-stu-id="a1846-472">The `FileUpload` is used in the Razor Pages form:</span></span>

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

<span data-ttu-id="a1846-473">當表單張貼至伺服器時，將複製 <xref:Microsoft.AspNetCore.Http.IFormFile> 到資料流程，並將它儲存為資料庫中的位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="a1846-473">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="a1846-474">在下列範例中，會 `_dbContext` 儲存應用程式的資料庫內容：</span><span class="sxs-lookup"><span data-stu-id="a1846-474">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="a1846-475">上述範例類似于範例應用程式中所示範的案例：</span><span class="sxs-lookup"><span data-stu-id="a1846-475">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="a1846-476">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="a1846-476">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="a1846-477">*Pages/BufferedSingleFileUploadDb，.cs*</span><span class="sxs-lookup"><span data-stu-id="a1846-477">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="a1846-478">將二進位資料儲存至關聯式資料庫時請小心，因為它可能會對效能造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="a1846-478">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="a1846-479">請勿依賴或信任 `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> 不含驗證的屬性。</span><span class="sxs-lookup"><span data-stu-id="a1846-479">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="a1846-480">`FileName`屬性只能用於顯示目的，而且只適用于 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="a1846-480">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="a1846-481">提供的範例不會考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="a1846-481">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="a1846-482">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="a1846-482">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="a1846-483">安全性考慮</span><span class="sxs-lookup"><span data-stu-id="a1846-483">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="a1846-484">驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-484">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="a1846-485">使用串流上傳大型檔案</span><span class="sxs-lookup"><span data-stu-id="a1846-485">Upload large files with streaming</span></span>

<span data-ttu-id="a1846-486">下列範例示範如何使用 JavaScript 將檔案串流至控制器動作。</span><span class="sxs-lookup"><span data-stu-id="a1846-486">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="a1846-487">檔案的 antiforgery token 是使用自訂篩選屬性所產生，並且傳遞至用戶端 HTTP 標頭，而不是在要求主體中。</span><span class="sxs-lookup"><span data-stu-id="a1846-487">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="a1846-488">因為動作方法會直接處理已上傳的資料，所以其他自訂篩選會停用表單模型系結。</span><span class="sxs-lookup"><span data-stu-id="a1846-488">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="a1846-489">在動作內，會使用 `MultipartReader` 來讀取表單內容，以讀取每個個別 `MultipartSection`、處理檔案，或視需要儲存內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-489">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="a1846-490">讀取多部分區段之後，動作會執行它自己的模型系結。</span><span class="sxs-lookup"><span data-stu-id="a1846-490">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="a1846-491">初始頁面回應會載入表單，並透過屬性) 將 antiforgery token 儲存在 cookie (中 `GenerateAntiforgeryTokenCookieAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-491">The initial page response loads the form and saves an antiforgery token in a cookie (via the `GenerateAntiforgeryTokenCookieAttribute` attribute).</span></span> <span data-ttu-id="a1846-492">屬性使用 ASP.NET Core 的內建 [antiforgery 支援](xref:security/anti-request-forgery) 來設定 cookie 具有要求權杖的。</span><span class="sxs-lookup"><span data-stu-id="a1846-492">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a cookie with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryTokenCookieAttribute)]

<span data-ttu-id="a1846-493">`DisableFormValueModelBindingAttribute`用來停用模型系結：</span><span class="sxs-lookup"><span data-stu-id="a1846-493">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="a1846-494">在範例應用程式中 `GenerateAntiforgeryTokenCookieAttribute` ， `DisableFormValueModelBindingAttribute` 會 `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ Razor 頁面慣例](xref:razor-pages/razor-pages-conventions)，並在和的頁面應用程式模型中套用為篩選：</span><span class="sxs-lookup"><span data-stu-id="a1846-494">In the sample app, `GenerateAntiforgeryTokenCookieAttribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [Razor Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="a1846-495">由於模型系結不會讀取表單，因此從表單系結的參數不會系結 (查詢、路由和標頭會繼續運作) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-495">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="a1846-496">動作方法會直接與屬性搭配使用 `Request` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-496">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="a1846-497">`MultipartReader` 是用來讀取每個區段。</span><span class="sxs-lookup"><span data-stu-id="a1846-497">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="a1846-498">索引鍵/值資料儲存在中 `KeyValueAccumulator` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-498">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="a1846-499">讀取多部分區段之後，的內容 `KeyValueAccumulator` 會用來將表單資料系結至模型類型。</span><span class="sxs-lookup"><span data-stu-id="a1846-499">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="a1846-500">`StreamingController.UploadDatabase`使用 EF Core 串流至資料庫的完整方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-500">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="a1846-501">`MultipartRequestHelper` (*公用程式/MultipartRequestHelper .cs*) ：</span><span class="sxs-lookup"><span data-stu-id="a1846-501">`MultipartRequestHelper` (*Utilities/MultipartRequestHelper.cs*):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="a1846-502">`StreamingController.UploadPhysical`串流至實體位置的完整方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-502">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="a1846-503">在範例應用程式中，驗證檢查是由處理 `FileHelpers.ProcessStreamedFile` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-503">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="a1846-504">驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-504">Validation</span></span>

<span data-ttu-id="a1846-505">範例應用程式的類別會示範經過緩衝處理和串流處理檔案上 `FileHelpers` 傳的數個檢查 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="a1846-505">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="a1846-506">若要 <xref:Microsoft.AspNetCore.Http.IFormFile> 在範例應用程式中處理緩衝的檔案上傳，請參閱 `ProcessFormFile` *公用程式/FileHelpers .cs* 檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="a1846-506">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="a1846-507">如需處理資料流程檔，請參閱 `ProcessStreamedFile` 相同檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="a1846-507">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="a1846-508">範例應用程式中所示範的驗證處理方法，不會掃描所上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-508">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="a1846-509">在大部分的生產案例中，會在檔案上使用病毒/惡意程式碼掃描器 API，然後將檔案提供給使用者或其他系統。</span><span class="sxs-lookup"><span data-stu-id="a1846-509">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="a1846-510">雖然本主題範例提供驗證技術的實用範例，但 `FileHelpers` 除非您執行下列作業，否則請勿在生產應用程式中執行類別：</span><span class="sxs-lookup"><span data-stu-id="a1846-510">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="a1846-511">充分瞭解執行。</span><span class="sxs-lookup"><span data-stu-id="a1846-511">Fully understand the implementation.</span></span>
> * <span data-ttu-id="a1846-512">針對應用程式的環境和規格，適當地修改執行。</span><span class="sxs-lookup"><span data-stu-id="a1846-512">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="a1846-513">**絕對不要在應用程式中執行安全程式碼，而不需滿足這些需求。**</span><span class="sxs-lookup"><span data-stu-id="a1846-513">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="a1846-514">內容驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-514">Content validation</span></span>

<span data-ttu-id="a1846-515">**在上傳的內容上使用協力廠商病毒/惡意程式碼掃描 API。**</span><span class="sxs-lookup"><span data-stu-id="a1846-515">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="a1846-516">掃描檔案對大量案例中的伺服器資源有需求。</span><span class="sxs-lookup"><span data-stu-id="a1846-516">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="a1846-517">如果要求處理效能因為檔案掃描而降低，請考慮將掃描工作卸載至 [背景服務](xref:fundamentals/host/hosted-services)，可能是在與應用程式伺服器不同的伺服器上執行的服務。</span><span class="sxs-lookup"><span data-stu-id="a1846-517">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="a1846-518">上傳的檔案通常會保留在隔離區域中，直到背景病毒掃描程式檢查它們為止。</span><span class="sxs-lookup"><span data-stu-id="a1846-518">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="a1846-519">當檔案通過時，檔案會移至一般檔案儲存位置。</span><span class="sxs-lookup"><span data-stu-id="a1846-519">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="a1846-520">這些步驟通常會與指出檔案掃描狀態的資料庫記錄一起執行。</span><span class="sxs-lookup"><span data-stu-id="a1846-520">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="a1846-521">藉由使用這種方法，應用程式和應用程式伺服器仍會專注于回應要求。</span><span class="sxs-lookup"><span data-stu-id="a1846-521">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="a1846-522">副檔名驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-522">File extension validation</span></span>

<span data-ttu-id="a1846-523">所上傳檔案的副檔名應針對允許的延伸模組清單進行檢查。</span><span class="sxs-lookup"><span data-stu-id="a1846-523">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="a1846-524">例如：</span><span class="sxs-lookup"><span data-stu-id="a1846-524">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="a1846-525">檔案簽章驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-525">File signature validation</span></span>

<span data-ttu-id="a1846-526">檔案的簽章是由檔案開頭的前幾個位元組所決定。</span><span class="sxs-lookup"><span data-stu-id="a1846-526">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="a1846-527">您可以使用這些位元組來指出延伸模組是否符合檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-527">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="a1846-528">範例應用程式會檢查檔案簽章中有幾個常見的檔案類型。</span><span class="sxs-lookup"><span data-stu-id="a1846-528">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="a1846-529">在下列範例中，會針對檔案檢查 JPEG 影像的檔案簽章：</span><span class="sxs-lookup"><span data-stu-id="a1846-529">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="a1846-530">若要取得其他檔案簽章，請參閱檔案簽章 [資料庫](https://www.filesignatures.net/) 與官方檔案規格。</span><span class="sxs-lookup"><span data-stu-id="a1846-530">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="a1846-531">檔案名安全性</span><span class="sxs-lookup"><span data-stu-id="a1846-531">File name security</span></span>

<span data-ttu-id="a1846-532">絕對不要使用用戶端提供的檔案名將檔案儲存至實體儲存體。</span><span class="sxs-lookup"><span data-stu-id="a1846-532">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="a1846-533">使用 [GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 建立檔案的安全檔案名，以建立完整路徑 (包括暫存儲存體的檔案名) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-533">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="a1846-534">Razor 自動為顯示的 HTML 編碼屬性值。</span><span class="sxs-lookup"><span data-stu-id="a1846-534">Razor automatically HTML encodes property values for display.</span></span> <span data-ttu-id="a1846-535">下列程式碼可安全地使用：</span><span class="sxs-lookup"><span data-stu-id="a1846-535">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="a1846-536">Razor從開始，一律 <xref:System.Net.WebUtility.HtmlEncode*> 是使用者要求的檔案名內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-536">Outside of Razor, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="a1846-537">許多執行必須包含檢查檔案是否存在;否則，檔案會覆寫相同名稱的檔案。</span><span class="sxs-lookup"><span data-stu-id="a1846-537">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="a1846-538">提供其他邏輯以符合您應用程式的規格。</span><span class="sxs-lookup"><span data-stu-id="a1846-538">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="a1846-539">大小驗證</span><span class="sxs-lookup"><span data-stu-id="a1846-539">Size validation</span></span>

<span data-ttu-id="a1846-540">限制上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="a1846-540">Limit the size of uploaded files.</span></span>

<span data-ttu-id="a1846-541">在範例應用程式中，檔案大小限制為 2 MB (以位元組) 表示。</span><span class="sxs-lookup"><span data-stu-id="a1846-541">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="a1846-542">這項限制是透過[Configuration](xref:fundamentals/configuration/index)檔案*appsettings.js*的設定提供：</span><span class="sxs-lookup"><span data-stu-id="a1846-542">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *appsettings.json* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="a1846-543">`FileSizeLimit`會插入至 `PageModel` 類別：</span><span class="sxs-lookup"><span data-stu-id="a1846-543">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="a1846-544">當檔案大小超過限制時，會拒絕檔案：</span><span class="sxs-lookup"><span data-stu-id="a1846-544">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="a1846-545">將名稱屬性值與 POST 方法的參數名稱相符</span><span class="sxs-lookup"><span data-stu-id="a1846-545">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="a1846-546">在 Razor 張貼表單資料或直接使用 JavaScript 的非表單中 `FormData` ，在表單的元素中指定的名稱或 `FormData` 必須符合控制器動作中參數的名稱。</span><span class="sxs-lookup"><span data-stu-id="a1846-546">In non-Razor forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="a1846-547">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="a1846-547">In the following example:</span></span>

* <span data-ttu-id="a1846-548">使用專案時 `<input>` ， `name` 屬性會設定為值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-548">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="a1846-549">`FormData`在 JavaScript 中使用時，名稱會設定為下列值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-549">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="a1846-550">針對 c # 方法 () 的參數使用相符的名稱 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-550">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="a1846-551">針對名為的 Razor 頁面頁面處理常式方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-551">For a Razor Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="a1846-552">針對 MVC POST 控制器動作方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-552">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="a1846-553">伺服器和應用程式設定</span><span class="sxs-lookup"><span data-stu-id="a1846-553">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="a1846-554">多部分主體長度限制</span><span class="sxs-lookup"><span data-stu-id="a1846-554">Multipart body length limit</span></span>

<span data-ttu-id="a1846-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 設定每個多部分主體長度的限制。</span><span class="sxs-lookup"><span data-stu-id="a1846-555"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="a1846-556">超過此限制的表單區段會 <xref:System.IO.InvalidDataException> 在剖析時擲回。</span><span class="sxs-lookup"><span data-stu-id="a1846-556">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="a1846-557">預設值為 134217728 (128 MB) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-557">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="a1846-558">使用中的設定來自訂限制 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-558">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1846-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 用來設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 單一頁面或動作的。</span><span class="sxs-lookup"><span data-stu-id="a1846-559"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="a1846-560">在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-560">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1846-561">在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面模型或動作方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-561">In a Razor Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="a1846-562">Kestrel 要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="a1846-562">Kestrel maximum request body size</span></span>

<span data-ttu-id="a1846-563">針對 Kestrel 所裝載的應用程式，預設的要求主體大小上限為30000000個位元組，大約是 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="a1846-563">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="a1846-564">使用 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 伺服器選項自訂限制：</span><span class="sxs-lookup"><span data-stu-id="a1846-564">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="a1846-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 用來設定單一頁面或動作的 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) 。</span><span class="sxs-lookup"><span data-stu-id="a1846-565"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="a1846-566">在 Razor 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="a1846-566">In a Razor Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="a1846-567">在 Razor 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面處理常式類別或動作方法：</span><span class="sxs-lookup"><span data-stu-id="a1846-567">In a Razor pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="a1846-568">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="a1846-568">Other Kestrel limits</span></span>

<span data-ttu-id="a1846-569">其他 Kestrel 限制可能適用于 Kestrel 所裝載的應用程式：</span><span class="sxs-lookup"><span data-stu-id="a1846-569">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="a1846-570">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="a1846-570">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="a1846-571">要求和回應資料速率</span><span class="sxs-lookup"><span data-stu-id="a1846-571">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis-content-length-limit"></a><span data-ttu-id="a1846-572">IIS 內容長度限制</span><span class="sxs-lookup"><span data-stu-id="a1846-572">IIS content length limit</span></span>

<span data-ttu-id="a1846-573">預設的要求限制 (`maxAllowedContentLength`) 為30000000個位元組，大約是 28.6 mb。</span><span class="sxs-lookup"><span data-stu-id="a1846-573">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6MB.</span></span> <span data-ttu-id="a1846-574">自訂 *web.config* 檔案中的限制：</span><span class="sxs-lookup"><span data-stu-id="a1846-574">Customize the limit in the *web.config* file:</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <!-- Handle requests up to 50 MB -->
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="a1846-575">這個設定只適用於 IIS。</span><span class="sxs-lookup"><span data-stu-id="a1846-575">This setting only applies to IIS.</span></span> <span data-ttu-id="a1846-576">在 Kestrel 上裝載時，預設不會發生此行為。</span><span class="sxs-lookup"><span data-stu-id="a1846-576">The behavior doesn't occur by default when hosting on Kestrel.</span></span> <span data-ttu-id="a1846-577">如需詳細資訊，請參閱[要求限制 \<requestLimits> ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="a1846-577">For more information, see [Request Limits \<requestLimits>](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="a1846-578">ASP.NET Core 模組中的限制或 IIS 要求篩選模組的存在，可能會將上傳限制為2或 4 GB。</span><span class="sxs-lookup"><span data-stu-id="a1846-578">Limitations in the ASP.NET Core Module or presence of the IIS Request Filtering Module may limit uploads to either 2 or 4 GB.</span></span> <span data-ttu-id="a1846-579">如需詳細資訊，請參閱 [無法上傳大小超過2gb 的檔案 (dotnet/AspNetCore #2711) ](https://github.com/dotnet/AspNetCore/issues/2711)。</span><span class="sxs-lookup"><span data-stu-id="a1846-579">For more information, see [Unable to upload file greater than 2GB in size (dotnet/AspNetCore #2711)](https://github.com/dotnet/AspNetCore/issues/2711).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a1846-580">疑難排解</span><span class="sxs-lookup"><span data-stu-id="a1846-580">Troubleshoot</span></span>

<span data-ttu-id="a1846-581">以下是使用上傳檔案和其可能解決方案時發現的一些常見問題。</span><span class="sxs-lookup"><span data-stu-id="a1846-581">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="a1846-582">部署至 IIS 伺服器時找不到錯誤</span><span class="sxs-lookup"><span data-stu-id="a1846-582">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="a1846-583">下列錯誤表示上傳的檔案超過伺服器設定的內容長度：</span><span class="sxs-lookup"><span data-stu-id="a1846-583">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="a1846-584">如需增加限制的詳細資訊，請參閱 [IIS 內容長度限制](#iis-content-length-limit) 一節。</span><span class="sxs-lookup"><span data-stu-id="a1846-584">For more information on increasing the limit, see the [IIS content length limit](#iis-content-length-limit) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="a1846-585">連線失敗</span><span class="sxs-lookup"><span data-stu-id="a1846-585">Connection failure</span></span>

<span data-ttu-id="a1846-586">連接錯誤和重設伺服器連接可能表示上傳的檔案超過 Kestrel 的要求主體大小上限。</span><span class="sxs-lookup"><span data-stu-id="a1846-586">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="a1846-587">如需詳細資訊，請參閱 [Kestrel 的要求主體大小上限](#kestrel-maximum-request-body-size) 一節。</span><span class="sxs-lookup"><span data-stu-id="a1846-587">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="a1846-588">Kestrel 用戶端連接限制也可能需要調整。</span><span class="sxs-lookup"><span data-stu-id="a1846-588">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="a1846-589">IFormFile 的 Null 參考例外狀況</span><span class="sxs-lookup"><span data-stu-id="a1846-589">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="a1846-590">如果控制器使用已上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> ，但值為 `null` ，請確認 HTML 表單指定 `enctype` 的值 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-590">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="a1846-591">如果未在專案上設定此屬性 `<form>` ，則不會進行檔案上傳，而且任何系結的 <xref:Microsoft.AspNetCore.Http.IFormFile> 引數都是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-591">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="a1846-592">也請確認 [表單資料中的上傳命名符合應用程式的命名](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="a1846-592">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="a1846-593">資料流程太長</span><span class="sxs-lookup"><span data-stu-id="a1846-593">Stream was too long</span></span>

<span data-ttu-id="a1846-594">本主題中的範例會依賴 <xref:System.IO.MemoryStream> 保存已上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-594">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="a1846-595">的大小限制 `MemoryStream` 為 `int.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="a1846-595">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="a1846-596">如果應用程式的檔案上傳案例需要保存大於 50 MB 的檔案內容，請使用另一種方法，而不依賴單一 `MemoryStream` 的方法來保存上傳的檔案內容。</span><span class="sxs-lookup"><span data-stu-id="a1846-596">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="a1846-597">其他資源</span><span class="sxs-lookup"><span data-stu-id="a1846-597">Additional resources</span></span>

* [<span data-ttu-id="a1846-598">HTTP 連接要求清空</span><span class="sxs-lookup"><span data-stu-id="a1846-598">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="a1846-599">不受限制的檔案上傳</span><span class="sxs-lookup"><span data-stu-id="a1846-599">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="a1846-600">Azure 安全性：安全性框架：輸入驗證 |措施</span><span class="sxs-lookup"><span data-stu-id="a1846-600">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="a1846-601">Azure 雲端設計模式： Valet 金鑰模式</span><span class="sxs-lookup"><span data-stu-id="a1846-601">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
