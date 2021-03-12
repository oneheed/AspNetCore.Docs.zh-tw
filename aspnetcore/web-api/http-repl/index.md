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
# <a name="test-web-apis-with-the-httprepl"></a>使用 HttpRepl 測試 web Api

作者：[Scott Addie](https://twitter.com/Scott_Addie)

HTTP「讀取、求值、輸出」迴圈 (REPL) 是：

* 輕量型的跨平台命令列工具，其支援需求和 .NET Core 相同。
* 用來提出 HTTP 要求來測試 ASP.NET Core Web API (及非 ASP.NET Core 的 Web API) 並檢視其結果。
* 能夠測試裝載於任何環境中的 Web API，包括 localhost 和 Azure App Service。

支援的 [HTTP 動詞命令](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)如下：

* [DELETE](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [頭](#test-http-head-requests)
* [選項](#test-http-options-requests)
* [補丁](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

若要跟著做，[請檢視或下載範例 ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/web-api/http-repl/samples) ([如何下載](xref:index#how-to-download-a-sample))。

## <a name="prerequisites"></a>必要條件

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>安裝

若要安裝 HttpRepl，請執行下列命令：

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

會從 [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) \(英文\) NuGet 套件安裝 [.NET Core 全域工具](/dotnet/core/tools/global-tools#install-a-global-tool)。

## <a name="usage"></a>使用方式

成功安裝工具之後，請執行下列命令來啟動 HttpRepl：

```console
httprepl
```

若要查看可用的 HttpRepl 命令，請執行下列其中一個命令：

```console
httprepl -h
```

```console
httprepl --help
```

下列輸出隨即顯示：

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

HttpRepl 會提供命令完成。 按 <kbd>Tab</kbd> 鍵會逐一查看完成您所鍵入之字元或 API 端點的命令清單。 下列各節將概述可用的 CLI 命令。

## <a name="connect-to-the-web-api"></a>連線至 web API

執行下列命令來連線至 web API：

```console
httprepl <ROOT URI>
```

`<ROOT URI>` 是 web API 的基底 URI。 例如：

```console
httprepl https://localhost:5001
```

或者，在 HttpRepl 執行時隨時執行下列命令：

```console
connect <ROOT URI>
```

例如：

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a>手動指向 web API 的 OpenAPI 描述

上述 connect 命令會自動嘗試尋找 OpenAPI 描述。 如果基於某些原因而無法這麼做，您可以使用選項來指定 web API 的 OpenAPI 描述的 URI `--openapi` ：

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

例如：

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a>啟用詳細資訊輸出，以取得 OpenAPI 描述搜尋、剖析和驗證的詳細資料

`--verbose` `connect` 當工具搜尋 OpenAPI 描述、剖析和驗證時，使用命令指定選項會產生更多詳細資料。

```console
connect <ROOT URI> --verbose
```

例如：

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a>瀏覽 web API

### <a name="view-available-endpoints"></a>檢視可用的端點

若要列出 web API 位址目前路徑上的不同端點 (控制器)，請執行 `ls` 或 `dir` 命令：

```console
https://localhost:5001/> ls
```

下列輸出格式會隨即顯示：

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

上述輸出代表有兩個控制器可用：`Fruits` 與 `People`。 兩個控制器均支援無參數的 HTTP GET 和 POST 作業。

瀏覽至特定控制器會顯示更多詳細資料。 舉例來說，以下命令的輸出會顯示 `Fruits` 控制器也支援 HTTP GET、PUT 和 DELETE 作業。 這些作業在路由中都需要 `id` 參數：

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

或者，執行 `ui` 命令在瀏覽器中開啟 web API 的 Swagger UI 頁面。 例如：

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a>瀏覽至端點

若要瀏覽至 web API 上的不同端點，請執行 `cd` 命令：

```console
https://localhost:5001/> cd people
```

接著 `cd` 命令的路徑不會區分大小寫。 下列輸出格式會隨即顯示：

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a>自訂 HttpRepl

您可以自訂 HttpRepl 的預設 [色彩](#set-color-preferences) 。 此外，還可定義[預設文字編輯器](#set-the-default-text-editor)。 HttpRepl 喜好設定會保存在目前的會話中，並在未來的會話中接受。 修改後，喜好設定會儲存在以下檔案中：

# <a name="linux"></a>[Linux](#tab/linux)

*%HOME%/.httpreplprefs*

# <a name="macos"></a>[macOS](#tab/macos)

*%HOME%/.httpreplprefs*

# <a name="windows"></a>[Windows](#tab/windows)

*%USERPROFILE%\\.httpreplprefs*

---

*.httpreplprefs* 檔案會於啟動時載入，且其變更不會於執行階段受到監視。 對檔案進行的手動修改只會在重新啟動工具後生效。

### <a name="view-the-settings"></a>檢視設定

若要檢視可用的設定，請執行 `pref get` 命令。 例如：

```console
https://localhost:5001/> pref get
```

上述命令會顯示可用的機碼值組：

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

### <a name="set-color-preferences"></a>設定色彩喜好設定

目前僅為 JSON 支援回應著色。 若要自訂預設 HttpRepl 工具色彩，請找出對應到要變更之色彩的索引鍵。 如需如何尋找機碼的指示，請參閱[檢視設定](#view-the-settings)一節。 舉例來說，將 `colors.json` 機碼值從 `Green` 變更為 `White`，如下所示：

```console
https://localhost:5001/people> pref set colors.json White
```

只能使用[允許的色彩](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs)。 後續的 HTTP 要求會顯示含有新著色的輸出。

未設定特定色彩機碼時，會使用較泛用的機碼。 為了示範此遞補行為，請參考以下範例：

* 如果 `colors.json.name` 沒有值，即使用 `colors.json.string`。
* 如果 `colors.json.string` 沒有值，即使用 `colors.json.literal`。
* 如果 `colors.json.literal` 沒有值，即使用 `colors.json`。 
* 如果 `colors.json` 沒有值，即使用命令殼層的預設文字色彩 (`AllowedColors.None`)。

### <a name="set-indentation-size"></a>設定縮排大小

目前僅為 JSON 支援回應縮排大小自訂。 預設大小為兩個空格。 例如：

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

若要變更預設大小，請設定 `formatting.json.indentSize` 機碼。 舉例來說，若要一律使用四個空格：

```console
pref set formatting.json.indentSize 4
```

後續回應皆會套用四個空格的設定：

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

### <a name="set-the-default-text-editor"></a>設定預設文字編輯器

根據預設，HttpRepl 沒有設定為使用的文字編輯器。 您必須設定預設文字編輯器，才能測試需要 HTTP 要求本文的 web API 方法。 HttpRepl 工具會針對撰寫要求本文的唯一目的，啟動已設定的文字編輯器。 請執行以下命令，來將您偏好的文字編輯器設為預設：

```console
pref set editor.command.default "<EXECUTABLE>"
```

在上述命令中，`<EXECUTABLE>` 是文字編輯器可執行檔的完整路徑。 舉例來說，執行以下命令將 Visual Studio Code 設為預設文字編輯器：

# <a name="linux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

若要以特定 CLI 引數啟動預設文字編輯器，請設定 `editor.command.default.arguments` 機碼。 例如，假設 Visual Studio Code 是預設文字編輯器，而且您一律希望 HttpRepl 在已停用擴充功能的新會話中開啟 Visual Studio Code。 執行以下命令：

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> 如果您的預設編輯器是 Visual Studio Code，您通常會想要傳遞 `-w` 或 `--wait` 引數，以強制 Visual studio Code 先等候您關閉檔案，然後再返回。

### <a name="set-the-openapi-description-search-paths"></a>設定 OpenAPI Description 搜尋路徑

根據預設，HttpRepl 在執行命令時，會使用一組相對路徑來尋找 OpenAPI 描述（ `connect` 沒有 `--openapi` 選項）。 這些相對路徑會與 `connect` 命令中指定的根路徑和基本路徑結合。 預設的相對路徑為：

- *swagger.js開啟*
- *swagger/v1/swagger.js開啟*
- */swagger.js開啟*
- */swagger/v1/swagger.json*
- *openapi.js開啟*
- */openapi.js開啟*

若要在您的環境中使用一組不同的搜尋路徑，請設定 `swagger.searchPaths` 喜好設定。 此值必須是以管線分隔的相對路徑清單。 例如：

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

除了取代預設清單之外，也可以藉由新增或移除路徑來修改清單。

若要將一或多個搜尋路徑新增至預設清單，請設定 `swagger.addToSearchPaths` 喜好設定。 此值必須是以管線分隔的相對路徑清單。 例如：

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

若要從預設清單中移除一個或多個搜尋路徑，請設定 `swagger.addToSearchPaths` 喜好設定。 此值必須是以管線分隔的相對路徑清單。 例如：

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a>測試 HTTP GET 要求

### <a name="synopsis"></a>概要

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引數

`PARAMETER`

相關控制器動作方法預期的路由參數 (如果有的話)。

### <a name="options"></a>選項

以下是使用 `get` 命令時可用的選項：

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>範例

若要發出 HTTP GET 要求：

1. 在支援的端點上執行 `get` 命令：

    ```console
    https://localhost:5001/people> get
    ```

    上述命令會顯示以下輸出格式：

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

1. 對 `get` 命令傳遞參數來擷取特定記錄：

    ```console
    https://localhost:5001/people> get 2
    ```

    上述命令會顯示以下輸出格式：

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

## <a name="test-http-post-requests"></a>測試 HTTP POST 要求

### <a name="synopsis"></a>概要

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引數

`PARAMETER`

相關控制器動作方法預期的路由參數 (如果有的話)。

### <a name="options"></a>選項

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>範例

若要發出 HTTP POST 要求：

1. 在支援的端點上執行 `post` 命令：

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    在上述命令中，`Content-Type` HTTP 要求標頭設定為指出 JSON 類型的要求本文媒體。 預設文字編輯器會開啟 *.tmp* 檔案，其中包含代表 HTTP 要求本文的 JSON 範本。 例如：

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > 若要設定預設文字編輯器，請參閱[設定預設文字編輯器](#set-the-default-text-editor)一節。

1. 修改 JSON 範本以滿足模型驗證需求：

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. 儲存 *.tmp* 檔案，然後關閉文字編輯器。 下列輸出會出現在命令殼層中：

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

## <a name="test-http-put-requests"></a>測試 HTTP PUT 要求

### <a name="synopsis"></a>概要

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引數

`PARAMETER`

相關控制器動作方法預期的路由參數 (如果有的話)。

### <a name="options"></a>選項

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>範例

若要發出 HTTP PUT 要求：

1. *選擇性*：執行 `get` 命令以在修改資料之前加以查看：

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

1. 在支援的端點上執行 `put` 命令：

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    在上述命令中，`Content-Type` HTTP 要求標頭設定為指出 JSON 類型的要求本文媒體。 預設文字編輯器會開啟 *.tmp* 檔案，其中包含代表 HTTP 要求本文的 JSON 範本。 例如：

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > 若要設定預設文字編輯器，請參閱[設定預設文字編輯器](#set-the-default-text-editor)一節。

1. 修改 JSON 範本以滿足模型驗證需求：

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. 儲存 *.tmp* 檔案，然後關閉文字編輯器。 下列輸出會出現在命令殼層中：

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *選擇性*：發出 `get` 命令以查看修改。 例如，如果您在文字編輯器中輸入 "揀選"，則會傳回 `get` 下列輸出：

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

## <a name="test-http-delete-requests"></a>測試 HTTP DELETE 要求

### <a name="synopsis"></a>概要

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引數

`PARAMETER`

相關控制器動作方法預期的路由參數 (如果有的話)。

### <a name="options"></a>選項

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>範例

若要發出 HTTP DELETE 要求：

1. *選擇性*：執行 `get` 命令以在修改資料之前加以查看：

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

1. 在支援的端點上執行 `delete` 命令：

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    上述命令會顯示以下輸出格式：

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *選擇性*：發出 `get` 命令以查看修改。 在此範例中，會傳回 `get` 下列輸出：

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

## <a name="test-http-patch-requests"></a>測試 HTTP PATCH 要求

### <a name="synopsis"></a>概要

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引數

`PARAMETER`

相關控制器動作方法預期的路由參數 (如果有的話)。

### <a name="options"></a>選項

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>測試 HTTP HEAD 要求

### <a name="synopsis"></a>概要

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引數

`PARAMETER`

相關控制器動作方法預期的路由參數 (如果有的話)。

### <a name="options"></a>選項

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>測試 HTTP OPTIONS 要求

### <a name="synopsis"></a>概要

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>引數

`PARAMETER`

相關控制器動作方法預期的路由參數 (如果有的話)。

### <a name="options"></a>選項

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>設定 HTTP 要求標頭

若要設定 HTTP 要求標頭，請使用下列其中一個方法：

* 與 HTTP 要求一同設定。 例如：

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    若使用上述方法，則各相異的 HTTP 要求標頭都需要自己的 `-h` 選項。

* 於傳送 HTTP 要求之前設定。 例如：

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    若在傳送要求之前設定標頭，則標頭會保留命令殼層工作階段的持續時間設定。 若要清除標頭，請提供空白值。 例如：
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a>測試安全的端點

HttpRepl 可透過下列方式支援保護端點的測試：

* 透過登入使用者的預設認證。
* 透過使用 HTTP 要求標頭。

### <a name="default-credentials"></a>預設認證

假設您要測試的 web API 是裝載在 IIS 中，並使用 Windows 驗證來保護。 您希望執行工具之使用者的認證流經所測試的 HTTP 端點。 若要傳遞已登入使用者的預設認證：

1. 將喜好設定設 `httpClient.useDefaultCredentials` 為 `true` ：

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. 先結束並重新啟動工具，再將另一個要求傳送至 web API。
 
### <a name="default-proxy-credentials"></a>預設 proxy 認證

假設您要測試的 web API 位於使用 Windows 驗證保護的 proxy 後方的案例。 您希望執行工具之使用者的認證流向 proxy。 若要傳遞已登入使用者的預設認證：

1. 將喜好設定設 `httpClient.proxy.useDefaultCredentials` 為 `true` ：

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. 先結束並重新啟動工具，再將另一個要求傳送至 web API。

### <a name="http-request-headers"></a>HTTP 要求標頭

支援的驗證和授權配置範例包括：

* basic authentication
* JWT 持有人權杖
* 摘要式驗證

例如，您可以使用下列命令，將持有人權杖傳送至端點：

```console
set header Authorization "bearer <TOKEN VALUE>"
```

若要存取 Azure 託管端點或使用 [AZURE REST API](/rest/api/azure/)，您需要持有人權杖。 使用下列步驟，透過 [AZURE CLI](/cli/azure/)為您的 azure 訂用帳戶取得持有人權杖。 HttpRepl 會在 HTTP 要求標頭中設定持有人權杖。 已抓取 Azure App Service Web 應用程式的清單。

1. 登入 Azure：

    ```azurecli
    az login
    ```

1. 使用下列命令取得您的訂用帳戶識別碼：

    ```azurecli
    az account show --query id
    ```

1. 複製您的訂用帳戶識別碼，然後執行下列命令：

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. 使用下列命令取得您的持有人權杖：

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. 透過 HttpRepl 連接到 Azure REST API：

    ```console
    httprepl https://management.azure.com
    ```

1. 設定 `Authorization` HTTP 要求標頭：

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. 流覽至訂用帳戶：

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. 取得訂用帳戶的 Azure App Service Web Apps 清單：

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    隨即顯示下列回應：

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

## <a name="toggle-http-request-display"></a>切換 HTTP 要求顯示

根據預設，會隱藏所傳送之 HTTP 要求的顯示。 您可以針對命令殼層工作階段的持續時間變更對應的設定。

### <a name="enable-request-display"></a>啟用要求顯示

透過執行 `echo on` 命令來檢視要傳送的 HTTP 要求。 例如：

```console
https://localhost:5001/people> echo on
Request echoing is on
```

目前工作階段中的後續 HTTP 要求會顯示要求標頭。 例如：

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

### <a name="disable-request-display"></a>停用要求顯示

透過執行 `echo off` 命令來隱藏要傳送的 HTTP 要求顯示。 例如：

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a>執行指令碼

如果您經常執行一組相同的 HttpRepl 命令，請考慮將它們儲存在文字檔中。 檔案中的命令所採用的格式，與在命令列上手動執行的命令相同。 您可使用 `run` 命令以批次的方式執行命令。 例如：

1. 建立包含一組以新行分隔命令的文字檔。 為了說明，請參考包含以下命令的 *people-script.txt* 檔案：

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. 執行 `run` 命令，傳入文字檔的路徑。 例如：

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    下列輸出會出現：

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

## <a name="clear-the-output"></a>清除輸出

若要移除 HttpRepl 工具寫入命令 shell 的所有輸出，請執行 `clear` 或 `cls` 命令。 為了說明，請參考包含以下輸出的命令殼層：

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

執行以下命令來清除輸出：

```console
https://localhost:5001/> clear
```

執行上述命令後，命令殼層只會包含以下輸出：

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a>其他資源

* [REST API 要求](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [HttpRepl GitHub 存放庫](https://github.com/dotnet/HttpRepl)
* [設定 Visual Studio 以啟動 HttpRepl](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-windows-to-launch-httprepl-on-f5)
* [設定 Visual Studio Code 以啟動 HttpRepl](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-code-to-launch-httprepl-on-debug)
* [將 Visual Studio for Mac 設定為啟動 HttpRepl](https://devblogs.microsoft.com/aspnet/httprepl-a-command-line-tool-for-interacting-with-restful-http-services/#configure-visual-studio-for-mac-to-launch-httprepl-as-a-custom-tool)
