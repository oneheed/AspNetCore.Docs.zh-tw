---
title: 搭配 C# 的 gRPC 服務
author: juntaoluo
description: '瞭解使用 c # 撰寫 gRPC 服務時的基本概念。'
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/09/2020
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
uid: grpc/basics
ms.openlocfilehash: 4968ac889cd3b4e0780ce73dc729d0107a416932
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061011"
---
# <a name="grpc-services-with-c"></a>使用 C gRPC 服務\#

本檔概述以 c # 撰寫 [gRPC](https://grpc.io/docs/guides/) 應用程式所需的概念。 本文所涵蓋的主題適用于以 [C 核心](https://grpc.io/blog/grpc-stacks)為基礎和 ASP.NET Core 為基礎的 gRPC 應用程式。

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="proto-file"></a>proto 檔案

gRPC 使用合約優先的方法來開發 API。 根據預設， (protobuf) 的通訊協定緩衝區作為介面定義語言 (IDL) 。 *\* Proto* 檔案包含：

* GRPC 服務的定義。
* 用戶端與伺服器之間傳送的訊息。

如需 protobuf 檔語法的詳細資訊，請參閱 <xref:grpc/protobuf> 。

例如，請考慮開始 [使用 gRPC service](xref:tutorials/grpc/grpc-start)中所使用的 *歡迎* 檔：

* 定義 `Greeter` 服務。
* `Greeter`服務會定義 `SayHello` 呼叫。
* `SayHello` 傳送 `HelloRequest` 訊息並接收 `HelloReply` 訊息：

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a>將 proto 檔案新增至 C \# 應用程式

藉由將 *\* proto* 檔案新增至專案群組，即可將該檔案包含在專案中 `<Protobuf>` ：

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

根據預設， `<Protobuf>` 參考會產生實體用戶端和服務基類。 參考專案的 `GrpcServices` 屬性可以用來限制產生 c # 資產。 有效 `GrpcServices` 的選項包括：

* `Both` 不存在時 (預設值) 
* `Server`
* `Client`
* `None`

## <a name="c-tooling-support-for-proto-files"></a>適用于 proto 檔案的 c # 工具支援

需要工具套件 [Grpc](https://www.nuget.org/packages/Grpc.Tools/) ，才能從 *\* Proto* 檔案產生 c # 資產。 產生的資產 (檔) ：

* 每次建立專案時，會根據需要來產生。
* 不會加入至專案或簽入原始檔控制。
* 是包含在 *obj* 目錄中的組建成品。

伺服器和用戶端專案都需要此封裝。 `Grpc.AspNetCore`中繼套件包含的參考 `Grpc.Tools` 。 您可以 `Grpc.AspNetCore` 使用 Visual Studio 中的封裝管理員，或將加入至專案檔，以新增伺服器專案 `<PackageReference>` ：

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

用戶端專案應該直接參考 `Grpc.Tools` 使用 gRPC 用戶端所需的其他套件。 在執行時間不需要工具套件，因此相依性會標示為 `PrivateAssets="All"` ：

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a>產生的 c # 資產

工具套件會產生 c # 類型，代表包含的 *\* proto* 檔案中定義的訊息。

針對伺服器端資產，會產生抽象服務基底類型。 基底類型包含 gRPC 中包含的所有呼叫的定義 *。* 建立衍生自此基底類型的具體服務執行，並執行 gRPC 呼叫的邏輯。 如 `greet.proto` 先前所述的範例， `GreeterBase` 會產生包含虛擬方法的抽象型別 `SayHello` 。 具體的實作為會 `GreeterService` 覆寫方法，並執行處理 gRPC 呼叫的邏輯。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

若為用戶端資產，則會產生具體的用戶端類型。 在 *proto* 檔中的 gRPC 呼叫會轉譯成具象型別上的方法，可以呼叫。 針對 `greet.proto` ，先前所述的範例 `GreeterClient` 會產生實體型別。 呼叫 `GreeterClient.SayHelloAsync` 以初始化對伺服器的 gRPC 呼叫。

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

依預設，會針對專案群組中包含的每個 *\* proto* 檔案產生伺服器和用戶端資產 `<Protobuf>` 。 為確保伺服器專案中只會產生伺服器資產， `GrpcServices` 屬性會設定為 `Server` 。

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

同樣地，在 `Client` 用戶端專案中，屬性會設定為。

## <a name="additional-resources"></a>其他資源

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
