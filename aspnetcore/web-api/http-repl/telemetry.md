---
title: HttpRepl 遙測
author: scottaddie
description: 瞭解 HttpRepl 收集的遙測資料。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550604"
---
# <a name="httprepl-telemetry"></a><span data-ttu-id="93c6d-103">HttpRepl 遙測</span><span class="sxs-lookup"><span data-stu-id="93c6d-103">HttpRepl telemetry</span></span>

<span data-ttu-id="93c6d-104">[HttpRepl](xref:web-api/http-repl)包含收集使用量資料的遙測功能。</span><span class="sxs-lookup"><span data-stu-id="93c6d-104">The [HttpRepl](xref:web-api/http-repl) includes a telemetry feature that collects usage data.</span></span> <span data-ttu-id="93c6d-105">HttpRepl 小組必須瞭解工具的使用方式，才能加以改善。</span><span class="sxs-lookup"><span data-stu-id="93c6d-105">It's important that the HttpRepl team understands how the tool is used so it can be improved.</span></span>

## <a name="how-to-opt-out"></a><span data-ttu-id="93c6d-106">如何選擇退出</span><span class="sxs-lookup"><span data-stu-id="93c6d-106">How to opt out</span></span>

<span data-ttu-id="93c6d-107">預設會啟用 HttpRepl 遙測功能。</span><span class="sxs-lookup"><span data-stu-id="93c6d-107">The HttpRepl telemetry feature is enabled by default.</span></span> <span data-ttu-id="93c6d-108">若要退出遙測功能，請將 `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` 環境變數設定為 `1` 或 `true`。</span><span class="sxs-lookup"><span data-stu-id="93c6d-108">To opt out of the telemetry feature, set the `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` environment variable to `1` or `true`.</span></span>

## <a name="disclosure"></a><span data-ttu-id="93c6d-109">公開</span><span class="sxs-lookup"><span data-stu-id="93c6d-109">Disclosure</span></span>

<span data-ttu-id="93c6d-110">當您第一次執行此工具時，HttpRepl 會顯示類似下面的文字。</span><span class="sxs-lookup"><span data-stu-id="93c6d-110">The HttpRepl displays text similar to the following when you first run the tool.</span></span> <span data-ttu-id="93c6d-111">根據您所執行的工具版本，文字可能會稍有不同。</span><span class="sxs-lookup"><span data-stu-id="93c6d-111">Text may vary slightly depending on the version of the tool you're running.</span></span> <span data-ttu-id="93c6d-112">這個「第一次執行」經驗是 Microsoft 如何通知您有關資料收集。</span><span class="sxs-lookup"><span data-stu-id="93c6d-112">This "first run" experience is how Microsoft notifies you about data collection.</span></span>

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

<span data-ttu-id="93c6d-113">若要隱藏「第一次執行」經驗文字，請將 `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` 環境變數設為 `1` 或 `true` 。</span><span class="sxs-lookup"><span data-stu-id="93c6d-113">To suppress the "first run" experience text, set the `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` environment variable to `1` or `true`.</span></span>

## <a name="data-points"></a><span data-ttu-id="93c6d-114">資料點</span><span class="sxs-lookup"><span data-stu-id="93c6d-114">Data points</span></span>

<span data-ttu-id="93c6d-115">遙測功能不會：</span><span class="sxs-lookup"><span data-stu-id="93c6d-115">The telemetry feature doesn't:</span></span>

* <span data-ttu-id="93c6d-116">收集個人資料，例如使用者名稱、電子郵件地址或 Url。</span><span class="sxs-lookup"><span data-stu-id="93c6d-116">Collect personal data, such as usernames, email addresses, or URLs.</span></span>
* <span data-ttu-id="93c6d-117">掃描您的 HTTP 要求或回應。</span><span class="sxs-lookup"><span data-stu-id="93c6d-117">Scan your HTTP requests or responses.</span></span>

<span data-ttu-id="93c6d-118">資料會安全地傳送到 Microsoft 伺服器，並保留在受限制的存取之下。</span><span class="sxs-lookup"><span data-stu-id="93c6d-118">The data is sent securely to Microsoft servers and held under restricted access.</span></span>

