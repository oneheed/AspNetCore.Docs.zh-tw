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
* <span data-ttu-id="ad724-101">藉由執行下列命令來信任 HTTPS 開發憑證：</span><span class="sxs-lookup"><span data-stu-id="ad724-101">Trust the HTTPS development certificate by running the following command:</span></span>

  ```dotnetcli
  dotnet dev-certs https --trust
  ```
  
  <span data-ttu-id="ad724-102">上述命令無法在 Linux 上使用。</span><span class="sxs-lookup"><span data-stu-id="ad724-102">The preceding command doesn't work on Linux.</span></span> <span data-ttu-id="ad724-103">如需信任憑證的資訊，請參閱您的 Linux 發行版本文件。</span><span class="sxs-lookup"><span data-stu-id="ad724-103">See your Linux distribution's documentation for trusting a certificate.</span></span>

  <span data-ttu-id="ad724-104">上述命令會顯示以下對話方塊：</span><span class="sxs-lookup"><span data-stu-id="ad724-104">The preceding command displays the following dialog:</span></span>

  ![安全性警告對話方塊](~/getting-started/_static/cert.png)

* <span data-ttu-id="ad724-106">若您同意信任開發憑證，請選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="ad724-106">Select **Yes** if you agree to trust the development certificate.</span></span>

  <span data-ttu-id="ad724-107">如需詳細資訊，請參閱[信任 ASP.NET Core HTTPS 開發憑證](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos)。</span><span class="sxs-lookup"><span data-stu-id="ad724-107">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>
  
[!INCLUDE[trust FF](~/includes/trust-ff.md)]