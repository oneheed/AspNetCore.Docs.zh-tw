---
title: 使用 dotnet-grpc 管理 Protobuf 參考
author: juntaoluo
description: 瞭解如何使用 dotnet grpc 的全域工具來新增、更新、移除及列出 Protobuf 參考。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059906"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>使用 dotnet-grpc 管理 Protobuf 參考

作者：[John Luo](https://github.com/juntaoluo)

`dotnet-grpc` 是一種 .NET Core 通用工具，可用於管理 [Protobuf (*proto*)](xref:grpc/basics#proto-file) .net gRPC 專案中的參考。 您可以使用此工具來新增、重新整理、移除及列出 Protobuf 參考。

## <a name="installation"></a>安裝

若要安裝 `dotnet-grpc` [.Net Core 通用工具](/dotnet/core/tools/global-tools)，請執行下列命令：

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>新增參考

`dotnet-grpc` 可以用來將 Protobuf 參考當做 `<Protobuf />` 專案新增至 *.csproj* 檔案：

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Protobuf 參考可用來產生 c # 用戶端和/或伺服器資產。 此 `dotnet-grpc` 工具可以：

* 從磁片上的本機檔案建立 Protobuf 參考。
* 從 URL 指定的遠端檔案建立 Protobuf 參考。
* 確定已將正確的 gRPC 套件相依性新增至專案。

例如，將 `Grpc.AspNetCore` 套件新增至 web 應用程式。 `Grpc.AspNetCore` 包含 gRPC 伺服器和用戶端程式庫和工具支援。 或者， `Grpc.Net.Client` `Grpc.Tools` `Google.Protobuf` 只會將包含 gRPC 用戶端程式庫和工具支援的和套件新增至主控台應用程式。

### <a name="add-file"></a>新增檔案

此 `add-file` 命令可用來將磁片上的本機檔案新增為 Protobuf 參考。 提供的檔案路徑：

* 可以相對於目前的目錄或絕對路徑。
* 可能包含以模式為基礎的檔案萬用字元的[萬用字元。](https://wikipedia.org/wiki/Glob_(programming))

如果有任何檔案位於專案目錄之外， `Link` 就會加入專案，以在 Visual Studio 的資料夾下顯示檔案 `Protos` 。

### <a name="usage"></a>使用方式

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>引數

| 引數 | 描述 |
|-|-|
| files | Protobuf 檔參考。 這些可以是本機 protobuf 檔案的 glob 路徑。 |

#### <a name="options"></a>選項

| Short 選項 | Long 選項 | 描述 |
|-|-|-|
| -p | --project | 要操作之專案檔的路徑。 如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。
| -S | --服務 | 應產生的 gRPC 服務類型。 如果 `Default` 指定了， `Both` 就會用於 Web 專案，並用於 `Client` 非 Web 專案。 接受的值為 `Both` 、 `Client` 、 `Default` 、 `None` 、 `Server` 。
| -i | --其他-匯入-目錄 | 解析 protobuf 檔匯入時要使用的其他目錄。 這是以分號分隔的路徑清單。
| | --存取 | 要用於產生的 c # 類別的存取修飾詞。 預設值是 `Public`。 接受的值為 `Internal` 和 `Public`。

### <a name="add-url"></a>新增 URL

此 `add-url` 命令是用來新增來源 URL 所指定的遠端檔案，做為 Protobuf 參考。 必須提供檔案路徑，以指定遠端檔案的下載位置。 檔案路徑可以相對於目前的目錄或絕對路徑。 如果檔案路徑是在專案目錄外， `Link` 則會新增一個專案，以在 Visual Studio 的虛擬資料夾下顯示該檔案 `Protos` 。

### <a name="usage"></a>使用方式

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>引數

| 引數 | 描述 |
|-|-|
| url | 遠端 protobuf 檔的 URL。 |

#### <a name="options"></a>選項

| Short 選項 | Long 選項 | 描述 |
|-|-|-|
| -o | --output | 指定遠端 protobuf 檔的下載路徑。 這是必要選項。
| -p | --project | 要操作之專案檔的路徑。 如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。
| -S | --服務 | 應產生的 gRPC 服務類型。 如果 `Default` 指定了， `Both` 就會用於 Web 專案，並用於 `Client` 非 Web 專案。 接受的值為 `Both` 、 `Client` 、 `Default` 、 `None` 、 `Server` 。
| -i | --其他-匯入-目錄 | 解析 protobuf 檔匯入時要使用的其他目錄。 這是以分號分隔的路徑清單。
| | --存取 | 要用於產生的 c # 類別的存取修飾詞。 預設值為 `Public`。 接受的值為 `Internal` 和 `Public`。

## <a name="remove"></a>移除

此 `remove` 命令可用來移除 *.csproj* 檔案中的 Protobuf 參考。 命令接受路徑引數和來源 Url 作為引數。 工具：

* 只會移除 Protobuf 參考。
* 不會刪除 *proto* 檔案，即使它原本是從遠端 URL 下載也一樣。

### <a name="usage"></a>使用方式

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>引數

| 引數 | 描述 |
|-|-|
| 參考 | 要移除之 protobuf 參考的 Url 或檔案路徑。 |

### <a name="options"></a>選項

| Short 選項 | Long 選項 | 描述 |
|-|-|-|
| -p | --project | 要操作之專案檔的路徑。 如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。

## <a name="refresh"></a>Refresh

此 `refresh` 命令可用來從來源 URL 的最新內容更新遠端參考。 下載檔案路徑和來源 URL 都可以用來指定要更新的參考。 附註：

* 檔案內容的雜湊會進行比較，以判斷是否應該更新本機檔案。
* 不會比較時間戳記資訊。

如果需要更新，此工具一律會將本機檔案取代為遠端檔案。

### <a name="usage"></a>使用方式

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>引數

| 引數 | 描述 |
|-|-|
| 參考 | 應更新之遠端 protobuf 參考的 Url 或檔案路徑。 將此引數保留為空白，以重新整理所有遠端參考。 |

### <a name="options"></a>選項

| Short 選項 | Long 選項 | 描述 |
|-|-|-|
| -p | --project | 要操作之專案檔的路徑。 如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。
| | --試執行 | 輸出會更新而不需要下載任何新內容的檔案清單。

## <a name="list"></a>清單

此 `list` 命令會用來顯示專案檔中的所有 Protobuf 參考。 如果資料行的所有值都是預設值，則可能會省略該資料行。

### <a name="usage"></a>使用方式

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>選項

| Short 選項 | Long 選項 | 描述 |
|-|-|-|
| -p | --project | 要操作之專案檔的路徑。 如果未指定檔案，此命令會在目前的目錄中搜尋其中一個檔案。

## <a name="additional-resources"></a>其他資源

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
