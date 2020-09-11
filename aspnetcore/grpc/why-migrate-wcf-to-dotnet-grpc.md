---
title: 為何要將 WCF 遷移至 ASP.NET Core gRPC
author: markrendle
description: 本文提供 ASP.NET Core gRPC 適用于 Windows Communication Foundation (WCF) 開發人員想要遷移到新式架構和平臺的相關摘要。
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
no-loc:
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
uid: grpc/wcf
ms.openlocfilehash: 811e6037b058b26fcf91063123d04d448a9a28a8
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90012755"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>Windows Communication Foundation (WCF) 開發人員的 gRPC

本文提供 ASP.NET Core gRPC 適用于 Windows Communication Foundation (WCF) 開發人員想要遷移到新式架構和平臺的相關摘要。

## <a name="comparison-to-wcf"></a>與 WCF 的比較

雖然 gRPC 的實和方法不同，但使用 gRPC 開發和取用服務的經驗對於 WCF 開發人員來說應該是直覺的。 WCF 和 gRPC 是 RPC (遠端程序呼叫，) 具有相同目標的架構：

* 讓它可以像用戶端和伺服器位於相同的平臺一樣撰寫程式碼。
* 提供簡化的便攜網路 API。

這兩個平臺都會共用宣告和執行介面的需求，雖然宣告介面的程式不同。 GRPC 的許多 RPC 呼叫類型都支援對應到 WCF 服務可用的系結。 如需詳細資訊和範例，請參閱 [將 WCF 方案遷移至 gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc)。

## <a name="benefits-of-grpc"></a>GRPC 的優點

gRPC 提供比其他方法更好的架構，原因如下。

### <a name="performance"></a>效能

gRPC 使用 HTTP/2。 與 HTTP/1.1 相較之下，HTTP/2：

* 是較小、更快速的二進位通訊協定。
* 更有效率地剖析電腦。
* 支援透過單一連接的多工要求。 多工化可讓您透過一個連線傳送多個要求，而不需要封鎖彼此的要求。 在 HTTP/1.1 中，封鎖稱為「行標頭 (住) 封鎖」。

gRPC 使用 Protobuf （有效率的二進位格式）來序列化訊息。 Protobuf 訊息為：
* 快速序列化和還原序列化。
* 使用較少的頻寬，而不是以文字為基礎的格式。 

gRPC 是適用于具有頻寬限制之行動裝置和網路的絕佳解決方案。

### <a name="interoperability"></a>互通性

所有主要的程式設計語言和平臺都有 gRPC 的工具和程式庫，包括 .NET、JAVA、Python、Go、c + +、Node.js、Swift、Dart、Ruby 和 PHP。 由於 Protobuf 二進位電傳格式和每個平臺的有效率程式碼產生，開發人員可以建立跨平臺、高效能的應用程式。

### <a name="usability-and-productivity"></a>可用性和產能

gRPC 是全方位的 RPC 解決方案。 它在多種語言和平臺上都能一致地運作。 它也提供絕佳的工具，並自動產生大部分的重複使用程式碼。 如同 WCF，gRPC 會自動產生訊息和強型別的用戶端。 開發人員時間會釋出以專注于商務邏輯。

### <a name="streaming"></a>串流

gRPC 具有完整的雙向串流，可提供 WCF 完整雙工服務的類似功能。 gRPC 串流可以透過一般的網際網路連線、負載平衡器和服務網格來運作。

### <a name="deadlines-timeouts-and-cancellation"></a>期限、超時和取消

gRPC 可讓用戶端指定 RPC 完成的最長時間。 如果超過指定的期限，伺服器可以在用戶端之外取消作業。 期限和取消可透過後續的 gRPC 呼叫傳播，以協助強制執行資源使用量限制。 用戶端可以在超過期限時或稍早（如有需要）停止作業。 例如，用戶端可能會因為使用者互動而停止作業。

### <a name="security"></a>安全性

gRPC 可以使用 TLS 和 HTTP/2，在用戶端與伺服器之間提供端對端加密的連接。 用戶端憑證驗證支援可進一步提高用戶端與伺服器之間的安全性與信任。

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>gRPC 做為 WCF 到 .NET Core 和 .NET 5 的遷移路徑

.NET Core 和 .NET 5 標示了 Microsoft 為想要跨各種平臺提供服務的開發人員提供遠端通訊解決方案的方式。 .NET Core 和 .NET 5 支援 [呼叫 wcf 服務](/dotnet/core/additional-tools/wcf-web-service-reference-guide)，但不提供裝載 WCF 的伺服器端支援。

現代化 WCF 應用程式有兩個建議的路徑：

* gRPC 是以新式技術為基礎，並已成為適用于 RPC 應用程式之開發人員群體最受歡迎的選擇。 從 .NET Core 3.0 開始，新式 .NET 平臺對 gRPC 有絕佳的支援。 將 WCF 服務遷移至使用 gRPC 有助於提供 RPC 功能、效能、新式應用程式所需的互通性。

* [CoreWCF](https://github.com/CoreWCF/CoreWCF) 是一項支援將 WCF 服務裝載到 .net Core 和 .net 5 的支援。 預覽版本可供使用，且專案正在生產環境就緒。 CoreWCF 只支援 WCF 功能的子集，而遷移使用它的 .NET Framework 應用程式將需要變更程式碼和測試才能成功。 如果應用程式必須維持與呼叫 WCF 服務之現有用戶端的相容性，則 CoreWCF 是不錯的選擇。

## <a name="get-started"></a>開始使用

如需有關在 WCF 開發人員 ASP.NET Core 中建立 gRPC 服務的詳細指引，請參閱 [ASP.NET Core 適用于 Wcf 開發人員的 gRPC](/dotnet/architecture/grpc-for-wcf-developers)
