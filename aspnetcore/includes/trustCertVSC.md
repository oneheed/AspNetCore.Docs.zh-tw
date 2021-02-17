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
ms.openlocfilehash: df4a9a7db8097ea765b2d0f404305b771f994ddf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551439"
---
* 藉由執行下列命令來信任 HTTPS 開發憑證：

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  上述命令無法在 Linux 上使用。 如需信任憑證的資訊，請參閱您的 Linux 發行版本文件。

  上述命令會顯示以下對話方塊：

  ![安全性警告對話方塊](~/getting-started/_static/cert.png)

* 若您同意信任開發憑證，請選取 [是]。

  如需詳細資訊，請參閱[信任 ASP.NET Core HTTPS 開發憑證](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]