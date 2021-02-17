---
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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551296"
---
* `-F|--no-formatting`

  其目前狀態會隱藏 HTTP 回應格式的旗標。

* `-h|--header`

  設定 HTTP 要求標頭。 支援下列兩種值格式：

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  指定 HTTP 回應本文應寫入的檔案。 例如： `--response:body "C:\response.json"` 。 如果檔案不存在，就會建立檔案。

* `--response:headers`

  指定 HTTP 回應標頭應寫入的檔案。 例如： `--response:headers "C:\response.txt"` 。 如果檔案不存在，就會建立檔案。

* `-s|--streaming`

  其目前狀態會啟用 HTTP 回應串流的旗標。