<span data-ttu-id="93c6d-119">保護您的隱私權對我們而言很重要。</span><span class="sxs-lookup"><span data-stu-id="93c6d-119">Protecting your privacy is important to us.</span></span> <span data-ttu-id="93c6d-120">如果您懷疑遙測功能正在收集敏感性資料，或資料正在, 或不當處理，請採取下列其中一項動作：</span><span class="sxs-lookup"><span data-stu-id="93c6d-120">If you suspect the telemetry feature is collecting sensitive data or the data is being insecurely or inappropriately handled, take one of the following actions:</span></span>

* <span data-ttu-id="93c6d-121">在 [dotnet/HTTPrepl](https://github.com/dotnet/httprepl/issues) 存放庫中提出問題。</span><span class="sxs-lookup"><span data-stu-id="93c6d-121">File an issue in the [dotnet/httprepl](https://github.com/dotnet/httprepl/issues) repository.</span></span>
* <span data-ttu-id="93c6d-122">傳送電子郵件以 [dotnet@microsoft.com](mailto:dotnet@microsoft.com) 供調查。</span><span class="sxs-lookup"><span data-stu-id="93c6d-122">Send an email to [dotnet@microsoft.com](mailto:dotnet@microsoft.com) for investigation.</span></span>

<span data-ttu-id="93c6d-123">遙測功能會收集下列資料。</span><span class="sxs-lookup"><span data-stu-id="93c6d-123">The telemetry feature collects the following data.</span></span>

| <span data-ttu-id="93c6d-124">.NET SDK 版本</span><span class="sxs-lookup"><span data-stu-id="93c6d-124">.NET SDK versions</span></span> | <span data-ttu-id="93c6d-125">資料</span><span class="sxs-lookup"><span data-stu-id="93c6d-125">Data</span></span> |
|--------------|------|
| <span data-ttu-id="93c6d-126">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-126">>=5.0</span></span>        | <span data-ttu-id="93c6d-127">叫用的時間戳記。</span><span class="sxs-lookup"><span data-stu-id="93c6d-127">Timestamp of invocation.</span></span> |
| <span data-ttu-id="93c6d-128">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-128">>=5.0</span></span>        | <span data-ttu-id="93c6d-129">用來判斷地理位置的三個八位 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="93c6d-129">Three-octet IP address used to determine the geographical location.</span></span> |
| <span data-ttu-id="93c6d-130">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-130">>=5.0</span></span>        | <span data-ttu-id="93c6d-131">作業系統和版本。</span><span class="sxs-lookup"><span data-stu-id="93c6d-131">Operating system and version.</span></span> |
| <span data-ttu-id="93c6d-132">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-132">>=5.0</span></span>        | <span data-ttu-id="93c6d-133">執行時間識別碼 (RID) 工具正在執行。</span><span class="sxs-lookup"><span data-stu-id="93c6d-133">Runtime ID (RID) the tool is running on.</span></span> |
| <span data-ttu-id="93c6d-134">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-134">>=5.0</span></span>        | <span data-ttu-id="93c6d-135">工具是否正在容器中執行。</span><span class="sxs-lookup"><span data-stu-id="93c6d-135">Whether the tool is running in a container.</span></span> |
| <span data-ttu-id="93c6d-136">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-136">>=5.0</span></span>        | <span data-ttu-id="93c6d-137">雜湊媒體存取控制 (MAC) 位址：密碼編譯 (SHA256) 雜湊和唯一的機器識別碼。</span><span class="sxs-lookup"><span data-stu-id="93c6d-137">Hashed Media Access Control (MAC) address: a cryptographically (SHA256) hashed and unique ID for a machine.</span></span> |
| <span data-ttu-id="93c6d-138">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-138">>=5.0</span></span>        | <span data-ttu-id="93c6d-139">核心版本。</span><span class="sxs-lookup"><span data-stu-id="93c6d-139">Kernel version.</span></span> |
| <span data-ttu-id="93c6d-140">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-140">>=5.0</span></span>        | <span data-ttu-id="93c6d-141">HttpRepl 版本。</span><span class="sxs-lookup"><span data-stu-id="93c6d-141">HttpRepl version.</span></span> |
| <span data-ttu-id="93c6d-142">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-142">>=5.0</span></span>        | <span data-ttu-id="93c6d-143">工具是否以 `help` 、 `run` 或 `connect` 引數啟動。</span><span class="sxs-lookup"><span data-stu-id="93c6d-143">Whether the tool was started with `help`, `run`, or `connect` arguments.</span></span> <span data-ttu-id="93c6d-144">不會收集實際的引數值。</span><span class="sxs-lookup"><span data-stu-id="93c6d-144">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="93c6d-145">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-145">>=5.0</span></span>        | <span data-ttu-id="93c6d-146">叫用的命令 (例如 `get`) ，以及是否成功。</span><span class="sxs-lookup"><span data-stu-id="93c6d-146">Command invoked (for example, `get`) and whether it succeeded.</span></span> |
| <span data-ttu-id="93c6d-147">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-147">>=5.0</span></span>        | <span data-ttu-id="93c6d-148">針對 `connect` 命令，是否 `root` `base` 提供、或 `openapi` 引數。</span><span class="sxs-lookup"><span data-stu-id="93c6d-148">For the `connect` command, whether the `root`, `base`, or `openapi` arguments were supplied.</span></span> <span data-ttu-id="93c6d-149">不會收集實際的引數值。</span><span class="sxs-lookup"><span data-stu-id="93c6d-149">Actual argument values aren't collected.</span></span> |
| <span data-ttu-id="93c6d-150">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-150">>=5.0</span></span>        | <span data-ttu-id="93c6d-151">若為 `pref` 命令，則表示 `get` 是否 `set` 發出或，以及所存取的喜好設定。</span><span class="sxs-lookup"><span data-stu-id="93c6d-151">For the `pref` command, whether a `get` or `set` was issued and which preference was accessed.</span></span> <span data-ttu-id="93c6d-152">如果不是知名的喜好設定，則會雜湊名稱。</span><span class="sxs-lookup"><span data-stu-id="93c6d-152">If not a well-known preference, the name is hashed.</span></span> <span data-ttu-id="93c6d-153">未收集值。</span><span class="sxs-lookup"><span data-stu-id="93c6d-153">The value isn't collected.</span></span> |
| <span data-ttu-id="93c6d-154">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-154">>=5.0</span></span>        | <span data-ttu-id="93c6d-155">若為 `set header` 命令，則為要設定的標頭名稱。</span><span class="sxs-lookup"><span data-stu-id="93c6d-155">For the `set header` command, the header name being set.</span></span> <span data-ttu-id="93c6d-156">如果不是已知的標頭，則會將名稱雜湊。</span><span class="sxs-lookup"><span data-stu-id="93c6d-156">If not a well-known header, the name is hashed.</span></span> <span data-ttu-id="93c6d-157">未收集值。</span><span class="sxs-lookup"><span data-stu-id="93c6d-157">The value isn't collected.</span></span> |
| <span data-ttu-id="93c6d-158">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-158">>=5.0</span></span>        | <span data-ttu-id="93c6d-159">針對 `connect` 命令，是否使用的特殊案例 `dotnet new webapi` 和，是否已透過喜好設定略過。</span><span class="sxs-lookup"><span data-stu-id="93c6d-159">For the `connect` command, whether a special case for `dotnet new webapi` was used and, whether it was bypassed via preference.</span></span> |
| <span data-ttu-id="93c6d-160">>= 5。0</span><span class="sxs-lookup"><span data-stu-id="93c6d-160">>=5.0</span></span>        | <span data-ttu-id="93c6d-161">針對所有 HTTP 命令 (例如，GET、POST、PUT) 、是否已指定每個選項。</span><span class="sxs-lookup"><span data-stu-id="93c6d-161">For all HTTP commands (for example, GET, POST, PUT), whether each of the options was specified.</span></span> <span data-ttu-id="93c6d-162">不會收集選項的值。</span><span class="sxs-lookup"><span data-stu-id="93c6d-162">The values of the options aren't collected.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="93c6d-163">其他資源</span><span class="sxs-lookup"><span data-stu-id="93c6d-163">Additional resources</span></span>

* [<span data-ttu-id="93c6d-164">.NET Core SDK 遙測</span><span class="sxs-lookup"><span data-stu-id="93c6d-164">.NET Core SDK telemetry</span></span>](/dotnet/core/tools/telemetry)
* [<span data-ttu-id="93c6d-165">.NET Core CLI 遙測資料</span><span class="sxs-lookup"><span data-stu-id="93c6d-165">.NET Core CLI telemetry data</span></span>](https://dotnet.microsoft.com/platform/telemetry)
