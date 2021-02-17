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

此命令會產生在執行應用程式時所要提供的用戶端資產。 這些資產位於 *wwwroot* 資料夾中。

Webpack 已完成下列工作：

* 清除 *wwwroot* 目錄的內容。
* 在稱為 *轉譯* 的進程中，將 TypeScript 轉換為 JavaScript。
* 破壞產生的 JavaScript，以在稱為 *縮制* 的進程中減少檔案大小。
* 將處理的 JavaScript、CSS 與 HTML 檔案從 *src* 複製到 *wwwroot* 目錄。
* 將下列元素插入到 *wwwroot/index.html* 檔案：
  * `<link>`參考 *wwwroot/main 的標記 \<hash\> 。css* 檔案。 此標記緊接在結尾 `</head>` 標記之前。
  * `<script>`參考縮減 *wwwroot/main 的標記 \<hash\> 。js* 檔案。 此標記緊接在結尾 `</body>` 標記之前。