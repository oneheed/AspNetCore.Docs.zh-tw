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
ms.openlocfilehash: 378c74c930f6e3f9565f408a2761a8ed867450d3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552522"
---
## <a name="share-interop-code-in-a-class-library"></a><span data-ttu-id="ff458-101">在類別庫中共用 interop 程式碼</span><span class="sxs-lookup"><span data-stu-id="ff458-101">Share interop code in a class library</span></span>

<span data-ttu-id="ff458-102">JS interop 程式碼可以包含在類別庫中，以讓您共用 NuGet 套件中的程式碼。</span><span class="sxs-lookup"><span data-stu-id="ff458-102">JS interop code can be included in a class library, which allows you to share the code in a NuGet package.</span></span>

<span data-ttu-id="ff458-103">類別庫會處理在建立的元件中內嵌 JavaScript 資源。</span><span class="sxs-lookup"><span data-stu-id="ff458-103">The class library handles embedding JavaScript resources in the built assembly.</span></span> <span data-ttu-id="ff458-104">JavaScript 檔案會放在資料夾中 `wwwroot` 。</span><span class="sxs-lookup"><span data-stu-id="ff458-104">The JavaScript files are placed in the `wwwroot` folder.</span></span> <span data-ttu-id="ff458-105">在建立程式庫時，工具會負責內嵌資源。</span><span class="sxs-lookup"><span data-stu-id="ff458-105">The tooling takes care of embedding the resources when the library is built.</span></span>

<span data-ttu-id="ff458-106">在應用程式的專案檔中，會以參考任何 NuGet 套件的相同方式參考所建立的 NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="ff458-106">The built NuGet package is referenced in the app's project file the same way that any NuGet package is referenced.</span></span> <span data-ttu-id="ff458-107">還原封裝之後，應用程式程式碼就可以像 c # 一樣呼叫 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="ff458-107">After the package is restored, app code can call into JavaScript as if it were C#.</span></span>

<span data-ttu-id="ff458-108">如需詳細資訊，請參閱<xref:blazor/components/class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="ff458-108">For more information, see <xref:blazor/components/class-libraries>.</span></span>
