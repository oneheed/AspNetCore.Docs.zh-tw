---
title: 使用 OpenAPI 開發 ASP.NET Core 應用程式
author: ryanbrandenburg
description: 示範如何使用 ' dotnet-openapi ' 工具，將參考新增至 OpenAPI 檔案。
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
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
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 5d9f1684aa333c38c73673138a703b04d318c6df
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972024"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="555f7-103">使用 OpenAPI 工具開發 ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="555f7-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="555f7-104">依 Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="555f7-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="555f7-105">[Dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) 是一種 [.Net Core 通用工具](/dotnet/core/tools/global-tools) ，可用於管理專案內的 [openapi](https://github.com/OAI/OpenAPI-Specification) 參考。</span><span class="sxs-lookup"><span data-stu-id="555f7-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="555f7-106">安裝</span><span class="sxs-lookup"><span data-stu-id="555f7-106">Installation</span></span>

<span data-ttu-id="555f7-107">若要安裝 `Microsoft.dotnet-openapi` ，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="555f7-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="555f7-108">加</span><span class="sxs-lookup"><span data-stu-id="555f7-108">Add</span></span>

<span data-ttu-id="555f7-109">使用此頁面上的任何命令新增 OpenAPI 參考，會將 `<OpenApiReference />` 類似下列的元素加入 *.csproj* 檔案：</span><span class="sxs-lookup"><span data-stu-id="555f7-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="555f7-110">需要上述參考，應用程式才能呼叫所產生的用戶端程式代碼。</span><span class="sxs-lookup"><span data-stu-id="555f7-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/dotnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="555f7-111">新增檔案</span><span class="sxs-lookup"><span data-stu-id="555f7-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="555f7-112">選項</span><span class="sxs-lookup"><span data-stu-id="555f7-112">Options</span></span>

| <span data-ttu-id="555f7-113">Short 選項</span><span class="sxs-lookup"><span data-stu-id="555f7-113">Short option</span></span>| <span data-ttu-id="555f7-114">Long 選項</span><span class="sxs-lookup"><span data-stu-id="555f7-114">Long option</span></span>| <span data-ttu-id="555f7-115">描述</span><span class="sxs-lookup"><span data-stu-id="555f7-115">Description</span></span> | <span data-ttu-id="555f7-116">範例</span><span class="sxs-lookup"><span data-stu-id="555f7-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="555f7-117">-p</span><span class="sxs-lookup"><span data-stu-id="555f7-117">-p</span></span>|<span data-ttu-id="555f7-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="555f7-118">--updateProject</span></span> | <span data-ttu-id="555f7-119">要操作的專案。</span><span class="sxs-lookup"><span data-stu-id="555f7-119">The project to operate on.</span></span> |<span data-ttu-id="555f7-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="555f7-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="555f7-121">-c</span><span class="sxs-lookup"><span data-stu-id="555f7-121">-c</span></span>|<span data-ttu-id="555f7-122">--程式碼產生器</span><span class="sxs-lookup"><span data-stu-id="555f7-122">--code-generator</span></span>| <span data-ttu-id="555f7-123">要套用至參考的程式碼產生器。</span><span class="sxs-lookup"><span data-stu-id="555f7-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="555f7-124">選項為 `NSwagCSharp` 和 `NSwagTypeScript` 。</span><span class="sxs-lookup"><span data-stu-id="555f7-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="555f7-125">如果 `--code-generator` 未指定，則工具會預設為 `NSwagCSharp` 。</span><span class="sxs-lookup"><span data-stu-id="555f7-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="555f7-126">dotnet openapi 將檔案新增 .\OpenApi.js于程式碼產生器</span><span class="sxs-lookup"><span data-stu-id="555f7-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="555f7-127">-H</span><span class="sxs-lookup"><span data-stu-id="555f7-127">-h</span></span>|<span data-ttu-id="555f7-128">--help</span><span class="sxs-lookup"><span data-stu-id="555f7-128">--help</span></span>|<span data-ttu-id="555f7-129">顯示說明資訊</span><span class="sxs-lookup"><span data-stu-id="555f7-129">Show help information</span></span>|<span data-ttu-id="555f7-130">dotnet openapi add file--help</span><span class="sxs-lookup"><span data-stu-id="555f7-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="555f7-131">引數</span><span class="sxs-lookup"><span data-stu-id="555f7-131">Arguments</span></span>

