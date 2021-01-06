---
title: 使用 .NET 的 Code first gRPC 服務和用戶端
author: jamesnk
description: 瞭解使用 .NET 撰寫程式碼優先 gRPC 時的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880630"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a>使用 .NET 的 Code first gRPC 服務和用戶端

依 [James 牛頓](https://twitter.com/jamesnk) 和 [Marc Gravell](https://twitter.com/marcgravell)

程式碼優先 gRPC 使用 .NET 類型來定義服務和訊息合約。

當整個系統使用 .NET 時，程式碼優先是不錯的選擇：

* .Net 服務和資料合約類型可在 .NET 伺服器和用戶端之間共用。
* 避免需要在檔案和程式碼產生中定義合約 `.proto` 。

在具有多種語言的多語言系統中，不建議使用 Code first。 .NET 服務和資料合約類型無法與 non-.NET 平臺搭配使用。 若要呼叫使用 code first 撰寫的 gRPC 服務，其他平臺必須建立 `.proto` 符合服務的合約。

## <a name="protobuf-netgrpc"></a>protobuf-net。Grpc

> [!IMPORTANT]
> 如需 protobuf 的協助。Grpc，請造訪 [protobuf-網路。Grpc 網站](https://protobuf-net.github.io/protobuf-net.Grpc/) ，或在 protobuf 網路上建立問題 [。Grpc GitHub 存放庫](https://github.com/protobuf-net/protobuf-net.Grpc)。

[protobuf-net。Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) 是一個小組專案，Microsoft 並不支援。 它會將程式碼優先支援新增至 `Grpc.AspNetCore` 和 `Grpc.Net.Client` 。 它會使用以屬性標注的 .NET 類型來定義應用程式的 gRPC 服務和訊息。

建立程式碼優先 gRPC 服務的第一個步驟是定義程式碼合約：

* 建立將由伺服器和用戶端共用的新專案。
* 新增 [protobuf-網路。Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 套件參考。
* 建立服務和資料合約類型。

[!code-csharp[](code-first/Contracts.cs)]

上述程式碼：

* 定義 `HelloRequest` 和 `HelloReply` 訊息。
* `IGreeterService`使用一元 gRPC 方法定義合約介面 `SayHelloAsync` 。

服務合約會在伺服器上執行，並從用戶端呼叫。 服務介面上定義的方法必須符合特定簽章，這取決於它們是一元、伺服器串流、用戶端資料流程或雙向串流。

如需定義服務合約的詳細資訊，請參閱 [protobuf-網路。Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted)使用者入門檔。

## <a name="create-a-code-first-grpc-service"></a>建立程式碼優先 gRPC 服務

將 gRPC 程式碼優先服務新增至 ASP.NET Core 應用程式：

* 新增 [protobuf-網路。Grpc. AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) 封裝參考。
* 加入共用程式碼合約專案的參考。

建立新的檔案 `GreeterService.cs` ，並執行 `IGreeterService` 服務介面：

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

更新檔案 `Startup.cs` ：

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

在上述程式碼中：

* `AddCodeFirstGrpc` 註冊啟用程式碼優先的服務。
* `MapGrpcService<GreeterService>` 加入程式碼優先的服務端點。

使用程式碼優先的 gRPC 服務和檔案 `.proto` 可以並存存在於相同的應用程式中。 所有 gRPC 服務都會使用 [gRPC 服務](xref:grpc/configuration#configure-services-options)設定。

## <a name="create-a-code-first-grpc-client"></a>建立程式碼優先 gRPC 用戶端

程式碼優先 gRPC 用戶端會使用服務合約來呼叫 gRPC 服務。 若要使用程式碼優先用戶端呼叫 gRPC 服務：

* 新增 [protobuf-網路。Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) 套件參考。
* 加入共用程式碼合約專案的參考。

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

上述程式碼：

* 建立 gRPC 通道。
* 使用擴充方法，從通道建立程式碼優先用戶端 `CreateGrpcService<IGreeterService>` 。
* 使用呼叫 gRPC 服務 `SayHelloAsync` 。

程式碼優先 gRPC 用戶端是從通道建立。 就像一般用戶端一樣，程式碼優先用戶端會使用其 [通道](xref:grpc/configuration#configure-client-options)設定。

## <a name="additional-resources"></a>其他資源

* [protobuf-net。Grpc 網站](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [protobuf-net。Grpc GitHub 存放庫](https://github.com/protobuf-net/protobuf-net.Grpc)
