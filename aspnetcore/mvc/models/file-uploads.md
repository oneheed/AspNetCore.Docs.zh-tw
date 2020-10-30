---
title: 在 ASP.NET Core 上傳檔案
author: rick-anderson
description: 如何使用模型繫結和資料流在 ASP.NET Core MVC 上傳檔案。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/21/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/models/file-uploads
ms.openlocfilehash: 14561bace565c104d0a9c926cad3105c4865e72a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061167"
---
# <a name="upload-files-in-aspnet-core"></a><span data-ttu-id="e9835-103">在 ASP.NET Core 上傳檔案</span><span class="sxs-lookup"><span data-stu-id="e9835-103">Upload files in ASP.NET Core</span></span>

<span data-ttu-id="e9835-104">[Steve Smith](https://ardalis.com/)和[Rutger 風暴](https://github.com/rutix)</span><span class="sxs-lookup"><span data-stu-id="e9835-104">By [Steve Smith](https://ardalis.com/) and [Rutger Storm](https://github.com/rutix)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="e9835-105">ASP.NET Core 支援針對較小的檔案使用緩衝的模型系結上傳一或多個檔案，並針對較大的檔案上傳未緩衝</span><span class="sxs-lookup"><span data-stu-id="e9835-105">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="e9835-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e9835-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="e9835-107">安全性考量</span><span class="sxs-lookup"><span data-stu-id="e9835-107">Security considerations</span></span>

<span data-ttu-id="e9835-108">提供使用者將檔案上傳至伺服器的功能時，請特別小心。</span><span class="sxs-lookup"><span data-stu-id="e9835-108">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="e9835-109">攻擊者可能會嘗試：</span><span class="sxs-lookup"><span data-stu-id="e9835-109">Attackers may attempt to:</span></span>

* <span data-ttu-id="e9835-110">執行 [拒絕服務的](/windows-hardware/drivers/ifs/denial-of-service) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="e9835-110">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="e9835-111">上傳病毒或惡意程式碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-111">Upload viruses or malware.</span></span>
* <span data-ttu-id="e9835-112">以其他方式危害網路和伺服器。</span><span class="sxs-lookup"><span data-stu-id="e9835-112">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="e9835-113">降低成功攻擊可能性的安全性步驟如下：</span><span class="sxs-lookup"><span data-stu-id="e9835-113">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="e9835-114">將檔案上傳到專用檔案上傳區域，最好是非系統磁片磁碟機。</span><span class="sxs-lookup"><span data-stu-id="e9835-114">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="e9835-115">專用位置可讓您更輕鬆地對上傳的檔案強加安全性限制。</span><span class="sxs-lookup"><span data-stu-id="e9835-115">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="e9835-116">停用檔案上傳位置的執行許可權。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-116">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="e9835-117">請勿將上傳的 **檔案保存在** 與應用程式相同的目錄樹狀結構中。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-117">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="e9835-118">使用應用程式所決定的安全檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-118">Use a safe file name determined by the app.</span></span> <span data-ttu-id="e9835-119">請勿使用使用者提供的檔案名或上傳檔案的不受信任檔案名。 &dagger; HTML 會在顯示不受信任的檔案名時進行編碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-119">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="e9835-120">例如，記錄檔案名或在 UI 中顯示 (:::no-loc(Razor)::: 會自動以 HTML 編碼輸出) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-120">For example, logging the file name or displaying in UI (:::no-loc(Razor)::: automatically HTML encodes output).</span></span>
* <span data-ttu-id="e9835-121">只允許應用程式設計規格的核准副檔名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-121">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="e9835-122">確認已在伺服器上執行用戶端檢查。 &dagger; 用戶端檢查很容易規避。</span><span class="sxs-lookup"><span data-stu-id="e9835-122">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="e9835-123">檢查上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-123">Check the size of an uploaded file.</span></span> <span data-ttu-id="e9835-124">設定大小上限以防止大上傳。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-124">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="e9835-125">使用相同名稱的已上傳檔案覆寫檔案時，請先檢查資料庫或實體儲存體的檔案名，再上傳檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-125">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="e9835-126">**在儲存檔案之前，在上傳的內容上執行病毒/惡意程式碼掃描器。**</span><span class="sxs-lookup"><span data-stu-id="e9835-126">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="e9835-127">&dagger;範例應用程式會示範符合準則的方法。</span><span class="sxs-lookup"><span data-stu-id="e9835-127">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-128">將惡意程式碼上傳至系統經常是執行程式碼的第一步，該程式碼可能：</span><span class="sxs-lookup"><span data-stu-id="e9835-128">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="e9835-129">完全掌握系統的控制權。</span><span class="sxs-lookup"><span data-stu-id="e9835-129">Completely gain control of a system.</span></span>
> * <span data-ttu-id="e9835-130">使用系統損毀的結果來多載系統。</span><span class="sxs-lookup"><span data-stu-id="e9835-130">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="e9835-131">洩漏使用者或系統資料。</span><span class="sxs-lookup"><span data-stu-id="e9835-131">Compromise user or system data.</span></span>
> * <span data-ttu-id="e9835-132">將刻套用至公用 UI。</span><span class="sxs-lookup"><span data-stu-id="e9835-132">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="e9835-133">如需在接受來自使用者的檔案時減少攻擊介面區的資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="e9835-133">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="e9835-134">不受限制的檔案上傳</span><span class="sxs-lookup"><span data-stu-id="e9835-134">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="e9835-135">Azure 安全性：請確保在接受來自使用者的檔案時，適當的控制項已就緒</span><span class="sxs-lookup"><span data-stu-id="e9835-135">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="e9835-136">如需有關實施安全性措施的詳細資訊，包括範例應用程式中的範例，請參閱 [驗證](#validation) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-136">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="e9835-137">儲存體案例</span><span class="sxs-lookup"><span data-stu-id="e9835-137">Storage scenarios</span></span>

<span data-ttu-id="e9835-138">檔案的一般儲存選項包括：</span><span class="sxs-lookup"><span data-stu-id="e9835-138">Common storage options for files include:</span></span>

* <span data-ttu-id="e9835-139">資料庫</span><span class="sxs-lookup"><span data-stu-id="e9835-139">Database</span></span>

  * <span data-ttu-id="e9835-140">若為小型檔案上傳，資料庫通常會比實體儲存體 (檔案系統或網路共用) 選項更快。</span><span class="sxs-lookup"><span data-stu-id="e9835-140">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="e9835-141">資料庫通常比實體儲存體選項更方便，因為抓取使用者資料的資料庫記錄可以同時提供檔案內容 (例如，) 的圖片。</span><span class="sxs-lookup"><span data-stu-id="e9835-141">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="e9835-142">資料庫的成本可能比使用資料儲存體服務還便宜。</span><span class="sxs-lookup"><span data-stu-id="e9835-142">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="e9835-143">實體儲存體 (檔案系統或網路共用) </span><span class="sxs-lookup"><span data-stu-id="e9835-143">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="e9835-144">針對大型檔案上傳：</span><span class="sxs-lookup"><span data-stu-id="e9835-144">For large file uploads:</span></span>
    * <span data-ttu-id="e9835-145">資料庫限制可能會限制上傳的大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-145">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="e9835-146">實體儲存體通常比資料庫中的儲存體便宜。</span><span class="sxs-lookup"><span data-stu-id="e9835-146">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="e9835-147">實體儲存體的成本可能比使用資料儲存體服務更便宜。</span><span class="sxs-lookup"><span data-stu-id="e9835-147">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="e9835-148">應用程式的進程必須具有儲存位置的讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="e9835-148">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="e9835-149">**絕不授與 execute 許可權。**</span><span class="sxs-lookup"><span data-stu-id="e9835-149">**Never grant execute permission.**</span></span>

* <span data-ttu-id="e9835-150">資料儲存體服務 (例如 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)) </span><span class="sxs-lookup"><span data-stu-id="e9835-150">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="e9835-151">服務通常會在內部部署解決方案中提供改良的擴充性和復原能力，這些解決方案通常會受限於單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="e9835-151">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="e9835-152">在大型儲存體基礎結構案例中，服務可能會降低成本。</span><span class="sxs-lookup"><span data-stu-id="e9835-152">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="e9835-153">如需詳細資訊，請參閱 [快速入門：使用 .net 在物件儲存體中建立 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="e9835-153">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="e9835-154">檔案上傳案例</span><span class="sxs-lookup"><span data-stu-id="e9835-154">File upload scenarios</span></span>

<span data-ttu-id="e9835-155">上傳檔案的兩個一般方法是緩衝處理和串流處理。</span><span class="sxs-lookup"><span data-stu-id="e9835-155">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="e9835-156">**緩衝處理**</span><span class="sxs-lookup"><span data-stu-id="e9835-156">**Buffering**</span></span>

<span data-ttu-id="e9835-157">系統會將整個檔案讀入 <xref:Microsoft.AspNetCore.Http.IFormFile> ，這是用來處理或儲存檔案之檔案的 c # 標記法。</span><span class="sxs-lookup"><span data-stu-id="e9835-157">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="e9835-158">檔案上傳所使用的資源 (磁片、記憶體) 取決於並行檔案上傳的數目和大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-158">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="e9835-159">如果應用程式嘗試緩衝過多上傳，則當網站的記憶體或磁碟空間不足時，就會損毀。</span><span class="sxs-lookup"><span data-stu-id="e9835-159">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="e9835-160">如果檔案上傳的大小或頻率是耗盡應用程式資源，請使用串流處理。</span><span class="sxs-lookup"><span data-stu-id="e9835-160">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="e9835-161">任何超過 64 KB 的緩衝檔案都會從記憶體移至磁片上的暫存檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-161">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="e9835-162">本主題的下列各節涵蓋了小型檔案的緩衝：</span><span class="sxs-lookup"><span data-stu-id="e9835-162">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="e9835-163">實體儲存體</span><span class="sxs-lookup"><span data-stu-id="e9835-163">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="e9835-164">資料庫</span><span class="sxs-lookup"><span data-stu-id="e9835-164">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="e9835-165">**串流**</span><span class="sxs-lookup"><span data-stu-id="e9835-165">**Streaming**</span></span>

<span data-ttu-id="e9835-166">從多部分要求接收檔案，並由應用程式直接處理或儲存。</span><span class="sxs-lookup"><span data-stu-id="e9835-166">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="e9835-167">串流無法大幅改善效能。</span><span class="sxs-lookup"><span data-stu-id="e9835-167">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="e9835-168">串流處理會在上傳檔案時減少記憶體或磁碟空間的需求。</span><span class="sxs-lookup"><span data-stu-id="e9835-168">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="e9835-169">[上傳含有串流的大型](#upload-large-files-with-streaming)檔案一節中涵蓋了串流大型檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-169">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="e9835-170">將具有緩衝模型系結的小型檔案上傳至實體儲存體</span><span class="sxs-lookup"><span data-stu-id="e9835-170">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="e9835-171">若要上傳小型檔案，請使用多部分形式的表單，或使用 JavaScript 來建立 POST 要求。</span><span class="sxs-lookup"><span data-stu-id="e9835-171">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="e9835-172">下列範例將示範 :::no-loc(Razor)::: 如何使用頁面表單，在範例應用程式) 中上傳單一檔案 ( *Pages/BufferedSingleFileUploadPhysical. cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="e9835-172">The following example demonstrates the use of a :::no-loc(Razor)::: Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="e9835-173">下列範例類似于先前的範例，不同之處在于：</span><span class="sxs-lookup"><span data-stu-id="e9835-173">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="e9835-174">JavaScript 的 ([FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 用來提交表單的資料。</span><span class="sxs-lookup"><span data-stu-id="e9835-174">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="e9835-175">沒有任何驗證。</span><span class="sxs-lookup"><span data-stu-id="e9835-175">There's no validation.</span></span>

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

<span data-ttu-id="e9835-176">若要針對 [不支援 FETCH API](https://caniuse.com/#feat=fetch)的用戶端，在 JavaScript 中執行表單貼文，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-176">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="e9835-177">使用 Fetch Polyfill (例如， [Polyfill (github/fetch) ](https://github.com/github/fetch)) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-177">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="e9835-178">請使用 `XMLHttpRequest`。</span><span class="sxs-lookup"><span data-stu-id="e9835-178">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="e9835-179">例如：</span><span class="sxs-lookup"><span data-stu-id="e9835-179">For example:</span></span>

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

<span data-ttu-id="e9835-180">為了支援檔案上傳，HTML 表單必須指定 (`enctype`) 的編碼類型 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-180">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="e9835-181">`files`若要讓輸入元素支援上傳多個檔案，請 `multiple` 在元素上提供屬性 `<input>` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-181">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="e9835-182">您可以使用，透過 [模型](xref:mvc/models/model-binding) 系結來存取上傳至伺服器的個別檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-182">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="e9835-183">範例應用程式會示範針對資料庫和實體儲存案例的多個緩衝檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="e9835-183">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="e9835-184">請勿 **使用的** `FileName` 屬性做 <xref:Microsoft.AspNetCore.Http.IFormFile> 為顯示和記錄。</span><span class="sxs-lookup"><span data-stu-id="e9835-184">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="e9835-185">顯示或記錄時，HTML 會將檔案名編碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-185">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="e9835-186">攻擊者可以提供惡意檔案名，包括完整路徑或相對路徑。</span><span class="sxs-lookup"><span data-stu-id="e9835-186">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="e9835-187">應用程式應該：</span><span class="sxs-lookup"><span data-stu-id="e9835-187">Applications should:</span></span>
>
> * <span data-ttu-id="e9835-188">從使用者提供的檔案名中移除路徑。</span><span class="sxs-lookup"><span data-stu-id="e9835-188">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="e9835-189">針對 UI 或記錄，儲存 HTML 編碼、路徑移除的檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-189">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="e9835-190">為儲存體產生新的隨機檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-190">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="e9835-191">下列程式碼會將路徑從檔案名中移除：</span><span class="sxs-lookup"><span data-stu-id="e9835-191">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="e9835-192">目前為止所提供的範例不考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="e9835-192">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="e9835-193">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="e9835-193">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e9835-194">安全性考量</span><span class="sxs-lookup"><span data-stu-id="e9835-194">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e9835-195">驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-195">Validation</span></span>](#validation)

<span data-ttu-id="e9835-196">使用模型系結和來上傳檔案時 <xref:Microsoft.AspNetCore.Http.IFormFile> ，動作方法可以接受：</span><span class="sxs-lookup"><span data-stu-id="e9835-196">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="e9835-197">單一 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-197">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="e9835-198">下列任何代表數個檔案的集合：</span><span class="sxs-lookup"><span data-stu-id="e9835-198">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="e9835-199">[清單](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="e9835-199">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="e9835-200">系結符合依名稱的表單檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-200">Binding matches form files by name.</span></span> <span data-ttu-id="e9835-201">例如，中的 HTML `name` 值 `<input type="file" name="formFile">` 必須符合 () 系結的 c # 參數/屬性 `FormFile` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-201">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="e9835-202">如需詳細資訊，請參閱 [比 [對名稱] 屬性值與 POST 方法的參數名稱](#match-name-attribute-value-to-parameter-name-of-post-method) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-202">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="e9835-203">下列範例將：</span><span class="sxs-lookup"><span data-stu-id="e9835-203">The following example:</span></span>

* <span data-ttu-id="e9835-204">迴圈一或多個上傳的檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-204">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="e9835-205">使用 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 傳回檔案的完整路徑，包括檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-205">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="e9835-206">使用應用程式所產生的檔案名，將檔案儲存到本機檔案系統。</span><span class="sxs-lookup"><span data-stu-id="e9835-206">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="e9835-207">傳回上傳檔案的總數和大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-207">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="e9835-208">用 `Path.GetRandomFileName` 來產生不含路徑的檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-208">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="e9835-209">在下列範例中，路徑是從 configuration 取得：</span><span class="sxs-lookup"><span data-stu-id="e9835-209">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="e9835-210">傳遞至的路徑 <xref:System.IO.FileStream> *必須* 包含檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-210">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="e9835-211">如果未提供檔案名， <xref:System.UnauthorizedAccessException> 則會在執行時間擲回。</span><span class="sxs-lookup"><span data-stu-id="e9835-211">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="e9835-212">使用此技術所上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 會在處理之前，在記憶體或伺服器的磁片上進行緩衝處理。</span><span class="sxs-lookup"><span data-stu-id="e9835-212">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="e9835-213">在動作方法內， <xref:Microsoft.AspNetCore.Http.IFormFile> 內容可作為進行存取 <xref:System.IO.Stream> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-213">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="e9835-214">除了本機檔案系統之外，檔案也可以儲存到網路共用或檔案儲存體服務（例如 [Azure Blob 儲存體](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)）。</span><span class="sxs-lookup"><span data-stu-id="e9835-214">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="e9835-215">如需在多個要上傳的檔案上進行迴圈，並使用安全檔案名的另一個範例，請參閱範例應用程式中的 *Pages/BufferedMultipleFileUploadPhysical。*</span><span class="sxs-lookup"><span data-stu-id="e9835-215">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-216">[GetTempFileName](xref:System.IO.Path.GetTempFileName*) 會擲回， <xref:System.IO.IOException> 如果在不刪除先前的暫存檔的情況下建立65535個以上的檔案，則擲回。</span><span class="sxs-lookup"><span data-stu-id="e9835-216">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="e9835-217">65535檔案的限制為每個伺服器的限制。</span><span class="sxs-lookup"><span data-stu-id="e9835-217">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="e9835-218">如需 Windows OS 上這項限制的詳細資訊，請參閱下列主題中的備註：</span><span class="sxs-lookup"><span data-stu-id="e9835-218">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="e9835-219">GetTempFileNameA 函式</span><span class="sxs-lookup"><span data-stu-id="e9835-219">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="e9835-220">將具有緩衝模型系結的小型檔案上傳至資料庫</span><span class="sxs-lookup"><span data-stu-id="e9835-220">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="e9835-221">若要使用 [Entity Framework](/ef/core/index)將二進位檔案資料儲存在資料庫中，請 <xref:System.Byte> 在實體上定義陣列屬性：</span><span class="sxs-lookup"><span data-stu-id="e9835-221">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="e9835-222">針對包含下列各類別的類別，指定頁面模型屬性 <xref:Microsoft.AspNetCore.Http.IFormFile> ：</span><span class="sxs-lookup"><span data-stu-id="e9835-222">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="e9835-223"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接當做動作方法參數使用，或作為系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="e9835-223"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="e9835-224">先前的範例會使用系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="e9835-224">The prior example uses a bound model property.</span></span>

<span data-ttu-id="e9835-225">在 `FileUpload` :::no-loc(Razor)::: 頁面表單中使用：</span><span class="sxs-lookup"><span data-stu-id="e9835-225">The `FileUpload` is used in the :::no-loc(Razor)::: Pages form:</span></span>

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

<span data-ttu-id="e9835-226">當表單張貼至伺服器時，將複製 <xref:Microsoft.AspNetCore.Http.IFormFile> 到資料流程，並將它儲存為資料庫中的位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="e9835-226">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="e9835-227">在下列範例中，會 `_dbContext` 儲存應用程式的資料庫內容：</span><span class="sxs-lookup"><span data-stu-id="e9835-227">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="e9835-228">上述範例類似于範例應用程式中所示範的案例：</span><span class="sxs-lookup"><span data-stu-id="e9835-228">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="e9835-229">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e9835-229">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="e9835-230">*Pages/BufferedSingleFileUploadDb，.cs*</span><span class="sxs-lookup"><span data-stu-id="e9835-230">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-231">將二進位資料儲存至關聯式資料庫時請小心，因為它可能會對效能造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="e9835-231">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="e9835-232">請勿依賴或信任 `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> 不含驗證的屬性。</span><span class="sxs-lookup"><span data-stu-id="e9835-232">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="e9835-233">`FileName`屬性只能用於顯示目的，而且只適用于 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-233">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="e9835-234">提供的範例不會考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="e9835-234">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="e9835-235">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="e9835-235">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e9835-236">安全性考量</span><span class="sxs-lookup"><span data-stu-id="e9835-236">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e9835-237">驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-237">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="e9835-238">使用串流上傳大型檔案</span><span class="sxs-lookup"><span data-stu-id="e9835-238">Upload large files with streaming</span></span>

<span data-ttu-id="e9835-239">下列範例示範如何使用 JavaScript 將檔案串流至控制器動作。</span><span class="sxs-lookup"><span data-stu-id="e9835-239">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="e9835-240">檔案的 antiforgery token 是使用自訂篩選屬性所產生，並且傳遞至用戶端 HTTP 標頭，而不是在要求主體中。</span><span class="sxs-lookup"><span data-stu-id="e9835-240">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="e9835-241">因為動作方法會直接處理已上傳的資料，所以其他自訂篩選會停用表單模型系結。</span><span class="sxs-lookup"><span data-stu-id="e9835-241">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="e9835-242">在動作內，會使用 `MultipartReader` 來讀取表單內容，以讀取每個個別 `MultipartSection`、處理檔案，或視需要儲存內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-242">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="e9835-243">讀取多部分區段之後，動作會執行它自己的模型系結。</span><span class="sxs-lookup"><span data-stu-id="e9835-243">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="e9835-244">初始頁面回應會載入表單，並透過屬性) 將 antiforgery token 儲存在 :::no-loc(cookie)::: (中 `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-244">The initial page response loads the form and saves an antiforgery token in a :::no-loc(cookie)::: (via the `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attribute).</span></span> <span data-ttu-id="e9835-245">屬性使用 ASP.NET Core 的內建 [antiforgery 支援](xref:security/anti-request-forgery) 來設定 :::no-loc(cookie)::: 具有要求權杖的。</span><span class="sxs-lookup"><span data-stu-id="e9835-245">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a :::no-loc(cookie)::: with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute)]

<span data-ttu-id="e9835-246">`DisableFormValueModelBindingAttribute`用來停用模型系結：</span><span class="sxs-lookup"><span data-stu-id="e9835-246">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="e9835-247">在範例應用程式中 `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` ， `DisableFormValueModelBindingAttribute` 會 `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ :::no-loc(Razor)::: 頁面慣例](xref:razor-pages/razor-pages-conventions)，並在和的頁面應用程式模型中套用為篩選：</span><span class="sxs-lookup"><span data-stu-id="e9835-247">In the sample app, `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [:::no-loc(Razor)::: Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_Add:::no-loc(Razor):::Pages&highlight=7-10,16-19)]

<span data-ttu-id="e9835-248">由於模型系結不會讀取表單，因此從表單系結的參數不會系結 (查詢、路由和標頭會繼續運作) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-248">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="e9835-249">動作方法會直接與屬性搭配使用 `Request` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-249">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="e9835-250">`MultipartReader` 是用來讀取每個區段。</span><span class="sxs-lookup"><span data-stu-id="e9835-250">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="e9835-251">索引鍵/值資料儲存在中 `KeyValueAccumulator` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-251">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="e9835-252">讀取多部分區段之後，的內容 `KeyValueAccumulator` 會用來將表單資料系結至模型類型。</span><span class="sxs-lookup"><span data-stu-id="e9835-252">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="e9835-253">`StreamingController.UploadDatabase`使用 EF Core 串流至資料庫的完整方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-253">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="e9835-254">`MultipartRequestHelper` ( *公用程式/MultipartRequestHelper .cs* ) ：</span><span class="sxs-lookup"><span data-stu-id="e9835-254">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="e9835-255">`StreamingController.UploadPhysical`串流至實體位置的完整方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-255">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="e9835-256">在範例應用程式中，驗證檢查是由處理 `FileHelpers.ProcessStreamedFile` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-256">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="e9835-257">驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-257">Validation</span></span>

<span data-ttu-id="e9835-258">範例應用程式的類別會示範經過緩衝處理和串流處理檔案上 `FileHelpers` 傳的數個檢查 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-258">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="e9835-259">若要 <xref:Microsoft.AspNetCore.Http.IFormFile> 在範例應用程式中處理緩衝的檔案上傳，請參閱 `ProcessFormFile` *公用程式/FileHelpers .cs* 檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="e9835-259">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="e9835-260">如需處理資料流程檔，請參閱 `ProcessStreamedFile` 相同檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="e9835-260">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-261">範例應用程式中所示範的驗證處理方法，不會掃描所上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-261">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="e9835-262">在大部分的生產案例中，會在檔案上使用病毒/惡意程式碼掃描器 API，然後將檔案提供給使用者或其他系統。</span><span class="sxs-lookup"><span data-stu-id="e9835-262">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="e9835-263">雖然本主題範例提供驗證技術的實用範例，但 `FileHelpers` 除非您執行下列作業，否則請勿在生產應用程式中執行類別：</span><span class="sxs-lookup"><span data-stu-id="e9835-263">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="e9835-264">充分瞭解執行。</span><span class="sxs-lookup"><span data-stu-id="e9835-264">Fully understand the implementation.</span></span>
> * <span data-ttu-id="e9835-265">針對應用程式的環境和規格，適當地修改執行。</span><span class="sxs-lookup"><span data-stu-id="e9835-265">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="e9835-266">**絕對不要在應用程式中執行安全程式碼，而不需滿足這些需求。**</span><span class="sxs-lookup"><span data-stu-id="e9835-266">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="e9835-267">內容驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-267">Content validation</span></span>

<span data-ttu-id="e9835-268">**在上傳的內容上使用協力廠商病毒/惡意程式碼掃描 API。**</span><span class="sxs-lookup"><span data-stu-id="e9835-268">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="e9835-269">掃描檔案對大量案例中的伺服器資源有需求。</span><span class="sxs-lookup"><span data-stu-id="e9835-269">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="e9835-270">如果要求處理效能因為檔案掃描而降低，請考慮將掃描工作卸載至 [背景服務](xref:fundamentals/host/hosted-services)，可能是在與應用程式伺服器不同的伺服器上執行的服務。</span><span class="sxs-lookup"><span data-stu-id="e9835-270">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="e9835-271">上傳的檔案通常會保留在隔離區域中，直到背景病毒掃描程式檢查它們為止。</span><span class="sxs-lookup"><span data-stu-id="e9835-271">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="e9835-272">當檔案通過時，檔案會移至一般檔案儲存位置。</span><span class="sxs-lookup"><span data-stu-id="e9835-272">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="e9835-273">這些步驟通常會與指出檔案掃描狀態的資料庫記錄一起執行。</span><span class="sxs-lookup"><span data-stu-id="e9835-273">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="e9835-274">藉由使用這種方法，應用程式和應用程式伺服器仍會專注于回應要求。</span><span class="sxs-lookup"><span data-stu-id="e9835-274">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="e9835-275">副檔名驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-275">File extension validation</span></span>

<span data-ttu-id="e9835-276">所上傳檔案的副檔名應針對允許的延伸模組清單進行檢查。</span><span class="sxs-lookup"><span data-stu-id="e9835-276">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="e9835-277">例如：</span><span class="sxs-lookup"><span data-stu-id="e9835-277">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="e9835-278">檔案簽章驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-278">File signature validation</span></span>

<span data-ttu-id="e9835-279">檔案的簽章是由檔案開頭的前幾個位元組所決定。</span><span class="sxs-lookup"><span data-stu-id="e9835-279">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="e9835-280">您可以使用這些位元組來指出延伸模組是否符合檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-280">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="e9835-281">範例應用程式會檢查檔案簽章中有幾個常見的檔案類型。</span><span class="sxs-lookup"><span data-stu-id="e9835-281">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="e9835-282">在下列範例中，會針對檔案檢查 JPEG 影像的檔案簽章：</span><span class="sxs-lookup"><span data-stu-id="e9835-282">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="e9835-283">若要取得其他檔案簽章，請參閱檔案簽章 [資料庫](https://www.filesignatures.net/) 與官方檔案規格。</span><span class="sxs-lookup"><span data-stu-id="e9835-283">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="e9835-284">檔案名安全性</span><span class="sxs-lookup"><span data-stu-id="e9835-284">File name security</span></span>

<span data-ttu-id="e9835-285">絕對不要使用用戶端提供的檔案名將檔案儲存至實體儲存體。</span><span class="sxs-lookup"><span data-stu-id="e9835-285">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="e9835-286">使用 [GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 建立檔案的安全檔案名，以建立完整路徑 (包括暫存儲存體的檔案名) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-286">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="e9835-287">:::no-loc(Razor)::: 自動為顯示的 HTML 編碼屬性值。</span><span class="sxs-lookup"><span data-stu-id="e9835-287">:::no-loc(Razor)::: automatically HTML encodes property values for display.</span></span> <span data-ttu-id="e9835-288">下列程式碼可安全地使用：</span><span class="sxs-lookup"><span data-stu-id="e9835-288">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="e9835-289">:::no-loc(Razor):::從開始，一律 <xref:System.Net.WebUtility.HtmlEncode*> 是使用者要求的檔案名內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-289">Outside of :::no-loc(Razor):::, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="e9835-290">許多執行必須包含檢查檔案是否存在;否則，檔案會覆寫相同名稱的檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-290">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="e9835-291">提供其他邏輯以符合您應用程式的規格。</span><span class="sxs-lookup"><span data-stu-id="e9835-291">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="e9835-292">大小驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-292">Size validation</span></span>

<span data-ttu-id="e9835-293">限制上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-293">Limit the size of uploaded files.</span></span>

<span data-ttu-id="e9835-294">在範例應用程式中，檔案大小限制為 2 MB (以位元組) 表示。</span><span class="sxs-lookup"><span data-stu-id="e9835-294">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="e9835-295">這項限制是透過 [檔案的設定](xref:fundamentals/configuration/index) 提供的 *:::no-loc(appsettings.json):::* ：</span><span class="sxs-lookup"><span data-stu-id="e9835-295">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="e9835-296">`FileSizeLimit`會插入至 `PageModel` 類別：</span><span class="sxs-lookup"><span data-stu-id="e9835-296">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="e9835-297">當檔案大小超過限制時，會拒絕檔案：</span><span class="sxs-lookup"><span data-stu-id="e9835-297">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="e9835-298">將名稱屬性值與 POST 方法的參數名稱相符</span><span class="sxs-lookup"><span data-stu-id="e9835-298">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="e9835-299">在 :::no-loc(Razor)::: 張貼表單資料或直接使用 JavaScript 的非表單中 `FormData` ，在表單的元素中指定的名稱或 `FormData` 必須符合控制器動作中參數的名稱。</span><span class="sxs-lookup"><span data-stu-id="e9835-299">In non-:::no-loc(Razor)::: forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="e9835-300">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="e9835-300">In the following example:</span></span>

* <span data-ttu-id="e9835-301">使用專案時 `<input>` ， `name` 屬性會設定為值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-301">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="e9835-302">`FormData`在 JavaScript 中使用時，名稱會設定為下列值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-302">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="e9835-303">針對 c # 方法 () 的參數使用相符的名稱 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-303">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="e9835-304">針對名為的 :::no-loc(Razor)::: 頁面頁面處理常式方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-304">For a :::no-loc(Razor)::: Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="e9835-305">針對 MVC POST 控制器動作方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-305">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="e9835-306">伺服器和應用程式設定</span><span class="sxs-lookup"><span data-stu-id="e9835-306">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="e9835-307">多部分主體長度限制</span><span class="sxs-lookup"><span data-stu-id="e9835-307">Multipart body length limit</span></span>

<span data-ttu-id="e9835-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 設定每個多部分主體長度的限制。</span><span class="sxs-lookup"><span data-stu-id="e9835-308"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="e9835-309">超過此限制的表單區段會 <xref:System.IO.InvalidDataException> 在剖析時擲回。</span><span class="sxs-lookup"><span data-stu-id="e9835-309">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="e9835-310">預設值為 134217728 (128 MB) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-310">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="e9835-311">使用中的設定來自訂限制 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-311">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e9835-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 用來設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 單一頁面或動作的。</span><span class="sxs-lookup"><span data-stu-id="e9835-312"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="e9835-313">在 :::no-loc(Razor)::: 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-313">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
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

<span data-ttu-id="e9835-314">在 :::no-loc(Razor)::: 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面模型或動作方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-314">In a :::no-loc(Razor)::: Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="e9835-315">Kestrel 要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="e9835-315">Kestrel maximum request body size</span></span>

<span data-ttu-id="e9835-316">針對 Kestrel 所裝載的應用程式，預設的要求主體大小上限為30000000個位元組，大約是 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="e9835-316">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e9835-317">使用 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 伺服器選項自訂限制：</span><span class="sxs-lookup"><span data-stu-id="e9835-317">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="e9835-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 用來設定單一頁面或動作的 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-318"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="e9835-319">在 :::no-loc(Razor)::: 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-319">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
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

<span data-ttu-id="e9835-320">在 :::no-loc(Razor)::: 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面處理常式類別或動作方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-320">In a :::no-loc(Razor)::: pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="e9835-321">`RequestSizeLimitAttribute`也可以使用指示詞套用 [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: ：</span><span class="sxs-lookup"><span data-stu-id="e9835-321">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="e9835-322">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="e9835-322">Other Kestrel limits</span></span>

<span data-ttu-id="e9835-323">其他 Kestrel 限制可能適用于 Kestrel 所裝載的應用程式：</span><span class="sxs-lookup"><span data-stu-id="e9835-323">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="e9835-324">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="e9835-324">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="e9835-325">要求和回應資料速率</span><span class="sxs-lookup"><span data-stu-id="e9835-325">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="e9835-326">IIS</span><span class="sxs-lookup"><span data-stu-id="e9835-326">IIS</span></span>

<span data-ttu-id="e9835-327">預設的要求限制 (`maxAllowedContentLength`) 為30000000個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="e9835-327">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e9835-328">自訂檔案中的限制 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-328">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="e9835-329">在下列範例中，此限制設定為 50 MB (52428800 bytes) ：</span><span class="sxs-lookup"><span data-stu-id="e9835-329">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="e9835-330">此 `maxAllowedContentLength` 設定僅適用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="e9835-330">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="e9835-331">如需詳細資訊，請參閱[要求限制 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="e9835-331">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e9835-332">疑難排解</span><span class="sxs-lookup"><span data-stu-id="e9835-332">Troubleshoot</span></span>

<span data-ttu-id="e9835-333">以下是使用上傳檔案和其可能解決方案時發現的一些常見問題。</span><span class="sxs-lookup"><span data-stu-id="e9835-333">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="e9835-334">部署至 IIS 伺服器時找不到錯誤</span><span class="sxs-lookup"><span data-stu-id="e9835-334">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="e9835-335">下列錯誤表示上傳的檔案超過伺服器設定的內容長度：</span><span class="sxs-lookup"><span data-stu-id="e9835-335">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="e9835-336">如需詳細資訊，請參閱 [IIS](#iis) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-336">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="e9835-337">連線失敗</span><span class="sxs-lookup"><span data-stu-id="e9835-337">Connection failure</span></span>

<span data-ttu-id="e9835-338">連接錯誤和重設伺服器連接可能表示上傳的檔案超過 Kestrel 的要求主體大小上限。</span><span class="sxs-lookup"><span data-stu-id="e9835-338">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="e9835-339">如需詳細資訊，請參閱 [Kestrel 的要求主體大小上限](#kestrel-maximum-request-body-size) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-339">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="e9835-340">Kestrel 用戶端連接限制也可能需要調整。</span><span class="sxs-lookup"><span data-stu-id="e9835-340">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="e9835-341">IFormFile 的 Null 參考例外狀況</span><span class="sxs-lookup"><span data-stu-id="e9835-341">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="e9835-342">如果控制器使用已上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> ，但值為 `null` ，請確認 HTML 表單指定 `enctype` 的值 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-342">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="e9835-343">如果未在專案上設定此屬性 `<form>` ，則不會進行檔案上傳，而且任何系結的 <xref:Microsoft.AspNetCore.Http.IFormFile> 引數都是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-343">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="e9835-344">也請確認 [表單資料中的上傳命名符合應用程式的命名](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="e9835-344">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="e9835-345">資料流程太長</span><span class="sxs-lookup"><span data-stu-id="e9835-345">Stream was too long</span></span>

<span data-ttu-id="e9835-346">本主題中的範例會依賴 <xref:System.IO.MemoryStream> 保存已上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-346">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="e9835-347">的大小限制 `MemoryStream` 為 `int.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-347">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="e9835-348">如果應用程式的檔案上傳案例需要保存大於 50 MB 的檔案內容，請使用另一種方法，而不依賴單一 `MemoryStream` 的方法來保存上傳的檔案內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-348">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="e9835-349">ASP.NET Core 支援針對較小的檔案使用緩衝的模型系結上傳一或多個檔案，並針對較大的檔案上傳未緩衝</span><span class="sxs-lookup"><span data-stu-id="e9835-349">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="e9835-350">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e9835-350">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="e9835-351">安全性考量</span><span class="sxs-lookup"><span data-stu-id="e9835-351">Security considerations</span></span>

<span data-ttu-id="e9835-352">提供使用者將檔案上傳至伺服器的功能時，請特別小心。</span><span class="sxs-lookup"><span data-stu-id="e9835-352">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="e9835-353">攻擊者可能會嘗試：</span><span class="sxs-lookup"><span data-stu-id="e9835-353">Attackers may attempt to:</span></span>

* <span data-ttu-id="e9835-354">執行 [拒絕服務的](/windows-hardware/drivers/ifs/denial-of-service) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="e9835-354">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="e9835-355">上傳病毒或惡意程式碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-355">Upload viruses or malware.</span></span>
* <span data-ttu-id="e9835-356">以其他方式危害網路和伺服器。</span><span class="sxs-lookup"><span data-stu-id="e9835-356">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="e9835-357">降低成功攻擊可能性的安全性步驟如下：</span><span class="sxs-lookup"><span data-stu-id="e9835-357">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="e9835-358">將檔案上傳到專用檔案上傳區域，最好是非系統磁片磁碟機。</span><span class="sxs-lookup"><span data-stu-id="e9835-358">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="e9835-359">專用位置可讓您更輕鬆地對上傳的檔案強加安全性限制。</span><span class="sxs-lookup"><span data-stu-id="e9835-359">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="e9835-360">停用檔案上傳位置的執行許可權。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-360">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="e9835-361">請勿將上傳的 **檔案保存在** 與應用程式相同的目錄樹狀結構中。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-361">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="e9835-362">使用應用程式所決定的安全檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-362">Use a safe file name determined by the app.</span></span> <span data-ttu-id="e9835-363">請勿使用使用者提供的檔案名或上傳檔案的不受信任檔案名。 &dagger; HTML 會在顯示不受信任的檔案名時進行編碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-363">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="e9835-364">例如，記錄檔案名或在 UI 中顯示 (:::no-loc(Razor)::: 會自動以 HTML 編碼輸出) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-364">For example, logging the file name or displaying in UI (:::no-loc(Razor)::: automatically HTML encodes output).</span></span>
* <span data-ttu-id="e9835-365">只允許應用程式設計規格的核准副檔名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-365">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="e9835-366">確認已在伺服器上執行用戶端檢查。 &dagger; 用戶端檢查很容易規避。</span><span class="sxs-lookup"><span data-stu-id="e9835-366">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="e9835-367">檢查上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-367">Check the size of an uploaded file.</span></span> <span data-ttu-id="e9835-368">設定大小上限以防止大上傳。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-368">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="e9835-369">使用相同名稱的已上傳檔案覆寫檔案時，請先檢查資料庫或實體儲存體的檔案名，再上傳檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-369">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="e9835-370">**在儲存檔案之前，在上傳的內容上執行病毒/惡意程式碼掃描器。**</span><span class="sxs-lookup"><span data-stu-id="e9835-370">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="e9835-371">&dagger;範例應用程式會示範符合準則的方法。</span><span class="sxs-lookup"><span data-stu-id="e9835-371">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-372">將惡意程式碼上傳至系統經常是執行程式碼的第一步，該程式碼可能：</span><span class="sxs-lookup"><span data-stu-id="e9835-372">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="e9835-373">完全掌握系統的控制權。</span><span class="sxs-lookup"><span data-stu-id="e9835-373">Completely gain control of a system.</span></span>
> * <span data-ttu-id="e9835-374">使用系統損毀的結果來多載系統。</span><span class="sxs-lookup"><span data-stu-id="e9835-374">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="e9835-375">洩漏使用者或系統資料。</span><span class="sxs-lookup"><span data-stu-id="e9835-375">Compromise user or system data.</span></span>
> * <span data-ttu-id="e9835-376">將刻套用至公用 UI。</span><span class="sxs-lookup"><span data-stu-id="e9835-376">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="e9835-377">如需在接受來自使用者的檔案時減少攻擊介面區的資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="e9835-377">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="e9835-378">不受限制的檔案上傳</span><span class="sxs-lookup"><span data-stu-id="e9835-378">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="e9835-379">Azure 安全性：請確保在接受來自使用者的檔案時，適當的控制項已就緒</span><span class="sxs-lookup"><span data-stu-id="e9835-379">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="e9835-380">如需有關實施安全性措施的詳細資訊，包括範例應用程式中的範例，請參閱 [驗證](#validation) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-380">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="e9835-381">儲存體案例</span><span class="sxs-lookup"><span data-stu-id="e9835-381">Storage scenarios</span></span>

<span data-ttu-id="e9835-382">檔案的一般儲存選項包括：</span><span class="sxs-lookup"><span data-stu-id="e9835-382">Common storage options for files include:</span></span>

* <span data-ttu-id="e9835-383">資料庫</span><span class="sxs-lookup"><span data-stu-id="e9835-383">Database</span></span>

  * <span data-ttu-id="e9835-384">若為小型檔案上傳，資料庫通常會比實體儲存體 (檔案系統或網路共用) 選項更快。</span><span class="sxs-lookup"><span data-stu-id="e9835-384">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="e9835-385">資料庫通常比實體儲存體選項更方便，因為抓取使用者資料的資料庫記錄可以同時提供檔案內容 (例如，) 的圖片。</span><span class="sxs-lookup"><span data-stu-id="e9835-385">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="e9835-386">資料庫的成本可能比使用資料儲存體服務還便宜。</span><span class="sxs-lookup"><span data-stu-id="e9835-386">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="e9835-387">實體儲存體 (檔案系統或網路共用) </span><span class="sxs-lookup"><span data-stu-id="e9835-387">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="e9835-388">針對大型檔案上傳：</span><span class="sxs-lookup"><span data-stu-id="e9835-388">For large file uploads:</span></span>
    * <span data-ttu-id="e9835-389">資料庫限制可能會限制上傳的大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-389">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="e9835-390">實體儲存體通常比資料庫中的儲存體便宜。</span><span class="sxs-lookup"><span data-stu-id="e9835-390">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="e9835-391">實體儲存體的成本可能比使用資料儲存體服務更便宜。</span><span class="sxs-lookup"><span data-stu-id="e9835-391">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="e9835-392">應用程式的進程必須具有儲存位置的讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="e9835-392">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="e9835-393">**絕不授與 execute 許可權。**</span><span class="sxs-lookup"><span data-stu-id="e9835-393">**Never grant execute permission.**</span></span>

* <span data-ttu-id="e9835-394">資料儲存體服務 (例如 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)) </span><span class="sxs-lookup"><span data-stu-id="e9835-394">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="e9835-395">服務通常會在內部部署解決方案中提供改良的擴充性和復原能力，這些解決方案通常會受限於單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="e9835-395">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="e9835-396">在大型儲存體基礎結構案例中，服務可能會降低成本。</span><span class="sxs-lookup"><span data-stu-id="e9835-396">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="e9835-397">如需詳細資訊，請參閱 [快速入門：使用 .net 在物件儲存體中建立 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="e9835-397">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="e9835-398">檔案上傳案例</span><span class="sxs-lookup"><span data-stu-id="e9835-398">File upload scenarios</span></span>

<span data-ttu-id="e9835-399">上傳檔案的兩個一般方法是緩衝處理和串流處理。</span><span class="sxs-lookup"><span data-stu-id="e9835-399">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="e9835-400">**緩衝處理**</span><span class="sxs-lookup"><span data-stu-id="e9835-400">**Buffering**</span></span>

<span data-ttu-id="e9835-401">系統會將整個檔案讀入 <xref:Microsoft.AspNetCore.Http.IFormFile> ，這是用來處理或儲存檔案之檔案的 c # 標記法。</span><span class="sxs-lookup"><span data-stu-id="e9835-401">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="e9835-402">檔案上傳所使用的資源 (磁片、記憶體) 取決於並行檔案上傳的數目和大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-402">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="e9835-403">如果應用程式嘗試緩衝過多上傳，則當網站的記憶體或磁碟空間不足時，就會損毀。</span><span class="sxs-lookup"><span data-stu-id="e9835-403">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="e9835-404">如果檔案上傳的大小或頻率是耗盡應用程式資源，請使用串流處理。</span><span class="sxs-lookup"><span data-stu-id="e9835-404">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="e9835-405">任何超過 64 KB 的緩衝檔案都會從記憶體移至磁片上的暫存檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-405">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="e9835-406">本主題的下列各節涵蓋了小型檔案的緩衝：</span><span class="sxs-lookup"><span data-stu-id="e9835-406">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="e9835-407">實體儲存體</span><span class="sxs-lookup"><span data-stu-id="e9835-407">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="e9835-408">資料庫</span><span class="sxs-lookup"><span data-stu-id="e9835-408">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="e9835-409">**串流**</span><span class="sxs-lookup"><span data-stu-id="e9835-409">**Streaming**</span></span>

<span data-ttu-id="e9835-410">從多部分要求接收檔案，並由應用程式直接處理或儲存。</span><span class="sxs-lookup"><span data-stu-id="e9835-410">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="e9835-411">串流無法大幅改善效能。</span><span class="sxs-lookup"><span data-stu-id="e9835-411">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="e9835-412">串流處理會在上傳檔案時減少記憶體或磁碟空間的需求。</span><span class="sxs-lookup"><span data-stu-id="e9835-412">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="e9835-413">[上傳含有串流的大型](#upload-large-files-with-streaming)檔案一節中涵蓋了串流大型檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-413">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="e9835-414">將具有緩衝模型系結的小型檔案上傳至實體儲存體</span><span class="sxs-lookup"><span data-stu-id="e9835-414">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="e9835-415">若要上傳小型檔案，請使用多部分形式的表單，或使用 JavaScript 來建立 POST 要求。</span><span class="sxs-lookup"><span data-stu-id="e9835-415">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="e9835-416">下列範例將示範 :::no-loc(Razor)::: 如何使用頁面表單，在範例應用程式) 中上傳單一檔案 ( *Pages/BufferedSingleFileUploadPhysical. cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="e9835-416">The following example demonstrates the use of a :::no-loc(Razor)::: Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="e9835-417">下列範例類似于先前的範例，不同之處在于：</span><span class="sxs-lookup"><span data-stu-id="e9835-417">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="e9835-418">JavaScript 的 ([FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 用來提交表單的資料。</span><span class="sxs-lookup"><span data-stu-id="e9835-418">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="e9835-419">沒有任何驗證。</span><span class="sxs-lookup"><span data-stu-id="e9835-419">There's no validation.</span></span>

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

<span data-ttu-id="e9835-420">若要針對 [不支援 FETCH API](https://caniuse.com/#feat=fetch)的用戶端，在 JavaScript 中執行表單貼文，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-420">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="e9835-421">使用 Fetch Polyfill (例如， [Polyfill (github/fetch) ](https://github.com/github/fetch)) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-421">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="e9835-422">請使用 `XMLHttpRequest`。</span><span class="sxs-lookup"><span data-stu-id="e9835-422">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="e9835-423">例如：</span><span class="sxs-lookup"><span data-stu-id="e9835-423">For example:</span></span>

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

<span data-ttu-id="e9835-424">為了支援檔案上傳，HTML 表單必須指定 (`enctype`) 的編碼類型 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-424">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="e9835-425">`files`若要讓輸入元素支援上傳多個檔案，請 `multiple` 在元素上提供屬性 `<input>` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-425">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="e9835-426">您可以使用，透過 [模型](xref:mvc/models/model-binding) 系結來存取上傳至伺服器的個別檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-426">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="e9835-427">範例應用程式會示範針對資料庫和實體儲存案例的多個緩衝檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="e9835-427">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename"></a>

> [!WARNING]
> <span data-ttu-id="e9835-428">請勿 **使用的** `FileName` 屬性做 <xref:Microsoft.AspNetCore.Http.IFormFile> 為顯示和記錄。</span><span class="sxs-lookup"><span data-stu-id="e9835-428">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="e9835-429">顯示或記錄時，HTML 會將檔案名編碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-429">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="e9835-430">攻擊者可以提供惡意檔案名，包括完整路徑或相對路徑。</span><span class="sxs-lookup"><span data-stu-id="e9835-430">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="e9835-431">應用程式應該：</span><span class="sxs-lookup"><span data-stu-id="e9835-431">Applications should:</span></span>
>
> * <span data-ttu-id="e9835-432">從使用者提供的檔案名中移除路徑。</span><span class="sxs-lookup"><span data-stu-id="e9835-432">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="e9835-433">針對 UI 或記錄，儲存 HTML 編碼、路徑移除的檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-433">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="e9835-434">為儲存體產生新的隨機檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-434">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="e9835-435">下列程式碼會將路徑從檔案名中移除：</span><span class="sxs-lookup"><span data-stu-id="e9835-435">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="e9835-436">目前為止所提供的範例不考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="e9835-436">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="e9835-437">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="e9835-437">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e9835-438">安全性考量</span><span class="sxs-lookup"><span data-stu-id="e9835-438">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e9835-439">驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-439">Validation</span></span>](#validation)

<span data-ttu-id="e9835-440">使用模型系結和來上傳檔案時 <xref:Microsoft.AspNetCore.Http.IFormFile> ，動作方法可以接受：</span><span class="sxs-lookup"><span data-stu-id="e9835-440">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="e9835-441">單一 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-441">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="e9835-442">下列任何代表數個檔案的集合：</span><span class="sxs-lookup"><span data-stu-id="e9835-442">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="e9835-443">[清單](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="e9835-443">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="e9835-444">系結符合依名稱的表單檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-444">Binding matches form files by name.</span></span> <span data-ttu-id="e9835-445">例如，中的 HTML `name` 值 `<input type="file" name="formFile">` 必須符合 () 系結的 c # 參數/屬性 `FormFile` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-445">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="e9835-446">如需詳細資訊，請參閱 [比 [對名稱] 屬性值與 POST 方法的參數名稱](#match-name-attribute-value-to-parameter-name-of-post-method) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-446">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="e9835-447">下列範例將：</span><span class="sxs-lookup"><span data-stu-id="e9835-447">The following example:</span></span>

* <span data-ttu-id="e9835-448">迴圈一或多個上傳的檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-448">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="e9835-449">使用 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 傳回檔案的完整路徑，包括檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-449">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="e9835-450">使用應用程式所產生的檔案名，將檔案儲存到本機檔案系統。</span><span class="sxs-lookup"><span data-stu-id="e9835-450">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="e9835-451">傳回上傳檔案的總數和大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-451">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="e9835-452">用 `Path.GetRandomFileName` 來產生不含路徑的檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-452">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="e9835-453">在下列範例中，路徑是從 configuration 取得：</span><span class="sxs-lookup"><span data-stu-id="e9835-453">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="e9835-454">傳遞至的路徑 <xref:System.IO.FileStream> *必須* 包含檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-454">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="e9835-455">如果未提供檔案名， <xref:System.UnauthorizedAccessException> 則會在執行時間擲回。</span><span class="sxs-lookup"><span data-stu-id="e9835-455">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="e9835-456">使用此技術所上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 會在處理之前，在記憶體或伺服器的磁片上進行緩衝處理。</span><span class="sxs-lookup"><span data-stu-id="e9835-456">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="e9835-457">在動作方法內， <xref:Microsoft.AspNetCore.Http.IFormFile> 內容可作為進行存取 <xref:System.IO.Stream> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-457">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="e9835-458">除了本機檔案系統之外，檔案也可以儲存到網路共用或檔案儲存體服務（例如 [Azure Blob 儲存體](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)）。</span><span class="sxs-lookup"><span data-stu-id="e9835-458">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="e9835-459">如需在多個要上傳的檔案上進行迴圈，並使用安全檔案名的另一個範例，請參閱範例應用程式中的 *Pages/BufferedMultipleFileUploadPhysical。*</span><span class="sxs-lookup"><span data-stu-id="e9835-459">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-460">[GetTempFileName](xref:System.IO.Path.GetTempFileName*) 會擲回， <xref:System.IO.IOException> 如果在不刪除先前的暫存檔的情況下建立65535個以上的檔案，則擲回。</span><span class="sxs-lookup"><span data-stu-id="e9835-460">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="e9835-461">65535檔案的限制為每個伺服器的限制。</span><span class="sxs-lookup"><span data-stu-id="e9835-461">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="e9835-462">如需 Windows OS 上這項限制的詳細資訊，請參閱下列主題中的備註：</span><span class="sxs-lookup"><span data-stu-id="e9835-462">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="e9835-463">GetTempFileNameA 函式</span><span class="sxs-lookup"><span data-stu-id="e9835-463">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="e9835-464">將具有緩衝模型系結的小型檔案上傳至資料庫</span><span class="sxs-lookup"><span data-stu-id="e9835-464">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="e9835-465">若要使用 [Entity Framework](/ef/core/index)將二進位檔案資料儲存在資料庫中，請 <xref:System.Byte> 在實體上定義陣列屬性：</span><span class="sxs-lookup"><span data-stu-id="e9835-465">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="e9835-466">針對包含下列各類別的類別，指定頁面模型屬性 <xref:Microsoft.AspNetCore.Http.IFormFile> ：</span><span class="sxs-lookup"><span data-stu-id="e9835-466">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="e9835-467"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接當做動作方法參數使用，或作為系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="e9835-467"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="e9835-468">先前的範例會使用系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="e9835-468">The prior example uses a bound model property.</span></span>

<span data-ttu-id="e9835-469">在 `FileUpload` :::no-loc(Razor)::: 頁面表單中使用：</span><span class="sxs-lookup"><span data-stu-id="e9835-469">The `FileUpload` is used in the :::no-loc(Razor)::: Pages form:</span></span>

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

<span data-ttu-id="e9835-470">當表單張貼至伺服器時，將複製 <xref:Microsoft.AspNetCore.Http.IFormFile> 到資料流程，並將它儲存為資料庫中的位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="e9835-470">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="e9835-471">在下列範例中，會 `_dbContext` 儲存應用程式的資料庫內容：</span><span class="sxs-lookup"><span data-stu-id="e9835-471">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="e9835-472">上述範例類似于範例應用程式中所示範的案例：</span><span class="sxs-lookup"><span data-stu-id="e9835-472">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="e9835-473">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e9835-473">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="e9835-474">*Pages/BufferedSingleFileUploadDb，.cs*</span><span class="sxs-lookup"><span data-stu-id="e9835-474">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-475">將二進位資料儲存至關聯式資料庫時請小心，因為它可能會對效能造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="e9835-475">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="e9835-476">請勿依賴或信任 `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> 不含驗證的屬性。</span><span class="sxs-lookup"><span data-stu-id="e9835-476">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="e9835-477">`FileName`屬性只能用於顯示目的，而且只適用于 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-477">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="e9835-478">提供的範例不會考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="e9835-478">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="e9835-479">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="e9835-479">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e9835-480">安全性考量</span><span class="sxs-lookup"><span data-stu-id="e9835-480">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e9835-481">驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-481">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="e9835-482">使用串流上傳大型檔案</span><span class="sxs-lookup"><span data-stu-id="e9835-482">Upload large files with streaming</span></span>

<span data-ttu-id="e9835-483">下列範例示範如何使用 JavaScript 將檔案串流至控制器動作。</span><span class="sxs-lookup"><span data-stu-id="e9835-483">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="e9835-484">檔案的 antiforgery token 是使用自訂篩選屬性所產生，並且傳遞至用戶端 HTTP 標頭，而不是在要求主體中。</span><span class="sxs-lookup"><span data-stu-id="e9835-484">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="e9835-485">因為動作方法會直接處理已上傳的資料，所以其他自訂篩選會停用表單模型系結。</span><span class="sxs-lookup"><span data-stu-id="e9835-485">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="e9835-486">在動作內，會使用 `MultipartReader` 來讀取表單內容，以讀取每個個別 `MultipartSection`、處理檔案，或視需要儲存內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-486">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="e9835-487">讀取多部分區段之後，動作會執行它自己的模型系結。</span><span class="sxs-lookup"><span data-stu-id="e9835-487">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="e9835-488">初始頁面回應會載入表單，並透過屬性) 將 antiforgery token 儲存在 :::no-loc(cookie)::: (中 `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-488">The initial page response loads the form and saves an antiforgery token in a :::no-loc(cookie)::: (via the `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attribute).</span></span> <span data-ttu-id="e9835-489">屬性使用 ASP.NET Core 的內建 [antiforgery 支援](xref:security/anti-request-forgery) 來設定 :::no-loc(cookie)::: 具有要求權杖的。</span><span class="sxs-lookup"><span data-stu-id="e9835-489">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a :::no-loc(cookie)::: with a request token:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute)]

<span data-ttu-id="e9835-490">`DisableFormValueModelBindingAttribute`用來停用模型系結：</span><span class="sxs-lookup"><span data-stu-id="e9835-490">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="e9835-491">在範例應用程式中 `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` ， `DisableFormValueModelBindingAttribute` 會 `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ :::no-loc(Razor)::: 頁面慣例](xref:razor-pages/razor-pages-conventions)，並在和的頁面應用程式模型中套用為篩選：</span><span class="sxs-lookup"><span data-stu-id="e9835-491">In the sample app, `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [:::no-loc(Razor)::: Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Startup.cs?name=snippet_Add:::no-loc(Razor):::Pages&highlight=7-10,16-19)]

<span data-ttu-id="e9835-492">由於模型系結不會讀取表單，因此從表單系結的參數不會系結 (查詢、路由和標頭會繼續運作) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-492">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="e9835-493">動作方法會直接與屬性搭配使用 `Request` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-493">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="e9835-494">`MultipartReader` 是用來讀取每個區段。</span><span class="sxs-lookup"><span data-stu-id="e9835-494">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="e9835-495">索引鍵/值資料儲存在中 `KeyValueAccumulator` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-495">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="e9835-496">讀取多部分區段之後，的內容 `KeyValueAccumulator` 會用來將表單資料系結至模型類型。</span><span class="sxs-lookup"><span data-stu-id="e9835-496">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="e9835-497">`StreamingController.UploadDatabase`使用 EF Core 串流至資料庫的完整方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-497">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="e9835-498">`MultipartRequestHelper` ( *公用程式/MultipartRequestHelper .cs* ) ：</span><span class="sxs-lookup"><span data-stu-id="e9835-498">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="e9835-499">`StreamingController.UploadPhysical`串流至實體位置的完整方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-499">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/3.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="e9835-500">在範例應用程式中，驗證檢查是由處理 `FileHelpers.ProcessStreamedFile` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-500">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="e9835-501">驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-501">Validation</span></span>

<span data-ttu-id="e9835-502">範例應用程式的類別會示範經過緩衝處理和串流處理檔案上 `FileHelpers` 傳的數個檢查 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-502">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="e9835-503">若要 <xref:Microsoft.AspNetCore.Http.IFormFile> 在範例應用程式中處理緩衝的檔案上傳，請參閱 `ProcessFormFile` *公用程式/FileHelpers .cs* 檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="e9835-503">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="e9835-504">如需處理資料流程檔，請參閱 `ProcessStreamedFile` 相同檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="e9835-504">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-505">範例應用程式中所示範的驗證處理方法，不會掃描所上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-505">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="e9835-506">在大部分的生產案例中，會在檔案上使用病毒/惡意程式碼掃描器 API，然後將檔案提供給使用者或其他系統。</span><span class="sxs-lookup"><span data-stu-id="e9835-506">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="e9835-507">雖然本主題範例提供驗證技術的實用範例，但 `FileHelpers` 除非您執行下列作業，否則請勿在生產應用程式中執行類別：</span><span class="sxs-lookup"><span data-stu-id="e9835-507">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="e9835-508">充分瞭解執行。</span><span class="sxs-lookup"><span data-stu-id="e9835-508">Fully understand the implementation.</span></span>
> * <span data-ttu-id="e9835-509">針對應用程式的環境和規格，適當地修改執行。</span><span class="sxs-lookup"><span data-stu-id="e9835-509">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="e9835-510">**絕對不要在應用程式中執行安全程式碼，而不需滿足這些需求。**</span><span class="sxs-lookup"><span data-stu-id="e9835-510">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="e9835-511">內容驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-511">Content validation</span></span>

<span data-ttu-id="e9835-512">**在上傳的內容上使用協力廠商病毒/惡意程式碼掃描 API。**</span><span class="sxs-lookup"><span data-stu-id="e9835-512">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="e9835-513">掃描檔案對大量案例中的伺服器資源有需求。</span><span class="sxs-lookup"><span data-stu-id="e9835-513">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="e9835-514">如果要求處理效能因為檔案掃描而降低，請考慮將掃描工作卸載至 [背景服務](xref:fundamentals/host/hosted-services)，可能是在與應用程式伺服器不同的伺服器上執行的服務。</span><span class="sxs-lookup"><span data-stu-id="e9835-514">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="e9835-515">上傳的檔案通常會保留在隔離區域中，直到背景病毒掃描程式檢查它們為止。</span><span class="sxs-lookup"><span data-stu-id="e9835-515">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="e9835-516">當檔案通過時，檔案會移至一般檔案儲存位置。</span><span class="sxs-lookup"><span data-stu-id="e9835-516">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="e9835-517">這些步驟通常會與指出檔案掃描狀態的資料庫記錄一起執行。</span><span class="sxs-lookup"><span data-stu-id="e9835-517">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="e9835-518">藉由使用這種方法，應用程式和應用程式伺服器仍會專注于回應要求。</span><span class="sxs-lookup"><span data-stu-id="e9835-518">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="e9835-519">副檔名驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-519">File extension validation</span></span>

<span data-ttu-id="e9835-520">所上傳檔案的副檔名應針對允許的延伸模組清單進行檢查。</span><span class="sxs-lookup"><span data-stu-id="e9835-520">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="e9835-521">例如：</span><span class="sxs-lookup"><span data-stu-id="e9835-521">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="e9835-522">檔案簽章驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-522">File signature validation</span></span>

<span data-ttu-id="e9835-523">檔案的簽章是由檔案開頭的前幾個位元組所決定。</span><span class="sxs-lookup"><span data-stu-id="e9835-523">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="e9835-524">您可以使用這些位元組來指出延伸模組是否符合檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-524">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="e9835-525">範例應用程式會檢查檔案簽章中有幾個常見的檔案類型。</span><span class="sxs-lookup"><span data-stu-id="e9835-525">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="e9835-526">在下列範例中，會針對檔案檢查 JPEG 影像的檔案簽章：</span><span class="sxs-lookup"><span data-stu-id="e9835-526">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="e9835-527">若要取得其他檔案簽章，請參閱檔案簽章 [資料庫](https://www.filesignatures.net/) 與官方檔案規格。</span><span class="sxs-lookup"><span data-stu-id="e9835-527">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="e9835-528">檔案名安全性</span><span class="sxs-lookup"><span data-stu-id="e9835-528">File name security</span></span>

<span data-ttu-id="e9835-529">絕對不要使用用戶端提供的檔案名將檔案儲存至實體儲存體。</span><span class="sxs-lookup"><span data-stu-id="e9835-529">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="e9835-530">使用 [GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 建立檔案的安全檔案名，以建立完整路徑 (包括暫存儲存體的檔案名) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-530">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="e9835-531">:::no-loc(Razor)::: 自動為顯示的 HTML 編碼屬性值。</span><span class="sxs-lookup"><span data-stu-id="e9835-531">:::no-loc(Razor)::: automatically HTML encodes property values for display.</span></span> <span data-ttu-id="e9835-532">下列程式碼可安全地使用：</span><span class="sxs-lookup"><span data-stu-id="e9835-532">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="e9835-533">:::no-loc(Razor):::從開始，一律 <xref:System.Net.WebUtility.HtmlEncode*> 是使用者要求的檔案名內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-533">Outside of :::no-loc(Razor):::, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="e9835-534">許多執行必須包含檢查檔案是否存在;否則，檔案會覆寫相同名稱的檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-534">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="e9835-535">提供其他邏輯以符合您應用程式的規格。</span><span class="sxs-lookup"><span data-stu-id="e9835-535">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="e9835-536">大小驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-536">Size validation</span></span>

<span data-ttu-id="e9835-537">限制上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-537">Limit the size of uploaded files.</span></span>

<span data-ttu-id="e9835-538">在範例應用程式中，檔案大小限制為 2 MB (以位元組) 表示。</span><span class="sxs-lookup"><span data-stu-id="e9835-538">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="e9835-539">這項限制是透過 [檔案的設定](xref:fundamentals/configuration/index) 提供的 *:::no-loc(appsettings.json):::* ：</span><span class="sxs-lookup"><span data-stu-id="e9835-539">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="e9835-540">`FileSizeLimit`會插入至 `PageModel` 類別：</span><span class="sxs-lookup"><span data-stu-id="e9835-540">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="e9835-541">當檔案大小超過限制時，會拒絕檔案：</span><span class="sxs-lookup"><span data-stu-id="e9835-541">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="e9835-542">將名稱屬性值與 POST 方法的參數名稱相符</span><span class="sxs-lookup"><span data-stu-id="e9835-542">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="e9835-543">在 :::no-loc(Razor)::: 張貼表單資料或直接使用 JavaScript 的非表單中 `FormData` ，在表單的元素中指定的名稱或 `FormData` 必須符合控制器動作中參數的名稱。</span><span class="sxs-lookup"><span data-stu-id="e9835-543">In non-:::no-loc(Razor)::: forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="e9835-544">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="e9835-544">In the following example:</span></span>

* <span data-ttu-id="e9835-545">使用專案時 `<input>` ， `name` 屬性會設定為值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-545">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="e9835-546">`FormData`在 JavaScript 中使用時，名稱會設定為下列值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-546">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="e9835-547">針對 c # 方法 () 的參數使用相符的名稱 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-547">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="e9835-548">針對名為的 :::no-loc(Razor)::: 頁面頁面處理常式方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-548">For a :::no-loc(Razor)::: Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="e9835-549">針對 MVC POST 控制器動作方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-549">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="e9835-550">伺服器和應用程式設定</span><span class="sxs-lookup"><span data-stu-id="e9835-550">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="e9835-551">多部分主體長度限制</span><span class="sxs-lookup"><span data-stu-id="e9835-551">Multipart body length limit</span></span>

<span data-ttu-id="e9835-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 設定每個多部分主體長度的限制。</span><span class="sxs-lookup"><span data-stu-id="e9835-552"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="e9835-553">超過此限制的表單區段會 <xref:System.IO.InvalidDataException> 在剖析時擲回。</span><span class="sxs-lookup"><span data-stu-id="e9835-553">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="e9835-554">預設值為 134217728 (128 MB) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-554">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="e9835-555">使用中的設定來自訂限制 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-555">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e9835-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 用來設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 單一頁面或動作的。</span><span class="sxs-lookup"><span data-stu-id="e9835-556"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="e9835-557">在 :::no-loc(Razor)::: 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-557">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
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

<span data-ttu-id="e9835-558">在 :::no-loc(Razor)::: 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面模型或動作方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-558">In a :::no-loc(Razor)::: Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="e9835-559">Kestrel 要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="e9835-559">Kestrel maximum request body size</span></span>

<span data-ttu-id="e9835-560">針對 Kestrel 所裝載的應用程式，預設的要求主體大小上限為30000000個位元組，大約是 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="e9835-560">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e9835-561">使用 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 伺服器選項自訂限制：</span><span class="sxs-lookup"><span data-stu-id="e9835-561">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="e9835-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 用來設定單一頁面或動作的 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-562"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="e9835-563">在 :::no-loc(Razor)::: 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-563">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.Add:::no-loc(Razor):::Pages(options =>
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

<span data-ttu-id="e9835-564">在 :::no-loc(Razor)::: 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面處理常式類別或動作方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-564">In a :::no-loc(Razor)::: pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

<span data-ttu-id="e9835-565">`RequestSizeLimitAttribute`也可以使用指示詞套用 [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: ：</span><span class="sxs-lookup"><span data-stu-id="e9835-565">The `RequestSizeLimitAttribute` can also be applied using the [`@attribute`](xref:mvc/views/razor#attribute) :::no-loc(Razor)::: directive:</span></span>

```cshtml
@attribute [RequestSizeLimitAttribute(52428800)]
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="e9835-566">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="e9835-566">Other Kestrel limits</span></span>

<span data-ttu-id="e9835-567">其他 Kestrel 限制可能適用于 Kestrel 所裝載的應用程式：</span><span class="sxs-lookup"><span data-stu-id="e9835-567">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="e9835-568">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="e9835-568">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="e9835-569">要求和回應資料速率</span><span class="sxs-lookup"><span data-stu-id="e9835-569">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="e9835-570">IIS</span><span class="sxs-lookup"><span data-stu-id="e9835-570">IIS</span></span>

<span data-ttu-id="e9835-571">預設的要求限制 (`maxAllowedContentLength`) 為30000000個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="e9835-571">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e9835-572">自訂檔案中的限制 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-572">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="e9835-573">在下列範例中，此限制設定為 50 MB (52428800 bytes) ：</span><span class="sxs-lookup"><span data-stu-id="e9835-573">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="e9835-574">此 `maxAllowedContentLength` 設定僅適用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="e9835-574">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="e9835-575">如需詳細資訊，請參閱[要求限制 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="e9835-575">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="e9835-576">在中設定，以增加 HTTP 要求的要求主體大小 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> 上限 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-576">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e9835-577">在下列範例中，此限制設定為 50 MB (52428800 bytes) ：</span><span class="sxs-lookup"><span data-stu-id="e9835-577">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="e9835-578">如需詳細資訊，請參閱<xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="e9835-578">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e9835-579">疑難排解</span><span class="sxs-lookup"><span data-stu-id="e9835-579">Troubleshoot</span></span>

<span data-ttu-id="e9835-580">以下是使用上傳檔案和其可能解決方案時發現的一些常見問題。</span><span class="sxs-lookup"><span data-stu-id="e9835-580">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="e9835-581">部署至 IIS 伺服器時找不到錯誤</span><span class="sxs-lookup"><span data-stu-id="e9835-581">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="e9835-582">下列錯誤表示上傳的檔案超過伺服器設定的內容長度：</span><span class="sxs-lookup"><span data-stu-id="e9835-582">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="e9835-583">如需詳細資訊，請參閱 [IIS](#iis) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-583">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="e9835-584">連線失敗</span><span class="sxs-lookup"><span data-stu-id="e9835-584">Connection failure</span></span>

<span data-ttu-id="e9835-585">連接錯誤和重設伺服器連接可能表示上傳的檔案超過 Kestrel 的要求主體大小上限。</span><span class="sxs-lookup"><span data-stu-id="e9835-585">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="e9835-586">如需詳細資訊，請參閱 [Kestrel 的要求主體大小上限](#kestrel-maximum-request-body-size) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-586">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="e9835-587">Kestrel 用戶端連接限制也可能需要調整。</span><span class="sxs-lookup"><span data-stu-id="e9835-587">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="e9835-588">IFormFile 的 Null 參考例外狀況</span><span class="sxs-lookup"><span data-stu-id="e9835-588">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="e9835-589">如果控制器使用已上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> ，但值為 `null` ，請確認 HTML 表單指定 `enctype` 的值 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-589">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="e9835-590">如果未在專案上設定此屬性 `<form>` ，則不會進行檔案上傳，而且任何系結的 <xref:Microsoft.AspNetCore.Http.IFormFile> 引數都是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-590">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="e9835-591">也請確認 [表單資料中的上傳命名符合應用程式的命名](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="e9835-591">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="e9835-592">資料流程太長</span><span class="sxs-lookup"><span data-stu-id="e9835-592">Stream was too long</span></span>

<span data-ttu-id="e9835-593">本主題中的範例會依賴 <xref:System.IO.MemoryStream> 保存已上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-593">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="e9835-594">的大小限制 `MemoryStream` 為 `int.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-594">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="e9835-595">如果應用程式的檔案上傳案例需要保存大於 50 MB 的檔案內容，請使用另一種方法，而不依賴單一 `MemoryStream` 的方法來保存上傳的檔案內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-595">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9835-596">ASP.NET Core 支援針對較小的檔案使用緩衝的模型系結上傳一或多個檔案，並針對較大的檔案上傳未緩衝</span><span class="sxs-lookup"><span data-stu-id="e9835-596">ASP.NET Core supports uploading one or more files using buffered model binding for smaller files and unbuffered streaming for larger files.</span></span>

<span data-ttu-id="e9835-597">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e9835-597">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="security-considerations"></a><span data-ttu-id="e9835-598">安全性考量</span><span class="sxs-lookup"><span data-stu-id="e9835-598">Security considerations</span></span>

<span data-ttu-id="e9835-599">提供使用者將檔案上傳至伺服器的功能時，請特別小心。</span><span class="sxs-lookup"><span data-stu-id="e9835-599">Use caution when providing users with the ability to upload files to a server.</span></span> <span data-ttu-id="e9835-600">攻擊者可能會嘗試：</span><span class="sxs-lookup"><span data-stu-id="e9835-600">Attackers may attempt to:</span></span>

* <span data-ttu-id="e9835-601">執行 [拒絕服務的](/windows-hardware/drivers/ifs/denial-of-service) 攻擊。</span><span class="sxs-lookup"><span data-stu-id="e9835-601">Execute [denial of service](/windows-hardware/drivers/ifs/denial-of-service) attacks.</span></span>
* <span data-ttu-id="e9835-602">上傳病毒或惡意程式碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-602">Upload viruses or malware.</span></span>
* <span data-ttu-id="e9835-603">以其他方式危害網路和伺服器。</span><span class="sxs-lookup"><span data-stu-id="e9835-603">Compromise networks and servers in other ways.</span></span>

<span data-ttu-id="e9835-604">降低成功攻擊可能性的安全性步驟如下：</span><span class="sxs-lookup"><span data-stu-id="e9835-604">Security steps that reduce the likelihood of a successful attack are:</span></span>

* <span data-ttu-id="e9835-605">將檔案上傳到專用檔案上傳區域，最好是非系統磁片磁碟機。</span><span class="sxs-lookup"><span data-stu-id="e9835-605">Upload files to a dedicated file upload area, preferably to a non-system drive.</span></span> <span data-ttu-id="e9835-606">專用位置可讓您更輕鬆地對上傳的檔案強加安全性限制。</span><span class="sxs-lookup"><span data-stu-id="e9835-606">A dedicated location makes it easier to impose security restrictions on uploaded files.</span></span> <span data-ttu-id="e9835-607">停用檔案上傳位置的執行許可權。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-607">Disable execute permissions on the file upload location.&dagger;</span></span>
* <span data-ttu-id="e9835-608">請勿將上傳的 **檔案保存在** 與應用程式相同的目錄樹狀結構中。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-608">Do **not** persist uploaded files in the same directory tree as the app.&dagger;</span></span>
* <span data-ttu-id="e9835-609">使用應用程式所決定的安全檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-609">Use a safe file name determined by the app.</span></span> <span data-ttu-id="e9835-610">請勿使用使用者提供的檔案名或上傳檔案的不受信任檔案名。 &dagger; HTML 會在顯示不受信任的檔案名時進行編碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-610">Don't use a file name provided by the user or the untrusted file name of the uploaded file.&dagger; HTML encode the untrusted file name when displaying it.</span></span> <span data-ttu-id="e9835-611">例如，記錄檔案名或在 UI 中顯示 (:::no-loc(Razor)::: 會自動以 HTML 編碼輸出) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-611">For example, logging the file name or displaying in UI (:::no-loc(Razor)::: automatically HTML encodes output).</span></span>
* <span data-ttu-id="e9835-612">只允許應用程式設計規格的核准副檔名。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-612">Allow only approved file extensions for the app's design specification.&dagger;</span></span> <!-- * Check the file format signature to prevent a user from uploading a masqueraded file.&dagger; For example, don't permit a user to upload an *.exe* file with a *.txt* extension. Add this back when we get instructions how to do this.  -->
* <span data-ttu-id="e9835-613">確認已在伺服器上執行用戶端檢查。 &dagger; 用戶端檢查很容易規避。</span><span class="sxs-lookup"><span data-stu-id="e9835-613">Verify that client-side checks are performed on the server.&dagger; Client-side checks are easy to circumvent.</span></span>
* <span data-ttu-id="e9835-614">檢查上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-614">Check the size of an uploaded file.</span></span> <span data-ttu-id="e9835-615">設定大小上限以防止大上傳。&dagger;</span><span class="sxs-lookup"><span data-stu-id="e9835-615">Set a maximum size limit to prevent large uploads.&dagger;</span></span>
* <span data-ttu-id="e9835-616">使用相同名稱的已上傳檔案覆寫檔案時，請先檢查資料庫或實體儲存體的檔案名，再上傳檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-616">When files shouldn't be overwritten by an uploaded file with the same name, check the file name against the database or physical storage before uploading the file.</span></span>
* <span data-ttu-id="e9835-617">**在儲存檔案之前，在上傳的內容上執行病毒/惡意程式碼掃描器。**</span><span class="sxs-lookup"><span data-stu-id="e9835-617">**Run a virus/malware scanner on uploaded content before the file is stored.**</span></span>

<span data-ttu-id="e9835-618">&dagger;範例應用程式會示範符合準則的方法。</span><span class="sxs-lookup"><span data-stu-id="e9835-618">&dagger;The sample app demonstrates an approach that meets the criteria.</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-619">將惡意程式碼上傳至系統經常是執行程式碼的第一步，該程式碼可能：</span><span class="sxs-lookup"><span data-stu-id="e9835-619">Uploading malicious code to a system is frequently the first step to executing code that can:</span></span>
>
> * <span data-ttu-id="e9835-620">完全掌握系統的控制權。</span><span class="sxs-lookup"><span data-stu-id="e9835-620">Completely gain control of a system.</span></span>
> * <span data-ttu-id="e9835-621">使用系統損毀的結果來多載系統。</span><span class="sxs-lookup"><span data-stu-id="e9835-621">Overload a system with the result that the system crashes.</span></span>
> * <span data-ttu-id="e9835-622">洩漏使用者或系統資料。</span><span class="sxs-lookup"><span data-stu-id="e9835-622">Compromise user or system data.</span></span>
> * <span data-ttu-id="e9835-623">將刻套用至公用 UI。</span><span class="sxs-lookup"><span data-stu-id="e9835-623">Apply graffiti to a public UI.</span></span>
>
> <span data-ttu-id="e9835-624">如需在接受來自使用者的檔案時減少攻擊介面區的資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="e9835-624">For information on reducing the attack surface area when accepting files from users, see the following resources:</span></span>
>
> * [<span data-ttu-id="e9835-625">不受限制的檔案上傳</span><span class="sxs-lookup"><span data-stu-id="e9835-625">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
> * [<span data-ttu-id="e9835-626">Azure 安全性：請確保在接受來自使用者的檔案時，適當的控制項已就緒</span><span class="sxs-lookup"><span data-stu-id="e9835-626">Azure Security: Ensure appropriate controls are in place when accepting files from users</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

<span data-ttu-id="e9835-627">如需有關實施安全性措施的詳細資訊，包括範例應用程式中的範例，請參閱 [驗證](#validation) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-627">For more information on implementing security measures, including examples from the sample app, see the [Validation](#validation) section.</span></span>

## <a name="storage-scenarios"></a><span data-ttu-id="e9835-628">儲存體案例</span><span class="sxs-lookup"><span data-stu-id="e9835-628">Storage scenarios</span></span>

<span data-ttu-id="e9835-629">檔案的一般儲存選項包括：</span><span class="sxs-lookup"><span data-stu-id="e9835-629">Common storage options for files include:</span></span>

* <span data-ttu-id="e9835-630">資料庫</span><span class="sxs-lookup"><span data-stu-id="e9835-630">Database</span></span>

  * <span data-ttu-id="e9835-631">若為小型檔案上傳，資料庫通常會比實體儲存體 (檔案系統或網路共用) 選項更快。</span><span class="sxs-lookup"><span data-stu-id="e9835-631">For small file uploads, a database is often faster than physical storage (file system or network share) options.</span></span>
  * <span data-ttu-id="e9835-632">資料庫通常比實體儲存體選項更方便，因為抓取使用者資料的資料庫記錄可以同時提供檔案內容 (例如，) 的圖片。</span><span class="sxs-lookup"><span data-stu-id="e9835-632">A database is often more convenient than physical storage options because retrieval of a database record for user data can concurrently supply the file content (for example, an avatar image).</span></span>
  * <span data-ttu-id="e9835-633">資料庫的成本可能比使用資料儲存體服務還便宜。</span><span class="sxs-lookup"><span data-stu-id="e9835-633">A database is potentially less expensive than using a data storage service.</span></span>

* <span data-ttu-id="e9835-634">實體儲存體 (檔案系統或網路共用) </span><span class="sxs-lookup"><span data-stu-id="e9835-634">Physical storage (file system or network share)</span></span>

  * <span data-ttu-id="e9835-635">針對大型檔案上傳：</span><span class="sxs-lookup"><span data-stu-id="e9835-635">For large file uploads:</span></span>
    * <span data-ttu-id="e9835-636">資料庫限制可能會限制上傳的大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-636">Database limits may restrict the size of the upload.</span></span>
    * <span data-ttu-id="e9835-637">實體儲存體通常比資料庫中的儲存體便宜。</span><span class="sxs-lookup"><span data-stu-id="e9835-637">Physical storage is often less economical than storage in a database.</span></span>
  * <span data-ttu-id="e9835-638">實體儲存體的成本可能比使用資料儲存體服務更便宜。</span><span class="sxs-lookup"><span data-stu-id="e9835-638">Physical storage is potentially less expensive than using a data storage service.</span></span>
  * <span data-ttu-id="e9835-639">應用程式的進程必須具有儲存位置的讀取和寫入權限。</span><span class="sxs-lookup"><span data-stu-id="e9835-639">The app's process must have read and write permissions to the storage location.</span></span> <span data-ttu-id="e9835-640">**絕不授與 execute 許可權。**</span><span class="sxs-lookup"><span data-stu-id="e9835-640">**Never grant execute permission.**</span></span>

* <span data-ttu-id="e9835-641">資料儲存體服務 (例如 [Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)) </span><span class="sxs-lookup"><span data-stu-id="e9835-641">Data storage service (for example, [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/))</span></span>

  * <span data-ttu-id="e9835-642">服務通常會在內部部署解決方案中提供改良的擴充性和復原能力，這些解決方案通常會受限於單一失敗點。</span><span class="sxs-lookup"><span data-stu-id="e9835-642">Services usually offer improved scalability and resiliency over on-premises solutions that are usually subject to single points of failure.</span></span>
  * <span data-ttu-id="e9835-643">在大型儲存體基礎結構案例中，服務可能會降低成本。</span><span class="sxs-lookup"><span data-stu-id="e9835-643">Services are potentially lower cost in large storage infrastructure scenarios.</span></span>

  <span data-ttu-id="e9835-644">如需詳細資訊，請參閱 [快速入門：使用 .net 在物件儲存體中建立 blob](/azure/storage/blobs/storage-quickstart-blobs-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="e9835-644">For more information, see [Quickstart: Use .NET to create a blob in object storage](/azure/storage/blobs/storage-quickstart-blobs-dotnet).</span></span> <span data-ttu-id="e9835-645">本主題將示範 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*> ，但 <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> 在使用時，可以用來儲存 <xref:System.IO.FileStream> 至 blob 儲存體 <xref:System.IO.Stream> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-645">The topic demonstrates <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromFileAsync*>, but <xref:Microsoft.Azure.Storage.File.CloudFile.UploadFromStreamAsync*> can be used to save a <xref:System.IO.FileStream> to blob storage when working with a <xref:System.IO.Stream>.</span></span>

## <a name="file-upload-scenarios"></a><span data-ttu-id="e9835-646">檔案上傳案例</span><span class="sxs-lookup"><span data-stu-id="e9835-646">File upload scenarios</span></span>

<span data-ttu-id="e9835-647">上傳檔案的兩個一般方法是緩衝處理和串流處理。</span><span class="sxs-lookup"><span data-stu-id="e9835-647">Two general approaches for uploading files are buffering and streaming.</span></span>

<span data-ttu-id="e9835-648">**緩衝處理**</span><span class="sxs-lookup"><span data-stu-id="e9835-648">**Buffering**</span></span>

<span data-ttu-id="e9835-649">系統會將整個檔案讀入 <xref:Microsoft.AspNetCore.Http.IFormFile> ，這是用來處理或儲存檔案之檔案的 c # 標記法。</span><span class="sxs-lookup"><span data-stu-id="e9835-649">The entire file is read into an <xref:Microsoft.AspNetCore.Http.IFormFile>, which is a C# representation of the file used to process or save the file.</span></span>

<span data-ttu-id="e9835-650">檔案上傳所使用的資源 (磁片、記憶體) 取決於並行檔案上傳的數目和大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-650">The resources (disk, memory) used by file uploads depend on the number and size of concurrent file uploads.</span></span> <span data-ttu-id="e9835-651">如果應用程式嘗試緩衝過多上傳，則當網站的記憶體或磁碟空間不足時，就會損毀。</span><span class="sxs-lookup"><span data-stu-id="e9835-651">If an app attempts to buffer too many uploads, the site crashes when it runs out of memory or disk space.</span></span> <span data-ttu-id="e9835-652">如果檔案上傳的大小或頻率是耗盡應用程式資源，請使用串流處理。</span><span class="sxs-lookup"><span data-stu-id="e9835-652">If the size or frequency of file uploads is exhausting app resources, use streaming.</span></span>

> [!NOTE]
> <span data-ttu-id="e9835-653">任何超過 64 KB 的緩衝檔案都會從記憶體移至磁片上的暫存檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-653">Any single buffered file exceeding 64 KB is moved from memory to a temp file on disk.</span></span>

<span data-ttu-id="e9835-654">本主題的下列各節涵蓋了小型檔案的緩衝：</span><span class="sxs-lookup"><span data-stu-id="e9835-654">Buffering small files is covered in the following sections of this topic:</span></span>

* [<span data-ttu-id="e9835-655">實體儲存體</span><span class="sxs-lookup"><span data-stu-id="e9835-655">Physical storage</span></span>](#upload-small-files-with-buffered-model-binding-to-physical-storage)
* [<span data-ttu-id="e9835-656">資料庫</span><span class="sxs-lookup"><span data-stu-id="e9835-656">Database</span></span>](#upload-small-files-with-buffered-model-binding-to-a-database)

<span data-ttu-id="e9835-657">**串流**</span><span class="sxs-lookup"><span data-stu-id="e9835-657">**Streaming**</span></span>

<span data-ttu-id="e9835-658">從多部分要求接收檔案，並由應用程式直接處理或儲存。</span><span class="sxs-lookup"><span data-stu-id="e9835-658">The file is received from a multipart request and directly processed or saved by the app.</span></span> <span data-ttu-id="e9835-659">串流無法大幅改善效能。</span><span class="sxs-lookup"><span data-stu-id="e9835-659">Streaming doesn't improve performance significantly.</span></span> <span data-ttu-id="e9835-660">串流處理會在上傳檔案時減少記憶體或磁碟空間的需求。</span><span class="sxs-lookup"><span data-stu-id="e9835-660">Streaming reduces the demands for memory or disk space when uploading files.</span></span>

<span data-ttu-id="e9835-661">[上傳含有串流的大型](#upload-large-files-with-streaming)檔案一節中涵蓋了串流大型檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-661">Streaming large files is covered in the [Upload large files with streaming](#upload-large-files-with-streaming) section.</span></span>

### <a name="upload-small-files-with-buffered-model-binding-to-physical-storage"></a><span data-ttu-id="e9835-662">將具有緩衝模型系結的小型檔案上傳至實體儲存體</span><span class="sxs-lookup"><span data-stu-id="e9835-662">Upload small files with buffered model binding to physical storage</span></span>

<span data-ttu-id="e9835-663">若要上傳小型檔案，請使用多部分形式的表單，或使用 JavaScript 來建立 POST 要求。</span><span class="sxs-lookup"><span data-stu-id="e9835-663">To upload small files, use a multipart form or construct a POST request using JavaScript.</span></span>

<span data-ttu-id="e9835-664">下列範例將示範 :::no-loc(Razor)::: 如何使用頁面表單，在範例應用程式) 中上傳單一檔案 ( *Pages/BufferedSingleFileUploadPhysical. cshtml* ：</span><span class="sxs-lookup"><span data-stu-id="e9835-664">The following example demonstrates the use of a :::no-loc(Razor)::: Pages form to upload a single file ( *Pages/BufferedSingleFileUploadPhysical.cshtml* in the sample app):</span></span>

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

<span data-ttu-id="e9835-665">下列範例類似于先前的範例，不同之處在于：</span><span class="sxs-lookup"><span data-stu-id="e9835-665">The following example is analogous to the prior example except that:</span></span>

* <span data-ttu-id="e9835-666">JavaScript 的 ([FETCH API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) 用來提交表單的資料。</span><span class="sxs-lookup"><span data-stu-id="e9835-666">JavaScript's ([Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)) is used to submit the form's data.</span></span>
* <span data-ttu-id="e9835-667">沒有任何驗證。</span><span class="sxs-lookup"><span data-stu-id="e9835-667">There's no validation.</span></span>

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

<span data-ttu-id="e9835-668">若要針對 [不支援 FETCH API](https://caniuse.com/#feat=fetch)的用戶端，在 JavaScript 中執行表單貼文，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-668">To perform the form POST in JavaScript for clients that [don't support the Fetch API](https://caniuse.com/#feat=fetch), use one of the following approaches:</span></span>

* <span data-ttu-id="e9835-669">使用 Fetch Polyfill (例如， [Polyfill (github/fetch) ](https://github.com/github/fetch)) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-669">Use a Fetch Polyfill (for example, [window.fetch polyfill (github/fetch)](https://github.com/github/fetch)).</span></span>
* <span data-ttu-id="e9835-670">請使用 `XMLHttpRequest`。</span><span class="sxs-lookup"><span data-stu-id="e9835-670">Use `XMLHttpRequest`.</span></span> <span data-ttu-id="e9835-671">例如：</span><span class="sxs-lookup"><span data-stu-id="e9835-671">For example:</span></span>

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

<span data-ttu-id="e9835-672">為了支援檔案上傳，HTML 表單必須指定 (`enctype`) 的編碼類型 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-672">In order to support file uploads, HTML forms must specify an encoding type (`enctype`) of `multipart/form-data`.</span></span>

<span data-ttu-id="e9835-673">`files`若要讓輸入元素支援上傳多個檔案，請 `multiple` 在元素上提供屬性 `<input>` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-673">For a `files` input element to support uploading multiple files provide the `multiple` attribute on the `<input>` element:</span></span>

```cshtml
<input asp-for="FileUpload.FormFiles" type="file" multiple>
```

<span data-ttu-id="e9835-674">您可以使用，透過 [模型](xref:mvc/models/model-binding) 系結來存取上傳至伺服器的個別檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-674">The individual files uploaded to the server can be accessed through [Model Binding](xref:mvc/models/model-binding) using <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span> <span data-ttu-id="e9835-675">範例應用程式會示範針對資料庫和實體儲存案例的多個緩衝檔案上傳。</span><span class="sxs-lookup"><span data-stu-id="e9835-675">The sample app demonstrates multiple buffered file uploads for database and physical storage scenarios.</span></span>

<a name="filename2"></a>

> [!WARNING]
> <span data-ttu-id="e9835-676">請勿 **使用的** `FileName` 屬性做 <xref:Microsoft.AspNetCore.Http.IFormFile> 為顯示和記錄。</span><span class="sxs-lookup"><span data-stu-id="e9835-676">Do **not** use the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> other than for display and logging.</span></span> <span data-ttu-id="e9835-677">顯示或記錄時，HTML 會將檔案名編碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-677">When displaying or logging, HTML encode the file name.</span></span> <span data-ttu-id="e9835-678">攻擊者可以提供惡意檔案名，包括完整路徑或相對路徑。</span><span class="sxs-lookup"><span data-stu-id="e9835-678">An attacker can provide a malicious filename, including full paths or relative paths.</span></span> <span data-ttu-id="e9835-679">應用程式應該：</span><span class="sxs-lookup"><span data-stu-id="e9835-679">Applications should:</span></span>
>
> * <span data-ttu-id="e9835-680">從使用者提供的檔案名中移除路徑。</span><span class="sxs-lookup"><span data-stu-id="e9835-680">Remove the path from the user-supplied filename.</span></span>
> * <span data-ttu-id="e9835-681">針對 UI 或記錄，儲存 HTML 編碼、路徑移除的檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-681">Save the HTML-encoded, path-removed filename for UI or logging.</span></span>
> * <span data-ttu-id="e9835-682">為儲存體產生新的隨機檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-682">Generate a new random filename for storage.</span></span>
>
> <span data-ttu-id="e9835-683">下列程式碼會將路徑從檔案名中移除：</span><span class="sxs-lookup"><span data-stu-id="e9835-683">The following code removes the path from the file name:</span></span>
>
> ```csharp
> string untrustedFileName = Path.GetFileName(pathName);
> ```
>
> <span data-ttu-id="e9835-684">目前為止所提供的範例不考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="e9835-684">The examples provided thus far don't take into account security considerations.</span></span> <span data-ttu-id="e9835-685">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="e9835-685">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e9835-686">安全性考量</span><span class="sxs-lookup"><span data-stu-id="e9835-686">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e9835-687">驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-687">Validation</span></span>](#validation)

<span data-ttu-id="e9835-688">使用模型系結和來上傳檔案時 <xref:Microsoft.AspNetCore.Http.IFormFile> ，動作方法可以接受：</span><span class="sxs-lookup"><span data-stu-id="e9835-688">When uploading files using model binding and <xref:Microsoft.AspNetCore.Http.IFormFile>, the action method can accept:</span></span>

* <span data-ttu-id="e9835-689">單一 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-689">A single <xref:Microsoft.AspNetCore.Http.IFormFile>.</span></span>
* <span data-ttu-id="e9835-690">下列任何代表數個檔案的集合：</span><span class="sxs-lookup"><span data-stu-id="e9835-690">Any of the following collections that represent several files:</span></span>
  * <xref:Microsoft.AspNetCore.Http.IFormFileCollection>
  * <xref:System.Collections.IEnumerable>\<<xref:Microsoft.AspNetCore.Http.IFormFile>>
  * <span data-ttu-id="e9835-691">[清單](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span><span class="sxs-lookup"><span data-stu-id="e9835-691">[List](xref:System.Collections.Generic.List`1)\<<xref:Microsoft.AspNetCore.Http.IFormFile>></span></span>

> [!NOTE]
> <span data-ttu-id="e9835-692">系結符合依名稱的表單檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-692">Binding matches form files by name.</span></span> <span data-ttu-id="e9835-693">例如，中的 HTML `name` 值 `<input type="file" name="formFile">` 必須符合 () 系結的 c # 參數/屬性 `FormFile` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-693">For example, the HTML `name` value in `<input type="file" name="formFile">` must match the C# parameter/property bound (`FormFile`).</span></span> <span data-ttu-id="e9835-694">如需詳細資訊，請參閱 [比 [對名稱] 屬性值與 POST 方法的參數名稱](#match-name-attribute-value-to-parameter-name-of-post-method) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-694">For more information, see the [Match name attribute value to parameter name of POST method](#match-name-attribute-value-to-parameter-name-of-post-method) section.</span></span>

<span data-ttu-id="e9835-695">下列範例將：</span><span class="sxs-lookup"><span data-stu-id="e9835-695">The following example:</span></span>

* <span data-ttu-id="e9835-696">迴圈一或多個上傳的檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-696">Loops through one or more uploaded files.</span></span>
* <span data-ttu-id="e9835-697">使用 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 傳回檔案的完整路徑，包括檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-697">Uses [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to return a full path for a file, including the file name.</span></span> 
* <span data-ttu-id="e9835-698">使用應用程式所產生的檔案名，將檔案儲存到本機檔案系統。</span><span class="sxs-lookup"><span data-stu-id="e9835-698">Saves the files to the local file system using a file name generated by the app.</span></span>
* <span data-ttu-id="e9835-699">傳回上傳檔案的總數和大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-699">Returns the total number and size of files uploaded.</span></span>

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

<span data-ttu-id="e9835-700">用 `Path.GetRandomFileName` 來產生不含路徑的檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-700">Use `Path.GetRandomFileName` to generate a file name without a path.</span></span> <span data-ttu-id="e9835-701">在下列範例中，路徑是從 configuration 取得：</span><span class="sxs-lookup"><span data-stu-id="e9835-701">In the following example, the path is obtained from configuration:</span></span>

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

<span data-ttu-id="e9835-702">傳遞至的路徑 <xref:System.IO.FileStream> *必須* 包含檔案名。</span><span class="sxs-lookup"><span data-stu-id="e9835-702">The path passed to the <xref:System.IO.FileStream> *must* include the file name.</span></span> <span data-ttu-id="e9835-703">如果未提供檔案名， <xref:System.UnauthorizedAccessException> 則會在執行時間擲回。</span><span class="sxs-lookup"><span data-stu-id="e9835-703">If the file name isn't provided, an <xref:System.UnauthorizedAccessException> is thrown at runtime.</span></span>

<span data-ttu-id="e9835-704">使用此技術所上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> 會在處理之前，在記憶體或伺服器的磁片上進行緩衝處理。</span><span class="sxs-lookup"><span data-stu-id="e9835-704">Files uploaded using the <xref:Microsoft.AspNetCore.Http.IFormFile> technique are buffered in memory or on disk on the server before processing.</span></span> <span data-ttu-id="e9835-705">在動作方法內， <xref:Microsoft.AspNetCore.Http.IFormFile> 內容可作為進行存取 <xref:System.IO.Stream> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-705">Inside the action method, the <xref:Microsoft.AspNetCore.Http.IFormFile> contents are accessible as a <xref:System.IO.Stream>.</span></span> <span data-ttu-id="e9835-706">除了本機檔案系統之外，檔案也可以儲存到網路共用或檔案儲存體服務（例如 [Azure Blob 儲存體](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs)）。</span><span class="sxs-lookup"><span data-stu-id="e9835-706">In addition to the local file system, files can be saved to a network share or to a file storage service, such as [Azure Blob storage](/azure/visual-studio/vs-storage-aspnet5-getting-started-blobs).</span></span>

<span data-ttu-id="e9835-707">如需在多個要上傳的檔案上進行迴圈，並使用安全檔案名的另一個範例，請參閱範例應用程式中的 *Pages/BufferedMultipleFileUploadPhysical。*</span><span class="sxs-lookup"><span data-stu-id="e9835-707">For another example that loops over multiple files for upload and uses safe file names, see *Pages/BufferedMultipleFileUploadPhysical.cshtml.cs* in the sample app.</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-708">[GetTempFileName](xref:System.IO.Path.GetTempFileName*) 會擲回， <xref:System.IO.IOException> 如果在不刪除先前的暫存檔的情況下建立65535個以上的檔案，則擲回。</span><span class="sxs-lookup"><span data-stu-id="e9835-708">[Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) throws an <xref:System.IO.IOException> if more than 65,535 files are created without deleting previous temporary files.</span></span> <span data-ttu-id="e9835-709">65535檔案的限制為每個伺服器的限制。</span><span class="sxs-lookup"><span data-stu-id="e9835-709">The limit of 65,535 files is a per-server limit.</span></span> <span data-ttu-id="e9835-710">如需 Windows OS 上這項限制的詳細資訊，請參閱下列主題中的備註：</span><span class="sxs-lookup"><span data-stu-id="e9835-710">For more information on this limit on Windows OS, see the remarks in the following topics:</span></span>
>
> * [<span data-ttu-id="e9835-711">GetTempFileNameA 函式</span><span class="sxs-lookup"><span data-stu-id="e9835-711">GetTempFileNameA function</span></span>](/windows/desktop/api/fileapi/nf-fileapi-gettempfilenamea#remarks)
> * <xref:System.IO.Path.GetTempFileName*>

### <a name="upload-small-files-with-buffered-model-binding-to-a-database"></a><span data-ttu-id="e9835-712">將具有緩衝模型系結的小型檔案上傳至資料庫</span><span class="sxs-lookup"><span data-stu-id="e9835-712">Upload small files with buffered model binding to a database</span></span>

<span data-ttu-id="e9835-713">若要使用 [Entity Framework](/ef/core/index)將二進位檔案資料儲存在資料庫中，請 <xref:System.Byte> 在實體上定義陣列屬性：</span><span class="sxs-lookup"><span data-stu-id="e9835-713">To store binary file data in a database using [Entity Framework](/ef/core/index), define a <xref:System.Byte> array property on the entity:</span></span>

```csharp
public class AppFile
{
    public int Id { get; set; }
    public byte[] Content { get; set; }
}
```

<span data-ttu-id="e9835-714">針對包含下列各類別的類別，指定頁面模型屬性 <xref:Microsoft.AspNetCore.Http.IFormFile> ：</span><span class="sxs-lookup"><span data-stu-id="e9835-714">Specify a page model property for the class that includes an <xref:Microsoft.AspNetCore.Http.IFormFile>:</span></span>

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
> <span data-ttu-id="e9835-715"><xref:Microsoft.AspNetCore.Http.IFormFile> 可以直接當做動作方法參數使用，或作為系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="e9835-715"><xref:Microsoft.AspNetCore.Http.IFormFile> can be used directly as an action method parameter or as a bound model property.</span></span> <span data-ttu-id="e9835-716">先前的範例會使用系結模型屬性。</span><span class="sxs-lookup"><span data-stu-id="e9835-716">The prior example uses a bound model property.</span></span>

<span data-ttu-id="e9835-717">在 `FileUpload` :::no-loc(Razor)::: 頁面表單中使用：</span><span class="sxs-lookup"><span data-stu-id="e9835-717">The `FileUpload` is used in the :::no-loc(Razor)::: Pages form:</span></span>

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

<span data-ttu-id="e9835-718">當表單張貼至伺服器時，將複製 <xref:Microsoft.AspNetCore.Http.IFormFile> 到資料流程，並將它儲存為資料庫中的位元組陣列。</span><span class="sxs-lookup"><span data-stu-id="e9835-718">When the form is POSTed to the server, copy the <xref:Microsoft.AspNetCore.Http.IFormFile> to a stream and save it as a byte array in the database.</span></span> <span data-ttu-id="e9835-719">在下列範例中，會 `_dbContext` 儲存應用程式的資料庫內容：</span><span class="sxs-lookup"><span data-stu-id="e9835-719">In the following example, `_dbContext` stores the app's database context:</span></span>

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

<span data-ttu-id="e9835-720">上述範例類似于範例應用程式中所示範的案例：</span><span class="sxs-lookup"><span data-stu-id="e9835-720">The preceding example is similar to a scenario demonstrated in the sample app:</span></span>

* <span data-ttu-id="e9835-721">*Pages/BufferedSingleFileUploadDb. cshtml*</span><span class="sxs-lookup"><span data-stu-id="e9835-721">*Pages/BufferedSingleFileUploadDb.cshtml*</span></span>
* <span data-ttu-id="e9835-722">*Pages/BufferedSingleFileUploadDb，.cs*</span><span class="sxs-lookup"><span data-stu-id="e9835-722">*Pages/BufferedSingleFileUploadDb.cshtml.cs*</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-723">將二進位資料儲存至關聯式資料庫時請小心，因為它可能會對效能造成不良影響。</span><span class="sxs-lookup"><span data-stu-id="e9835-723">Use caution when storing binary data in relational databases, as it can adversely impact performance.</span></span>
>
> <span data-ttu-id="e9835-724">請勿依賴或信任 `FileName` <xref:Microsoft.AspNetCore.Http.IFormFile> 不含驗證的屬性。</span><span class="sxs-lookup"><span data-stu-id="e9835-724">Don't rely on or trust the `FileName` property of <xref:Microsoft.AspNetCore.Http.IFormFile> without validation.</span></span> <span data-ttu-id="e9835-725">`FileName`屬性只能用於顯示目的，而且只適用于 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e9835-725">The `FileName` property should only be used for display purposes and only after HTML encoding.</span></span>
>
> <span data-ttu-id="e9835-726">提供的範例不會考慮安全性考慮。</span><span class="sxs-lookup"><span data-stu-id="e9835-726">The examples provided don't take into account security considerations.</span></span> <span data-ttu-id="e9835-727">下列各節和 [範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/)會提供其他資訊：</span><span class="sxs-lookup"><span data-stu-id="e9835-727">Additional information is provided by the following sections and the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/file-uploads/samples/):</span></span>
>
> * [<span data-ttu-id="e9835-728">安全性考量</span><span class="sxs-lookup"><span data-stu-id="e9835-728">Security considerations</span></span>](#security-considerations)
> * [<span data-ttu-id="e9835-729">驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-729">Validation</span></span>](#validation)

### <a name="upload-large-files-with-streaming"></a><span data-ttu-id="e9835-730">使用串流上傳大型檔案</span><span class="sxs-lookup"><span data-stu-id="e9835-730">Upload large files with streaming</span></span>

<span data-ttu-id="e9835-731">下列範例示範如何使用 JavaScript 將檔案串流至控制器動作。</span><span class="sxs-lookup"><span data-stu-id="e9835-731">The following example demonstrates how to use JavaScript to stream a file to a controller action.</span></span> <span data-ttu-id="e9835-732">檔案的 antiforgery token 是使用自訂篩選屬性所產生，並且傳遞至用戶端 HTTP 標頭，而不是在要求主體中。</span><span class="sxs-lookup"><span data-stu-id="e9835-732">The file's antiforgery token is generated using a custom filter attribute and passed to the client HTTP headers instead of in the request body.</span></span> <span data-ttu-id="e9835-733">因為動作方法會直接處理已上傳的資料，所以其他自訂篩選會停用表單模型系結。</span><span class="sxs-lookup"><span data-stu-id="e9835-733">Because the action method processes the uploaded data directly, form model binding is disabled by another custom filter.</span></span> <span data-ttu-id="e9835-734">在動作內，會使用 `MultipartReader` 來讀取表單內容，以讀取每個個別 `MultipartSection`、處理檔案，或視需要儲存內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-734">Within the action, the form's contents are read using a `MultipartReader`, which reads each individual `MultipartSection`, processing the file or storing the contents as appropriate.</span></span> <span data-ttu-id="e9835-735">讀取多部分區段之後，動作會執行它自己的模型系結。</span><span class="sxs-lookup"><span data-stu-id="e9835-735">After the multipart sections are read, the action performs its own model binding.</span></span>

<span data-ttu-id="e9835-736">初始頁面回應會載入表單，並透過屬性) 將 antiforgery token 儲存在 :::no-loc(cookie)::: (中 `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-736">The initial page response loads the form and saves an antiforgery token in a :::no-loc(cookie)::: (via the `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` attribute).</span></span> <span data-ttu-id="e9835-737">屬性使用 ASP.NET Core 的內建 [antiforgery 支援](xref:security/anti-request-forgery) 來設定 :::no-loc(cookie)::: 具有要求權杖的。</span><span class="sxs-lookup"><span data-stu-id="e9835-737">The attribute uses ASP.NET Core's built-in [antiforgery support](xref:security/anti-request-forgery) to set a :::no-loc(cookie)::: with a request token:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/Antiforgery.cs?name=snippet_GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute)]

<span data-ttu-id="e9835-738">`DisableFormValueModelBindingAttribute`用來停用模型系結：</span><span class="sxs-lookup"><span data-stu-id="e9835-738">The `DisableFormValueModelBindingAttribute` is used to disable model binding:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Filters/ModelBinding.cs?name=snippet_DisableFormValueModelBindingAttribute)]

<span data-ttu-id="e9835-739">在範例應用程式中 `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` ， `DisableFormValueModelBindingAttribute` 會 `/StreamedSingleFileUploadDb` `/StreamedSingleFileUploadPhysical` `Startup.ConfigureServices` 使用[ :::no-loc(Razor)::: 頁面慣例](xref:razor-pages/razor-pages-conventions)，並在和的頁面應用程式模型中套用為篩選：</span><span class="sxs-lookup"><span data-stu-id="e9835-739">In the sample app, `GenerateAntiforgeryToken:::no-loc(Cookie):::Attribute` and `DisableFormValueModelBindingAttribute` are applied as filters to the page application models of `/StreamedSingleFileUploadDb` and `/StreamedSingleFileUploadPhysical` in `Startup.ConfigureServices` using [:::no-loc(Razor)::: Pages conventions](xref:razor-pages/razor-pages-conventions):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Startup.cs?name=snippet_AddMvc&highlight=8-11,17-20)]

<span data-ttu-id="e9835-740">由於模型系結不會讀取表單，因此從表單系結的參數不會系結 (查詢、路由和標頭會繼續運作) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-740">Since model binding doesn't read the form, parameters that are bound from the form don't bind (query, route, and header continue to work).</span></span> <span data-ttu-id="e9835-741">動作方法會直接與屬性搭配使用 `Request` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-741">The action method works directly with the `Request` property.</span></span> <span data-ttu-id="e9835-742">`MultipartReader` 是用來讀取每個區段。</span><span class="sxs-lookup"><span data-stu-id="e9835-742">A `MultipartReader` is used to read each section.</span></span> <span data-ttu-id="e9835-743">索引鍵/值資料儲存在中 `KeyValueAccumulator` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-743">Key/value data is stored in a `KeyValueAccumulator`.</span></span> <span data-ttu-id="e9835-744">讀取多部分區段之後，的內容 `KeyValueAccumulator` 會用來將表單資料系結至模型類型。</span><span class="sxs-lookup"><span data-stu-id="e9835-744">After the multipart sections are read, the contents of the `KeyValueAccumulator` are used to bind the form data to a model type.</span></span>

<span data-ttu-id="e9835-745">`StreamingController.UploadDatabase`使用 EF Core 串流至資料庫的完整方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-745">The complete `StreamingController.UploadDatabase` method for streaming to a database with EF Core:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadDatabase)]

<span data-ttu-id="e9835-746">`MultipartRequestHelper` ( *公用程式/MultipartRequestHelper .cs* ) ：</span><span class="sxs-lookup"><span data-stu-id="e9835-746">`MultipartRequestHelper` ( *Utilities/MultipartRequestHelper.cs* ):</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Utilities/MultipartRequestHelper.cs)]

<span data-ttu-id="e9835-747">`StreamingController.UploadPhysical`串流至實體位置的完整方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-747">The complete `StreamingController.UploadPhysical` method for streaming to a physical location:</span></span>

[!code-csharp[](file-uploads/samples/2.x/SampleApp/Controllers/StreamingController.cs?name=snippet_UploadPhysical)]

<span data-ttu-id="e9835-748">在範例應用程式中，驗證檢查是由處理 `FileHelpers.ProcessStreamedFile` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-748">In the sample app, validation checks are handled by `FileHelpers.ProcessStreamedFile`.</span></span>

## <a name="validation"></a><span data-ttu-id="e9835-749">驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-749">Validation</span></span>

<span data-ttu-id="e9835-750">範例應用程式的類別會示範經過緩衝處理和串流處理檔案上 `FileHelpers` 傳的數個檢查 <xref:Microsoft.AspNetCore.Http.IFormFile> 。</span><span class="sxs-lookup"><span data-stu-id="e9835-750">The sample app's `FileHelpers` class demonstrates a several checks for buffered <xref:Microsoft.AspNetCore.Http.IFormFile> and streamed file uploads.</span></span> <span data-ttu-id="e9835-751">若要 <xref:Microsoft.AspNetCore.Http.IFormFile> 在範例應用程式中處理緩衝的檔案上傳，請參閱 `ProcessFormFile` *公用程式/FileHelpers .cs* 檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="e9835-751">For processing <xref:Microsoft.AspNetCore.Http.IFormFile> buffered file uploads in the sample app, see the `ProcessFormFile` method in the *Utilities/FileHelpers.cs* file.</span></span> <span data-ttu-id="e9835-752">如需處理資料流程檔，請參閱 `ProcessStreamedFile` 相同檔案中的方法。</span><span class="sxs-lookup"><span data-stu-id="e9835-752">For processing streamed files, see the `ProcessStreamedFile` method in the same file.</span></span>

> [!WARNING]
> <span data-ttu-id="e9835-753">範例應用程式中所示範的驗證處理方法，不會掃描所上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-753">The validation processing methods demonstrated in the sample app don't scan the content of uploaded files.</span></span> <span data-ttu-id="e9835-754">在大部分的生產案例中，會在檔案上使用病毒/惡意程式碼掃描器 API，然後將檔案提供給使用者或其他系統。</span><span class="sxs-lookup"><span data-stu-id="e9835-754">In most production scenarios, a virus/malware scanner API is used on the file before making the file available to users or other systems.</span></span>
>
> <span data-ttu-id="e9835-755">雖然本主題範例提供驗證技術的實用範例，但 `FileHelpers` 除非您執行下列作業，否則請勿在生產應用程式中執行類別：</span><span class="sxs-lookup"><span data-stu-id="e9835-755">Although the topic sample provides a working example of validation techniques, don't implement the `FileHelpers` class in a production app unless you:</span></span>
>
> * <span data-ttu-id="e9835-756">充分瞭解執行。</span><span class="sxs-lookup"><span data-stu-id="e9835-756">Fully understand the implementation.</span></span>
> * <span data-ttu-id="e9835-757">針對應用程式的環境和規格，適當地修改執行。</span><span class="sxs-lookup"><span data-stu-id="e9835-757">Modify the implementation as appropriate for the app's environment and specifications.</span></span>
>
> <span data-ttu-id="e9835-758">**絕對不要在應用程式中執行安全程式碼，而不需滿足這些需求。**</span><span class="sxs-lookup"><span data-stu-id="e9835-758">**Never indiscriminately implement security code in an app without addressing these requirements.**</span></span>

### <a name="content-validation"></a><span data-ttu-id="e9835-759">內容驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-759">Content validation</span></span>

<span data-ttu-id="e9835-760">**在上傳的內容上使用協力廠商病毒/惡意程式碼掃描 API。**</span><span class="sxs-lookup"><span data-stu-id="e9835-760">**Use a third party virus/malware scanning API on uploaded content.**</span></span>

<span data-ttu-id="e9835-761">掃描檔案對大量案例中的伺服器資源有需求。</span><span class="sxs-lookup"><span data-stu-id="e9835-761">Scanning files is demanding on server resources in high volume scenarios.</span></span> <span data-ttu-id="e9835-762">如果要求處理效能因為檔案掃描而降低，請考慮將掃描工作卸載至 [背景服務](xref:fundamentals/host/hosted-services)，可能是在與應用程式伺服器不同的伺服器上執行的服務。</span><span class="sxs-lookup"><span data-stu-id="e9835-762">If request processing performance is diminished due to file scanning, consider offloading the scanning work to a [background service](xref:fundamentals/host/hosted-services), possibly a service running on a server different from the app's server.</span></span> <span data-ttu-id="e9835-763">上傳的檔案通常會保留在隔離區域中，直到背景病毒掃描程式檢查它們為止。</span><span class="sxs-lookup"><span data-stu-id="e9835-763">Typically, uploaded files are held in a quarantined area until the background virus scanner checks them.</span></span> <span data-ttu-id="e9835-764">當檔案通過時，檔案會移至一般檔案儲存位置。</span><span class="sxs-lookup"><span data-stu-id="e9835-764">When a file passes, the file is moved to the normal file storage location.</span></span> <span data-ttu-id="e9835-765">這些步驟通常會與指出檔案掃描狀態的資料庫記錄一起執行。</span><span class="sxs-lookup"><span data-stu-id="e9835-765">These steps are usually performed in conjunction with a database record that indicates the scanning status of a file.</span></span> <span data-ttu-id="e9835-766">藉由使用這種方法，應用程式和應用程式伺服器仍會專注于回應要求。</span><span class="sxs-lookup"><span data-stu-id="e9835-766">By using such an approach, the app and app server remain focused on responding to requests.</span></span>

### <a name="file-extension-validation"></a><span data-ttu-id="e9835-767">副檔名驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-767">File extension validation</span></span>

<span data-ttu-id="e9835-768">所上傳檔案的副檔名應針對允許的延伸模組清單進行檢查。</span><span class="sxs-lookup"><span data-stu-id="e9835-768">The uploaded file's extension should be checked against a list of permitted extensions.</span></span> <span data-ttu-id="e9835-769">例如：</span><span class="sxs-lookup"><span data-stu-id="e9835-769">For example:</span></span>

```csharp
private string[] permittedExtensions = { ".txt", ".pdf" };

var ext = Path.GetExtension(uploadedFileName).ToLowerInvariant();

if (string.IsNullOrEmpty(ext) || !permittedExtensions.Contains(ext))
{
    // The extension is invalid ... discontinue processing the file
}
```

### <a name="file-signature-validation"></a><span data-ttu-id="e9835-770">檔案簽章驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-770">File signature validation</span></span>

<span data-ttu-id="e9835-771">檔案的簽章是由檔案開頭的前幾個位元組所決定。</span><span class="sxs-lookup"><span data-stu-id="e9835-771">A file's signature is determined by the first few bytes at the start of a file.</span></span> <span data-ttu-id="e9835-772">您可以使用這些位元組來指出延伸模組是否符合檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-772">These bytes can be used to indicate if the extension matches the content of the file.</span></span> <span data-ttu-id="e9835-773">範例應用程式會檢查檔案簽章中有幾個常見的檔案類型。</span><span class="sxs-lookup"><span data-stu-id="e9835-773">The sample app checks file signatures for a few common file types.</span></span> <span data-ttu-id="e9835-774">在下列範例中，會針對檔案檢查 JPEG 影像的檔案簽章：</span><span class="sxs-lookup"><span data-stu-id="e9835-774">In the following example, the file signature for a JPEG image is checked against the file:</span></span>

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

<span data-ttu-id="e9835-775">若要取得其他檔案簽章，請參閱檔案簽章 [資料庫](https://www.filesignatures.net/) 與官方檔案規格。</span><span class="sxs-lookup"><span data-stu-id="e9835-775">To obtain additional file signatures, see the [File Signatures Database](https://www.filesignatures.net/) and official file specifications.</span></span>

### <a name="file-name-security"></a><span data-ttu-id="e9835-776">檔案名安全性</span><span class="sxs-lookup"><span data-stu-id="e9835-776">File name security</span></span>

<span data-ttu-id="e9835-777">絕對不要使用用戶端提供的檔案名將檔案儲存至實體儲存體。</span><span class="sxs-lookup"><span data-stu-id="e9835-777">Never use a client-supplied file name for saving a file to physical storage.</span></span> <span data-ttu-id="e9835-778">使用 [GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) 或 [GetTempFileName](xref:System.IO.Path.GetTempFileName*) 建立檔案的安全檔案名，以建立完整路徑 (包括暫存儲存體的檔案名) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-778">Create a safe file name for the file using [Path.GetRandomFileName](xref:System.IO.Path.GetRandomFileName*) or [Path.GetTempFileName](xref:System.IO.Path.GetTempFileName*) to create a full path (including the file name) for temporary storage.</span></span>

<span data-ttu-id="e9835-779">:::no-loc(Razor)::: 自動為顯示的 HTML 編碼屬性值。</span><span class="sxs-lookup"><span data-stu-id="e9835-779">:::no-loc(Razor)::: automatically HTML encodes property values for display.</span></span> <span data-ttu-id="e9835-780">下列程式碼可安全地使用：</span><span class="sxs-lookup"><span data-stu-id="e9835-780">The following code is safe to use:</span></span>

```cshtml
@foreach (var file in Model.DatabaseFiles) {
    <tr>
        <td>
            @file.UntrustedName
        </td>
    </tr>
}
```

<span data-ttu-id="e9835-781">:::no-loc(Razor):::從開始，一律 <xref:System.Net.WebUtility.HtmlEncode*> 是使用者要求的檔案名內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-781">Outside of :::no-loc(Razor):::, always <xref:System.Net.WebUtility.HtmlEncode*> file name content from a user's request.</span></span>

<span data-ttu-id="e9835-782">許多執行必須包含檢查檔案是否存在;否則，檔案會覆寫相同名稱的檔案。</span><span class="sxs-lookup"><span data-stu-id="e9835-782">Many implementations must include a check that the file exists; otherwise, the file is overwritten by a file of the same name.</span></span> <span data-ttu-id="e9835-783">提供其他邏輯以符合您應用程式的規格。</span><span class="sxs-lookup"><span data-stu-id="e9835-783">Supply additional logic to meet your app's specifications.</span></span>

### <a name="size-validation"></a><span data-ttu-id="e9835-784">大小驗證</span><span class="sxs-lookup"><span data-stu-id="e9835-784">Size validation</span></span>

<span data-ttu-id="e9835-785">限制上傳檔案的大小。</span><span class="sxs-lookup"><span data-stu-id="e9835-785">Limit the size of uploaded files.</span></span>

<span data-ttu-id="e9835-786">在範例應用程式中，檔案大小限制為 2 MB (以位元組) 表示。</span><span class="sxs-lookup"><span data-stu-id="e9835-786">In the sample app, the size of the file is limited to 2 MB (indicated in bytes).</span></span> <span data-ttu-id="e9835-787">這項限制是透過 [檔案的設定](xref:fundamentals/configuration/index) 提供的 *:::no-loc(appsettings.json):::* ：</span><span class="sxs-lookup"><span data-stu-id="e9835-787">The limit is supplied via [Configuration](xref:fundamentals/configuration/index) from the *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
  "FileSizeLimit": 2097152
}
```

<span data-ttu-id="e9835-788">`FileSizeLimit`會插入至 `PageModel` 類別：</span><span class="sxs-lookup"><span data-stu-id="e9835-788">The `FileSizeLimit` is injected into `PageModel` classes:</span></span>

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

<span data-ttu-id="e9835-789">當檔案大小超過限制時，會拒絕檔案：</span><span class="sxs-lookup"><span data-stu-id="e9835-789">When a file size exceeds the limit, the file is rejected:</span></span>

```csharp
if (formFile.Length > _fileSizeLimit)
{
    // The file is too large ... discontinue processing the file
}
```

### <a name="match-name-attribute-value-to-parameter-name-of-post-method"></a><span data-ttu-id="e9835-790">將名稱屬性值與 POST 方法的參數名稱相符</span><span class="sxs-lookup"><span data-stu-id="e9835-790">Match name attribute value to parameter name of POST method</span></span>

<span data-ttu-id="e9835-791">在 :::no-loc(Razor)::: 張貼表單資料或直接使用 JavaScript 的非表單中 `FormData` ，在表單的元素中指定的名稱或 `FormData` 必須符合控制器動作中參數的名稱。</span><span class="sxs-lookup"><span data-stu-id="e9835-791">In non-:::no-loc(Razor)::: forms that POST form data or use JavaScript's `FormData` directly, the name specified in the form's element or `FormData` must match the name of the parameter in the controller's action.</span></span>

<span data-ttu-id="e9835-792">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="e9835-792">In the following example:</span></span>

* <span data-ttu-id="e9835-793">使用專案時 `<input>` ， `name` 屬性會設定為值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-793">When using an `<input>` element, the `name` attribute is set to the value `battlePlans`:</span></span>

  ```html
  <input type="file" name="battlePlans" multiple>
  ```

* <span data-ttu-id="e9835-794">`FormData`在 JavaScript 中使用時，名稱會設定為下列值 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-794">When using `FormData` in JavaScript, the name is set to the value `battlePlans`:</span></span>

  ```javascript
  var formData = new FormData();

  for (var file in files) {
    formData.append("battlePlans", file, file.name);
  }
  ```

<span data-ttu-id="e9835-795">針對 c # 方法 () 的參數使用相符的名稱 `battlePlans` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-795">Use a matching name for the parameter of the C# method (`battlePlans`):</span></span>

* <span data-ttu-id="e9835-796">針對名為的 :::no-loc(Razor)::: 頁面頁面處理常式方法 `Upload` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-796">For a :::no-loc(Razor)::: Pages page handler method named `Upload`:</span></span>

  ```csharp
  public async Task<IActionResult> OnPostUploadAsync(List<IFormFile> battlePlans)
  ```

* <span data-ttu-id="e9835-797">針對 MVC POST 控制器動作方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-797">For an MVC POST controller action method:</span></span>

  ```csharp
  public async Task<IActionResult> Post(List<IFormFile> battlePlans)
  ```

## <a name="server-and-app-configuration"></a><span data-ttu-id="e9835-798">伺服器和應用程式設定</span><span class="sxs-lookup"><span data-stu-id="e9835-798">Server and app configuration</span></span>

### <a name="multipart-body-length-limit"></a><span data-ttu-id="e9835-799">多部分主體長度限制</span><span class="sxs-lookup"><span data-stu-id="e9835-799">Multipart body length limit</span></span>

<span data-ttu-id="e9835-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 設定每個多部分主體長度的限制。</span><span class="sxs-lookup"><span data-stu-id="e9835-800"><xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> sets the limit for the length of each multipart body.</span></span> <span data-ttu-id="e9835-801">超過此限制的表單區段會 <xref:System.IO.InvalidDataException> 在剖析時擲回。</span><span class="sxs-lookup"><span data-stu-id="e9835-801">Form sections that exceed this limit throw an <xref:System.IO.InvalidDataException> when parsed.</span></span> <span data-ttu-id="e9835-802">預設值為 134217728 (128 MB) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-802">The default is 134,217,728 (128 MB).</span></span> <span data-ttu-id="e9835-803">使用中的設定來自訂限制 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-803">Customize the limit using the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> setting in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="e9835-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> 用來設定 <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> 單一頁面或動作的。</span><span class="sxs-lookup"><span data-stu-id="e9835-804"><xref:Microsoft.AspNetCore.Mvc.RequestFormLimitsAttribute> is used to set the <xref:Microsoft.AspNetCore.Http.Features.FormOptions.MultipartBodyLengthLimit> for a single page or action.</span></span>

<span data-ttu-id="e9835-805">在 :::no-loc(Razor)::: 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-805">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .Add:::no-loc(Razor):::PagesOptions(options =>
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

<span data-ttu-id="e9835-806">在 :::no-loc(Razor)::: 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面模型或動作方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-806">In a :::no-loc(Razor)::: Pages app or an MVC app, apply the filter to the page model or action method:</span></span>

```csharp
// Set the limit to 256 MB
[RequestFormLimits(MultipartBodyLengthLimit = 268435456)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="kestrel-maximum-request-body-size"></a><span data-ttu-id="e9835-807">Kestrel 要求主體大小上限</span><span class="sxs-lookup"><span data-stu-id="e9835-807">Kestrel maximum request body size</span></span>

<span data-ttu-id="e9835-808">針對 Kestrel 所裝載的應用程式，預設的要求主體大小上限為30000000個位元組，大約是 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="e9835-808">For apps hosted by Kestrel, the default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e9835-809">使用 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel 伺服器選項自訂限制：</span><span class="sxs-lookup"><span data-stu-id="e9835-809">Customize the limit using the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) Kestrel server option:</span></span>

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

<span data-ttu-id="e9835-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 用來設定單一頁面或動作的 [>limits.maxrequestbodysize](xref:fundamentals/servers/kestrel#maximum-request-body-size) 。</span><span class="sxs-lookup"><span data-stu-id="e9835-810"><xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> is used to set the [MaxRequestBodySize](xref:fundamentals/servers/kestrel#maximum-request-body-size) for a single page or action.</span></span>

<span data-ttu-id="e9835-811">在 :::no-loc(Razor)::: 頁面應用程式中，將篩選套用至[convention](xref:razor-pages/razor-pages-conventions)下列慣例 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e9835-811">In a :::no-loc(Razor)::: Pages app, apply the filter with a [convention](xref:razor-pages/razor-pages-conventions) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .Add:::no-loc(Razor):::PagesOptions(options =>
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

<span data-ttu-id="e9835-812">在 :::no-loc(Razor)::: 頁面應用程式或 MVC 應用程式中，將篩選套用至頁面處理常式類別或動作方法：</span><span class="sxs-lookup"><span data-stu-id="e9835-812">In a :::no-loc(Razor)::: pages app or an MVC app, apply the filter to the page handler class or action method:</span></span>

```csharp
// Handle requests up to 50 MB
[RequestSizeLimit(52428800)]
public class BufferedSingleFileUploadPhysicalModel : PageModel
{
    ...
}
```

### <a name="other-kestrel-limits"></a><span data-ttu-id="e9835-813">其他 Kestrel 限制</span><span class="sxs-lookup"><span data-stu-id="e9835-813">Other Kestrel limits</span></span>

<span data-ttu-id="e9835-814">其他 Kestrel 限制可能適用于 Kestrel 所裝載的應用程式：</span><span class="sxs-lookup"><span data-stu-id="e9835-814">Other Kestrel limits may apply for apps hosted by Kestrel:</span></span>

* [<span data-ttu-id="e9835-815">用戶端連線數目上限</span><span class="sxs-lookup"><span data-stu-id="e9835-815">Maximum client connections</span></span>](xref:fundamentals/servers/kestrel#maximum-client-connections)
* [<span data-ttu-id="e9835-816">要求和回應資料速率</span><span class="sxs-lookup"><span data-stu-id="e9835-816">Request and response data rates</span></span>](xref:fundamentals/servers/kestrel#minimum-request-body-data-rate)

### <a name="iis"></a><span data-ttu-id="e9835-817">IIS</span><span class="sxs-lookup"><span data-stu-id="e9835-817">IIS</span></span>

<span data-ttu-id="e9835-818">預設的要求限制 (`maxAllowedContentLength`) 為30000000個位元組，大約 28.6 MB。</span><span class="sxs-lookup"><span data-stu-id="e9835-818">The default request limit (`maxAllowedContentLength`) is 30,000,000 bytes, which is approximately 28.6 MB.</span></span> <span data-ttu-id="e9835-819">自訂檔案中的限制 `web.config` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-819">Customize the limit in the `web.config` file.</span></span> <span data-ttu-id="e9835-820">在下列範例中，此限制設定為 50 MB (52428800 bytes) ：</span><span class="sxs-lookup"><span data-stu-id="e9835-820">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```xml
<system.webServer>
  <security>
    <requestFiltering>
      <requestLimits maxAllowedContentLength="52428800" />
    </requestFiltering>
  </security>
</system.webServer>
```

<span data-ttu-id="e9835-821">此 `maxAllowedContentLength` 設定僅適用于 IIS。</span><span class="sxs-lookup"><span data-stu-id="e9835-821">The `maxAllowedContentLength` setting only applies to IIS.</span></span> <span data-ttu-id="e9835-822">如需詳細資訊，請參閱[要求限制 `<requestLimits>` ](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/)。</span><span class="sxs-lookup"><span data-stu-id="e9835-822">For more information, see [Request Limits `<requestLimits>`](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/).</span></span>

<span data-ttu-id="e9835-823">在中設定，以增加 HTTP 要求的要求主體大小 <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> 上限 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-823">Increase the maximum request body size for the HTTP request by setting <xref:Microsoft.AspNetCore.Builder.IISServerOptions.MaxRequestBodySize%2A?displayProperty=nameWithType> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="e9835-824">在下列範例中，此限制設定為 50 MB (52428800 bytes) ：</span><span class="sxs-lookup"><span data-stu-id="e9835-824">In the following example, the limit is set to 50 MB (52,428,800 bytes):</span></span>

```csharp
services.Configure<IISServerOptions>(options =>
{
    options.MaxRequestBodySize = 52428800;
});
```

<span data-ttu-id="e9835-825">如需詳細資訊，請參閱<xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="e9835-825">For more information, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e9835-826">疑難排解</span><span class="sxs-lookup"><span data-stu-id="e9835-826">Troubleshoot</span></span>

<span data-ttu-id="e9835-827">以下是使用上傳檔案和其可能解決方案時發現的一些常見問題。</span><span class="sxs-lookup"><span data-stu-id="e9835-827">Below are some common problems encountered when working with uploading files and their possible solutions.</span></span>

### <a name="not-found-error-when-deployed-to-an-iis-server"></a><span data-ttu-id="e9835-828">部署至 IIS 伺服器時找不到錯誤</span><span class="sxs-lookup"><span data-stu-id="e9835-828">Not Found error when deployed to an IIS server</span></span>

<span data-ttu-id="e9835-829">下列錯誤表示上傳的檔案超過伺服器設定的內容長度：</span><span class="sxs-lookup"><span data-stu-id="e9835-829">The following error indicates that the uploaded file exceeds the server's configured content length:</span></span>

```
HTTP 404.13 - Not Found
The request filtering module is configured to deny a request that exceeds the request content length.
```

<span data-ttu-id="e9835-830">如需詳細資訊，請參閱 [IIS](#iis) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-830">For more information, see the [IIS](#iis) section.</span></span>

### <a name="connection-failure"></a><span data-ttu-id="e9835-831">連線失敗</span><span class="sxs-lookup"><span data-stu-id="e9835-831">Connection failure</span></span>

<span data-ttu-id="e9835-832">連接錯誤和重設伺服器連接可能表示上傳的檔案超過 Kestrel 的要求主體大小上限。</span><span class="sxs-lookup"><span data-stu-id="e9835-832">A connection error and a reset server connection probably indicates that the uploaded file exceeds Kestrel's maximum request body size.</span></span> <span data-ttu-id="e9835-833">如需詳細資訊，請參閱 [Kestrel 的要求主體大小上限](#kestrel-maximum-request-body-size) 一節。</span><span class="sxs-lookup"><span data-stu-id="e9835-833">For more information, see the [Kestrel maximum request body size](#kestrel-maximum-request-body-size) section.</span></span> <span data-ttu-id="e9835-834">Kestrel 用戶端連接限制也可能需要調整。</span><span class="sxs-lookup"><span data-stu-id="e9835-834">Kestrel client connection limits may also require adjustment.</span></span>

### <a name="null-reference-exception-with-iformfile"></a><span data-ttu-id="e9835-835">IFormFile 的 Null 參考例外狀況</span><span class="sxs-lookup"><span data-stu-id="e9835-835">Null Reference Exception with IFormFile</span></span>

<span data-ttu-id="e9835-836">如果控制器使用已上傳的檔案 <xref:Microsoft.AspNetCore.Http.IFormFile> ，但值為 `null` ，請確認 HTML 表單指定 `enctype` 的值 `multipart/form-data` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-836">If the controller is accepting uploaded files using <xref:Microsoft.AspNetCore.Http.IFormFile> but the value is `null`, confirm that the HTML form is specifying an `enctype` value of `multipart/form-data`.</span></span> <span data-ttu-id="e9835-837">如果未在專案上設定此屬性 `<form>` ，則不會進行檔案上傳，而且任何系結的 <xref:Microsoft.AspNetCore.Http.IFormFile> 引數都是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-837">If this attribute isn't set on the `<form>` element, the file upload doesn't occur and any bound <xref:Microsoft.AspNetCore.Http.IFormFile> arguments are `null`.</span></span> <span data-ttu-id="e9835-838">也請確認 [表單資料中的上傳命名符合應用程式的命名](#match-name-attribute-value-to-parameter-name-of-post-method)。</span><span class="sxs-lookup"><span data-stu-id="e9835-838">Also confirm that the [upload naming in form data matches the app's naming](#match-name-attribute-value-to-parameter-name-of-post-method).</span></span>

### <a name="stream-was-too-long"></a><span data-ttu-id="e9835-839">資料流程太長</span><span class="sxs-lookup"><span data-stu-id="e9835-839">Stream was too long</span></span>

<span data-ttu-id="e9835-840">本主題中的範例會依賴 <xref:System.IO.MemoryStream> 保存已上傳檔案的內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-840">The examples in this topic rely upon <xref:System.IO.MemoryStream> to hold the uploaded file's content.</span></span> <span data-ttu-id="e9835-841">的大小限制 `MemoryStream` 為 `int.MaxValue` 。</span><span class="sxs-lookup"><span data-stu-id="e9835-841">The size limit of a `MemoryStream` is `int.MaxValue`.</span></span> <span data-ttu-id="e9835-842">如果應用程式的檔案上傳案例需要保存大於 50 MB 的檔案內容，請使用另一種方法，而不依賴單一 `MemoryStream` 的方法來保存上傳的檔案內容。</span><span class="sxs-lookup"><span data-stu-id="e9835-842">If the app's file upload scenario requires holding file content larger than 50 MB, use an alternative approach that doesn't rely upon a single `MemoryStream` for holding an uploaded file's content.</span></span>

::: moniker-end


## <a name="additional-resources"></a><span data-ttu-id="e9835-843">其他資源</span><span class="sxs-lookup"><span data-stu-id="e9835-843">Additional resources</span></span>

* [<span data-ttu-id="e9835-844">HTTP 連接要求清空</span><span class="sxs-lookup"><span data-stu-id="e9835-844">HTTP connection request draining</span></span>](xref:fundamentals/servers/kestrel#http11-request-draining)
* [<span data-ttu-id="e9835-845">不受限制的檔案上傳</span><span class="sxs-lookup"><span data-stu-id="e9835-845">Unrestricted File Upload</span></span>](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)
* [<span data-ttu-id="e9835-846">Azure 安全性：安全性框架：輸入驗證 |措施</span><span class="sxs-lookup"><span data-stu-id="e9835-846">Azure Security: Security Frame: Input Validation | Mitigations</span></span>](/azure/security/azure-security-threat-modeling-tool-input-validation)
* [<span data-ttu-id="e9835-847">Azure 雲端設計模式： Valet 金鑰模式</span><span class="sxs-lookup"><span data-stu-id="e9835-847">Azure Cloud Design Patterns: Valet Key pattern</span></span>](/azure/architecture/patterns/valet-key)
