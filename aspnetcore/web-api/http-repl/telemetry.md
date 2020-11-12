---
title: HttpRepl 遙測
author: scottaddie
description: 瞭解 HttpRepl 收集的遙測資料。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550604"
---
# <a name="httprepl-telemetry"></a>HttpRepl 遙測

[HttpRepl](xref:web-api/http-repl)包含收集使用量資料的遙測功能。 HttpRepl 小組必須瞭解工具的使用方式，才能加以改善。

## <a name="how-to-opt-out"></a>如何選擇退出

預設會啟用 HttpRepl 遙測功能。 若要退出遙測功能，請將 `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` 環境變數設定為 `1` 或 `true`。

## <a name="disclosure"></a>公開

當您第一次執行此工具時，HttpRepl 會顯示類似下面的文字。 根據您所執行的工具版本，文字可能會稍有不同。 這個「第一次執行」經驗是 Microsoft 如何通知您有關資料收集。

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

若要隱藏「第一次執行」經驗文字，請將 `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` 環境變數設為 `1` 或 `true` 。

## <a name="data-points"></a>資料點

遙測功能不會：

* 收集個人資料，例如使用者名稱、電子郵件地址或 Url。
* 掃描您的 HTTP 要求或回應。

資料會安全地傳送到 Microsoft 伺服器，並保留在受限制的存取之下。

保護您的隱私權對我們而言很重要。 如果您懷疑遙測功能正在收集敏感性資料，或資料正在, 或不當處理，請採取下列其中一項動作：

* 在 [dotnet/HTTPrepl](https://github.com/dotnet/httprepl/issues) 存放庫中提出問題。
* 傳送電子郵件以 [dotnet@microsoft.com](mailto:dotnet@microsoft.com) 供調查。

遙測功能會收集下列資料。

| .NET SDK 版本 | 資料 |
|--------------|------|
| >= 5。0        | 叫用的時間戳記。 |
| >= 5。0        | 用來判斷地理位置的三個八位 IP 位址。 |
| >= 5。0        | 作業系統和版本。 |
| >= 5。0        | 執行時間識別碼 (RID) 工具正在執行。 |
| >= 5。0        | 工具是否正在容器中執行。 |
| >= 5。0        | 雜湊媒體存取控制 (MAC) 位址：密碼編譯 (SHA256) 雜湊和唯一的機器識別碼。 |
| >= 5。0        | 核心版本。 |
| >= 5。0        | HttpRepl 版本。 |
| >= 5。0        | 工具是否以 `help` 、 `run` 或 `connect` 引數啟動。 不會收集實際的引數值。 |
| >= 5。0        | 叫用的命令 (例如 `get`) ，以及是否成功。 |
| >= 5。0        | 針對 `connect` 命令，是否 `root` `base` 提供、或 `openapi` 引數。 不會收集實際的引數值。 |
| >= 5。0        | 若為 `pref` 命令，則表示 `get` 是否 `set` 發出或，以及所存取的喜好設定。 如果不是知名的喜好設定，則會雜湊名稱。 未收集值。 |
| >= 5。0        | 若為 `set header` 命令，則為要設定的標頭名稱。 如果不是已知的標頭，則會將名稱雜湊。 未收集值。 |
| >= 5。0        | 針對 `connect` 命令，是否使用的特殊案例 `dotnet new webapi` 和，是否已透過喜好設定略過。 |
| >= 5。0        | 針對所有 HTTP 命令 (例如，GET、POST、PUT) 、是否已指定每個選項。 不會收集選項的值。 |

## <a name="additional-resources"></a>其他資源

* [.NET Core SDK 遙測](/dotnet/core/tools/telemetry)
* [.NET Core CLI 遙測資料](https://dotnet.microsoft.com/platform/telemetry)
