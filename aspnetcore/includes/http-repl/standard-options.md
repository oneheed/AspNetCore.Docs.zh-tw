* `-F|--no-formatting`

  <span data-ttu-id="acd58-101">其目前狀態會隱藏 HTTP 回應格式的旗標。</span><span class="sxs-lookup"><span data-stu-id="acd58-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="acd58-102">設定 HTTP 要求標頭。</span><span class="sxs-lookup"><span data-stu-id="acd58-102">Sets an HTTP request header.</span></span> <span data-ttu-id="acd58-103">支援下列兩種值格式：</span><span class="sxs-lookup"><span data-stu-id="acd58-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="acd58-104">指定 HTTP 回應本文應寫入的檔案。</span><span class="sxs-lookup"><span data-stu-id="acd58-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="acd58-105">例如 `--response:body "C:\response.json"`。</span><span class="sxs-lookup"><span data-stu-id="acd58-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="acd58-106">如果檔案不存在，就會建立檔案。</span><span class="sxs-lookup"><span data-stu-id="acd58-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="acd58-107">指定 HTTP 回應標頭應寫入的檔案。</span><span class="sxs-lookup"><span data-stu-id="acd58-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="acd58-108">例如 `--response:headers "C:\response.txt"`。</span><span class="sxs-lookup"><span data-stu-id="acd58-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="acd58-109">如果檔案不存在，就會建立檔案。</span><span class="sxs-lookup"><span data-stu-id="acd58-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="acd58-110">其目前狀態會啟用 HTTP 回應串流的旗標。</span><span class="sxs-lookup"><span data-stu-id="acd58-110">A flag whose presence enables streaming of the HTTP response.</span></span>
