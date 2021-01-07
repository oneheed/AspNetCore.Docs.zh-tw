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
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a>使用 OpenAPI 工具開發 ASP.NET Core 應用程式

依 Ryan Brandenburg

[Dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) 是一種 [.Net Core 通用工具](/dotnet/core/tools/global-tools) ，可用於管理專案內的 [openapi](https://github.com/OAI/OpenAPI-Specification) 參考。

## <a name="installation"></a>安裝

若要安裝 `Microsoft.dotnet-openapi` ，請執行下列命令：

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a>加

使用此頁面上的任何命令新增 OpenAPI 參考，會將 `<OpenApiReference />` 類似下列的元素加入 *.csproj* 檔案：

```xml
<OpenApiReference Include="openapi.json" />
```

需要上述參考，應用程式才能呼叫所產生的用戶端程式代碼。

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

### <a name="add-file"></a>新增檔案

#### <a name="options"></a>選項

| Short 選項| Long 選項| 描述 | 範例 |
|-------|------|-------|---------|
| -p|--updateProject | 要操作的專案。 |dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -c|--程式碼產生器| 要套用至參考的程式碼產生器。 選項為 `NSwagCSharp` 和 `NSwagTypeScript` 。 如果 `--code-generator` 未指定，則工具會預設為 `NSwagCSharp` 。|dotnet openapi 將檔案新增 .\OpenApi.js于程式碼產生器
| -H|--help|顯示說明資訊|dotnet openapi add file--help|

#### <a name="arguments"></a>引數

|  引數  | 描述 | 範例 |
|-------------|-------------|---------|
| 來源檔案 | 要從中建立參考的來源。 必須是 OpenAPI 檔。 |dotnet openapi add file *.\OpenAPI.json* |

### <a name="add-url"></a>新增 URL

#### <a name="options"></a>選項

| Short 選項| Long 選項| 描述 | 範例 |
|-------|------|-------------|---------|
| -p|--updateProject | 要操作的專案。 |dotnet openapi 新增 url *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -o|--output-file | 要在何處放置 OpenAPI 檔案的本機複本。 |dotnet openapi 新增 url `https://contoso.com/openapi.json` *--output-file myclient.json* |
| -c|--程式碼產生器| 要套用至參考的程式碼產生器。 選項為 `NSwagCSharp` 和 `NSwagTypeScript` 。 |dotnet openapi 新增 url `https://contoso.com/openapi.json` --程式碼產生器
| -H|--help|顯示說明資訊|dotnet openapi 新增 url--說明|

#### <a name="arguments"></a>引數

|  引數  | 描述 | 範例 |
|-------------|-------------|---------|
| 來源-URL | 要從中建立參考的來源。 必須是 URL。 |dotnet openapi 新增 url `https://contoso.com/openapi.json` |

## <a name="remove"></a>移除

從 *.csproj* 檔案移除符合指定檔案名的 OpenAPI 參考。 移除 OpenAPI 的參考時，不會產生用戶端。 將會刪除本機 *json* 和 *yaml* 檔案。

### <a name="options"></a>選項

| Short 選項| Long 選項| 描述| 範例 |
|-------|------|------------|---------|
| -p|--updateProject | 要操作的專案。 |dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json |
| -H|--help|顯示說明資訊|dotnet openapi remove--help|

### <a name="arguments"></a>引數

|  引數  | 描述| 範例 |
| ------------|------------|---------|
| 來源檔案 | 要移除參考的來源。 |dotnet openapi 移除 *.\OpenAPI.js開啟* |

## <a name="refresh"></a>Refresh

重新整理使用下載 URL 的最新內容所下載之檔案的本機版本。

### <a name="options"></a>選項

| Short 選項| Long 選項| 描述 | 範例 |
|-------|------|-------------|---------|
| -p|--updateProject | 要操作的專案。 | dotnet openapi refresh *--updateProject .\Ref.csproj*`https://contoso.com/openapi.json` |
| -H|--help|顯示說明資訊|dotnet openapi refresh--help|

### <a name="arguments"></a>引數

|  引數  | 描述 | 範例 |
| ------------|-------------|---------|
| 來源-URL | 重新整理參考的 URL。 | dotnet openapi refresh `https://contoso.com/openapi.json` |
