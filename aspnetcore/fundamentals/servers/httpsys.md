---
title: ASP.NET Core 中的 HTTP.sys 網頁伺服器實作
author: rick-anderson
description: 深入了解 HTTP.sys，這是 Windows 上的 ASP.NET Core 網頁伺服器。 HTTP.sys 建置在 HTTP.sys 核心模式驅動程式之上，是 Kestrel 的替代方式，可以用來直接連線到網際網路而不使用 IIS。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: e44cdcb7e427c1ae2531c452a7c8b49e104b3d11
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102586068"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="a471d-104">ASP.NET Core 中的 HTTP.sys 網頁伺服器實作</span><span class="sxs-lookup"><span data-stu-id="a471d-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="a471d-105">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="a471d-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="a471d-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="a471d-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="a471d-107">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="a471d-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a471d-108">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="a471d-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="a471d-109">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="a471d-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="a471d-110">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="a471d-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="a471d-111">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="a471d-111">Port sharing</span></span>
* <span data-ttu-id="a471d-112">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="a471d-112">HTTPS with SNI</span></span>
* <span data-ttu-id="a471d-113">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="a471d-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="a471d-114">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="a471d-114">Direct file transmission</span></span>
* <span data-ttu-id="a471d-115">回應快取</span><span class="sxs-lookup"><span data-stu-id="a471d-115">Response caching</span></span>
* <span data-ttu-id="a471d-116">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="a471d-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="a471d-117">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="a471d-117">Supported Windows versions:</span></span>

* <span data-ttu-id="a471d-118">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-118">Windows 7 or later</span></span>
* <span data-ttu-id="a471d-119">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a471d-120">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a471d-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="a471d-121">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="a471d-121">When to use HTTP.sys</span></span>

