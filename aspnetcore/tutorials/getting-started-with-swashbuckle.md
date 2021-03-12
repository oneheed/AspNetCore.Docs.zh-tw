---
title: Swashbuckle 與 ASP.NET Core 使用者入門
author: zuckerthoben
description: 了解如何將 Swashbuckle 新增至 ASP.NET Core Web API 專案，以整合 Swagger UI。
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
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
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: fdec03422f4a4517950ff6de2a0400df5307b40f
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588532"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Swashbuckle 與 ASP.NET Core 使用者入門

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([如何下載](xref:index#how-to-download-a-sample)) 

Swashbuckle 有三個主要元件：

* [Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/)：Swagger 物件模型和中介軟體，用來公開 `SwaggerDocument` 物件作為 JSON 端點。

* [Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/)：Swagger 產生器，可直接從您的路由、控制器和模型建置 `SwaggerDocument` 物件。 它通常會結合 Swagger 端點中介軟體，以便自動公開 Swagger JSON。

* [Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/)：Swagger UI 工具的內嵌版本。 它可以解譯 Swagger JSON，建置豐富、可自訂的 Web API 功能描述體驗。 其中包括公用方法的內建測試載入器。

## <a name="package-installation"></a>套件安裝

可使用下列方法新增 Swashbuckle：

### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 從 [套件管理員主控台] 視窗中：
  * 移至 [**查看**  >  **其他 Windows**  >  **套件管理員主控台**]
  * 巡覽至 *TodoApi.csproj* 檔案所在目錄
  * 執行以下命令：

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.6.3
    ```

* 從 [管理 NuGet 套件] 對話方塊中：
  * 以滑鼠右鍵按一下 **方案 Explorer** 中的專案 [  >  **管理 NuGet 封裝**]
  * 將 [套件來源] 設定為 "nuget.org"
  * 請確定已啟用 [包含發行前版本] 選項
  * 在搜尋方塊中輸入 "Swashbuckle.AspNetCore"
  * 從 [瀏覽] 索引標籤中選取最新的 "Swashbuckle.AspNetCore" 套件，並按一下 [安裝]

### <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在 [Solution Pad] > [新增套件...] 中，以滑鼠右鍵按一下 *Packages* 資料夾
* 將 [新增套件] 視窗的 [來源] 下拉式清單設定為 "nuget.org"
* 請確定已啟用 [選定發行前版本的套件] 選項
* 在搜尋方塊中輸入 "Swashbuckle.AspNetCore"
* 從結果窗格中選取最新的 "Swashbuckle.AspNetCore" 套件，並按一下 [新增套件]

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

從 [整合式終端機] 執行下列命令：

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

執行以下命令：

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.6.3
```

---

## <a name="add-and-configure-swagger-middleware"></a>新增和設定 Swagger 中介軟體

將 Swagger 產生器新增至 `Startup.ConfigureServices` 方法中的服務集合：

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

在 `Startup.Configure` 方法中，啟用中介軟體為產生的 JSON 文件和 Swagger UI 提供服務：

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> Swashbuckle 依賴 MVC <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> 來探索路由和端點。 如果專案呼叫 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A> ，就會自動探索路由和端點。 呼叫時 <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> ， <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> 必須明確呼叫方法。 如需詳細資訊，請參閱 [Swashbuckle、ApiExplorer 和 Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing)。

上述 `UseSwaggerUI` 方法呼叫會啟用[靜態檔案中介軟體](xref:fundamentals/static-files)。 如果以 .NET Framework 或 .NET Core 1.x 為目標，請將 [AspNetCore StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet 套件新增至專案。

啟動應用程式，並巡覽至 `http://localhost:<port>/swagger/v1/swagger.json`。 描述端點的已產生檔隨即出現，如 [) 的 OpenAPI 規格 (openapi.js](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson)所示。

您可以在 `http://localhost:<port>/swagger` 找到 Swagger UI。 透過 Swagger UI 探索 API，並將其併入其他程式。

> [!TIP]
> 若要在應用程式的根目錄 (`http://localhost:<port>/`) 上提供 Swagger UI，請將 `RoutePrefix` 屬性設為空字串：
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

若使用目錄搭配 IIS 或 反向 Proxy，請將 Swagger 端點設定為使用 `./` 前置詞的相對路徑。 例如： `./swagger/v1/swagger.json` 。 使用 `/swagger/v1/swagger.json` 指示應用程式在 URL 的真實根目錄 (若已使用，請加上路由前置詞) 尋找 JSON 檔案。 例如，請使用 `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json`，而不要使用 `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`。

> [!NOTE]
> 根據預設，Swashbuckle 會在 &mdash; 正式稱為 OpenAPI 規格的規格3.0 版中產生並公開 SWAGGER JSON。 若要支援回溯相容性，您可以改為選擇改為以2.0 格式公開 JSON。 此2.0 格式對於目前支援 OpenAPI 2.0 版的 Microsoft Power Apps 和 Microsoft Flow 等整合而言很重要。 若要加入宣告2.0 格式，請 `SerializeAsV2` 在中設定 `Startup.Configure` 下列屬性：
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a>自訂與擴充

Swagger 提供用來記載物件模型和自訂 UI 以符合佈景主題的選項。

在 `Startup` 類別中，加入下列命名空間：

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>API 資訊與描述

傳遞至 `AddSwaggerGen` 方法的組態動作會新增作者、授權和描述等資訊：

在 `Startup` 類別中，匯入下列命名空間以使用 `OpenApiInfo` 類別：

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

使用 `OpenApiInfo` 類別，修改顯示在 UI 中的資訊：

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Swagger UI 會顯示版本資訊：

![含有版本資訊的 Swagger UI：描述、作者，以及查看更多連結](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>XML 註解

XML 註解可以使用下列方式啟用：

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* 以滑鼠右鍵按一下 [方案總管] 中的專案，然後選取 [編輯 <專案名稱>.csproj]。
* 將醒目提示的程式碼行手動新增至 *.csproj* 檔案：

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* 以滑鼠右鍵按一下 [ **方案瀏覽器** ] 中的專案，然後選取 [ **屬性**]。
* 在 [**組建**] 索引標籤的 [**輸出**] 區段下，選取 [ **XML 檔** 檔案] 方塊。

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* 從 [Solution Pad] 中，按下 [控制項]，然後按一下專案名稱。 流覽至 [**工具**  >  **編輯** 檔案]。
* 將醒目提示的程式碼行手動新增至 *.csproj* 檔案：

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* 開啟 [專案選項] 對話方塊 > [組建]**[編譯器]** > 
* 勾選 [**一般選項**] 區段底下的 [**產生 xml 檔**] 方塊

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

將醒目提示的程式碼行手動新增至 *.csproj* 檔案：

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

將醒目提示的程式碼行手動新增至 *.csproj* 檔案：

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

啟用 XML 註解，可提供未記載之公用類型與成員的偵錯資訊。 未記載的類型和成員會以警告訊息表示。 例如，下列訊息指出警告碼 1591 的違規：

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

在專案檔中定義要忽略的警告碼清單 (以分號分隔)，即可隱藏警告。 將警告碼附加至 `$(NoWarn);` 也會套用 [C# 預設值](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)。

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

若只要針對特定成員隱藏，請將程式碼放在 [#pragma 警告](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning)前置處理器指示詞中。 這種方法適用于不應透過 API 檔公開的程式碼。在下列範例中，會忽略整個類別的警告程式碼 CS1591 `Program` 。 會在類別定義結尾處還原強制執行的警告碼。 以逗號分隔清單指定多個警告碼。

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

設定 Swagger 以使用先前指示所產生的 XML 檔案。 對於 Linux 或非 Windows 作業系統，檔案名稱和路徑可以區分大小寫。 例如，*TodoApi.XML* 檔案在 Windows 上有效，但在 CentOS 上則無效。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

在上述程式碼中，會使用 [反映](/dotnet/csharp/programming-guide/concepts/reflection) 來建立符合 web API 專案之 XML 檔案名的名稱。 [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) 屬性用來建構 XML 檔案的路徑。 某些 Swagger 功能 (例如輸入參數的結構描述，或來自相對應屬性的 HTTP 方法和回應碼) 能在不使用 XML 文件檔案的情況下運作。 對於大部分的功能 (也就是方法摘要，以及參數和回應碼的描述) 而言，皆必須使用 XML 檔案。

透過在區段標題新增描述，對動作新增三斜線註解，可增強 Swagger UI。 [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary)在動作上方新增元素 `Delete` ：

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Swagger UI 會顯示上述程式碼的 `<summary>` 項目的內部文字：

![顯示 XML 註解 'Deletes a specific TodoItem.' 的 Swagger UI 適用於 Delete 方法](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

UI 是由產生的 JSON 結構描述所驅動：

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

將 [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) 元素加入至 `Create` 動作方法檔。 它會補充 `<summary>` 項目中指定的資訊，並提供更強固的 Swagger UI。 `<remarks>` 項目內容可以包含文字、JSON 或 XML。

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

請注意使用這些額外註解的 UI 增強功能：

![顯示額外註解的 Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>資料註解

使用 [ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 命名空間中的屬性標記模型，以協助驅動 Swagger UI 元件。

將 `[Required]` 屬性 (attribute) 新增至 `TodoItem` 類別的 `Name` 屬性 (property)：

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

這個屬性的存在會變更 UI 行為，並改變基礎的 JSON 結構描述：

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

將 `[Produces("application/json")]` 屬性新增至 API 控制器。 其目的是要宣告控制器的動作支援回應內容類型為 *application/json*：

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

[ **回應內容類型** ] 下拉式清單會選取此內容類型作為控制器的 [取得動作] 的預設值：

![含有預設回應內容類型的 Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

當 Web API 中的資料註釋使用量增加時，UI 和 API 說明頁面會變得更清楚且更有用。

### <a name="describe-response-types"></a>描述回應類型

取用 Web API 的開發人員最關心的是傳回的內容 &mdash; 特別是回應類型和錯誤碼 (如果不是標準的話)。 回應類型和錯誤碼會在 XML 註解及資料註解中表示。

`Create` 動作在成功時會傳回 HTTP 201 狀態碼。 張貼的要求主體為 Null 時，會傳回 HTTP 400 狀態碼。 如果 Swagger UI 中沒有適當的文件，取用者便會缺乏對這些預期結果的了解。 在下列範例中新增強調顯示的那幾行來修正該問題：

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

現在，Swagger UI 清楚記載了預期的 HTTP 回應碼：

![顯示 POST 回應類別描述 'Returns the newly created Todo item'，並針對回應訊息下方的狀態碼和原因顯示 '400 - If the item is null' 的 Swagger UI](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

在 ASP.NET Core 2.2 或更新版本中，慣例可作為使用 `[ProducesResponseType]` 明確裝飾個別動作的替代方案。 如需詳細資訊，請參閱<xref:web-api/advanced/conventions>。

為了支援 `[ProducesResponseType]` 裝飾， [Swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) 會提供延伸模組，以啟用及擴充回應、架構和參數中繼資料。

::: moniker-end

### <a name="customize-the-ui"></a>自訂 UI

預設的 UI 都可以正常運作。 不過，API 的文件頁面應該表示您的品牌或佈景主題。 設定 Swashbuckle 元件商標時，需要新增資源來處理靜態檔案，然後建置裝載這些檔案的資料夾結構。

如果以 .NET Framework 或 .NET Core 1.x 為目標，請將 [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet 套件新增至您的專案：

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

如果以 .NET Core 2.x 為目標並使用[中繼套件](xref:fundamentals/metapackage)，則已安裝上述 NuGet 套件。

啟用靜態檔案中介軟體：

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

若要插入其他 CSS 樣式表單，請將它們新增至專案的 [ *wwwroot* ] 資料夾，並在中介軟體選項中指定相對路徑：

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```

## <a name="additional-resources"></a>其他資源

* [Microsoft 瞭解：使用 Swagger 檔改善 API 的開發人員體驗](/learn/modules/improve-api-developer-experience-with-swagger/)
