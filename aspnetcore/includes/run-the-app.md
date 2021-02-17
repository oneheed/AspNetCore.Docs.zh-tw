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
ms.openlocfilehash: d05b0f327eb1670847125e6a73ef7277ebd069b5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552725"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 按 Ctrl+F5 即可執行而不使用偵錯工具。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio 會啟動 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)，並執行應用程式。 位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。 這是因為 `localhost` 是本機電腦的標準主機名稱。 Localhost 只會為來自本機電腦的 Web 要求提供服務。 當 Visual Studio 建立 Web 專案時，會為網頁伺服器使用隨機連接埠。
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* 按 **Ctrl-F5** 即可執行而不使用偵錯工具。

  Visual Studio Code 會啟動 [Kestrel](xref:fundamentals/servers/kestrel)、啟動瀏覽器，然後瀏覽至 `http://localhost:5001`。 位址列會顯示 `localhost:port#`，而不是類似於 `example.com` 的內容。 這是因為 `localhost` 是本機電腦的標準主機名稱。 Localhost 只會為來自本機電腦的 Web 要求提供服務。

  
# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* 在 Visual Studio 中，按 [ **選擇-Cmd-** 傳回]，以在沒有偵錯工具的情況下執行。 或者，流覽至功能表列並移至 [ **執行]>啟動但不進行調試**。

  Visual Studio 會啟動 [Kestrel](xref:fundamentals/servers/kestrel)啟動瀏覽器，然後巡覽至 `http://localhost:5001`。

<!-- End of VS tabs -->

---