|  <span data-ttu-id="555f7-132">引數</span><span class="sxs-lookup"><span data-stu-id="555f7-132">Argument</span></span>  | <span data-ttu-id="555f7-133">描述</span><span class="sxs-lookup"><span data-stu-id="555f7-133">Description</span></span> | <span data-ttu-id="555f7-134">範例</span><span class="sxs-lookup"><span data-stu-id="555f7-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="555f7-135">來源檔案</span><span class="sxs-lookup"><span data-stu-id="555f7-135">source-file</span></span> | <span data-ttu-id="555f7-136">要從中建立參考的來源。</span><span class="sxs-lookup"><span data-stu-id="555f7-136">The source to create a reference from.</span></span> <span data-ttu-id="555f7-137">必須是 OpenAPI 檔。</span><span class="sxs-lookup"><span data-stu-id="555f7-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="555f7-138">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="555f7-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="555f7-139">新增 URL</span><span class="sxs-lookup"><span data-stu-id="555f7-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="555f7-140">選項</span><span class="sxs-lookup"><span data-stu-id="555f7-140">Options</span></span>

| <span data-ttu-id="555f7-141">Short 選項</span><span class="sxs-lookup"><span data-stu-id="555f7-141">Short option</span></span>| <span data-ttu-id="555f7-142">Long 選項</span><span class="sxs-lookup"><span data-stu-id="555f7-142">Long option</span></span>| <span data-ttu-id="555f7-143">描述</span><span class="sxs-lookup"><span data-stu-id="555f7-143">Description</span></span> | <span data-ttu-id="555f7-144">範例</span><span class="sxs-lookup"><span data-stu-id="555f7-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="555f7-145">-p</span><span class="sxs-lookup"><span data-stu-id="555f7-145">-p</span></span>|<span data-ttu-id="555f7-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="555f7-146">--updateProject</span></span> | <span data-ttu-id="555f7-147">要操作的專案。</span><span class="sxs-lookup"><span data-stu-id="555f7-147">The project to operate on.</span></span> |<span data-ttu-id="555f7-148">dotnet openapi 新增 url *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="555f7-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="555f7-149">-o</span><span class="sxs-lookup"><span data-stu-id="555f7-149">-o</span></span>|<span data-ttu-id="555f7-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="555f7-150">--output-file</span></span> | <span data-ttu-id="555f7-151">要在何處放置 OpenAPI 檔案的本機複本。</span><span class="sxs-lookup"><span data-stu-id="555f7-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="555f7-152">dotnet openapi 新增 url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="555f7-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="555f7-153">-c</span><span class="sxs-lookup"><span data-stu-id="555f7-153">-c</span></span>|<span data-ttu-id="555f7-154">--程式碼產生器</span><span class="sxs-lookup"><span data-stu-id="555f7-154">--code-generator</span></span>| <span data-ttu-id="555f7-155">要套用至參考的程式碼產生器。</span><span class="sxs-lookup"><span data-stu-id="555f7-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="555f7-156">選項為 `NSwagCSharp` 和 `NSwagTypeScript` 。</span><span class="sxs-lookup"><span data-stu-id="555f7-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="555f7-157">dotnet openapi 新增 url `https://contoso.com/openapi.json` --程式碼產生器</span><span class="sxs-lookup"><span data-stu-id="555f7-157">dotnet openapi add url `https://contoso.com/openapi.json` --code-generator</span></span>
| <span data-ttu-id="555f7-158">-H</span><span class="sxs-lookup"><span data-stu-id="555f7-158">-h</span></span>|<span data-ttu-id="555f7-159">--help</span><span class="sxs-lookup"><span data-stu-id="555f7-159">--help</span></span>|<span data-ttu-id="555f7-160">顯示說明資訊</span><span class="sxs-lookup"><span data-stu-id="555f7-160">Show help information</span></span>|<span data-ttu-id="555f7-161">dotnet openapi 新增 url--說明</span><span class="sxs-lookup"><span data-stu-id="555f7-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="555f7-162">引數</span><span class="sxs-lookup"><span data-stu-id="555f7-162">Arguments</span></span>

