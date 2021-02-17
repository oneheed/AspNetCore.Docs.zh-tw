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
ms.openlocfilehash: 1cc2a01725a2af8f3dbfe1b23ea81e99bab9ef8e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552610"
---
## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="cb6c6-101">內建的 ASP.NET Core 標記協助程式</span><span class="sxs-lookup"><span data-stu-id="cb6c6-101">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="cb6c6-102">**[錨點標記協助程式](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-102">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-103">**[快取標記協助程式](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-103">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-104">**[元件標記協助程式](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-104">**[Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-105">**[分散式快取標記協助程式](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-105">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-106">**[環境標記協助程式](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-106">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-107">**[表單標記協助程式](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-107">**[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-108">**[表單動作標記協助程式](xref:mvc/views/working-with-forms#the-form-action-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-108">**[Form Action Tag Helper](xref:mvc/views/working-with-forms#the-form-action-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-109">**[影像標記協助程式](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-109">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-110">**[輸入標記協助程式](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-110">**[Input Tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-111">**[標籤標記協助程式](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-111">**[Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-112">**[連結標記協助程式](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-112">**[Link Tag Helper](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-113">**[Partial 標籤協助程式](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-113">**[Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-114">**[腳本標記協助程式](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-114">**[Script Tag Helper](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-115">**[選取標記協助程式](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-115">**[Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-116">**[Textarea 標記協助程式](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-116">**[Textarea Tag Helper](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-117">**[驗證訊息標記協助程式](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-117">**[Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span></span>

<span data-ttu-id="cb6c6-118">**[驗證摘要標記協助程式](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="cb6c6-118">**[Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span></span>
