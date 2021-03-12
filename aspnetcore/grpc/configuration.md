---
title: 適用于 .NET 設定的 gRPC
author: jamesnk
description: 瞭解如何設定 .NET 應用程式的 gRPC。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/23/2020
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
uid: grpc/configuration
ms.openlocfilehash: 3f03d774a2223dc1fc08adcbc85cdc9a2b63dea0
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588889"
---
# <a name="grpc-for-net-configuration"></a>適用于 .NET 設定的 gRPC

## <a name="configure-services-options"></a>設定服務選項

gRPC 服務是 `AddGrpc` 在 *Startup.cs* 中設定。 下表描述設定 gRPC services 的選項：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| MaxSendMessageSize | `null` | 可以從伺服器傳送的訊息大小上限（以位元組為單位）。 嘗試傳送超過所設定之訊息大小上限的訊息，會導致例外狀況。 當設定為時 `null` ，訊息大小是無限制的。 |
| MaxReceiveMessageSize | 4 MB | 伺服器可以接收的訊息大小上限（以位元組為單位）。 如果伺服器收到超過此限制的訊息，則會擲回例外狀況。 提高此值可讓伺服器接收較大的訊息，但可能會對記憶體耗用量造成負面影響。 當設定為時 `null` ，訊息大小是無限制的。 |
| EnableDetailedErrors | `false` | 如果為，則在 `true` 服務方法中擲回例外狀況時，會將詳細的例外狀況訊息傳回給用戶端。 預設為 `false`。 將設定 `EnableDetailedErrors` 為 `true` 可能會洩漏機密資訊。 |
| CompressionProviders | gzip | 壓縮提供者的集合，用來壓縮和解壓縮訊息。 您可以建立自訂壓縮提供者，並將其加入至集合。 預設設定的提供者支援 **gzip** 壓縮。 |
| <span style="word-break:normal;word-wrap:normal">ResponseCompressionAlgorithm</span> | `null` | 壓縮演算法，用來壓縮從伺服器傳送的訊息。 演算法必須符合中的壓縮提供者 `CompressionProviders` 。 若要讓演算法壓縮回應，用戶端必須透過在 **grpc 接受編碼** 標頭中傳送來指出其支援演算法。 |
| ResponseCompressionLevel | `null` | 壓縮層級，用來壓縮從伺服器傳送的訊息。 |
| 攔截器 | 無 | 在每個 gRPC 呼叫中執行的攔截器集合。 攔截器會依註冊的循序執行。 全域設定的攔截器會在針對單一服務設定攔截器之前執行。 如需 gRPC 攔截器的詳細資訊，請參閱 [GRPC 攔截器與中介軟體](xref:grpc/migration#grpc-interceptors-vs-middleware)。 |
| IgnoreUnknownServices | `false` | 如果 `true` 為，則對未知服務和方法的呼叫不會傳回未產生的狀態，且要求會傳遞至 ASP.NET Core 中的下一個已註冊中介軟體。 |

您可以針對所有服務設定選項，方法是提供選項委派給 `AddGrpc` 中的呼叫 `Startup.ConfigureServices` ：

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup.cs?name=snippet)]

單一服務的選項會覆寫中提供的全域選項 `AddGrpc` ，可使用下列方法進行設定 `AddServiceOptions<TService>` ：

[!code-csharp[](~/grpc/configuration/sample/GrcpService/Startup2.cs?name=snippet)]

## <a name="configure-client-options"></a>設定用戶端選項

gRPC 用戶端設定設為開啟 `GrpcChannelOptions` 。 下表描述設定 gRPC 通道的選項：

| 選項 | 預設值 | 描述 |
| ------ | ------------- | ----------- |
| HttpHandler | 新執行個體 | `HttpMessageHandler`用來進行 gRPC 呼叫的。 您可以設定用戶端來設定自訂 `HttpClientHandler` 或將額外的處理常式新增至 HTTP 管線以進行 gRPC 呼叫。 如果未 `HttpMessageHandler` 指定，則 `HttpClientHandler` 會針對具有自動處置的通道建立新的實例。 |
| HttpClient | `null` | `HttpClient`用來進行 gRPC 呼叫的。 這項設定是的替代方案 `HttpHandler` 。 |
| DisposeHttpClient | `false` | 如果設定為 `true` 且 `HttpMessageHandler` 已指定或，則在 `HttpClient` `HttpHandler` `HttpClient` 處置時，會分別處置或 `GrpcChannel` 。 |
| Server.loggerfactory | `null` | `LoggerFactory`用戶端用來記錄 gRPC 呼叫相關資訊的。 您 `LoggerFactory` 可以從相依性插入或使用建立實例來解析實例 `LoggerFactory.Create` 。 如需設定記錄的範例，請參閱 <xref:grpc/diagnostics#grpc-client-logging> 。 |
| MaxSendMessageSize | `null` | 可從用戶端傳送的訊息大小上限（以位元組為單位）。 嘗試傳送超過所設定之訊息大小上限的訊息，會導致例外狀況。 當設定為時 `null` ，訊息大小是無限制的。 |
| MaxReceiveMessageSize | 4 MB | 用戶端可以接收的訊息大小上限（以位元組為單位）。 如果用戶端收到超過此限制的訊息，則會擲回例外狀況。 提高此值可讓用戶端接收較大的訊息，但可能會對記憶體耗用量造成負面影響。 當設定為時 `null` ，訊息大小是無限制的。 |
| 認證 | `null` | `ChannelCredentials` 執行個體。 認證是用來將驗證中繼資料新增至 gRPC 呼叫。 |
| CompressionProviders | gzip | 壓縮提供者的集合，用來壓縮和解壓縮訊息。 您可以建立自訂壓縮提供者，並將其加入至集合。 預設設定的提供者支援 **gzip** 壓縮。 |
| ThrowOperationCanceledOnCancellation | `false` | 如果設定為 `true` ，用戶端 <xref:System.OperationCanceledException> 會在呼叫取消或超過期限時擲回。 |
| MaxRetryAttempts | 5 | 重試次數上限。 此值會限制在服務設定中指定的任何重試和避險嘗試值。單獨設定此值時，不會重試。 在服務設定中會啟用重試，這可以使用來完成 `ServiceConfig` 。 `null`值會移除重試嘗試次數上限。 如需重試的詳細資訊，請參閱 <xref:grpc/retries> 。 |
| MaxRetryBufferSize | 16 MB | 在重試或避險呼叫時，可用來儲存傳送訊息的最大緩衝區大小（以位元組為單位）。 如果超過緩衝區限制，就不會再重試一次，而且所有避險呼叫都會取消。 這項限制會套用到使用通道進行的所有呼叫。 `null`值會移除重試緩衝區大小上限。 |
| <span style="word-break:normal;word-wrap:normal">MaxRetryBufferPerCallSize</span> | 1 MB | 在重試或避險呼叫時，可用來儲存傳送訊息的最大緩衝區大小（以位元組為單位）。 如果超過緩衝區限制，就不會再重試一次，而且所有避險呼叫都會取消。 這項限制會套用至一個呼叫。 `null`值會移除每個呼叫的重試緩衝區大小上限。 |
| ServiceConfig | `null` | GRPC 通道的服務設定。 服務設定可以用來設定 [gRPC 重試](xref:grpc/retries)。 |

下列程式碼：

* 設定通道上的最大傳送和接收訊息大小。
* 建立用戶端。

[!code-csharp[](~/grpc/configuration/sample/Program.cs?name=snippet&highlight=3-8)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>其他資源

* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/diagnostics>
* <xref:tutorials/grpc/grpc-start>
