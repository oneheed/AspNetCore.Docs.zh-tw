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
ms.openlocfilehash: 41d4fd2e746e08d32d9f666faab55acc56817be2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552956"
---
<!-- Options common to Razor Pages and Controller -->
| <span data-ttu-id="8e338-101">選項</span><span class="sxs-lookup"><span data-stu-id="8e338-101">Option</span></span>               | <span data-ttu-id="8e338-102">描述</span><span class="sxs-lookup"><span data-stu-id="8e338-102">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="8e338-103">--model 或 -m</span><span class="sxs-lookup"><span data-stu-id="8e338-103">--model or -m</span></span>  | <span data-ttu-id="8e338-104">要使用的模型類別。</span><span class="sxs-lookup"><span data-stu-id="8e338-104">Model class to use.</span></span> |
| <span data-ttu-id="8e338-105">--dataContext 或 -dc</span><span class="sxs-lookup"><span data-stu-id="8e338-105">--dataContext or -dc</span></span>  | <span data-ttu-id="8e338-106">要使用的 `DbContext` 類別。</span><span class="sxs-lookup"><span data-stu-id="8e338-106">The `DbContext` class to use.</span></span> |
| <span data-ttu-id="8e338-107">--bootstrapVersion 或 -b</span><span class="sxs-lookup"><span data-stu-id="8e338-107">--bootstrapVersion or -b</span></span>  | <span data-ttu-id="8e338-108">指定啟動程序版本。</span><span class="sxs-lookup"><span data-stu-id="8e338-108">Specifies the bootstrap version.</span></span> <span data-ttu-id="8e338-109">有效值為 `3` 或 `4`。</span><span class="sxs-lookup"><span data-stu-id="8e338-109">Valid values are `3` or `4`.</span></span> <span data-ttu-id="8e338-110">預設為 `4`。</span><span class="sxs-lookup"><span data-stu-id="8e338-110">Default is `4`.</span></span> <span data-ttu-id="8e338-111">如果需要但不存在，則會建立 *wwwroot* 目錄，其中包含指定版本的啟動程序檔案。</span><span class="sxs-lookup"><span data-stu-id="8e338-111">If needed and not present, a *wwwroot* directory is created that includes the bootstrap files of the specified version.</span></span> |
| <span data-ttu-id="8e338-112">--referenceScriptLibraries 或 -scripts</span><span class="sxs-lookup"><span data-stu-id="8e338-112">--referenceScriptLibraries or -scripts</span></span> |  <span data-ttu-id="8e338-113">所產生檢視中的參考指令碼程式庫。</span><span class="sxs-lookup"><span data-stu-id="8e338-113">Reference script libraries in the generated views.</span></span> <span data-ttu-id="8e338-114">將 `_ValidationScriptsPartial` 新增至 [編輯] 和 [建立] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8e338-114">Adds `_ValidationScriptsPartial` to Edit and Create pages.</span></span> |
| <span data-ttu-id="8e338-115">--layout 或 -l</span><span class="sxs-lookup"><span data-stu-id="8e338-115">--layout or -l</span></span> | <span data-ttu-id="8e338-116">要使用的自訂 [配置] 頁面。</span><span class="sxs-lookup"><span data-stu-id="8e338-116">Custom Layout page to use.</span></span> |
| <span data-ttu-id="8e338-117">--useDefaultLayout 或 -udl</span><span class="sxs-lookup"><span data-stu-id="8e338-117">--useDefaultLayout or -udl</span></span> | <span data-ttu-id="8e338-118">針對檢視使用預設的配置。</span><span class="sxs-lookup"><span data-stu-id="8e338-118">Use the default layout for the views.</span></span> |
| <span data-ttu-id="8e338-119">--force 或 -f</span><span class="sxs-lookup"><span data-stu-id="8e338-119">--force or -f</span></span> | <span data-ttu-id="8e338-120">覆寫現有檔案。</span><span class="sxs-lookup"><span data-stu-id="8e338-120">Overwrite existing files.</span></span> |
| <span data-ttu-id="8e338-121">--relativeFolderPath 或 -outDir</span><span class="sxs-lookup"><span data-stu-id="8e338-121">--relativeFolderPath or -outDir</span></span> | <span data-ttu-id="8e338-122">專案的相對輸出資料夾路徑，其為產生檔案的位置。</span><span class="sxs-lookup"><span data-stu-id="8e338-122">The relative output folder path from project where the file are generated.</span></span> <span data-ttu-id="8e338-123">若未指定，則會在專案資料夾中產生檔案。</span><span class="sxs-lookup"><span data-stu-id="8e338-123">If not specified, files are generated in the project folder.</span></span> |