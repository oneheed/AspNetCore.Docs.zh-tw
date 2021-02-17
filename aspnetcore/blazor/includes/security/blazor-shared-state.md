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
ms.openlocfilehash: 5ff4e4368d9e6d7c8525ae4ef0625d176a256a85
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552634"
---
Blazor 伺服器應用程式存留在伺服器記憶體中。 這表示在相同的進程中裝載多個應用程式。 針對每個應用程式會話， Blazor 使用自己的 DI 容器範圍啟動線路。 這表示範圍服務在每個會話中都是唯一的 Blazor 。

> [!WARNING]
> 除非特別小心，否則我們不建議在相同伺服器上使用單一服務共用狀態的應用程式，因為這可能會造成安全性弱點，例如線上路之間洩漏使用者狀態。

您可以在應用程式中使用具狀態的單一服務 Blazor （如果它們是專為其設計）。 例如，您可以使用記憶體快取做為 singleton，因為它需要索引鍵來存取指定的專案，前提是使用者無法控制所使用的快取索引鍵。

**此外，基於安全性考慮，您不能 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 在 Blazor 應用程式內使用。** Blazor 應用程式會在 ASP.NET Core 管線的內容之外執行。 <xref:Microsoft.AspNetCore.Http.HttpContext>不保證可在中使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> ，也不保證會保存啟動 Blazor 應用程式的內容。

將要求狀態傳遞給應用程式的建議方式 Blazor ，是在應用程式的初始呈現中透過根元件的參數：

* 使用您想要傳遞給應用程式的所有資料來定義類別 Blazor 。
* Razor使用當時可用的來填入頁面中的資料 <xref:Microsoft.AspNetCore.Http.HttpContext> 。
* 以參數的形式將資料傳遞至 Blazor 應用程式，以 (應用程式) 的根元件。
* 在根元件中定義參數，以保存要傳遞至應用程式的資料。
* 使用應用程式中的使用者特定資料;或者，將該資料複製到內的範圍服務， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 讓它可以跨應用程式使用。

如需詳細資訊和範例程式碼，請參閱 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>。
