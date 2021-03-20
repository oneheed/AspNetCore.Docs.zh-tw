---
title: 使用 gRPC 重試進行暫時性錯誤處理
author: jamesnk
description: 瞭解如何在 .NET 中使用重試來建立復原性、容錯的 gRPC 呼叫。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 03/18/2021
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
uid: grpc/retries
ms.openlocfilehash: 18ffab896adb6c1c5737b1e73adf06b22ca1ccb2
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711564"
---
# <a name="transient-fault-handling-with-grpc-retries"></a>使用 gRPC 重試進行暫時性錯誤處理

依 [James 牛頓](https://twitter.com/jamesnk)

gRPC 重試是一項功能，可讓 gRPC 用戶端自動重試失敗的呼叫。 本文討論如何設定重試原則，以在 .NET 中建立具有復原能力的容錯 gRPC 應用程式。

gRPC 重試需要 [gRPC .net。用戶端](https://www.nuget.org/packages/Grpc.Net.Client) 版本2.36.0 或更新版本。

## <a name="transient-fault-handling"></a>暫時性錯誤處理

暫時性錯誤可能會中斷 gRPC 呼叫。 暫時性錯誤包括：

* 暫時遺失網路連線能力。
* 暫時無法使用服務。
* 由於伺服器負載而造成的超時。

當 gRPC 呼叫中斷時，用戶端會擲回， `RpcException` 並提供錯誤的詳細資料。 用戶端應用程式必須攔截例外狀況，並選擇處理錯誤的方式。

```csharp
var client = new Greeter.GreeterClient(channel);
try
{
    var response = await client.SayHelloAsync(
        new HelloRequest { Name = ".NET" });

    Console.WriteLine("From server: " + response.Message);
}
catch (RpcException ex)
{
    // Write logic to inspect the error and retry
    // if the error is from a transient fault.
}
```

複製整個應用程式的重試邏輯很詳細，而且容易出錯。 幸運的是，.NET gRPC 用戶端具有自動重試的內建支援。

## <a name="configure-a-grpc-retry-policy"></a>設定 gRPC 重試原則

建立 gRPC 通道時，會設定一次重試原則：

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    RetryPolicy = new RetryPolicy
    {
        MaxAttempts = 5,
        InitialBackoff = TimeSpan.FromSeconds(1),
        MaxBackoff = TimeSpan.FromSeconds(5),
        BackoffMultiplier = 1.5,
        RetryableStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

上述程式碼：

* 建立 `MethodConfig`。 您可以針對每個方法設定重試原則，而且方法會使用屬性來進行比對 `Names` 。 這個方法是使用設定的 `MethodName.Default` ，因此會套用至此通道所呼叫的所有 gRPC 方法。
* 設定重試原則。 此原則會指示用戶端自動重試因狀態碼而失敗的 gRPC 呼叫 `Unavailable` 。
* 藉由設定，將建立的通道設定為使用重試原則 `GrpcChannelOptions.ServiceConfig` 。

使用通道建立的 gRPC 用戶端會自動重試失敗的呼叫：

```csharp
var client = new Greeter.GreeterClient(channel);
var response = await client.SayHelloAsync(
    new HelloRequest { Name = ".NET" });

Console.WriteLine("From server: " + response.Message);
```

### <a name="grpc-retry-options"></a>gRPC 重試選項

下表說明設定 gRPC 重試原則的選項：

| 選項 | Description |
| ------ | ----------- |
| `MaxAttempts` | 呼叫嘗試次數的上限，包括原始嘗試。 此值受限於 `GrpcChannelOptions.MaxRetryAttempts` 預設值為5。 值是必要的，且必須大於1。 |
| `InitialBackoff` | 重試嘗試之間的初始輪詢延遲。 0和目前輪詢之間的隨機延遲，會決定下次重試嘗試的時間。 每次嘗試之後，目前的輪詢會乘以 `BackoffMultiplier` 。 值是必要的，而且必須大於零。 |
| `MaxBackoff` | 最大輪詢會將指數輪詢成長的上限放在最大值。 值是必要的，而且必須大於零。 |
| `BackoffMultiplier` | 在每次重試嘗試之後，輪詢將乘以此值，並會在乘數大於1時以指數方式增加。 值是必要的，而且必須大於零。 |
| `RetryableStatusCodes` | 狀態碼的集合。 因符合狀態而失敗的 gRPC 呼叫會自動重試。 如需狀態碼的詳細資訊，請參閱 [狀態碼及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。 至少需要一個可重試的狀態碼。 |

## <a name="hedging"></a>對沖

避險是替代的重試策略。 避險可主動傳送單一 gRPC 呼叫的多個複本，而不需要等待回應。 Hedged gRPC 呼叫可能會在伺服器上執行多次，並使用第一個成功的結果。 請務必只針對可安全執行多次且不會造成負面影響的方法啟用避險。

相較于重試，避險具有優缺點： 

* 避險的優點是，它可能會更快地傳回成功的結果。 它允許多個同時 gRPC 的呼叫，並且會在第一個成功的結果可用時完成。 
* 避險的缺點是可能會很浪費。 可能會進行多個呼叫，且全部都成功。 只會使用第一個結果，並捨棄其餘部分。

## <a name="configure-a-grpc-hedging-policy"></a>設定 gRPC 避險原則

避險原則的設定就像是重試原則。 請注意，避險原則無法與重試原則合併。

```csharp
var defaultMethodConfig = new MethodConfig
{
    Names = { MethodName.Default },
    HedgingPolicy = new HedgingPolicy
    {
        MaxAttempts = 5,
        NonFatalStatusCodes = { StatusCode.Unavailable }
    }
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    ServiceConfig = new ServiceConfig { MethodConfigs = { defaultMethodConfig } }
});
```

### <a name="grpc-hedging-options"></a>gRPC 避險選項

下表說明設定 gRPC 避險原則的選項：

| 選項 | Description |
| ------ | ----------- |
| `MaxAttempts` | 避險原則會傳送到此次數的呼叫。 `MaxAttempts` 表示所有嘗試的總次數，包括原始嘗試。 此值受限於 `GrpcChannelOptions.MaxRetryAttempts` 預設值為5。 值是必要的，而且必須是2或以上。 |
| `HedgingDelay` | 第一個呼叫會立即傳送，但後續的避險呼叫將會被此值延遲。 當延遲設定為零或時 `null` ，會立即傳送所有 hedged 呼叫。 預設值為零。 |
| `NonFatalStatusCodes` | 表示其他籬呼叫仍可能成功的狀態碼集合。 如果伺服器傳回非嚴重的狀態碼，hedged 呼叫將會繼續。 否則，將會取消未完成的要求，並將錯誤傳回給應用程式。 如需狀態碼的詳細資訊，請參閱 [狀態碼及其在 gRPC 中的用法](https://grpc.github.io/grpc/core/md_doc_statuscodes.html)。 |

## <a name="additional-resources"></a>其他資源

* <xref:grpc/client>
* [重試一般指引-雲端應用程式的最佳作法](/azure/architecture/best-practices/transient-faults)