<span data-ttu-id="a471d-122">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="a471d-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="a471d-123">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="a471d-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="a471d-125">內部部署需要無法在 Kestrel 中使用的功能。</span><span class="sxs-lookup"><span data-stu-id="a471d-125">An internal deployment requires a feature not available in Kestrel.</span></span> <span data-ttu-id="a471d-126">如需詳細資訊，請參閱 [Kestrel 與 HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span><span class="sxs-lookup"><span data-stu-id="a471d-126">For more information, see [Kestrel vs. HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="a471d-128">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="a471d-128">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="a471d-129">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="a471d-129">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a471d-130">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="a471d-130">HTTP/2 support</span></span>

<span data-ttu-id="a471d-131">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="a471d-131">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="a471d-132">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-132">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="a471d-133">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="a471d-133">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="a471d-134">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="a471d-134">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a471d-135">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="a471d-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="a471d-136">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="a471d-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a471d-137">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="a471d-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="a471d-138">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="a471d-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="a471d-139">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="a471d-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="a471d-140">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="a471d-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a471d-141">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="a471d-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a471d-142">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="a471d-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a471d-143">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="a471d-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="a471d-144">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a471d-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="a471d-145">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a471d-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="a471d-146">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="a471d-146">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="a471d-147">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="a471d-147">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="a471d-148">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="a471d-148">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="a471d-149">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="a471d-149">**HTTP.sys options**</span></span>

| <span data-ttu-id="a471d-150">屬性</span><span class="sxs-lookup"><span data-stu-id="a471d-150">Property</span></span> | <span data-ttu-id="a471d-151">描述</span><span class="sxs-lookup"><span data-stu-id="a471d-151">Description</span></span> | <span data-ttu-id="a471d-152">預設</span><span class="sxs-lookup"><span data-stu-id="a471d-152">Default</span></span> |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | <span data-ttu-id="a471d-153">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="a471d-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="a471d-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="a471d-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="a471d-155">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="a471d-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="a471d-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="a471d-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="a471d-157">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="a471d-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="a471d-158">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="a471d-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="a471d-159">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="a471d-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | <span data-ttu-id="a471d-160">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="a471d-160">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="a471d-161">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-161">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="a471d-162">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-162">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | <span data-ttu-id="a471d-163">因為節流狀況而拒絕要求時的 HTTP.sys 行為。</span><span class="sxs-lookup"><span data-stu-id="a471d-163">The HTTP.sys behavior when rejecting requests due to throttling conditions.</span></span> | [<span data-ttu-id="a471d-164">Http503VerbosityLevel。 <br>基本</span><span class="sxs-lookup"><span data-stu-id="a471d-164">Http503VerbosityLevel.<br>Basic</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="a471d-165">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="a471d-165">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="a471d-166">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="a471d-166">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="a471d-167">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="a471d-167">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="a471d-168">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="a471d-168">Use `-1` for infinite.</span></span> <span data-ttu-id="a471d-169">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-169">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="a471d-170"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="a471d-170">(machine-wide</span></span><br><span data-ttu-id="a471d-171">設定) </span><span class="sxs-lookup"><span data-stu-id="a471d-171">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="a471d-172">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="a471d-172">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="a471d-173">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="a471d-173">30000000 bytes</span></span><br><span data-ttu-id="a471d-174">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="a471d-174">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="a471d-175">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="a471d-175">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="a471d-176">1000</span><span class="sxs-lookup"><span data-stu-id="a471d-176">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="a471d-177">這會指出伺服器是否負責建立和設定要求佇列，或是否應該附加至現有的佇列。</span><span class="sxs-lookup"><span data-stu-id="a471d-177">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="a471d-178">附加至現有的佇列時，大部分現有的設定選項都不適用。</span><span class="sxs-lookup"><span data-stu-id="a471d-178">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="a471d-179">HTTP.sys 要求佇列的名稱。</span><span class="sxs-lookup"><span data-stu-id="a471d-179">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="a471d-180">`null` (匿名佇列) </span><span class="sxs-lookup"><span data-stu-id="a471d-180">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="a471d-181">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="a471d-181">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="a471d-182">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="a471d-182">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="a471d-183">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-183">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="a471d-184">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="a471d-184">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="a471d-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體的允許時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="a471d-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>：允許要求實體主體抵達的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="a471d-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>： HTTP 伺服器 API 用來剖析要求標頭的允許時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="a471d-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="a471d-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="a471d-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: The minimum send rate for the response.</span></span></li><li><span data-ttu-id="a471d-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="a471d-191">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="a471d-191">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="a471d-192">最有用的是 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType> ，它是用來將前置詞加入至集合。</span><span class="sxs-lookup"><span data-stu-id="a471d-192">The most useful is <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>, which is used to add a prefix to the collection.</span></span> <span data-ttu-id="a471d-193">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="a471d-193">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="a471d-194">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="a471d-194">**MaxRequestBodySize**</span></span>

<span data-ttu-id="a471d-195">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="a471d-195">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="a471d-196">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="a471d-196">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="a471d-197">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="a471d-197">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="a471d-198">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="a471d-198">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="a471d-199">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a471d-199">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="a471d-200">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="a471d-200">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="a471d-201">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="a471d-201">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="a471d-202">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="a471d-202">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="a471d-203">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="a471d-203">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="a471d-204">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="a471d-204">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="a471d-206">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="a471d-206">Configure Windows Server</span></span>

1. <span data-ttu-id="a471d-207">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="a471d-207">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="a471d-208">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="a471d-208">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a471d-209">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-209">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="a471d-210">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="a471d-210">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a471d-211">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-211">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="a471d-212">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-212">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="a471d-213">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a471d-213">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="a471d-214">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** >  存放區中。</span><span class="sxs-lookup"><span data-stu-id="a471d-214">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="a471d-215">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="a471d-215">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="a471d-216">**.Net core**：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-216">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a471d-217">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="a471d-217">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="a471d-218">**.Net framework**：如果應用程式需要 .net framework，請參閱 [.net framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="a471d-218">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="a471d-219">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="a471d-219">Install the required .NET Framework.</span></span> <span data-ttu-id="a471d-220">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-220">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="a471d-221">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="a471d-221">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="a471d-222">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="a471d-222">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="a471d-223">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-223">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="a471d-224">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="a471d-224">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a471d-225">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="a471d-225">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="a471d-226">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="a471d-226">`urls` command-line argument</span></span>
   * <span data-ttu-id="a471d-227">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="a471d-227">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="a471d-228">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="a471d-228">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="a471d-229">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a471d-229">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="a471d-230">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-230">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="a471d-231">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="a471d-231">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="a471d-232">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="a471d-232">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="a471d-233">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="a471d-233">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a471d-234">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="a471d-234">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="a471d-235">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="a471d-235">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a471d-236">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="a471d-236">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="a471d-237">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="a471d-237">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a471d-238">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="a471d-238">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="a471d-239">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="a471d-239">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="a471d-240">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="a471d-240">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="a471d-241">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-241">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="a471d-242">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="a471d-242">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="a471d-243">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="a471d-243">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="a471d-244">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="a471d-244">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="a471d-245">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="a471d-245">Don't use a wildcard binding.</span></span> <span data-ttu-id="a471d-246">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-246">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="a471d-247">URL 必須包含結尾斜線。</span><span class="sxs-lookup"><span data-stu-id="a471d-247">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="a471d-248">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="a471d-248">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="a471d-249">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="a471d-249">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="a471d-250">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-250">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="a471d-251">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="a471d-251">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="a471d-252">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-252">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="a471d-253">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="a471d-253">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="a471d-254">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-254">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="a471d-255">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="a471d-255">Don't use a wildcard binding.</span></span> <span data-ttu-id="a471d-256">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-256">Use a valid IP address.</span></span>
   * <span data-ttu-id="a471d-257">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-257">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="a471d-258">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="a471d-258">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="a471d-259">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="a471d-259">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="a471d-260">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="a471d-260">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="a471d-261">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="a471d-261">In Visual Studio:</span></span>
     * <span data-ttu-id="a471d-262">在 [方案總管] 中的專案上按一下滑鼠右鍵，然後選取 [屬性]，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="a471d-262">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="a471d-263">選取 [套件] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="a471d-263">Select the **Package** tab.</span></span>
     * <span data-ttu-id="a471d-264">輸入您在 [標記] 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="a471d-264">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="a471d-265">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="a471d-265">When not using Visual Studio:</span></span>
     * <span data-ttu-id="a471d-266">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="a471d-266">Open the app's project file.</span></span>
     * <span data-ttu-id="a471d-267">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="a471d-267">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="a471d-268">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="a471d-268">In the following example:</span></span>

   * <span data-ttu-id="a471d-269">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="a471d-269">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="a471d-270">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="a471d-270">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="a471d-271">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-271">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="a471d-272">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="a471d-272">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="a471d-273">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="a471d-273">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="a471d-274">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="a471d-274">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="a471d-275">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="a471d-275">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="a471d-276">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-276">Run the app.</span></span>

   <span data-ttu-id="a471d-277">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-277">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="a471d-278">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-278">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="a471d-279">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-279">The app responds at the server's public IP address.</span></span> <span data-ttu-id="a471d-280">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="a471d-280">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="a471d-281">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-281">A development certificate is used in this example.</span></span> <span data-ttu-id="a471d-282">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="a471d-282">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a471d-284">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="a471d-284">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a471d-285">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="a471d-285">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="a471d-286">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="a471d-286">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="a471d-287">支援 gRPC 的 Advanced HTTP/2 功能</span><span class="sxs-lookup"><span data-stu-id="a471d-287">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="a471d-288">HTTP.sys 中的額外 HTTP/2 功能支援 gRPC，包括對回應結尾和傳送重設框架的支援。</span><span class="sxs-lookup"><span data-stu-id="a471d-288">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="a471d-289">使用 HTTP.sys 執行 gRPC 的需求：</span><span class="sxs-lookup"><span data-stu-id="a471d-289">Requirements to run gRPC with HTTP.sys:</span></span>

* <span data-ttu-id="a471d-290">Windows 10、OS 組建19041.508 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-290">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="a471d-291">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="a471d-291">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="a471d-292">預告片</span><span class="sxs-lookup"><span data-stu-id="a471d-292">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="a471d-293">重設</span><span class="sxs-lookup"><span data-stu-id="a471d-293">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="a471d-294">其他資源</span><span class="sxs-lookup"><span data-stu-id="a471d-294">Additional resources</span></span>

* <span data-ttu-id="a471d-295">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="a471d-295">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="a471d-296">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a471d-296">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="a471d-297">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a471d-297">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="a471d-298">主機</span><span class="sxs-lookup"><span data-stu-id="a471d-298">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="a471d-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="a471d-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="a471d-300">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="a471d-300">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a471d-301">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="a471d-301">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="a471d-302">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="a471d-302">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="a471d-303">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="a471d-303">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="a471d-304">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="a471d-304">Port sharing</span></span>
* <span data-ttu-id="a471d-305">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="a471d-305">HTTPS with SNI</span></span>
* <span data-ttu-id="a471d-306">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="a471d-306">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="a471d-307">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="a471d-307">Direct file transmission</span></span>
* <span data-ttu-id="a471d-308">回應快取</span><span class="sxs-lookup"><span data-stu-id="a471d-308">Response caching</span></span>
* <span data-ttu-id="a471d-309">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="a471d-309">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="a471d-310">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="a471d-310">Supported Windows versions:</span></span>

* <span data-ttu-id="a471d-311">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-311">Windows 7 or later</span></span>
* <span data-ttu-id="a471d-312">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-312">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a471d-313">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a471d-313">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="a471d-314">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="a471d-314">When to use HTTP.sys</span></span>

<span data-ttu-id="a471d-315">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="a471d-315">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="a471d-316">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="a471d-316">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="a471d-318">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="a471d-318">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="a471d-320">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="a471d-320">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="a471d-321">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="a471d-321">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a471d-322">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="a471d-322">HTTP/2 support</span></span>

<span data-ttu-id="a471d-323">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="a471d-323">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="a471d-324">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-324">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="a471d-325">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="a471d-325">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="a471d-326">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="a471d-326">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a471d-327">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="a471d-327">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="a471d-328">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="a471d-328">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a471d-329">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="a471d-329">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="a471d-330">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="a471d-330">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="a471d-331">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="a471d-331">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="a471d-332">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="a471d-332">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a471d-333">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="a471d-333">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a471d-334">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="a471d-334">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a471d-335">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="a471d-335">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="a471d-336">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a471d-336">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="a471d-337">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a471d-337">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="a471d-338">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="a471d-338">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="a471d-339">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="a471d-339">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="a471d-340">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="a471d-340">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="a471d-341">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="a471d-341">**HTTP.sys options**</span></span>

| <span data-ttu-id="a471d-342">屬性</span><span class="sxs-lookup"><span data-stu-id="a471d-342">Property</span></span> | <span data-ttu-id="a471d-343">描述</span><span class="sxs-lookup"><span data-stu-id="a471d-343">Description</span></span> | <span data-ttu-id="a471d-344">預設</span><span class="sxs-lookup"><span data-stu-id="a471d-344">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="a471d-345">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="a471d-345">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="a471d-346">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="a471d-346">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="a471d-347">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="a471d-347">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="a471d-348">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="a471d-348">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="a471d-349">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="a471d-349">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="a471d-350">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="a471d-350">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="a471d-351">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="a471d-351">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="a471d-352">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="a471d-352">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="a471d-353">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="a471d-353">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="a471d-354">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="a471d-354">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="a471d-355">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-355">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="a471d-356">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-356">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="a471d-357">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="a471d-357">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="a471d-358">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="a471d-358">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="a471d-359">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="a471d-359">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="a471d-360">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="a471d-360">Use `-1` for infinite.</span></span> <span data-ttu-id="a471d-361">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-361">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="a471d-362"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="a471d-362">(machine-wide</span></span><br><span data-ttu-id="a471d-363">設定) </span><span class="sxs-lookup"><span data-stu-id="a471d-363">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="a471d-364">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="a471d-364">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="a471d-365">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="a471d-365">30000000 bytes</span></span><br><span data-ttu-id="a471d-366">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="a471d-366">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="a471d-367">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="a471d-367">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="a471d-368">1000</span><span class="sxs-lookup"><span data-stu-id="a471d-368">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="a471d-369">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="a471d-369">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="a471d-370">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="a471d-370">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="a471d-371">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-371">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="a471d-372">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="a471d-372">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="a471d-373">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-373">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="a471d-374">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-374">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="a471d-375">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-375">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="a471d-376">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-376">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="a471d-377">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="a471d-377">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="a471d-378">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-378">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="a471d-379">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="a471d-379">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="a471d-380">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="a471d-380">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="a471d-381">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="a471d-381">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="a471d-382">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="a471d-382">**MaxRequestBodySize**</span></span>

<span data-ttu-id="a471d-383">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="a471d-383">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="a471d-384">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="a471d-384">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="a471d-385">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="a471d-385">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="a471d-386">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="a471d-386">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="a471d-387">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a471d-387">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="a471d-388">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="a471d-388">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="a471d-389">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="a471d-389">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="a471d-390">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="a471d-390">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="a471d-391">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="a471d-391">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="a471d-392">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="a471d-392">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="a471d-394">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="a471d-394">Configure Windows Server</span></span>

1. <span data-ttu-id="a471d-395">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="a471d-395">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="a471d-396">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="a471d-396">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a471d-397">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-397">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="a471d-398">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="a471d-398">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a471d-399">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-399">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="a471d-400">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-400">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="a471d-401">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a471d-401">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="a471d-402">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** >  存放區中。</span><span class="sxs-lookup"><span data-stu-id="a471d-402">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="a471d-403">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="a471d-403">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="a471d-404">**.Net core**：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-404">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a471d-405">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="a471d-405">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="a471d-406">**.Net framework**：如果應用程式需要 .net framework，請參閱 [.net framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="a471d-406">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="a471d-407">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="a471d-407">Install the required .NET Framework.</span></span> <span data-ttu-id="a471d-408">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-408">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="a471d-409">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="a471d-409">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="a471d-410">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="a471d-410">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="a471d-411">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-411">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="a471d-412">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="a471d-412">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a471d-413">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="a471d-413">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="a471d-414">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="a471d-414">`urls` command-line argument</span></span>
   * <span data-ttu-id="a471d-415">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="a471d-415">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="a471d-416">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="a471d-416">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="a471d-417">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a471d-417">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="a471d-418">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-418">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="a471d-419">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="a471d-419">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="a471d-420">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="a471d-420">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="a471d-421">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="a471d-421">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a471d-422">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="a471d-422">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="a471d-423">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="a471d-423">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a471d-424">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="a471d-424">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="a471d-425">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="a471d-425">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a471d-426">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="a471d-426">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="a471d-427">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="a471d-427">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="a471d-428">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="a471d-428">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="a471d-429">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-429">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="a471d-430">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="a471d-430">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="a471d-431">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="a471d-431">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="a471d-432">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="a471d-432">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="a471d-433">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="a471d-433">Don't use a wildcard binding.</span></span> <span data-ttu-id="a471d-434">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-434">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="a471d-435">URL 必須包含結尾斜線。</span><span class="sxs-lookup"><span data-stu-id="a471d-435">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="a471d-436">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="a471d-436">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="a471d-437">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="a471d-437">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="a471d-438">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-438">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="a471d-439">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="a471d-439">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="a471d-440">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-440">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="a471d-441">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="a471d-441">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="a471d-442">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-442">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="a471d-443">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="a471d-443">Don't use a wildcard binding.</span></span> <span data-ttu-id="a471d-444">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-444">Use a valid IP address.</span></span>
   * <span data-ttu-id="a471d-445">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-445">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="a471d-446">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="a471d-446">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="a471d-447">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="a471d-447">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="a471d-448">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="a471d-448">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="a471d-449">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="a471d-449">In Visual Studio:</span></span>
     * <span data-ttu-id="a471d-450">在 [方案總管] 中的專案上按一下滑鼠右鍵，然後選取 [屬性]，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="a471d-450">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="a471d-451">選取 [套件] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="a471d-451">Select the **Package** tab.</span></span>
     * <span data-ttu-id="a471d-452">輸入您在 [標記] 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="a471d-452">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="a471d-453">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="a471d-453">When not using Visual Studio:</span></span>
     * <span data-ttu-id="a471d-454">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="a471d-454">Open the app's project file.</span></span>
     * <span data-ttu-id="a471d-455">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="a471d-455">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="a471d-456">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="a471d-456">In the following example:</span></span>

   * <span data-ttu-id="a471d-457">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="a471d-457">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="a471d-458">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="a471d-458">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="a471d-459">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-459">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="a471d-460">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="a471d-460">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="a471d-461">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="a471d-461">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="a471d-462">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="a471d-462">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="a471d-463">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="a471d-463">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="a471d-464">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-464">Run the app.</span></span>

   <span data-ttu-id="a471d-465">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-465">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="a471d-466">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-466">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="a471d-467">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-467">The app responds at the server's public IP address.</span></span> <span data-ttu-id="a471d-468">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="a471d-468">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="a471d-469">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-469">A development certificate is used in this example.</span></span> <span data-ttu-id="a471d-470">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="a471d-470">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a471d-472">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="a471d-472">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a471d-473">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="a471d-473">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="a471d-474">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="a471d-474">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a471d-475">其他資源</span><span class="sxs-lookup"><span data-stu-id="a471d-475">Additional resources</span></span>

* <span data-ttu-id="a471d-476">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="a471d-476">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="a471d-477">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a471d-477">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="a471d-478">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a471d-478">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="a471d-479">主機</span><span class="sxs-lookup"><span data-stu-id="a471d-479">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a471d-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="a471d-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="a471d-481">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="a471d-481">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a471d-482">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="a471d-482">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="a471d-483">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="a471d-483">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="a471d-484">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="a471d-484">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="a471d-485">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="a471d-485">Port sharing</span></span>
* <span data-ttu-id="a471d-486">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="a471d-486">HTTPS with SNI</span></span>
* <span data-ttu-id="a471d-487">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="a471d-487">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="a471d-488">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="a471d-488">Direct file transmission</span></span>
* <span data-ttu-id="a471d-489">回應快取</span><span class="sxs-lookup"><span data-stu-id="a471d-489">Response caching</span></span>
* <span data-ttu-id="a471d-490">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="a471d-490">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="a471d-491">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="a471d-491">Supported Windows versions:</span></span>

* <span data-ttu-id="a471d-492">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-492">Windows 7 or later</span></span>
* <span data-ttu-id="a471d-493">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-493">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a471d-494">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a471d-494">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="a471d-495">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="a471d-495">When to use HTTP.sys</span></span>

<span data-ttu-id="a471d-496">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="a471d-496">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="a471d-497">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="a471d-497">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="a471d-499">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="a471d-499">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="a471d-501">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="a471d-501">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="a471d-502">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="a471d-502">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a471d-503">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="a471d-503">HTTP/2 support</span></span>

<span data-ttu-id="a471d-504">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="a471d-504">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="a471d-505">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-505">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="a471d-506">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="a471d-506">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="a471d-507">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="a471d-507">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a471d-508">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="a471d-508">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="a471d-509">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="a471d-509">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a471d-510">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="a471d-510">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="a471d-511">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="a471d-511">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="a471d-512">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="a471d-512">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="a471d-513">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="a471d-513">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a471d-514">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="a471d-514">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a471d-515">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="a471d-515">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a471d-516">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="a471d-516">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="a471d-517">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a471d-517">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="a471d-518">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a471d-518">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="a471d-519">使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 時，不需要專案檔中的套件參考。</span><span class="sxs-lookup"><span data-stu-id="a471d-519">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="a471d-520">若不是使用 `Microsoft.AspNetCore.App` 中繼套件，請將套件參考加入 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)。</span><span class="sxs-lookup"><span data-stu-id="a471d-520">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="a471d-521">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="a471d-521">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="a471d-522">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="a471d-522">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="a471d-523">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="a471d-523">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="a471d-524">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="a471d-524">**HTTP.sys options**</span></span>

| <span data-ttu-id="a471d-525">屬性</span><span class="sxs-lookup"><span data-stu-id="a471d-525">Property</span></span> | <span data-ttu-id="a471d-526">描述</span><span class="sxs-lookup"><span data-stu-id="a471d-526">Description</span></span> | <span data-ttu-id="a471d-527">預設</span><span class="sxs-lookup"><span data-stu-id="a471d-527">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="a471d-528">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="a471d-528">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="a471d-529">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="a471d-529">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="a471d-530">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="a471d-530">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="a471d-531">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="a471d-531">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="a471d-532">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="a471d-532">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="a471d-533">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="a471d-533">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="a471d-534">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="a471d-534">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="a471d-535">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="a471d-535">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="a471d-536">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="a471d-536">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="a471d-537">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="a471d-537">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="a471d-538">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-538">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="a471d-539">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-539">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="a471d-540">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="a471d-540">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="a471d-541">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="a471d-541">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="a471d-542">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="a471d-542">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="a471d-543">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="a471d-543">Use `-1` for infinite.</span></span> <span data-ttu-id="a471d-544">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-544">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="a471d-545"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="a471d-545">(machine-wide</span></span><br><span data-ttu-id="a471d-546">設定) </span><span class="sxs-lookup"><span data-stu-id="a471d-546">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="a471d-547">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="a471d-547">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="a471d-548">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="a471d-548">30000000 bytes</span></span><br><span data-ttu-id="a471d-549">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="a471d-549">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="a471d-550">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="a471d-550">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="a471d-551">1000</span><span class="sxs-lookup"><span data-stu-id="a471d-551">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="a471d-552">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="a471d-552">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="a471d-553">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="a471d-553">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="a471d-554">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-554">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="a471d-555">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="a471d-555">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="a471d-556">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-556">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="a471d-557">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-557">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="a471d-558">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-558">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="a471d-559">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-559">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="a471d-560">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="a471d-560">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="a471d-561">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-561">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="a471d-562">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="a471d-562">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="a471d-563">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="a471d-563">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="a471d-564">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="a471d-564">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="a471d-565">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="a471d-565">**MaxRequestBodySize**</span></span>

<span data-ttu-id="a471d-566">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="a471d-566">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="a471d-567">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="a471d-567">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="a471d-568">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="a471d-568">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="a471d-569">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="a471d-569">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="a471d-570">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a471d-570">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="a471d-571">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="a471d-571">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="a471d-572">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="a471d-572">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="a471d-573">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="a471d-573">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="a471d-574">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="a471d-574">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="a471d-575">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="a471d-575">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="a471d-577">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="a471d-577">Configure Windows Server</span></span>

1. <span data-ttu-id="a471d-578">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="a471d-578">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="a471d-579">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="a471d-579">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a471d-580">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-580">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="a471d-581">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="a471d-581">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a471d-582">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-582">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="a471d-583">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-583">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="a471d-584">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a471d-584">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="a471d-585">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** >  存放區中。</span><span class="sxs-lookup"><span data-stu-id="a471d-585">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="a471d-586">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="a471d-586">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="a471d-587">**.Net core**：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-587">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a471d-588">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="a471d-588">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="a471d-589">**.Net framework**：如果應用程式需要 .net framework，請參閱 [.net framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="a471d-589">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="a471d-590">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="a471d-590">Install the required .NET Framework.</span></span> <span data-ttu-id="a471d-591">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-591">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="a471d-592">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="a471d-592">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="a471d-593">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="a471d-593">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="a471d-594">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-594">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="a471d-595">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="a471d-595">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a471d-596">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="a471d-596">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="a471d-597">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="a471d-597">`urls` command-line argument</span></span>
   * <span data-ttu-id="a471d-598">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="a471d-598">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="a471d-599">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="a471d-599">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="a471d-600">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a471d-600">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="a471d-601">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-601">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="a471d-602">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="a471d-602">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="a471d-603">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="a471d-603">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="a471d-604">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="a471d-604">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a471d-605">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="a471d-605">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="a471d-606">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="a471d-606">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a471d-607">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="a471d-607">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="a471d-608">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="a471d-608">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a471d-609">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="a471d-609">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="a471d-610">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="a471d-610">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="a471d-611">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="a471d-611">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="a471d-612">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-612">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="a471d-613">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="a471d-613">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="a471d-614">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="a471d-614">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="a471d-615">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="a471d-615">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="a471d-616">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="a471d-616">Don't use a wildcard binding.</span></span> <span data-ttu-id="a471d-617">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-617">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="a471d-618">URL 必須包含結尾斜線。</span><span class="sxs-lookup"><span data-stu-id="a471d-618">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="a471d-619">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="a471d-619">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="a471d-620">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="a471d-620">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="a471d-621">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-621">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="a471d-622">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="a471d-622">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="a471d-623">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-623">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="a471d-624">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="a471d-624">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="a471d-625">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-625">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="a471d-626">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="a471d-626">Don't use a wildcard binding.</span></span> <span data-ttu-id="a471d-627">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-627">Use a valid IP address.</span></span>
   * <span data-ttu-id="a471d-628">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-628">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="a471d-629">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="a471d-629">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="a471d-630">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="a471d-630">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="a471d-631">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="a471d-631">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="a471d-632">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="a471d-632">In Visual Studio:</span></span>
     * <span data-ttu-id="a471d-633">在 [方案總管] 中的專案上按一下滑鼠右鍵，然後選取 [屬性]，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="a471d-633">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="a471d-634">選取 [套件] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="a471d-634">Select the **Package** tab.</span></span>
     * <span data-ttu-id="a471d-635">輸入您在 [標記] 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="a471d-635">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="a471d-636">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="a471d-636">When not using Visual Studio:</span></span>
     * <span data-ttu-id="a471d-637">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="a471d-637">Open the app's project file.</span></span>
     * <span data-ttu-id="a471d-638">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="a471d-638">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="a471d-639">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="a471d-639">In the following example:</span></span>

   * <span data-ttu-id="a471d-640">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="a471d-640">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="a471d-641">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="a471d-641">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="a471d-642">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-642">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="a471d-643">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="a471d-643">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="a471d-644">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="a471d-644">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="a471d-645">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="a471d-645">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="a471d-646">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="a471d-646">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="a471d-647">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-647">Run the app.</span></span>

   <span data-ttu-id="a471d-648">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-648">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="a471d-649">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-649">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="a471d-650">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-650">The app responds at the server's public IP address.</span></span> <span data-ttu-id="a471d-651">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="a471d-651">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="a471d-652">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-652">A development certificate is used in this example.</span></span> <span data-ttu-id="a471d-653">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="a471d-653">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a471d-655">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="a471d-655">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a471d-656">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="a471d-656">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="a471d-657">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="a471d-657">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a471d-658">其他資源</span><span class="sxs-lookup"><span data-stu-id="a471d-658">Additional resources</span></span>

* <span data-ttu-id="a471d-659">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="a471d-659">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="a471d-660">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a471d-660">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="a471d-661">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a471d-661">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="a471d-662">主機</span><span class="sxs-lookup"><span data-stu-id="a471d-662">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a471d-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="a471d-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="a471d-664">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="a471d-664">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a471d-665">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="a471d-665">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="a471d-666">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="a471d-666">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="a471d-667">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="a471d-667">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="a471d-668">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="a471d-668">Port sharing</span></span>
* <span data-ttu-id="a471d-669">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="a471d-669">HTTPS with SNI</span></span>
* <span data-ttu-id="a471d-670">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="a471d-670">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="a471d-671">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="a471d-671">Direct file transmission</span></span>
* <span data-ttu-id="a471d-672">回應快取</span><span class="sxs-lookup"><span data-stu-id="a471d-672">Response caching</span></span>
* <span data-ttu-id="a471d-673">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="a471d-673">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="a471d-674">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="a471d-674">Supported Windows versions:</span></span>

* <span data-ttu-id="a471d-675">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-675">Windows 7 or later</span></span>
* <span data-ttu-id="a471d-676">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-676">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="a471d-677">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="a471d-677">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="a471d-678">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="a471d-678">When to use HTTP.sys</span></span>

<span data-ttu-id="a471d-679">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="a471d-679">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="a471d-680">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="a471d-680">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="a471d-682">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="a471d-682">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="a471d-684">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="a471d-684">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="a471d-685">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="a471d-685">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="a471d-686">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="a471d-686">HTTP/2 support</span></span>

<span data-ttu-id="a471d-687">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="a471d-687">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="a471d-688">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="a471d-688">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="a471d-689">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="a471d-689">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="a471d-690">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="a471d-690">TLS 1.2 or later connection</span></span>

<span data-ttu-id="a471d-691">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="a471d-691">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="a471d-692">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="a471d-692">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="a471d-693">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="a471d-693">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="a471d-694">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="a471d-694">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="a471d-695">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="a471d-695">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="a471d-696">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="a471d-696">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a471d-697">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="a471d-697">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a471d-698">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="a471d-698">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a471d-699">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="a471d-699">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="a471d-700">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a471d-700">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="a471d-701">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a471d-701">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="a471d-702">使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 時，不需要專案檔中的套件參考。</span><span class="sxs-lookup"><span data-stu-id="a471d-702">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="a471d-703">若不是使用 `Microsoft.AspNetCore.App` 中繼套件，請將套件參考加入 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)。</span><span class="sxs-lookup"><span data-stu-id="a471d-703">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="a471d-704">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="a471d-704">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="a471d-705">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="a471d-705">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="a471d-706">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="a471d-706">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="a471d-707">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="a471d-707">**HTTP.sys options**</span></span>

| <span data-ttu-id="a471d-708">屬性</span><span class="sxs-lookup"><span data-stu-id="a471d-708">Property</span></span> | <span data-ttu-id="a471d-709">描述</span><span class="sxs-lookup"><span data-stu-id="a471d-709">Description</span></span> | <span data-ttu-id="a471d-710">預設</span><span class="sxs-lookup"><span data-stu-id="a471d-710">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="a471d-711">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="a471d-711">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="a471d-712">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="a471d-712">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="a471d-713">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="a471d-713">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="a471d-714">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="a471d-714">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="a471d-715">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="a471d-715">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="a471d-716">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="a471d-716">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="a471d-717">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="a471d-717">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="a471d-718">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="a471d-718">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="a471d-719">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="a471d-719">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="a471d-720">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="a471d-720">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="a471d-721">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-721">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="a471d-722">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-722">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="a471d-723">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="a471d-723">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="a471d-724">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="a471d-724">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="a471d-725">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="a471d-725">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="a471d-726">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="a471d-726">Use `-1` for infinite.</span></span> <span data-ttu-id="a471d-727">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-727">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="a471d-728"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="a471d-728">(machine-wide</span></span><br><span data-ttu-id="a471d-729">設定) </span><span class="sxs-lookup"><span data-stu-id="a471d-729">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="a471d-730">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="a471d-730">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="a471d-731">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="a471d-731">30000000 bytes</span></span><br><span data-ttu-id="a471d-732">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="a471d-732">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="a471d-733">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="a471d-733">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="a471d-734">1000</span><span class="sxs-lookup"><span data-stu-id="a471d-734">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="a471d-735">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="a471d-735">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="a471d-736">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="a471d-736">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="a471d-737">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-737">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="a471d-738">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="a471d-738">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="a471d-739">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-739">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="a471d-740">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-740">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="a471d-741">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="a471d-741">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="a471d-742">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-742">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="a471d-743">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="a471d-743">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="a471d-744">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="a471d-744">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="a471d-745">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="a471d-745">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="a471d-746">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="a471d-746">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="a471d-747">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="a471d-747">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="a471d-748">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="a471d-748">**MaxRequestBodySize**</span></span>

<span data-ttu-id="a471d-749">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="a471d-749">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="a471d-750">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="a471d-750">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="a471d-751">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="a471d-751">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="a471d-752">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="a471d-752">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="a471d-753">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="a471d-753">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="a471d-754">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="a471d-754">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="a471d-755">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="a471d-755">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="a471d-756">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="a471d-756">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="a471d-757">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="a471d-757">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="a471d-758">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="a471d-758">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="a471d-760">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="a471d-760">Configure Windows Server</span></span>

1. <span data-ttu-id="a471d-761">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="a471d-761">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="a471d-762">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="a471d-762">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a471d-763">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-763">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="a471d-764">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="a471d-764">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="a471d-765">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-765">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="a471d-766">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-766">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="a471d-767">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="a471d-767">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="a471d-768">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** >  存放區中。</span><span class="sxs-lookup"><span data-stu-id="a471d-768">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="a471d-769">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="a471d-769">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="a471d-770">**.Net core**：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-770">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="a471d-771">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="a471d-771">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="a471d-772">**.Net framework**：如果應用程式需要 .net framework，請參閱 [.net framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="a471d-772">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="a471d-773">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="a471d-773">Install the required .NET Framework.</span></span> <span data-ttu-id="a471d-774">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-774">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="a471d-775">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="a471d-775">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="a471d-776">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="a471d-776">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="a471d-777">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-777">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="a471d-778">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="a471d-778">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a471d-779">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="a471d-779">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="a471d-780">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="a471d-780">`urls` command-line argument</span></span>
   * <span data-ttu-id="a471d-781">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="a471d-781">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="a471d-782">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="a471d-782">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="a471d-783">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="a471d-783">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="a471d-784">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="a471d-784">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="a471d-785">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="a471d-785">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="a471d-786">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="a471d-786">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="a471d-787">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="a471d-787">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="a471d-788">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="a471d-788">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="a471d-789">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="a471d-789">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="a471d-790">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="a471d-790">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="a471d-791">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="a471d-791">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a471d-792">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="a471d-792">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="a471d-793">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="a471d-793">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="a471d-794">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="a471d-794">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="a471d-795">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-795">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="a471d-796">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="a471d-796">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="a471d-797">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="a471d-797">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="a471d-798">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="a471d-798">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="a471d-799">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="a471d-799">Don't use a wildcard binding.</span></span> <span data-ttu-id="a471d-800">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-800">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="a471d-801">URL 必須包含結尾斜線。</span><span class="sxs-lookup"><span data-stu-id="a471d-801">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="a471d-802">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="a471d-802">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="a471d-803">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="a471d-803">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="a471d-804">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-804">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="a471d-805">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="a471d-805">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="a471d-806">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-806">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="a471d-807">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="a471d-807">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="a471d-808">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-808">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="a471d-809">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="a471d-809">Don't use a wildcard binding.</span></span> <span data-ttu-id="a471d-810">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="a471d-810">Use a valid IP address.</span></span>
   * <span data-ttu-id="a471d-811">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="a471d-811">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="a471d-812">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="a471d-812">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="a471d-813">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="a471d-813">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="a471d-814">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="a471d-814">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="a471d-815">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="a471d-815">In Visual Studio:</span></span>
     * <span data-ttu-id="a471d-816">在 [方案總管] 中的專案上按一下滑鼠右鍵，然後選取 [屬性]，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="a471d-816">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="a471d-817">選取 [套件] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="a471d-817">Select the **Package** tab.</span></span>
     * <span data-ttu-id="a471d-818">輸入您在 [標記] 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="a471d-818">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="a471d-819">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="a471d-819">When not using Visual Studio:</span></span>
     * <span data-ttu-id="a471d-820">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="a471d-820">Open the app's project file.</span></span>
     * <span data-ttu-id="a471d-821">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="a471d-821">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="a471d-822">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="a471d-822">In the following example:</span></span>

   * <span data-ttu-id="a471d-823">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="a471d-823">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="a471d-824">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="a471d-824">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="a471d-825">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-825">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="a471d-826">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="a471d-826">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="a471d-827">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="a471d-827">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="a471d-828">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="a471d-828">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="a471d-829">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="a471d-829">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="a471d-830">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-830">Run the app.</span></span>

   <span data-ttu-id="a471d-831">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-831">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="a471d-832">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="a471d-832">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="a471d-833">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="a471d-833">The app responds at the server's public IP address.</span></span> <span data-ttu-id="a471d-834">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="a471d-834">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="a471d-835">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="a471d-835">A development certificate is used in this example.</span></span> <span data-ttu-id="a471d-836">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="a471d-836">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a471d-838">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="a471d-838">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a471d-839">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="a471d-839">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="a471d-840">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="a471d-840">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a471d-841">其他資源</span><span class="sxs-lookup"><span data-stu-id="a471d-841">Additional resources</span></span>

* <span data-ttu-id="a471d-842">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="a471d-842">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="a471d-843">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a471d-843">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="a471d-844">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="a471d-844">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="a471d-845">主機</span><span class="sxs-lookup"><span data-stu-id="a471d-845">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
