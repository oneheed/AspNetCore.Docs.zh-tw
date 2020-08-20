---
title: 比較 gRPC 服務與 HTTP API
author: jamesnk
description: 瞭解 gRPC 如何與 HTTP Api 和建議案例的比較。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 12/05/2019
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
uid: grpc/comparison
ms.openlocfilehash: d20740950f7ac56a3a3b2951b474151aaf9c6f5a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631221"
---
# <a name="compare-grpc-services-with-http-apis"></a>比較 gRPC 服務與 HTTP API

依 [James 牛頓](https://twitter.com/jamesnk)

本文說明如何使用 JSON (（包括 ASP.NET Core [Web api](xref:web-api/index)) ）來比較[GRPC Services](https://grpc.io/docs/guides/)與 HTTP api。 用來為您的應用程式提供 API 的技術是很重要的選擇，而 gRPC 提供與 HTTP Api 相較之下的獨特優點。 本文討論 gRPC 的優點和缺點，並建議在其他技術上使用 gRPC 的案例。

## <a name="high-level-comparison"></a>高層級比較

下表提供 gRPC 和 HTTP Api 與 JSON 之間功能的高階比較。

| 功能          | gRPC                                               | HTTP Api 與 JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| 合約         | 必要 (*。 proto*)                                 | 選擇性的 (OpenAPI)             |
| 通訊協定         | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (small、binary) ](#performance)           | JSON (大型、人類看得懂的)   |
| Prescriptiveness | [嚴格規格](#strict-specification)      | 鬆散。 任何 HTTP 都有效。     |
| 資料流        | [用戶端、伺服器、雙向](#streaming)       | 用戶端、伺服器                |
| 瀏覽器支援  | [無 (需要 grpc-web) ](#limited-browser-support) | 是                           |
| 安全性         | 傳輸 (TLS)                                     | 傳輸 (TLS)                |
| 用戶端程式代碼產生 | [是](#code-generation)                      | OpenAPI + 協力廠商工具 |

## <a name="grpc-strengths"></a>gRPC 的強項

### <a name="performance"></a>效能

gRPC 訊息是使用 [Protobuf](https://developers.google.com/protocol-buffers/docs/overview)（有效率的二進位訊息格式）進行序列化。 Protobuf 會在伺服器和用戶端上迅速序列化。 Protobuf 序列化會產生小型訊息承載，在有限的頻寬案例（例如行動應用程式）中很重要。

gRPC 是針對 HTTP/2 設計的，這是 HTTP 的主要版本，可透過 HTTP 1.x 提供顯著的效能優勢：

* 二進位框架和壓縮。 HTTP/2 通訊協定在傳送和接收時都是精簡且有效率的。
* 透過單一 TCP 連接的多個 HTTP/2 呼叫多工。 多工化可消除 [行標頭封鎖](https://en.wikipedia.org/wiki/Head-of-line_blocking)。

HTTP/2 不是 gRPC 專屬的。 許多要求類型（包括具有 JSON 的 HTTP Api）可以使用 HTTP/2，並受益于其效能改進。

### <a name="code-generation"></a>程式碼產生

所有 gRPC 架構都提供最先進的程式碼產生支援。 GRPC 開發的核心檔案是定義 gRPC 服務和訊息合約的[proto 檔。](https://developers.google.com/protocol-buffers/docs/proto3) 從這個檔案 gRPC 架構會產生服務基類、訊息和完整用戶端的程式碼。

藉由共用伺服器和用戶端之間的 *proto* 檔案，就可以從端對端產生訊息和用戶端程式代碼。 用戶端的程式碼產生會在用戶端和伺服器上消除重複的訊息，並為您建立強型別的用戶端。 不需要撰寫用戶端就能將大量開發時間節省在具有許多服務的應用程式中。

### <a name="strict-specification"></a>嚴格規格

具有 JSON 之 HTTP API 的正式規格不存在。 開發人員會爭論 Url、HTTP 動詞命令和回應碼的最佳格式。

[GRPC 規格](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md)是 gRPC 服務必須遵循的格式規範。 gRPC 可消除爭論並節省開發人員的時間，因為 gRPC 在各平臺和執行之間都是一致的。

### <a name="streaming"></a>資料流

HTTP/2 提供長期即時通訊資料流程的基礎。 gRPC 透過 HTTP/2 提供對串流的頂級支援。

GRPC 服務支援所有串流組合：

* 一元 (沒有串流) 
* 伺服器對用戶端串流
* 用戶端對伺服器串流
* 雙向串流

### <a name="deadlinetimeouts-and-cancellation"></a>期限/超時和取消

gRPC 可讓用戶端指定他們願意等待 RPC 完成的時間長度。 [期限](https://grpc.io/blog/deadlines)會傳送至伺服器，而且伺服器可以決定當超過期限時要採取的動作。 例如，伺服器可能會在超時時取消進行中的 gRPC/HTTP/資料庫要求。

透過子 gRPC 呼叫傳播期限和取消，有助於強制執行資源使用量限制。

## <a name="grpc-recommended-scenarios"></a>gRPC 建議的案例

gRPC 適用于下列案例：

* **微服務**： gRPC 是針對低延遲和高輸送量的通訊所設計。 gRPC 非常適合於高效率的輕量微服務。
* **點對點即時通訊**： gRPC 有絕佳的雙向串流支援。 gRPC services 可以即時推播訊息，而不會進行輪詢。
* **多語言環境**： gRPC 工具支援所有熱門的開發語言，讓 gRPC 成為多國語言環境的理想選擇。
* **網路受限的環境**： gRPC 訊息會以 Protobuf （輕量訊息格式）進行序列化。 GRPC 訊息一律小於相等的 JSON 訊息。

## <a name="grpc-weaknesses"></a>gRPC 弱點

### <a name="limited-browser-support"></a>有限的瀏覽器支援

目前無法直接從瀏覽器呼叫 gRPC 服務。 gRPC 會大量使用 HTTP/2 功能，而且沒有任何瀏覽器會提供 web 要求所需的控制層級來支援 gRPC 用戶端。 例如，瀏覽器不允許呼叫者要求使用 HTTP/2，或提供基礎 HTTP/2 框架的存取權。

[gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) 是 gRPC 小組提供的額外技術，可在瀏覽器中提供有限的 gRPC 支援。 gRPC Web 是由兩個部分所組成：支援所有新式瀏覽器的 JavaScript 用戶端，以及伺服器上的 gRPC Web proxy。 GRPC Web 用戶端會呼叫 proxy，而 proxy 會將 gRPC 要求轉寄至 gRPC 伺服器。

並非所有 gRPC 的功能都受 gRPC Web 支援。 用戶端和雙向串流不受支援，而且伺服器串流的支援有限。

> [!TIP]
> .NET Core 支援 gRPC Web。 如需詳細資訊，請造訪 <xref:grpc/browser> 。

### <a name="not-human-readable"></a>不是人類看得懂的

HTTP API 要求會以文字的形式傳送，並且可由人類讀取及建立。

gRPC 訊息預設會以 Protobuf 編碼。 雖然 Protobuf 的傳送和接收效率很高，但其二進位格式不是人類看得懂的。 Protobuf 需要在 *proto* 檔案中指定訊息的介面描述，才能正確還原序列化。 需要額外的工具才能分析網路上的 Protobuf 承載，並以手動方式撰寫要求。

[伺服器反映](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)和[gRPC 命令列工具](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md)等功能，可協助您使用二進位 Protobuf 訊息。 此外，Protobuf 訊息支援在 [JSON 之間進行轉換](https://developers.google.com/protocol-buffers/docs/proto3#json)。 內建的 JSON 轉換可讓您有效率地在進行偵錯工具時，將 Protobuf 訊息轉換成人類可讀取的表單。

## <a name="alternative-framework-scenarios"></a>替代架構案例

在下列案例中，建議使用其他架構，而不是 gRPC：

* **瀏覽器可存取的 api**： gRPC 在瀏覽器中未受到完整支援。 gRPC-Web 可以提供瀏覽器支援，但它有一些限制，並引進伺服器 proxy。
* **廣播即時通訊**： gRPC 透過串流支援即時通訊，但廣播訊息到已註冊連接的概念不存在。 例如，在應該將新的聊天訊息傳送到聊天室中所有用戶端的聊天室案例中，每個 gRPC 呼叫都需要個別將新的聊天訊息串流至用戶端。 [SignalR](xref:signalr/introduction) 是適用于此案例的實用架構。 SignalR 具有持續性連接的概念，以及廣播訊息的內建支援。
* **處理序間通訊**：進程必須裝載 HTTP/2 伺服器，才能接受傳入的 gRPC 呼叫。 針對 Windows，處理序間通訊 [管道](/dotnet/standard/io/pipe-operations) 是快速、輕量的通訊方法。

## <a name="additional-resources"></a>其他資源

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
