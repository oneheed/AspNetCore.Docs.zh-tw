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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551608"
---
<span data-ttu-id="c9e43-101">轉送的標頭中介軟體應該在其他中介軟體之前執行。</span><span class="sxs-lookup"><span data-stu-id="c9e43-101">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="c9e43-102">這種排序可確保依賴轉送標頭資訊的中介軟體可以耗用用於處理的標頭值。</span><span class="sxs-lookup"><span data-stu-id="c9e43-102">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="c9e43-103">若要在診斷和錯誤處理中介軟體之後執行轉送的標頭中介軟體，請參閱 [轉送標頭中介軟體順序](xref:host-and-deploy/proxy-load-balancer#fhmo)。</span><span class="sxs-lookup"><span data-stu-id="c9e43-103">To run Forwarded Headers Middleware after diagnostics and error handling middleware, see [Forwarded Headers Middleware order](xref:host-and-deploy/proxy-load-balancer#fhmo).</span></span>