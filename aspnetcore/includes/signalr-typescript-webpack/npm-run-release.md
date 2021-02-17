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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552975"
---
```console
npm run release
```

<span data-ttu-id="539e5-101">此命令會產生在執行應用程式時所要提供的用戶端資產。</span><span class="sxs-lookup"><span data-stu-id="539e5-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="539e5-102">這些資產位於 *wwwroot* 資料夾中。</span><span class="sxs-lookup"><span data-stu-id="539e5-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="539e5-103">Webpack 已完成下列工作：</span><span class="sxs-lookup"><span data-stu-id="539e5-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="539e5-104">清除 *wwwroot* 目錄的內容。</span><span class="sxs-lookup"><span data-stu-id="539e5-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="539e5-105">在稱為 *轉譯* 的進程中，將 TypeScript 轉換為 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="539e5-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="539e5-106">破壞產生的 JavaScript，以在稱為 *縮制* 的進程中減少檔案大小。</span><span class="sxs-lookup"><span data-stu-id="539e5-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="539e5-107">將處理的 JavaScript、CSS 與 HTML 檔案從 *src* 複製到 *wwwroot* 目錄。</span><span class="sxs-lookup"><span data-stu-id="539e5-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="539e5-108">將下列元素插入到 *wwwroot/index.html* 檔案：</span><span class="sxs-lookup"><span data-stu-id="539e5-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="539e5-109">`<link>`參考 *wwwroot/main 的標記 \<hash\> 。css* 檔案。</span><span class="sxs-lookup"><span data-stu-id="539e5-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="539e5-110">此標記緊接在結尾 `</head>` 標記之前。</span><span class="sxs-lookup"><span data-stu-id="539e5-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="539e5-111">`<script>`參考縮減 *wwwroot/main 的標記 \<hash\> 。js* 檔案。</span><span class="sxs-lookup"><span data-stu-id="539e5-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="539e5-112">此標記緊接在結尾 `</body>` 標記之前。</span><span class="sxs-lookup"><span data-stu-id="539e5-112">This tag is placed immediately before the closing `</body>` tag.</span></span>