|  <span data-ttu-id="555f7-163">引數</span><span class="sxs-lookup"><span data-stu-id="555f7-163">Argument</span></span>  | <span data-ttu-id="555f7-164">描述</span><span class="sxs-lookup"><span data-stu-id="555f7-164">Description</span></span> | <span data-ttu-id="555f7-165">範例</span><span class="sxs-lookup"><span data-stu-id="555f7-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="555f7-166">來源-URL</span><span class="sxs-lookup"><span data-stu-id="555f7-166">source-URL</span></span> | <span data-ttu-id="555f7-167">要從中建立參考的來源。</span><span class="sxs-lookup"><span data-stu-id="555f7-167">The source to create a reference from.</span></span> <span data-ttu-id="555f7-168">必須是 URL。</span><span class="sxs-lookup"><span data-stu-id="555f7-168">Must be a URL.</span></span> |<span data-ttu-id="555f7-169">dotnet openapi 新增 url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="555f7-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="555f7-170">移除</span><span class="sxs-lookup"><span data-stu-id="555f7-170">Remove</span></span>

<span data-ttu-id="555f7-171">從 *.csproj* 檔案移除符合指定檔案名的 OpenAPI 參考。</span><span class="sxs-lookup"><span data-stu-id="555f7-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="555f7-172">移除 OpenAPI 的參考時，不會產生用戶端。</span><span class="sxs-lookup"><span data-stu-id="555f7-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="555f7-173">將會刪除本機 *json* 和 *yaml* 檔案。</span><span class="sxs-lookup"><span data-stu-id="555f7-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="555f7-174">選項</span><span class="sxs-lookup"><span data-stu-id="555f7-174">Options</span></span>

| <span data-ttu-id="555f7-175">Short 選項</span><span class="sxs-lookup"><span data-stu-id="555f7-175">Short option</span></span>| <span data-ttu-id="555f7-176">Long 選項</span><span class="sxs-lookup"><span data-stu-id="555f7-176">Long option</span></span>| <span data-ttu-id="555f7-177">描述</span><span class="sxs-lookup"><span data-stu-id="555f7-177">Description</span></span>| <span data-ttu-id="555f7-178">範例</span><span class="sxs-lookup"><span data-stu-id="555f7-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="555f7-179">-p</span><span class="sxs-lookup"><span data-stu-id="555f7-179">-p</span></span>|<span data-ttu-id="555f7-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="555f7-180">--updateProject</span></span> | <span data-ttu-id="555f7-181">要操作的專案。</span><span class="sxs-lookup"><span data-stu-id="555f7-181">The project to operate on.</span></span> |<span data-ttu-id="555f7-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="555f7-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="555f7-183">-H</span><span class="sxs-lookup"><span data-stu-id="555f7-183">-h</span></span>|<span data-ttu-id="555f7-184">--help</span><span class="sxs-lookup"><span data-stu-id="555f7-184">--help</span></span>|<span data-ttu-id="555f7-185">顯示說明資訊</span><span class="sxs-lookup"><span data-stu-id="555f7-185">Show help information</span></span>|<span data-ttu-id="555f7-186">dotnet openapi remove--help</span><span class="sxs-lookup"><span data-stu-id="555f7-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="555f7-187">引數</span><span class="sxs-lookup"><span data-stu-id="555f7-187">Arguments</span></span>

