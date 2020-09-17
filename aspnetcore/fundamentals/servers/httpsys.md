---
title: ASP.NET Core 中的 HTTP.sys 網頁伺服器實作
author: rick-anderson
description: 深入了解 HTTP.sys，這是 Windows 上的 ASP.NET Core 網頁伺服器。 HTTP.sys 建置在 HTTP.sys 核心模式驅動程式之上，是 Kestrel 的替代方式，可以用來直接連線到網際網路而不使用 IIS。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: e5346c1e58127747d777b5040fe7bc7d99b9a489
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722867"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="ec05e-104">ASP.NET Core 中的 HTTP.sys 網頁伺服器實作</span><span class="sxs-lookup"><span data-stu-id="ec05e-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="ec05e-105">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="ec05e-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="ec05e-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="ec05e-107">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="ec05e-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ec05e-108">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="ec05e-109">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="ec05e-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="ec05e-110">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="ec05e-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="ec05e-111">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="ec05e-111">Port sharing</span></span>
* <span data-ttu-id="ec05e-112">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="ec05e-112">HTTPS with SNI</span></span>
* <span data-ttu-id="ec05e-113">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="ec05e-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="ec05e-114">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="ec05e-114">Direct file transmission</span></span>
* <span data-ttu-id="ec05e-115">回應快取</span><span class="sxs-lookup"><span data-stu-id="ec05e-115">Response caching</span></span>
* <span data-ttu-id="ec05e-116">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="ec05e-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="ec05e-117">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="ec05e-117">Supported Windows versions:</span></span>

* <span data-ttu-id="ec05e-118">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-118">Windows 7 or later</span></span>
* <span data-ttu-id="ec05e-119">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="ec05e-120">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="ec05e-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="ec05e-121">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="ec05e-121">When to use HTTP.sys</span></span>

<span data-ttu-id="ec05e-122">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="ec05e-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="ec05e-123">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="ec05e-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="ec05e-125">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="ec05e-127">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="ec05e-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="ec05e-128">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="ec05e-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="ec05e-129">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="ec05e-129">HTTP/2 support</span></span>

<span data-ttu-id="ec05e-130">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="ec05e-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="ec05e-131">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="ec05e-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="ec05e-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="ec05e-133">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="ec05e-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ec05e-134">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="ec05e-135">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="ec05e-136">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="ec05e-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="ec05e-137">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="ec05e-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="ec05e-138">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="ec05e-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="ec05e-139">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="ec05e-140">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="ec05e-141">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="ec05e-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="ec05e-142">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="ec05e-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="ec05e-143">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ec05e-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="ec05e-144">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ec05e-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="ec05e-145">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="ec05e-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="ec05e-146">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="ec05e-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="ec05e-147">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="ec05e-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="ec05e-148">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="ec05e-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="ec05e-149">屬性</span><span class="sxs-lookup"><span data-stu-id="ec05e-149">Property</span></span> | <span data-ttu-id="ec05e-150">描述</span><span class="sxs-lookup"><span data-stu-id="ec05e-150">Description</span></span> | <span data-ttu-id="ec05e-151">預設</span><span class="sxs-lookup"><span data-stu-id="ec05e-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="ec05e-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="ec05e-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="ec05e-153">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="ec05e-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="ec05e-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="ec05e-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="ec05e-155">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="ec05e-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="ec05e-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="ec05e-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="ec05e-157">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="ec05e-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="ec05e-158">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="ec05e-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="ec05e-159">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="ec05e-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="ec05e-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="ec05e-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="ec05e-161">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="ec05e-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="ec05e-162">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-162">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="ec05e-163">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="ec05e-164">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="ec05e-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="ec05e-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="ec05e-166">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="ec05e-167">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="ec05e-167">Use `-1` for infinite.</span></span> <span data-ttu-id="ec05e-168">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="ec05e-169"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="ec05e-169">(machine-wide</span></span><br><span data-ttu-id="ec05e-170">設定) </span><span class="sxs-lookup"><span data-stu-id="ec05e-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="ec05e-171">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="ec05e-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="ec05e-172">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="ec05e-172">30000000 bytes</span></span><br><span data-ttu-id="ec05e-173">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="ec05e-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="ec05e-174">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="ec05e-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="ec05e-175">1000</span><span class="sxs-lookup"><span data-stu-id="ec05e-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="ec05e-176">這會指出伺服器是否負責建立和設定要求佇列，或是否應該附加至現有的佇列。</span><span class="sxs-lookup"><span data-stu-id="ec05e-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="ec05e-177">附加至現有的佇列時，大部分現有的設定選項都不適用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="ec05e-178">HTTP.sys 要求佇列的名稱。</span><span class="sxs-lookup"><span data-stu-id="ec05e-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="ec05e-179">`null` (匿名佇列) </span><span class="sxs-lookup"><span data-stu-id="ec05e-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="ec05e-180">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="ec05e-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="ec05e-181">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="ec05e-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="ec05e-182">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="ec05e-183">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="ec05e-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="ec05e-184">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 keep-alive 連線上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="ec05e-185">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="ec05e-186">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="ec05e-187">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="ec05e-188">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="ec05e-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="ec05e-189">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="ec05e-190">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="ec05e-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="ec05e-191">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="ec05e-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="ec05e-192">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="ec05e-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="ec05e-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="ec05e-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="ec05e-194">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="ec05e-195">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="ec05e-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="ec05e-196">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="ec05e-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="ec05e-197">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="ec05e-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ec05e-198">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ec05e-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="ec05e-199">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="ec05e-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ec05e-200">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="ec05e-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="ec05e-201">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ec05e-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="ec05e-202">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ec05e-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="ec05e-203">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="ec05e-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="ec05e-205">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="ec05e-205">Configure Windows Server</span></span>

