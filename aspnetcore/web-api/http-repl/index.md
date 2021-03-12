---
title: 使用 HttpRepl 測試 web Api
author: scottaddie
description: 瞭解如何使用 HttpRepl .NET Core 通用工具來流覽和測試 ASP.NET Core web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2020
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
uid: web-api/http-repl
ms.openlocfilehash: aeff3fd06719095660d1b3bb794ef74a8549f761
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585756"
---
# <a name="test-web-apis-with-the-httprepl"></a><span data-ttu-id="72535-103">使用 HttpRepl 測試 web Api</span><span class="sxs-lookup"><span data-stu-id="72535-103">Test web APIs with the HttpRepl</span></span>

<span data-ttu-id="72535-104">作者：[Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="72535-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="72535-105">HTTP「讀取、求值、輸出」迴圈 (REPL) 是：</span><span class="sxs-lookup"><span data-stu-id="72535-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="72535-106">輕量型的跨平台命令列工具，其支援需求和 .NET Core 相同。</span><span class="sxs-lookup"><span data-stu-id="72535-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="72535-107">用來提出 HTTP 要求來測試 ASP.NET Core Web API (及非 ASP.NET Core 的 Web API) 並檢視其結果。</span><span class="sxs-lookup"><span data-stu-id="72535-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="72535-108">能夠測試裝載於任何環境中的 Web API，包括 localhost 和 Azure App Service。</span><span class="sxs-lookup"><span data-stu-id="72535-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="72535-109">支援的 [HTTP 動詞命令](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)如下：</span><span class="sxs-lookup"><span data-stu-id="72535-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="72535-110">DELETE</span><span class="sxs-lookup"><span data-stu-id="72535-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="72535-111">GET</span><span class="sxs-lookup"><span data-stu-id="72535-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="72535-112">頭</span><span class="sxs-lookup"><span data-stu-id="72535-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="72535-113">選項</span><span class="sxs-lookup"><span data-stu-id="72535-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="72535-114">補丁</span><span class="sxs-lookup"><span data-stu-id="72535-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="72535-115">POST</span><span class="sxs-lookup"><span data-stu-id="72535-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="72535-116">PUT</span><span class="sxs-lookup"><span data-stu-id="72535-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="72535-117">若要跟著做，[請檢視或下載範例 ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/http-repl/samples) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="72535-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="72535-118">必要條件</span><span class="sxs-lookup"><span data-stu-id="72535-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="72535-119">安裝</span><span class="sxs-lookup"><span data-stu-id="72535-119">Installation</span></span>

<span data-ttu-id="72535-120">若要安裝 HttpRepl，請執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="72535-120">To install the HttpRepl, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="72535-121">會從 [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) \(英文\) NuGet 套件安裝 [.NET Core 全域工具](/dotnet/core/tools/global-tools#install-a-global-tool)。</span><span class="sxs-lookup"><span data-stu-id="72535-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="72535-122">使用方式</span><span class="sxs-lookup"><span data-stu-id="72535-122">Usage</span></span>

<span data-ttu-id="72535-123">成功安裝工具之後，請執行下列命令來啟動 HttpRepl：</span><span class="sxs-lookup"><span data-stu-id="72535-123">After successful installation of the tool, run the following command to start the HttpRepl:</span></span>

```console
httprepl
```

<span data-ttu-id="72535-124">若要查看可用的 HttpRepl 命令，請執行下列其中一個命令：</span><span class="sxs-lookup"><span data-stu-id="72535-124">To view the available HttpRepl commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="72535-125">下列輸出隨即顯示：</span><span class="sxs-lookup"><span data-stu-id="72535-125">The following output is displayed:</span></span>

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

<span data-ttu-id="72535-126">HttpRepl 會提供命令完成。</span><span class="sxs-lookup"><span data-stu-id="72535-126">The HttpRepl offers command completion.</span></span> <span data-ttu-id="72535-127">按 <kbd>Tab</kbd> 鍵會逐一查看完成您所鍵入之字元或 API 端點的命令清單。</span><span class="sxs-lookup"><span data-stu-id="72535-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="72535-128">下列各節將概述可用的 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="72535-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="72535-129">連線至 web API</span><span class="sxs-lookup"><span data-stu-id="72535-129">Connect to the web API</span></span>

<span data-ttu-id="72535-130">執行下列命令來連線至 web API：</span><span class="sxs-lookup"><span data-stu-id="72535-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="72535-131">`<ROOT URI>` 是 web API 的基底 URI。</span><span class="sxs-lookup"><span data-stu-id="72535-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="72535-132">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="72535-133">或者，在 HttpRepl 執行時隨時執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="72535-133">Alternatively, run the following command at any time while the HttpRepl is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="72535-134">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="72535-135">手動指向 web API 的 OpenAPI 描述</span><span class="sxs-lookup"><span data-stu-id="72535-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="72535-136">上述 connect 命令會自動嘗試尋找 OpenAPI 描述。</span><span class="sxs-lookup"><span data-stu-id="72535-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="72535-137">如果基於某些原因而無法這麼做，您可以使用選項來指定 web API 的 OpenAPI 描述的 URI `--openapi` ：</span><span class="sxs-lookup"><span data-stu-id="72535-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="72535-138">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="72535-139">啟用詳細資訊輸出，以取得 OpenAPI 描述搜尋、剖析和驗證的詳細資料</span><span class="sxs-lookup"><span data-stu-id="72535-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="72535-140">`--verbose` `connect` 當工具搜尋 OpenAPI 描述、剖析和驗證時，使用命令指定選項會產生更多詳細資料。</span><span class="sxs-lookup"><span data-stu-id="72535-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="72535-141">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="72535-142">瀏覽 web API</span><span class="sxs-lookup"><span data-stu-id="72535-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="72535-143">檢視可用的端點</span><span class="sxs-lookup"><span data-stu-id="72535-143">View available endpoints</span></span>

<span data-ttu-id="72535-144">若要列出 web API 位址目前路徑上的不同端點 (控制器)，請執行 `ls` 或 `dir` 命令：</span><span class="sxs-lookup"><span data-stu-id="72535-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhost:5001/> ls
```

<span data-ttu-id="72535-145">下列輸出格式會隨即顯示：</span><span class="sxs-lookup"><span data-stu-id="72535-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="72535-146">上述輸出代表有兩個控制器可用：`Fruits` 與 `People`。</span><span class="sxs-lookup"><span data-stu-id="72535-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="72535-147">兩個控制器均支援無參數的 HTTP GET 和 POST 作業。</span><span class="sxs-lookup"><span data-stu-id="72535-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="72535-148">瀏覽至特定控制器會顯示更多詳細資料。</span><span class="sxs-lookup"><span data-stu-id="72535-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="72535-149">舉例來說，以下命令的輸出會顯示 `Fruits` 控制器也支援 HTTP GET、PUT 和 DELETE 作業。</span><span class="sxs-lookup"><span data-stu-id="72535-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="72535-150">這些作業在路由中都需要 `id` 參數：</span><span class="sxs-lookup"><span data-stu-id="72535-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="72535-151">或者，執行 `ui` 命令在瀏覽器中開啟 web API 的 Swagger UI 頁面。</span><span class="sxs-lookup"><span data-stu-id="72535-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="72535-152">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="72535-153">瀏覽至端點</span><span class="sxs-lookup"><span data-stu-id="72535-153">Navigate to an endpoint</span></span>

<span data-ttu-id="72535-154">若要瀏覽至 web API 上的不同端點，請執行 `cd` 命令：</span><span class="sxs-lookup"><span data-stu-id="72535-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="72535-155">接著 `cd` 命令的路徑不會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="72535-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="72535-156">下列輸出格式會隨即顯示：</span><span class="sxs-lookup"><span data-stu-id="72535-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a><span data-ttu-id="72535-157">自訂 HttpRepl</span><span class="sxs-lookup"><span data-stu-id="72535-157">Customize the HttpRepl</span></span>

<span data-ttu-id="72535-158">您可以自訂 HttpRepl 的預設 [色彩](#set-color-preferences) 。</span><span class="sxs-lookup"><span data-stu-id="72535-158">The HttpRepl's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="72535-159">此外，還可定義[預設文字編輯器](#set-the-default-text-editor)。</span><span class="sxs-lookup"><span data-stu-id="72535-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="72535-160">HttpRepl 喜好設定會保存在目前的會話中，並在未來的會話中接受。</span><span class="sxs-lookup"><span data-stu-id="72535-160">The HttpRepl preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="72535-161">修改後，喜好設定會儲存在以下檔案中：</span><span class="sxs-lookup"><span data-stu-id="72535-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="72535-162">Linux</span><span class="sxs-lookup"><span data-stu-id="72535-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="72535-163">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="72535-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="72535-164">macOS</span><span class="sxs-lookup"><span data-stu-id="72535-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="72535-165">*%HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="72535-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="72535-166">Windows</span><span class="sxs-lookup"><span data-stu-id="72535-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="72535-167">*%USERPROFILE%\\.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="72535-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="72535-168">*.httpreplprefs* 檔案會於啟動時載入，且其變更不會於執行階段受到監視。</span><span class="sxs-lookup"><span data-stu-id="72535-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="72535-169">對檔案進行的手動修改只會在重新啟動工具後生效。</span><span class="sxs-lookup"><span data-stu-id="72535-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="72535-170">檢視設定</span><span class="sxs-lookup"><span data-stu-id="72535-170">View the settings</span></span>

<span data-ttu-id="72535-171">若要檢視可用的設定，請執行 `pref get` 命令。</span><span class="sxs-lookup"><span data-stu-id="72535-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="72535-172">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="72535-173">上述命令會顯示可用的機碼值組：</span><span class="sxs-lookup"><span data-stu-id="72535-173">The preceding command displays the available key-value pairs:</span></span>

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a><span data-ttu-id="72535-174">設定色彩喜好設定</span><span class="sxs-lookup"><span data-stu-id="72535-174">Set color preferences</span></span>

<span data-ttu-id="72535-175">目前僅為 JSON 支援回應著色。</span><span class="sxs-lookup"><span data-stu-id="72535-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="72535-176">若要自訂預設 HttpRepl 工具色彩，請找出對應到要變更之色彩的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="72535-176">To customize the default HttpRepl tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="72535-177">如需如何尋找機碼的指示，請參閱[檢視設定](#view-the-settings)一節。</span><span class="sxs-lookup"><span data-stu-id="72535-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="72535-178">舉例來說，將 `colors.json` 機碼值從 `Green` 變更為 `White`，如下所示：</span><span class="sxs-lookup"><span data-stu-id="72535-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="72535-179">只能使用[允許的色彩](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)。</span><span class="sxs-lookup"><span data-stu-id="72535-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="72535-180">後續的 HTTP 要求會顯示含有新著色的輸出。</span><span class="sxs-lookup"><span data-stu-id="72535-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="72535-181">未設定特定色彩機碼時，會使用較泛用的機碼。</span><span class="sxs-lookup"><span data-stu-id="72535-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="72535-182">為了示範此遞補行為，請參考以下範例：</span><span class="sxs-lookup"><span data-stu-id="72535-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="72535-183">如果 `colors.json.name` 沒有值，即使用 `colors.json.string`。</span><span class="sxs-lookup"><span data-stu-id="72535-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="72535-184">如果 `colors.json.string` 沒有值，即使用 `colors.json.literal`。</span><span class="sxs-lookup"><span data-stu-id="72535-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="72535-185">如果 `colors.json.literal` 沒有值，即使用 `colors.json`。</span><span class="sxs-lookup"><span data-stu-id="72535-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="72535-186">如果 `colors.json` 沒有值，即使用命令殼層的預設文字色彩 (`AllowedColors.None`)。</span><span class="sxs-lookup"><span data-stu-id="72535-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="72535-187">設定縮排大小</span><span class="sxs-lookup"><span data-stu-id="72535-187">Set indentation size</span></span>

<span data-ttu-id="72535-188">目前僅為 JSON 支援回應縮排大小自訂。</span><span class="sxs-lookup"><span data-stu-id="72535-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="72535-189">預設大小為兩個空格。</span><span class="sxs-lookup"><span data-stu-id="72535-189">The default size is two spaces.</span></span> <span data-ttu-id="72535-190">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-190">For example:</span></span>

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

<span data-ttu-id="72535-191">若要變更預設大小，請設定 `formatting.json.indentSize` 機碼。</span><span class="sxs-lookup"><span data-stu-id="72535-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="72535-192">舉例來說，若要一律使用四個空格：</span><span class="sxs-lookup"><span data-stu-id="72535-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="72535-193">後續回應皆會套用四個空格的設定：</span><span class="sxs-lookup"><span data-stu-id="72535-193">Subsequent responses honor the setting of four spaces:</span></span>

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a><span data-ttu-id="72535-194">設定預設文字編輯器</span><span class="sxs-lookup"><span data-stu-id="72535-194">Set the default text editor</span></span>

<span data-ttu-id="72535-195">根據預設，HttpRepl 沒有設定為使用的文字編輯器。</span><span class="sxs-lookup"><span data-stu-id="72535-195">By default, the HttpRepl has no text editor configured for use.</span></span> <span data-ttu-id="72535-196">您必須設定預設文字編輯器，才能測試需要 HTTP 要求本文的 web API 方法。</span><span class="sxs-lookup"><span data-stu-id="72535-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="72535-197">HttpRepl 工具會針對撰寫要求本文的唯一目的，啟動已設定的文字編輯器。</span><span class="sxs-lookup"><span data-stu-id="72535-197">The HttpRepl tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="72535-198">請執行以下命令，來將您偏好的文字編輯器設為預設：</span><span class="sxs-lookup"><span data-stu-id="72535-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="72535-199">在上述命令中，`<EXECUTABLE>` 是文字編輯器可執行檔的完整路徑。</span><span class="sxs-lookup"><span data-stu-id="72535-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="72535-200">舉例來說，執行以下命令將 Visual Studio Code 設為預設文字編輯器：</span><span class="sxs-lookup"><span data-stu-id="72535-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="72535-201">Linux</span><span class="sxs-lookup"><span data-stu-id="72535-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="72535-202">macOS</span><span class="sxs-lookup"><span data-stu-id="72535-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="72535-203">Windows</span><span class="sxs-lookup"><span data-stu-id="72535-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="72535-204">若要以特定 CLI 引數啟動預設文字編輯器，請設定 `editor.command.default.arguments` 機碼。</span><span class="sxs-lookup"><span data-stu-id="72535-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="72535-205">例如，假設 Visual Studio Code 是預設文字編輯器，而且您一律希望 HttpRepl 在已停用擴充功能的新會話中開啟 Visual Studio Code。</span><span class="sxs-lookup"><span data-stu-id="72535-205">For example, assume Visual Studio Code is the default text editor and that you always want the HttpRepl to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="72535-206">執行以下命令：</span><span class="sxs-lookup"><span data-stu-id="72535-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="72535-207">如果您的預設編輯器是 Visual Studio Code，您通常會想要傳遞 `-w` 或 `--wait` 引數，以強制 Visual studio Code 先等候您關閉檔案，然後再返回。</span><span class="sxs-lookup"><span data-stu-id="72535-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="72535-208">設定 OpenAPI Description 搜尋路徑</span><span class="sxs-lookup"><span data-stu-id="72535-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="72535-209">根據預設，HttpRepl 在執行命令時，會使用一組相對路徑來尋找 OpenAPI 描述（ `connect` 沒有 `--openapi` 選項）。</span><span class="sxs-lookup"><span data-stu-id="72535-209">By default, the HttpRepl has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="72535-210">這些相對路徑會與 `connect` 命令中指定的根路徑和基本路徑結合。</span><span class="sxs-lookup"><span data-stu-id="72535-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="72535-211">預設的相對路徑為：</span><span class="sxs-lookup"><span data-stu-id="72535-211">The default relative paths are:</span></span>

- <span data-ttu-id="72535-212">*swagger.js開啟*</span><span class="sxs-lookup"><span data-stu-id="72535-212">*swagger.json*</span></span>
- <span data-ttu-id="72535-213">*swagger/v1/swagger.js開啟*</span><span class="sxs-lookup"><span data-stu-id="72535-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="72535-214">*/swagger.js開啟*</span><span class="sxs-lookup"><span data-stu-id="72535-214">*/swagger.json*</span></span>
- <span data-ttu-id="72535-215">*/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="72535-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="72535-216">*openapi.js開啟*</span><span class="sxs-lookup"><span data-stu-id="72535-216">*openapi.json*</span></span>
- <span data-ttu-id="72535-217">*/openapi.js開啟*</span><span class="sxs-lookup"><span data-stu-id="72535-217">*/openapi.json*</span></span>

<span data-ttu-id="72535-218">若要在您的環境中使用一組不同的搜尋路徑，請設定 `swagger.searchPaths` 喜好設定。</span><span class="sxs-lookup"><span data-stu-id="72535-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="72535-219">此值必須是以管線分隔的相對路徑清單。</span><span class="sxs-lookup"><span data-stu-id="72535-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="72535-220">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="72535-221">除了取代預設清單之外，也可以藉由新增或移除路徑來修改清單。</span><span class="sxs-lookup"><span data-stu-id="72535-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="72535-222">若要將一或多個搜尋路徑新增至預設清單，請設定 `swagger.addToSearchPaths` 喜好設定。</span><span class="sxs-lookup"><span data-stu-id="72535-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="72535-223">此值必須是以管線分隔的相對路徑清單。</span><span class="sxs-lookup"><span data-stu-id="72535-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="72535-224">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="72535-225">若要從預設清單中移除一個或多個搜尋路徑，請設定 `swagger.addToSearchPaths` 喜好設定。</span><span class="sxs-lookup"><span data-stu-id="72535-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="72535-226">此值必須是以管線分隔的相對路徑清單。</span><span class="sxs-lookup"><span data-stu-id="72535-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="72535-227">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="72535-228">測試 HTTP GET 要求</span><span class="sxs-lookup"><span data-stu-id="72535-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="72535-229">概要</span><span class="sxs-lookup"><span data-stu-id="72535-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="72535-230">引數</span><span class="sxs-lookup"><span data-stu-id="72535-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="72535-231">相關控制器動作方法預期的路由參數 (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="72535-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="72535-232">選項</span><span class="sxs-lookup"><span data-stu-id="72535-232">Options</span></span>

<span data-ttu-id="72535-233">以下是使用 `get` 命令時可用的選項：</span><span class="sxs-lookup"><span data-stu-id="72535-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="72535-234">範例</span><span class="sxs-lookup"><span data-stu-id="72535-234">Example</span></span>

<span data-ttu-id="72535-235">若要發出 HTTP GET 要求：</span><span class="sxs-lookup"><span data-stu-id="72535-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="72535-236">在支援的端點上執行 `get` 命令：</span><span class="sxs-lookup"><span data-stu-id="72535-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="72535-237">上述命令會顯示以下輸出格式：</span><span class="sxs-lookup"><span data-stu-id="72535-237">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people>
    ```

1. <span data-ttu-id="72535-238">對 `get` 命令傳遞參數來擷取特定記錄：</span><span class="sxs-lookup"><span data-stu-id="72535-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="72535-239">上述命令會顯示以下輸出格式：</span><span class="sxs-lookup"><span data-stu-id="72535-239">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="72535-240">測試 HTTP POST 要求</span><span class="sxs-lookup"><span data-stu-id="72535-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="72535-241">概要</span><span class="sxs-lookup"><span data-stu-id="72535-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="72535-242">引數</span><span class="sxs-lookup"><span data-stu-id="72535-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="72535-243">相關控制器動作方法預期的路由參數 (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="72535-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="72535-244">選項</span><span class="sxs-lookup"><span data-stu-id="72535-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="72535-245">範例</span><span class="sxs-lookup"><span data-stu-id="72535-245">Example</span></span>

<span data-ttu-id="72535-246">若要發出 HTTP POST 要求：</span><span class="sxs-lookup"><span data-stu-id="72535-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="72535-247">在支援的端點上執行 `post` 命令：</span><span class="sxs-lookup"><span data-stu-id="72535-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="72535-248">在上述命令中，`Content-Type` HTTP 要求標頭設定為指出 JSON 類型的要求本文媒體。</span><span class="sxs-lookup"><span data-stu-id="72535-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="72535-249">預設文字編輯器會開啟 *.tmp* 檔案，其中包含代表 HTTP 要求本文的 JSON 範本。</span><span class="sxs-lookup"><span data-stu-id="72535-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="72535-250">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="72535-251">若要設定預設文字編輯器，請參閱[設定預設文字編輯器](#set-the-default-text-editor)一節。</span><span class="sxs-lookup"><span data-stu-id="72535-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="72535-252">修改 JSON 範本以滿足模型驗證需求：</span><span class="sxs-lookup"><span data-stu-id="72535-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="72535-253">儲存 *.tmp* 檔案，然後關閉文字編輯器。</span><span class="sxs-lookup"><span data-stu-id="72535-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="72535-254">下列輸出會出現在命令殼層中：</span><span class="sxs-lookup"><span data-stu-id="72535-254">The following output appears in the command shell:</span></span>

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="72535-255">測試 HTTP PUT 要求</span><span class="sxs-lookup"><span data-stu-id="72535-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="72535-256">概要</span><span class="sxs-lookup"><span data-stu-id="72535-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="72535-257">引數</span><span class="sxs-lookup"><span data-stu-id="72535-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="72535-258">相關控制器動作方法預期的路由參數 (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="72535-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="72535-259">選項</span><span class="sxs-lookup"><span data-stu-id="72535-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="72535-260">範例</span><span class="sxs-lookup"><span data-stu-id="72535-260">Example</span></span>

<span data-ttu-id="72535-261">若要發出 HTTP PUT 要求：</span><span class="sxs-lookup"><span data-stu-id="72535-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="72535-262">*選擇性*：執行 `get` 命令以在修改資料之前加以查看：</span><span class="sxs-lookup"><span data-stu-id="72535-262">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="72535-263">在支援的端點上執行 `put` 命令：</span><span class="sxs-lookup"><span data-stu-id="72535-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="72535-264">在上述命令中，`Content-Type` HTTP 要求標頭設定為指出 JSON 類型的要求本文媒體。</span><span class="sxs-lookup"><span data-stu-id="72535-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="72535-265">預設文字編輯器會開啟 *.tmp* 檔案，其中包含代表 HTTP 要求本文的 JSON 範本。</span><span class="sxs-lookup"><span data-stu-id="72535-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="72535-266">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="72535-267">若要設定預設文字編輯器，請參閱[設定預設文字編輯器](#set-the-default-text-editor)一節。</span><span class="sxs-lookup"><span data-stu-id="72535-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="72535-268">修改 JSON 範本以滿足模型驗證需求：</span><span class="sxs-lookup"><span data-stu-id="72535-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="72535-269">儲存 *.tmp* 檔案，然後關閉文字編輯器。</span><span class="sxs-lookup"><span data-stu-id="72535-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="72535-270">下列輸出會出現在命令殼層中：</span><span class="sxs-lookup"><span data-stu-id="72535-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="72535-271">*選擇性*：發出 `get` 命令以查看修改。</span><span class="sxs-lookup"><span data-stu-id="72535-271">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="72535-272">例如，如果您在文字編輯器中輸入 "揀選"，則會傳回 `get` 下列輸出：</span><span class="sxs-lookup"><span data-stu-id="72535-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="72535-273">測試 HTTP DELETE 要求</span><span class="sxs-lookup"><span data-stu-id="72535-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="72535-274">概要</span><span class="sxs-lookup"><span data-stu-id="72535-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="72535-275">引數</span><span class="sxs-lookup"><span data-stu-id="72535-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="72535-276">相關控制器動作方法預期的路由參數 (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="72535-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="72535-277">選項</span><span class="sxs-lookup"><span data-stu-id="72535-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="72535-278">範例</span><span class="sxs-lookup"><span data-stu-id="72535-278">Example</span></span>

<span data-ttu-id="72535-279">若要發出 HTTP DELETE 要求：</span><span class="sxs-lookup"><span data-stu-id="72535-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="72535-280">*選擇性*：執行 `get` 命令以在修改資料之前加以查看：</span><span class="sxs-lookup"><span data-stu-id="72535-280">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. <span data-ttu-id="72535-281">在支援的端點上執行 `delete` 命令：</span><span class="sxs-lookup"><span data-stu-id="72535-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="72535-282">上述命令會顯示以下輸出格式：</span><span class="sxs-lookup"><span data-stu-id="72535-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="72535-283">*選擇性*：發出 `get` 命令以查看修改。</span><span class="sxs-lookup"><span data-stu-id="72535-283">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="72535-284">在此範例中，會傳回 `get` 下列輸出：</span><span class="sxs-lookup"><span data-stu-id="72535-284">In this example, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="72535-285">測試 HTTP PATCH 要求</span><span class="sxs-lookup"><span data-stu-id="72535-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="72535-286">概要</span><span class="sxs-lookup"><span data-stu-id="72535-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="72535-287">引數</span><span class="sxs-lookup"><span data-stu-id="72535-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="72535-288">相關控制器動作方法預期的路由參數 (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="72535-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="72535-289">選項</span><span class="sxs-lookup"><span data-stu-id="72535-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="72535-290">測試 HTTP HEAD 要求</span><span class="sxs-lookup"><span data-stu-id="72535-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="72535-291">概要</span><span class="sxs-lookup"><span data-stu-id="72535-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="72535-292">引數</span><span class="sxs-lookup"><span data-stu-id="72535-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="72535-293">相關控制器動作方法預期的路由參數 (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="72535-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="72535-294">選項</span><span class="sxs-lookup"><span data-stu-id="72535-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="72535-295">測試 HTTP OPTIONS 要求</span><span class="sxs-lookup"><span data-stu-id="72535-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="72535-296">概要</span><span class="sxs-lookup"><span data-stu-id="72535-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="72535-297">引數</span><span class="sxs-lookup"><span data-stu-id="72535-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="72535-298">相關控制器動作方法預期的路由參數 (如果有的話)。</span><span class="sxs-lookup"><span data-stu-id="72535-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="72535-299">選項</span><span class="sxs-lookup"><span data-stu-id="72535-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="72535-300">設定 HTTP 要求標頭</span><span class="sxs-lookup"><span data-stu-id="72535-300">Set HTTP request headers</span></span>

<span data-ttu-id="72535-301">若要設定 HTTP 要求標頭，請使用下列其中一個方法：</span><span class="sxs-lookup"><span data-stu-id="72535-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="72535-302">與 HTTP 要求一同設定。</span><span class="sxs-lookup"><span data-stu-id="72535-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="72535-303">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="72535-304">若使用上述方法，則各相異的 HTTP 要求標頭都需要自己的 `-h` 選項。</span><span class="sxs-lookup"><span data-stu-id="72535-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="72535-305">於傳送 HTTP 要求之前設定。</span><span class="sxs-lookup"><span data-stu-id="72535-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="72535-306">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="72535-307">若在傳送要求之前設定標頭，則標頭會保留命令殼層工作階段的持續時間設定。</span><span class="sxs-lookup"><span data-stu-id="72535-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="72535-308">若要清除標頭，請提供空白值。</span><span class="sxs-lookup"><span data-stu-id="72535-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="72535-309">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="72535-310">測試安全的端點</span><span class="sxs-lookup"><span data-stu-id="72535-310">Test secured endpoints</span></span>

<span data-ttu-id="72535-311">HttpRepl 可透過下列方式支援保護端點的測試：</span><span class="sxs-lookup"><span data-stu-id="72535-311">The HttpRepl supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="72535-312">透過登入使用者的預設認證。</span><span class="sxs-lookup"><span data-stu-id="72535-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="72535-313">透過使用 HTTP 要求標頭。</span><span class="sxs-lookup"><span data-stu-id="72535-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="72535-314">預設認證</span><span class="sxs-lookup"><span data-stu-id="72535-314">Default credentials</span></span>

<span data-ttu-id="72535-315">假設您要測試的 web API 是裝載在 IIS 中，並使用 Windows 驗證來保護。</span><span class="sxs-lookup"><span data-stu-id="72535-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="72535-316">您希望執行工具之使用者的認證流經所測試的 HTTP 端點。</span><span class="sxs-lookup"><span data-stu-id="72535-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="72535-317">若要傳遞已登入使用者的預設認證：</span><span class="sxs-lookup"><span data-stu-id="72535-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="72535-318">將喜好設定設 `httpClient.useDefaultCredentials` 為 `true` ：</span><span class="sxs-lookup"><span data-stu-id="72535-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="72535-319">先結束並重新啟動工具，再將另一個要求傳送至 web API。</span><span class="sxs-lookup"><span data-stu-id="72535-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="72535-320">預設 proxy 認證</span><span class="sxs-lookup"><span data-stu-id="72535-320">Default proxy credentials</span></span>

<span data-ttu-id="72535-321">假設您要測試的 web API 位於使用 Windows 驗證保護的 proxy 後方的案例。</span><span class="sxs-lookup"><span data-stu-id="72535-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="72535-322">您希望執行工具之使用者的認證流向 proxy。</span><span class="sxs-lookup"><span data-stu-id="72535-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="72535-323">若要傳遞已登入使用者的預設認證：</span><span class="sxs-lookup"><span data-stu-id="72535-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="72535-324">將喜好設定設 `httpClient.proxy.useDefaultCredentials` 為 `true` ：</span><span class="sxs-lookup"><span data-stu-id="72535-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="72535-325">先結束並重新啟動工具，再將另一個要求傳送至 web API。</span><span class="sxs-lookup"><span data-stu-id="72535-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="72535-326">HTTP 要求標頭</span><span class="sxs-lookup"><span data-stu-id="72535-326">HTTP request headers</span></span>

<span data-ttu-id="72535-327">支援的驗證和授權配置範例包括：</span><span class="sxs-lookup"><span data-stu-id="72535-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="72535-328">basic authentication</span><span class="sxs-lookup"><span data-stu-id="72535-328">basic authentication</span></span>
* <span data-ttu-id="72535-329">JWT 持有人權杖</span><span class="sxs-lookup"><span data-stu-id="72535-329">JWT bearer tokens</span></span>
* <span data-ttu-id="72535-330">摘要式驗證</span><span class="sxs-lookup"><span data-stu-id="72535-330">digest authentication</span></span>

<span data-ttu-id="72535-331">例如，您可以使用下列命令，將持有人權杖傳送至端點：</span><span class="sxs-lookup"><span data-stu-id="72535-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="72535-332">若要存取 Azure 託管端點或使用 [AZURE REST API](/rest/api/azure/)，您需要持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="72535-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="72535-333">使用下列步驟，透過 [AZURE CLI](/cli/azure/)為您的 azure 訂用帳戶取得持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="72535-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="72535-334">HttpRepl 會在 HTTP 要求標頭中設定持有人權杖。</span><span class="sxs-lookup"><span data-stu-id="72535-334">The HttpRepl sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="72535-335">已抓取 Azure App Service Web 應用程式的清單。</span><span class="sxs-lookup"><span data-stu-id="72535-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="72535-336">登入 Azure：</span><span class="sxs-lookup"><span data-stu-id="72535-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="72535-337">使用下列命令取得您的訂用帳戶識別碼：</span><span class="sxs-lookup"><span data-stu-id="72535-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="72535-338">複製您的訂用帳戶識別碼，然後執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="72535-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="72535-339">使用下列命令取得您的持有人權杖：</span><span class="sxs-lookup"><span data-stu-id="72535-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="72535-340">透過 HttpRepl 連接到 Azure REST API：</span><span class="sxs-lookup"><span data-stu-id="72535-340">Connect to the Azure REST API via the HttpRepl:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="72535-341">設定 `Authorization` HTTP 要求標頭：</span><span class="sxs-lookup"><span data-stu-id="72535-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="72535-342">流覽至訂用帳戶：</span><span class="sxs-lookup"><span data-stu-id="72535-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="72535-343">取得訂用帳戶的 Azure App Service Web Apps 清單：</span><span class="sxs-lookup"><span data-stu-id="72535-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="72535-344">隨即顯示下列回應：</span><span class="sxs-lookup"><span data-stu-id="72535-344">The following response is displayed:</span></span>

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a><span data-ttu-id="72535-345">切換 HTTP 要求顯示</span><span class="sxs-lookup"><span data-stu-id="72535-345">Toggle HTTP request display</span></span>

<span data-ttu-id="72535-346">根據預設，會隱藏所傳送之 HTTP 要求的顯示。</span><span class="sxs-lookup"><span data-stu-id="72535-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="72535-347">您可以針對命令殼層工作階段的持續時間變更對應的設定。</span><span class="sxs-lookup"><span data-stu-id="72535-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="72535-348">啟用要求顯示</span><span class="sxs-lookup"><span data-stu-id="72535-348">Enable request display</span></span>

<span data-ttu-id="72535-349">透過執行 `echo on` 命令來檢視要傳送的 HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="72535-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="72535-350">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="72535-351">目前工作階段中的後續 HTTP 要求會顯示要求標頭。</span><span class="sxs-lookup"><span data-stu-id="72535-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="72535-352">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-352">For example:</span></span>

```console
https://localhost:5001/people> post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people>
```

### <a name="disable-request-display"></a><span data-ttu-id="72535-353">停用要求顯示</span><span class="sxs-lookup"><span data-stu-id="72535-353">Disable request display</span></span>

<span data-ttu-id="72535-354">透過執行 `echo off` 命令來隱藏要傳送的 HTTP 要求顯示。</span><span class="sxs-lookup"><span data-stu-id="72535-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="72535-355">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="72535-356">執行指令碼</span><span class="sxs-lookup"><span data-stu-id="72535-356">Run a script</span></span>

<span data-ttu-id="72535-357">如果您經常執行一組相同的 HttpRepl 命令，請考慮將它們儲存在文字檔中。</span><span class="sxs-lookup"><span data-stu-id="72535-357">If you frequently execute the same set of HttpRepl commands, consider storing them in a text file.</span></span> <span data-ttu-id="72535-358">檔案中的命令所採用的格式，與在命令列上手動執行的命令相同。</span><span class="sxs-lookup"><span data-stu-id="72535-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="72535-359">您可使用 `run` 命令以批次的方式執行命令。</span><span class="sxs-lookup"><span data-stu-id="72535-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="72535-360">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-360">For example:</span></span>

1. <span data-ttu-id="72535-361">建立包含一組以新行分隔命令的文字檔。</span><span class="sxs-lookup"><span data-stu-id="72535-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="72535-362">為了說明，請參考包含以下命令的 *people-script.txt* 檔案：</span><span class="sxs-lookup"><span data-stu-id="72535-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="72535-363">執行 `run` 命令，傳入文字檔的路徑。</span><span class="sxs-lookup"><span data-stu-id="72535-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="72535-364">例如：</span><span class="sxs-lookup"><span data-stu-id="72535-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="72535-365">下列輸出會出現：</span><span class="sxs-lookup"><span data-stu-id="72535-365">The following output appears:</span></span>

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a><span data-ttu-id="72535-366">清除輸出</span><span class="sxs-lookup"><span data-stu-id="72535-366">Clear the output</span></span>

<span data-ttu-id="72535-367">若要移除 HttpRepl 工具寫入命令 shell 的所有輸出，請執行 `clear` 或 `cls` 命令。</span><span class="sxs-lookup"><span data-stu-id="72535-367">To remove all output written to the command shell by the HttpRepl tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="72535-368">為了說明，請參考包含以下輸出的命令殼層：</span><span class="sxs-lookup"><span data-stu-id="72535-368">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="72535-369">執行以下命令來清除輸出：</span><span class="sxs-lookup"><span data-stu-id="72535-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="72535-370">執行上述命令後，命令殼層只會包含以下輸出：</span><span class="sxs-lookup"><span data-stu-id="72535-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="72535-371">其他資源</span><span class="sxs-lookup"><span data-stu-id="72535-371">Additional resources</span></span>

* [<span data-ttu-id="72535-372">REST API 要求</span><span class="sxs-lookup"><span data-stu-id="72535-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="72535-373">HttpRepl GitHub 存放庫</span><span class="sxs-lookup"><span data-stu-id="72535-373">HttpRepl GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
* [<span data-ttu-id="72535-374">設定 Visual Studio 以啟動 HttpRepl</span><span class="sxs-lookup"><span data-stu-id="72535-374">Configure Visual Studio to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-windows-to-launch-httprepl-on-f5)
* [<span data-ttu-id="72535-375">設定 Visual Studio Code 以啟動 HttpRepl</span><span class="sxs-lookup"><span data-stu-id="72535-375">Configure Visual Studio Code to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-code-to-launch-httprepl-on-debug)
* [<span data-ttu-id="72535-376">將 Visual Studio for Mac 設定為啟動 HttpRepl</span><span class="sxs-lookup"><span data-stu-id="72535-376">Configure Visual Studio for Mac to launch HttpRepl</span></span>](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-mac-to-launch-httprepl-as-a-custom-tool)