|  <span data-ttu-id="555f7-188">引數</span><span class="sxs-lookup"><span data-stu-id="555f7-188">Argument</span></span>  | <span data-ttu-id="555f7-189">描述</span><span class="sxs-lookup"><span data-stu-id="555f7-189">Description</span></span>| <span data-ttu-id="555f7-190">範例</span><span class="sxs-lookup"><span data-stu-id="555f7-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="555f7-191">來源檔案</span><span class="sxs-lookup"><span data-stu-id="555f7-191">source-file</span></span> | <span data-ttu-id="555f7-192">要移除參考的來源。</span><span class="sxs-lookup"><span data-stu-id="555f7-192">The source to remove the reference to.</span></span> |<span data-ttu-id="555f7-193">dotnet openapi 移除 *.\OpenAPI.js開啟*</span><span class="sxs-lookup"><span data-stu-id="555f7-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="555f7-194">Refresh</span><span class="sxs-lookup"><span data-stu-id="555f7-194">Refresh</span></span>

<span data-ttu-id="555f7-195">重新整理使用下載 URL 的最新內容所下載之檔案的本機版本。</span><span class="sxs-lookup"><span data-stu-id="555f7-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="555f7-196">選項</span><span class="sxs-lookup"><span data-stu-id="555f7-196">Options</span></span>

| <span data-ttu-id="555f7-197">Short 選項</span><span class="sxs-lookup"><span data-stu-id="555f7-197">Short option</span></span>| <span data-ttu-id="555f7-198">Long 選項</span><span class="sxs-lookup"><span data-stu-id="555f7-198">Long option</span></span>| <span data-ttu-id="555f7-199">描述</span><span class="sxs-lookup"><span data-stu-id="555f7-199">Description</span></span> | <span data-ttu-id="555f7-200">範例</span><span class="sxs-lookup"><span data-stu-id="555f7-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="555f7-201">-p</span><span class="sxs-lookup"><span data-stu-id="555f7-201">-p</span></span>|<span data-ttu-id="555f7-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="555f7-202">--updateProject</span></span> | <span data-ttu-id="555f7-203">要操作的專案。</span><span class="sxs-lookup"><span data-stu-id="555f7-203">The project to operate on.</span></span> | <span data-ttu-id="555f7-204">dotnet openapi refresh *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="555f7-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="555f7-205">-H</span><span class="sxs-lookup"><span data-stu-id="555f7-205">-h</span></span>|<span data-ttu-id="555f7-206">--help</span><span class="sxs-lookup"><span data-stu-id="555f7-206">--help</span></span>|<span data-ttu-id="555f7-207">顯示說明資訊</span><span class="sxs-lookup"><span data-stu-id="555f7-207">Show help information</span></span>|<span data-ttu-id="555f7-208">dotnet openapi refresh--help</span><span class="sxs-lookup"><span data-stu-id="555f7-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="555f7-209">引數</span><span class="sxs-lookup"><span data-stu-id="555f7-209">Arguments</span></span>

|  <span data-ttu-id="555f7-210">引數</span><span class="sxs-lookup"><span data-stu-id="555f7-210">Argument</span></span>  | <span data-ttu-id="555f7-211">描述</span><span class="sxs-lookup"><span data-stu-id="555f7-211">Description</span></span> | <span data-ttu-id="555f7-212">範例</span><span class="sxs-lookup"><span data-stu-id="555f7-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="555f7-213">來源-URL</span><span class="sxs-lookup"><span data-stu-id="555f7-213">source-URL</span></span> | <span data-ttu-id="555f7-214">重新整理參考的 URL。</span><span class="sxs-lookup"><span data-stu-id="555f7-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="555f7-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="555f7-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