1. <span data-ttu-id="ec05e-206">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="ec05e-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="ec05e-207">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="ec05e-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="ec05e-208">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="ec05e-209">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="ec05e-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="ec05e-210">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="ec05e-211">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="ec05e-212">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="ec05e-213">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** > \*\*\*\* 存放區中。</span><span class="sxs-lookup"><span data-stu-id="ec05e-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="ec05e-214">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="ec05e-215">**.Net core**：如果應用程式需要 .net core，請從[.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行**時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="ec05e-216">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="ec05e-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="ec05e-217">**.NET Framework**：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="ec05e-218">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="ec05e-218">Install the required .NET Framework.</span></span> <span data-ttu-id="ec05e-219">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="ec05e-220">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="ec05e-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="ec05e-221">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="ec05e-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="ec05e-222">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="ec05e-223">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="ec05e-224">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="ec05e-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="ec05e-225">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="ec05e-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="ec05e-226">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="ec05e-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="ec05e-227">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="ec05e-228">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="ec05e-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="ec05e-229">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="ec05e-230">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="ec05e-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="ec05e-231">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="ec05e-232">請**勿**使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="ec05e-233">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="ec05e-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="ec05e-234">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="ec05e-235">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="ec05e-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="ec05e-236">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="ec05e-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="ec05e-237">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="ec05e-238">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="ec05e-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="ec05e-239">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="ec05e-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="ec05e-240">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="ec05e-241">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="ec05e-242">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="ec05e-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="ec05e-243">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="ec05e-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="ec05e-244">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="ec05e-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="ec05e-245">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="ec05e-246">URL 必須包含結尾斜線。\*\*</span><span class="sxs-lookup"><span data-stu-id="ec05e-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="ec05e-247">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="ec05e-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="ec05e-248">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="ec05e-249">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="ec05e-250">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="ec05e-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="ec05e-251">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="ec05e-252">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="ec05e-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="ec05e-253">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="ec05e-254">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="ec05e-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="ec05e-255">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="ec05e-256">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="ec05e-257">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="ec05e-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="ec05e-258">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="ec05e-259">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="ec05e-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="ec05e-260">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="ec05e-260">In Visual Studio:</span></span>
     * <span data-ttu-id="ec05e-261">在 [方案總管]\*\*\*\* 中的專案上按一下滑鼠右鍵，然後選取 [屬性]\*\*\*\*，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="ec05e-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="ec05e-262">選取 [套件]\*\*\*\* 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ec05e-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="ec05e-263">輸入您在 [標記]\*\*\*\* 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="ec05e-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="ec05e-264">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="ec05e-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="ec05e-265">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="ec05e-265">Open the app's project file.</span></span>
     * <span data-ttu-id="ec05e-266">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="ec05e-267">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="ec05e-267">In the following example:</span></span>

   * <span data-ttu-id="ec05e-268">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="ec05e-269">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="ec05e-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="ec05e-270">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="ec05e-271">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="ec05e-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="ec05e-272">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="ec05e-272">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="ec05e-273">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="ec05e-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="ec05e-274">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="ec05e-274">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="ec05e-275">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-275">Run the app.</span></span>

   <span data-ttu-id="ec05e-276">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="ec05e-277">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="ec05e-278">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="ec05e-279">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="ec05e-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="ec05e-280">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-280">A development certificate is used in this example.</span></span> <span data-ttu-id="ec05e-281">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="ec05e-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ec05e-283">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="ec05e-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ec05e-284">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="ec05e-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="ec05e-285">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec05e-286">其他資源</span><span class="sxs-lookup"><span data-stu-id="ec05e-286">Additional resources</span></span>

* <span data-ttu-id="ec05e-287">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-287">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="ec05e-288">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-288">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="ec05e-289">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-289">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="ec05e-290">主機</span><span class="sxs-lookup"><span data-stu-id="ec05e-290">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="ec05e-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-291">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="ec05e-292">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="ec05e-292">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ec05e-293">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-293">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="ec05e-294">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="ec05e-294">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="ec05e-295">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="ec05e-295">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="ec05e-296">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="ec05e-296">Port sharing</span></span>
* <span data-ttu-id="ec05e-297">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="ec05e-297">HTTPS with SNI</span></span>
* <span data-ttu-id="ec05e-298">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="ec05e-298">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="ec05e-299">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="ec05e-299">Direct file transmission</span></span>
* <span data-ttu-id="ec05e-300">回應快取</span><span class="sxs-lookup"><span data-stu-id="ec05e-300">Response caching</span></span>
* <span data-ttu-id="ec05e-301">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="ec05e-301">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="ec05e-302">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="ec05e-302">Supported Windows versions:</span></span>

* <span data-ttu-id="ec05e-303">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-303">Windows 7 or later</span></span>
* <span data-ttu-id="ec05e-304">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-304">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="ec05e-305">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="ec05e-305">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="ec05e-306">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="ec05e-306">When to use HTTP.sys</span></span>

<span data-ttu-id="ec05e-307">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="ec05e-307">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="ec05e-308">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="ec05e-308">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="ec05e-310">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-310">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="ec05e-312">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="ec05e-312">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="ec05e-313">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="ec05e-313">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="ec05e-314">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="ec05e-314">HTTP/2 support</span></span>

<span data-ttu-id="ec05e-315">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="ec05e-315">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="ec05e-316">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-316">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="ec05e-317">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="ec05e-317">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="ec05e-318">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="ec05e-318">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ec05e-319">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-319">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="ec05e-320">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-320">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="ec05e-321">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="ec05e-321">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="ec05e-322">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="ec05e-322">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="ec05e-323">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="ec05e-323">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="ec05e-324">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-324">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="ec05e-325">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-325">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="ec05e-326">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="ec05e-326">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="ec05e-327">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="ec05e-327">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="ec05e-328">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ec05e-328">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="ec05e-329">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ec05e-329">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="ec05e-330">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="ec05e-330">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="ec05e-331">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="ec05e-331">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="ec05e-332">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="ec05e-332">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="ec05e-333">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="ec05e-333">**HTTP.sys options**</span></span>

| <span data-ttu-id="ec05e-334">屬性</span><span class="sxs-lookup"><span data-stu-id="ec05e-334">Property</span></span> | <span data-ttu-id="ec05e-335">描述</span><span class="sxs-lookup"><span data-stu-id="ec05e-335">Description</span></span> | <span data-ttu-id="ec05e-336">預設</span><span class="sxs-lookup"><span data-stu-id="ec05e-336">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="ec05e-337">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="ec05e-337">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="ec05e-338">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="ec05e-338">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="ec05e-339">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="ec05e-339">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="ec05e-340">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="ec05e-340">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="ec05e-341">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="ec05e-341">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="ec05e-342">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="ec05e-342">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="ec05e-343">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="ec05e-343">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="ec05e-344">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="ec05e-344">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="ec05e-345">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="ec05e-345">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="ec05e-346">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="ec05e-346">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="ec05e-347">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-347">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="ec05e-348">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-348">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="ec05e-349">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-349">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="ec05e-350">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="ec05e-350">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="ec05e-351">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-351">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="ec05e-352">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="ec05e-352">Use `-1` for infinite.</span></span> <span data-ttu-id="ec05e-353">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-353">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="ec05e-354"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="ec05e-354">(machine-wide</span></span><br><span data-ttu-id="ec05e-355">設定) </span><span class="sxs-lookup"><span data-stu-id="ec05e-355">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="ec05e-356">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="ec05e-356">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="ec05e-357">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="ec05e-357">30000000 bytes</span></span><br><span data-ttu-id="ec05e-358">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="ec05e-358">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="ec05e-359">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="ec05e-359">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="ec05e-360">1000</span><span class="sxs-lookup"><span data-stu-id="ec05e-360">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="ec05e-361">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="ec05e-361">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="ec05e-362">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="ec05e-362">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="ec05e-363">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-363">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="ec05e-364">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="ec05e-364">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="ec05e-365">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 keep-alive 連線上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-365">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="ec05e-366">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-366">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="ec05e-367">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-367">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="ec05e-368">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-368">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="ec05e-369">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="ec05e-369">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="ec05e-370">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-370">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="ec05e-371">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="ec05e-371">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="ec05e-372">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="ec05e-372">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="ec05e-373">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="ec05e-373">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="ec05e-374">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="ec05e-374">**MaxRequestBodySize**</span></span>

<span data-ttu-id="ec05e-375">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-375">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="ec05e-376">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="ec05e-376">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="ec05e-377">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="ec05e-377">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="ec05e-378">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="ec05e-378">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ec05e-379">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ec05e-379">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="ec05e-380">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="ec05e-380">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ec05e-381">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="ec05e-381">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="ec05e-382">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ec05e-382">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="ec05e-383">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ec05e-383">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="ec05e-384">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="ec05e-384">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="ec05e-386">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="ec05e-386">Configure Windows Server</span></span>

1. <span data-ttu-id="ec05e-387">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="ec05e-387">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="ec05e-388">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="ec05e-388">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="ec05e-389">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-389">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="ec05e-390">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="ec05e-390">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="ec05e-391">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-391">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="ec05e-392">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-392">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="ec05e-393">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-393">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="ec05e-394">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** > \*\*\*\* 存放區中。</span><span class="sxs-lookup"><span data-stu-id="ec05e-394">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="ec05e-395">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-395">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="ec05e-396">**.Net core**：如果應用程式需要 .net core，請從[.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行**時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-396">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="ec05e-397">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="ec05e-397">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="ec05e-398">**.NET Framework**：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-398">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="ec05e-399">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="ec05e-399">Install the required .NET Framework.</span></span> <span data-ttu-id="ec05e-400">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-400">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="ec05e-401">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="ec05e-401">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="ec05e-402">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="ec05e-402">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="ec05e-403">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-403">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="ec05e-404">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-404">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="ec05e-405">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="ec05e-405">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="ec05e-406">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="ec05e-406">`urls` command-line argument</span></span>
   * <span data-ttu-id="ec05e-407">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="ec05e-407">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="ec05e-408">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-408">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="ec05e-409">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="ec05e-409">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="ec05e-410">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-410">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="ec05e-411">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="ec05e-411">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="ec05e-412">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-412">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="ec05e-413">請**勿**使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-413">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="ec05e-414">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="ec05e-414">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="ec05e-415">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-415">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="ec05e-416">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="ec05e-416">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="ec05e-417">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="ec05e-417">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="ec05e-418">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-418">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="ec05e-419">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="ec05e-419">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="ec05e-420">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="ec05e-420">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="ec05e-421">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-421">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="ec05e-422">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-422">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="ec05e-423">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="ec05e-423">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="ec05e-424">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="ec05e-424">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="ec05e-425">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="ec05e-425">Don't use a wildcard binding.</span></span> <span data-ttu-id="ec05e-426">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-426">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="ec05e-427">URL 必須包含結尾斜線。\*\*</span><span class="sxs-lookup"><span data-stu-id="ec05e-427">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="ec05e-428">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="ec05e-428">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="ec05e-429">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-429">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="ec05e-430">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-430">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="ec05e-431">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="ec05e-431">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="ec05e-432">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-432">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="ec05e-433">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="ec05e-433">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="ec05e-434">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-434">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="ec05e-435">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="ec05e-435">Don't use a wildcard binding.</span></span> <span data-ttu-id="ec05e-436">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-436">Use a valid IP address.</span></span>
   * <span data-ttu-id="ec05e-437">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-437">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="ec05e-438">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="ec05e-438">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="ec05e-439">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-439">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="ec05e-440">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="ec05e-440">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="ec05e-441">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="ec05e-441">In Visual Studio:</span></span>
     * <span data-ttu-id="ec05e-442">在 [方案總管]\*\*\*\* 中的專案上按一下滑鼠右鍵，然後選取 [屬性]\*\*\*\*，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="ec05e-442">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="ec05e-443">選取 [套件]\*\*\*\* 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ec05e-443">Select the **Package** tab.</span></span>
     * <span data-ttu-id="ec05e-444">輸入您在 [標記]\*\*\*\* 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="ec05e-444">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="ec05e-445">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="ec05e-445">When not using Visual Studio:</span></span>
     * <span data-ttu-id="ec05e-446">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="ec05e-446">Open the app's project file.</span></span>
     * <span data-ttu-id="ec05e-447">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-447">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="ec05e-448">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="ec05e-448">In the following example:</span></span>

   * <span data-ttu-id="ec05e-449">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-449">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="ec05e-450">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="ec05e-450">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="ec05e-451">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-451">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="ec05e-452">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="ec05e-452">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="ec05e-453">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="ec05e-453">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="ec05e-454">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="ec05e-454">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="ec05e-455">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="ec05e-455">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="ec05e-456">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-456">Run the app.</span></span>

   <span data-ttu-id="ec05e-457">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-457">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="ec05e-458">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-458">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="ec05e-459">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-459">The app responds at the server's public IP address.</span></span> <span data-ttu-id="ec05e-460">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="ec05e-460">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="ec05e-461">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-461">A development certificate is used in this example.</span></span> <span data-ttu-id="ec05e-462">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="ec05e-462">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ec05e-464">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="ec05e-464">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ec05e-465">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="ec05e-465">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="ec05e-466">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-466">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec05e-467">其他資源</span><span class="sxs-lookup"><span data-stu-id="ec05e-467">Additional resources</span></span>

* <span data-ttu-id="ec05e-468">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-468">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="ec05e-469">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-469">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="ec05e-470">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-470">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="ec05e-471">主機</span><span class="sxs-lookup"><span data-stu-id="ec05e-471">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ec05e-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-472">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="ec05e-473">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="ec05e-473">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ec05e-474">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-474">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="ec05e-475">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="ec05e-475">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="ec05e-476">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="ec05e-476">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="ec05e-477">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="ec05e-477">Port sharing</span></span>
* <span data-ttu-id="ec05e-478">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="ec05e-478">HTTPS with SNI</span></span>
* <span data-ttu-id="ec05e-479">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="ec05e-479">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="ec05e-480">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="ec05e-480">Direct file transmission</span></span>
* <span data-ttu-id="ec05e-481">回應快取</span><span class="sxs-lookup"><span data-stu-id="ec05e-481">Response caching</span></span>
* <span data-ttu-id="ec05e-482">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="ec05e-482">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="ec05e-483">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="ec05e-483">Supported Windows versions:</span></span>

* <span data-ttu-id="ec05e-484">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-484">Windows 7 or later</span></span>
* <span data-ttu-id="ec05e-485">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-485">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="ec05e-486">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="ec05e-486">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="ec05e-487">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="ec05e-487">When to use HTTP.sys</span></span>

<span data-ttu-id="ec05e-488">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="ec05e-488">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="ec05e-489">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="ec05e-489">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="ec05e-491">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-491">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="ec05e-493">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="ec05e-493">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="ec05e-494">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="ec05e-494">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="ec05e-495">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="ec05e-495">HTTP/2 support</span></span>

<span data-ttu-id="ec05e-496">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="ec05e-496">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="ec05e-497">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-497">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="ec05e-498">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="ec05e-498">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="ec05e-499">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="ec05e-499">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ec05e-500">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-500">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="ec05e-501">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-501">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="ec05e-502">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="ec05e-502">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="ec05e-503">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="ec05e-503">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="ec05e-504">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="ec05e-504">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="ec05e-505">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-505">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="ec05e-506">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-506">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="ec05e-507">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="ec05e-507">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="ec05e-508">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="ec05e-508">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="ec05e-509">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ec05e-509">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="ec05e-510">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ec05e-510">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="ec05e-511">使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 時，不需要專案檔中的套件參考。</span><span class="sxs-lookup"><span data-stu-id="ec05e-511">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="ec05e-512">若不是使用 `Microsoft.AspNetCore.App` 中繼套件，請將套件參考加入 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-512">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="ec05e-513">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="ec05e-513">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="ec05e-514">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="ec05e-514">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="ec05e-515">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="ec05e-515">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="ec05e-516">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="ec05e-516">**HTTP.sys options**</span></span>

| <span data-ttu-id="ec05e-517">屬性</span><span class="sxs-lookup"><span data-stu-id="ec05e-517">Property</span></span> | <span data-ttu-id="ec05e-518">描述</span><span class="sxs-lookup"><span data-stu-id="ec05e-518">Description</span></span> | <span data-ttu-id="ec05e-519">預設</span><span class="sxs-lookup"><span data-stu-id="ec05e-519">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="ec05e-520">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="ec05e-520">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="ec05e-521">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="ec05e-521">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="ec05e-522">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="ec05e-522">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="ec05e-523">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="ec05e-523">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="ec05e-524">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="ec05e-524">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="ec05e-525">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="ec05e-525">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="ec05e-526">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="ec05e-526">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="ec05e-527">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="ec05e-527">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="ec05e-528">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="ec05e-528">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="ec05e-529">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="ec05e-529">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="ec05e-530">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-530">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="ec05e-531">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-531">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="ec05e-532">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-532">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="ec05e-533">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="ec05e-533">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="ec05e-534">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-534">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="ec05e-535">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="ec05e-535">Use `-1` for infinite.</span></span> <span data-ttu-id="ec05e-536">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-536">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="ec05e-537"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="ec05e-537">(machine-wide</span></span><br><span data-ttu-id="ec05e-538">設定) </span><span class="sxs-lookup"><span data-stu-id="ec05e-538">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="ec05e-539">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="ec05e-539">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="ec05e-540">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="ec05e-540">30000000 bytes</span></span><br><span data-ttu-id="ec05e-541">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="ec05e-541">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="ec05e-542">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="ec05e-542">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="ec05e-543">1000</span><span class="sxs-lookup"><span data-stu-id="ec05e-543">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="ec05e-544">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="ec05e-544">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="ec05e-545">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="ec05e-545">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="ec05e-546">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-546">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="ec05e-547">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="ec05e-547">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="ec05e-548">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 keep-alive 連線上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-548">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="ec05e-549">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-549">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="ec05e-550">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-550">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="ec05e-551">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-551">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="ec05e-552">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="ec05e-552">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="ec05e-553">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-553">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="ec05e-554">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="ec05e-554">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="ec05e-555">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="ec05e-555">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="ec05e-556">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="ec05e-556">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="ec05e-557">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="ec05e-557">**MaxRequestBodySize**</span></span>

<span data-ttu-id="ec05e-558">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-558">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="ec05e-559">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="ec05e-559">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="ec05e-560">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="ec05e-560">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="ec05e-561">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="ec05e-561">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ec05e-562">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ec05e-562">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="ec05e-563">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="ec05e-563">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ec05e-564">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="ec05e-564">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="ec05e-565">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ec05e-565">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="ec05e-566">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ec05e-566">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="ec05e-567">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="ec05e-567">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="ec05e-569">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="ec05e-569">Configure Windows Server</span></span>

1. <span data-ttu-id="ec05e-570">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="ec05e-570">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="ec05e-571">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="ec05e-571">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="ec05e-572">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-572">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="ec05e-573">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="ec05e-573">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="ec05e-574">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-574">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="ec05e-575">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-575">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="ec05e-576">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-576">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="ec05e-577">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** > \*\*\*\* 存放區中。</span><span class="sxs-lookup"><span data-stu-id="ec05e-577">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="ec05e-578">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-578">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="ec05e-579">**.Net core**：如果應用程式需要 .net core，請從[.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行**時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-579">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="ec05e-580">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="ec05e-580">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="ec05e-581">**.NET Framework**：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-581">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="ec05e-582">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="ec05e-582">Install the required .NET Framework.</span></span> <span data-ttu-id="ec05e-583">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-583">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="ec05e-584">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="ec05e-584">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="ec05e-585">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="ec05e-585">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="ec05e-586">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-586">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="ec05e-587">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-587">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="ec05e-588">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="ec05e-588">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="ec05e-589">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="ec05e-589">`urls` command-line argument</span></span>
   * <span data-ttu-id="ec05e-590">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="ec05e-590">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="ec05e-591">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-591">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="ec05e-592">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="ec05e-592">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="ec05e-593">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-593">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="ec05e-594">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="ec05e-594">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="ec05e-595">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-595">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="ec05e-596">請**勿**使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-596">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="ec05e-597">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="ec05e-597">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="ec05e-598">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-598">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="ec05e-599">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="ec05e-599">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="ec05e-600">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="ec05e-600">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="ec05e-601">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-601">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="ec05e-602">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="ec05e-602">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="ec05e-603">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="ec05e-603">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="ec05e-604">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-604">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="ec05e-605">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-605">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="ec05e-606">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="ec05e-606">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="ec05e-607">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="ec05e-607">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="ec05e-608">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="ec05e-608">Don't use a wildcard binding.</span></span> <span data-ttu-id="ec05e-609">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-609">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="ec05e-610">URL 必須包含結尾斜線。\*\*</span><span class="sxs-lookup"><span data-stu-id="ec05e-610">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="ec05e-611">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="ec05e-611">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="ec05e-612">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-612">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="ec05e-613">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-613">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="ec05e-614">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="ec05e-614">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="ec05e-615">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-615">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="ec05e-616">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="ec05e-616">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="ec05e-617">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-617">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="ec05e-618">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="ec05e-618">Don't use a wildcard binding.</span></span> <span data-ttu-id="ec05e-619">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-619">Use a valid IP address.</span></span>
   * <span data-ttu-id="ec05e-620">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-620">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="ec05e-621">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="ec05e-621">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="ec05e-622">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-622">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="ec05e-623">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="ec05e-623">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="ec05e-624">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="ec05e-624">In Visual Studio:</span></span>
     * <span data-ttu-id="ec05e-625">在 [方案總管]\*\*\*\* 中的專案上按一下滑鼠右鍵，然後選取 [屬性]\*\*\*\*，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="ec05e-625">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="ec05e-626">選取 [套件]\*\*\*\* 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ec05e-626">Select the **Package** tab.</span></span>
     * <span data-ttu-id="ec05e-627">輸入您在 [標記]\*\*\*\* 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="ec05e-627">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="ec05e-628">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="ec05e-628">When not using Visual Studio:</span></span>
     * <span data-ttu-id="ec05e-629">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="ec05e-629">Open the app's project file.</span></span>
     * <span data-ttu-id="ec05e-630">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-630">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="ec05e-631">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="ec05e-631">In the following example:</span></span>

   * <span data-ttu-id="ec05e-632">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-632">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="ec05e-633">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="ec05e-633">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="ec05e-634">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-634">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="ec05e-635">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="ec05e-635">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="ec05e-636">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="ec05e-636">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="ec05e-637">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="ec05e-637">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="ec05e-638">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="ec05e-638">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="ec05e-639">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-639">Run the app.</span></span>

   <span data-ttu-id="ec05e-640">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-640">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="ec05e-641">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-641">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="ec05e-642">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-642">The app responds at the server's public IP address.</span></span> <span data-ttu-id="ec05e-643">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="ec05e-643">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="ec05e-644">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-644">A development certificate is used in this example.</span></span> <span data-ttu-id="ec05e-645">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="ec05e-645">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ec05e-647">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="ec05e-647">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ec05e-648">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="ec05e-648">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="ec05e-649">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-649">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec05e-650">其他資源</span><span class="sxs-lookup"><span data-stu-id="ec05e-650">Additional resources</span></span>

* <span data-ttu-id="ec05e-651">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-651">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="ec05e-652">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-652">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="ec05e-653">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-653">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="ec05e-654">主機</span><span class="sxs-lookup"><span data-stu-id="ec05e-654">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="ec05e-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-655">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="ec05e-656">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="ec05e-656">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ec05e-657">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-657">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="ec05e-658">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="ec05e-658">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="ec05e-659">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="ec05e-659">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="ec05e-660">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="ec05e-660">Port sharing</span></span>
* <span data-ttu-id="ec05e-661">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="ec05e-661">HTTPS with SNI</span></span>
* <span data-ttu-id="ec05e-662">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="ec05e-662">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="ec05e-663">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="ec05e-663">Direct file transmission</span></span>
* <span data-ttu-id="ec05e-664">回應快取</span><span class="sxs-lookup"><span data-stu-id="ec05e-664">Response caching</span></span>
* <span data-ttu-id="ec05e-665">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="ec05e-665">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="ec05e-666">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="ec05e-666">Supported Windows versions:</span></span>

* <span data-ttu-id="ec05e-667">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-667">Windows 7 or later</span></span>
* <span data-ttu-id="ec05e-668">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-668">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="ec05e-669">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="ec05e-669">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="ec05e-670">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="ec05e-670">When to use HTTP.sys</span></span>

<span data-ttu-id="ec05e-671">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="ec05e-671">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="ec05e-672">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="ec05e-672">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="ec05e-674">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-674">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="ec05e-676">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="ec05e-676">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="ec05e-677">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="ec05e-677">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="ec05e-678">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="ec05e-678">HTTP/2 support</span></span>

<span data-ttu-id="ec05e-679">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="ec05e-679">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="ec05e-680">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="ec05e-680">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="ec05e-681">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="ec05e-681">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="ec05e-682">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="ec05e-682">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ec05e-683">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-683">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="ec05e-684">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-684">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="ec05e-685">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="ec05e-685">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="ec05e-686">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="ec05e-686">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="ec05e-687">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="ec05e-687">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="ec05e-688">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-688">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="ec05e-689">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-689">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="ec05e-690">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="ec05e-690">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="ec05e-691">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="ec05e-691">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="ec05e-692">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ec05e-692">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="ec05e-693">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="ec05e-693">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="ec05e-694">使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 時，不需要專案檔中的套件參考。</span><span class="sxs-lookup"><span data-stu-id="ec05e-694">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="ec05e-695">若不是使用 `Microsoft.AspNetCore.App` 中繼套件，請將套件參考加入 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-695">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="ec05e-696">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="ec05e-696">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="ec05e-697">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="ec05e-697">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="ec05e-698">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="ec05e-698">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="ec05e-699">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="ec05e-699">**HTTP.sys options**</span></span>

| <span data-ttu-id="ec05e-700">屬性</span><span class="sxs-lookup"><span data-stu-id="ec05e-700">Property</span></span> | <span data-ttu-id="ec05e-701">描述</span><span class="sxs-lookup"><span data-stu-id="ec05e-701">Description</span></span> | <span data-ttu-id="ec05e-702">預設</span><span class="sxs-lookup"><span data-stu-id="ec05e-702">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="ec05e-703">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="ec05e-703">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="ec05e-704">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="ec05e-704">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="ec05e-705">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="ec05e-705">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="ec05e-706">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="ec05e-706">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="ec05e-707">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="ec05e-707">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="ec05e-708">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="ec05e-708">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="ec05e-709">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="ec05e-709">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="ec05e-710">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="ec05e-710">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="ec05e-711">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="ec05e-711">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="ec05e-712">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="ec05e-712">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="ec05e-713">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-713">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="ec05e-714">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-714">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="ec05e-715">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-715">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="ec05e-716">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="ec05e-716">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="ec05e-717">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-717">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="ec05e-718">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="ec05e-718">Use `-1` for infinite.</span></span> <span data-ttu-id="ec05e-719">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-719">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="ec05e-720"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="ec05e-720">(machine-wide</span></span><br><span data-ttu-id="ec05e-721">設定) </span><span class="sxs-lookup"><span data-stu-id="ec05e-721">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="ec05e-722">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="ec05e-722">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="ec05e-723">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="ec05e-723">30000000 bytes</span></span><br><span data-ttu-id="ec05e-724">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="ec05e-724">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="ec05e-725">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="ec05e-725">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="ec05e-726">1000</span><span class="sxs-lookup"><span data-stu-id="ec05e-726">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="ec05e-727">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="ec05e-727">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="ec05e-728">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="ec05e-728">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="ec05e-729">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-729">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="ec05e-730">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="ec05e-730">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="ec05e-731">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 keep-alive 連線上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-731">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="ec05e-732">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-732">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="ec05e-733">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="ec05e-733">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="ec05e-734">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-734">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="ec05e-735">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="ec05e-735">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="ec05e-736">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="ec05e-736">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="ec05e-737">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="ec05e-737">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="ec05e-738">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="ec05e-738">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="ec05e-739">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="ec05e-739">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="ec05e-740">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="ec05e-740">**MaxRequestBodySize**</span></span>

<span data-ttu-id="ec05e-741">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-741">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="ec05e-742">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="ec05e-742">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="ec05e-743">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="ec05e-743">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="ec05e-744">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="ec05e-744">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ec05e-745">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="ec05e-745">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="ec05e-746">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="ec05e-746">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ec05e-747">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="ec05e-747">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="ec05e-748">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ec05e-748">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="ec05e-749">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ec05e-749">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="ec05e-750">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="ec05e-750">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="ec05e-752">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="ec05e-752">Configure Windows Server</span></span>

1. <span data-ttu-id="ec05e-753">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="ec05e-753">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="ec05e-754">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="ec05e-754">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="ec05e-755">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-755">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="ec05e-756">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="ec05e-756">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="ec05e-757">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-757">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="ec05e-758">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-758">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="ec05e-759">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-759">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="ec05e-760">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** > \*\*\*\* 存放區中。</span><span class="sxs-lookup"><span data-stu-id="ec05e-760">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="ec05e-761">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-761">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="ec05e-762">**.Net core**：如果應用程式需要 .net core，請從[.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行**時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-762">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="ec05e-763">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="ec05e-763">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="ec05e-764">**.NET Framework**：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-764">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="ec05e-765">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="ec05e-765">Install the required .NET Framework.</span></span> <span data-ttu-id="ec05e-766">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-766">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="ec05e-767">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="ec05e-767">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="ec05e-768">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="ec05e-768">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="ec05e-769">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-769">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="ec05e-770">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-770">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="ec05e-771">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="ec05e-771">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="ec05e-772">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="ec05e-772">`urls` command-line argument</span></span>
   * <span data-ttu-id="ec05e-773">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="ec05e-773">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="ec05e-774">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-774">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="ec05e-775">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="ec05e-775">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="ec05e-776">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="ec05e-776">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="ec05e-777">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="ec05e-777">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="ec05e-778">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-778">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="ec05e-779">請**勿**使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-779">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="ec05e-780">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="ec05e-780">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="ec05e-781">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-781">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="ec05e-782">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="ec05e-782">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="ec05e-783">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="ec05e-783">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="ec05e-784">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-784">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="ec05e-785">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="ec05e-785">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="ec05e-786">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="ec05e-786">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="ec05e-787">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-787">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="ec05e-788">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="ec05e-788">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="ec05e-789">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="ec05e-789">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="ec05e-790">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="ec05e-790">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="ec05e-791">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="ec05e-791">Don't use a wildcard binding.</span></span> <span data-ttu-id="ec05e-792">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-792">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="ec05e-793">URL 必須包含結尾斜線。\*\*</span><span class="sxs-lookup"><span data-stu-id="ec05e-793">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="ec05e-794">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="ec05e-794">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="ec05e-795">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-795">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="ec05e-796">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-796">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="ec05e-797">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="ec05e-797">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="ec05e-798">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-798">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="ec05e-799">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="ec05e-799">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="ec05e-800">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-800">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="ec05e-801">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="ec05e-801">Don't use a wildcard binding.</span></span> <span data-ttu-id="ec05e-802">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="ec05e-802">Use a valid IP address.</span></span>
   * <span data-ttu-id="ec05e-803">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="ec05e-803">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="ec05e-804">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="ec05e-804">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="ec05e-805">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="ec05e-805">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="ec05e-806">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="ec05e-806">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="ec05e-807">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="ec05e-807">In Visual Studio:</span></span>
     * <span data-ttu-id="ec05e-808">在 [方案總管]\*\*\*\* 中的專案上按一下滑鼠右鍵，然後選取 [屬性]\*\*\*\*，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="ec05e-808">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="ec05e-809">選取 [套件]\*\*\*\* 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="ec05e-809">Select the **Package** tab.</span></span>
     * <span data-ttu-id="ec05e-810">輸入您在 [標記]\*\*\*\* 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="ec05e-810">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="ec05e-811">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="ec05e-811">When not using Visual Studio:</span></span>
     * <span data-ttu-id="ec05e-812">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="ec05e-812">Open the app's project file.</span></span>
     * <span data-ttu-id="ec05e-813">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="ec05e-813">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="ec05e-814">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="ec05e-814">In the following example:</span></span>

   * <span data-ttu-id="ec05e-815">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="ec05e-815">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="ec05e-816">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="ec05e-816">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="ec05e-817">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-817">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="ec05e-818">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="ec05e-818">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="ec05e-819">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="ec05e-819">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="ec05e-820">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="ec05e-820">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="ec05e-821">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="ec05e-821">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="ec05e-822">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-822">Run the app.</span></span>

   <span data-ttu-id="ec05e-823">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-823">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="ec05e-824">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="ec05e-824">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="ec05e-825">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="ec05e-825">The app responds at the server's public IP address.</span></span> <span data-ttu-id="ec05e-826">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="ec05e-826">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="ec05e-827">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="ec05e-827">A development certificate is used in this example.</span></span> <span data-ttu-id="ec05e-828">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="ec05e-828">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="ec05e-830">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="ec05e-830">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="ec05e-831">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="ec05e-831">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="ec05e-832">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="ec05e-832">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ec05e-833">其他資源</span><span class="sxs-lookup"><span data-stu-id="ec05e-833">Additional resources</span></span>

* <span data-ttu-id="ec05e-834">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-834">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="ec05e-835">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-835">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="ec05e-836">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="ec05e-836">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="ec05e-837">主機</span><span class="sxs-lookup"><span data-stu-id="ec05e-837">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end