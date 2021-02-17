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
ms.openlocfilehash: 9c977e5407f9a3dc562ef0fb1127fefaa0dc5fc2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552925"
---
<a name="ddav"></a>
### <a name="disable-default-account-verification"></a>停用預設帳戶驗證

使用預設範本時，系統會將使用者重新導向至 `Account.RegisterConfirmation` 可選取連結以確認帳戶的位置。 預設 `Account.RegisterConfirmation` ***只*** 會用於測試，在生產應用程式中應該停用自動帳戶驗證。

若要要求確認的帳戶並防止在註冊時立即登 `DisplayConfirmAccountLink = false` 入，請在 */Areas/ Identity /Pages/Account/RegisterConfirmation.cshtml.cs* 中設定：

[!code-csharp[](~/security/authentication/identity/sample/WebApp3/Areas/Identity/Pages/Account/RegisterConfirmation.cshtml.cs?name=snippet&highlight=34)]