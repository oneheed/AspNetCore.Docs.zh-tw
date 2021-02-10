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
ms.openlocfilehash: ad37f8434b6025c5f3ec97dc52987f5660a64edc
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106670"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="e7f8c-104">ASP.NET Core 中的 HTTP.sys 網頁伺服器實作</span><span class="sxs-lookup"><span data-stu-id="e7f8c-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="e7f8c-105">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="e7f8c-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="e7f8c-107">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e7f8c-108">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="e7f8c-109">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="e7f8c-110">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="e7f8c-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="e7f8c-111">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="e7f8c-111">Port sharing</span></span>
* <span data-ttu-id="e7f8c-112">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="e7f8c-112">HTTPS with SNI</span></span>
* <span data-ttu-id="e7f8c-113">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="e7f8c-114">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="e7f8c-114">Direct file transmission</span></span>
* <span data-ttu-id="e7f8c-115">回應快取</span><span class="sxs-lookup"><span data-stu-id="e7f8c-115">Response caching</span></span>
* <span data-ttu-id="e7f8c-116">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="e7f8c-117">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-117">Supported Windows versions:</span></span>

* <span data-ttu-id="e7f8c-118">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-118">Windows 7 or later</span></span>
* <span data-ttu-id="e7f8c-119">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e7f8c-120">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e7f8c-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="e7f8c-121">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="e7f8c-121">When to use HTTP.sys</span></span>

<span data-ttu-id="e7f8c-122">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="e7f8c-123">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="e7f8c-125">內部部署需要無法在 Kestrel 中使用的功能。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-125">An internal deployment requires a feature not available in Kestrel.</span></span> <span data-ttu-id="e7f8c-126">如需詳細資訊，請參閱 [Kestrel 與 HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-126">For more information, see [Kestrel vs. HTTP.sys](xref:fundamentals/servers/index#kestrel-vs-httpsys)</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="e7f8c-128">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-128">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="e7f8c-129">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-129">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e7f8c-130">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="e7f8c-130">HTTP/2 support</span></span>

<span data-ttu-id="e7f8c-131">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-131">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="e7f8c-132">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-132">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="e7f8c-133">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="e7f8c-133">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="e7f8c-134">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="e7f8c-134">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e7f8c-135">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-135">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="e7f8c-136">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-136">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e7f8c-137">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-137">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="e7f8c-138">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-138">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="e7f8c-139">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="e7f8c-139">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="e7f8c-140">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-140">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="e7f8c-141">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-141">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="e7f8c-142">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-142">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="e7f8c-143">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-143">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="e7f8c-144">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7f8c-144">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="e7f8c-145">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7f8c-145">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="e7f8c-146">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-146">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="e7f8c-147">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-147">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="e7f8c-148">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-148">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="e7f8c-149">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="e7f8c-149">**HTTP.sys options**</span></span>

| <span data-ttu-id="e7f8c-150">屬性</span><span class="sxs-lookup"><span data-stu-id="e7f8c-150">Property</span></span> | <span data-ttu-id="e7f8c-151">描述</span><span class="sxs-lookup"><span data-stu-id="e7f8c-151">Description</span></span> | <span data-ttu-id="e7f8c-152">預設</span><span class="sxs-lookup"><span data-stu-id="e7f8c-152">Default</span></span> |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | <span data-ttu-id="e7f8c-153">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="e7f8c-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="e7f8c-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="e7f8c-155">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="e7f8c-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="e7f8c-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="e7f8c-157">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="e7f8c-158">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="e7f8c-159">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | <span data-ttu-id="e7f8c-160">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-160">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="e7f8c-161">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-161">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="e7f8c-162">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-162">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | <span data-ttu-id="e7f8c-163">因為節流狀況而拒絕要求時的 HTTP.sys 行為。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-163">The HTTP.sys behavior when rejecting requests due to throttling conditions.</span></span> | [<span data-ttu-id="e7f8c-164">Http503VerbosityLevel。 <br>基本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-164">Http503VerbosityLevel.<br>Basic</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="e7f8c-165">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-165">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="e7f8c-166">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-166">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="e7f8c-167">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-167">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="e7f8c-168">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-168">Use `-1` for infinite.</span></span> <span data-ttu-id="e7f8c-169">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-169">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="e7f8c-170"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="e7f8c-170">(machine-wide</span></span><br><span data-ttu-id="e7f8c-171">設定) </span><span class="sxs-lookup"><span data-stu-id="e7f8c-171">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="e7f8c-172">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-172">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="e7f8c-173">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="e7f8c-173">30000000 bytes</span></span><br><span data-ttu-id="e7f8c-174">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-174">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="e7f8c-175">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-175">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="e7f8c-176">1000</span><span class="sxs-lookup"><span data-stu-id="e7f8c-176">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="e7f8c-177">這會指出伺服器是否負責建立和設定要求佇列，或是否應該附加至現有的佇列。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-177">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="e7f8c-178">附加至現有的佇列時，大部分現有的設定選項都不適用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-178">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="e7f8c-179">HTTP.sys 要求佇列的名稱。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-179">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="e7f8c-180">`null` (匿名佇列) </span><span class="sxs-lookup"><span data-stu-id="e7f8c-180">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="e7f8c-181">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-181">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="e7f8c-182">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-182">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="e7f8c-183">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-183">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="e7f8c-184">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-184">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="e7f8c-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體的允許時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="e7f8c-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>：允許要求實體主體抵達的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="e7f8c-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>： HTTP 伺服器 API 用來剖析要求標頭的允許時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="e7f8c-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="e7f8c-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: The minimum send rate for the response.</span></span></li><li><span data-ttu-id="e7f8c-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-190"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="e7f8c-191">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-191">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="e7f8c-192">最有用的是 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType> ，它是用來將前置詞加入至集合。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-192">The most useful is <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>, which is used to add a prefix to the collection.</span></span> <span data-ttu-id="e7f8c-193">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-193">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="e7f8c-194">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="e7f8c-194">**MaxRequestBodySize**</span></span>

<span data-ttu-id="e7f8c-195">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-195">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="e7f8c-196">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-196">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="e7f8c-197">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-197">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="e7f8c-198">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-198">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="e7f8c-199">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-199">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="e7f8c-200">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-200">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="e7f8c-201">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-201">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="e7f8c-202">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-202">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="e7f8c-203">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-203">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="e7f8c-204">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-204">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="e7f8c-206">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="e7f8c-206">Configure Windows Server</span></span>

1. <span data-ttu-id="e7f8c-207">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-207">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="e7f8c-208">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-208">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e7f8c-209">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-209">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="e7f8c-210">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-210">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e7f8c-211">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-211">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="e7f8c-212">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-212">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="e7f8c-213">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-213">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="e7f8c-214">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** >  存放區中。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-214">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="e7f8c-215">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-215">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="e7f8c-216">**.Net core**：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-216">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="e7f8c-217">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-217">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="e7f8c-218">**.NET Framework**：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-218">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="e7f8c-219">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-219">Install the required .NET Framework.</span></span> <span data-ttu-id="e7f8c-220">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-220">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="e7f8c-221">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-221">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="e7f8c-222">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-222">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="e7f8c-223">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-223">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="e7f8c-224">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-224">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e7f8c-225">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-225">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="e7f8c-226">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="e7f8c-226">`urls` command-line argument</span></span>
   * <span data-ttu-id="e7f8c-227">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="e7f8c-227">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="e7f8c-228">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-228">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="e7f8c-229">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-229">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="e7f8c-230">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-230">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="e7f8c-231">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-231">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="e7f8c-232">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-232">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="e7f8c-233">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-233">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e7f8c-234">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-234">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="e7f8c-235">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-235">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e7f8c-236">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-236">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="e7f8c-237">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-237">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e7f8c-238">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-238">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="e7f8c-239">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-239">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="e7f8c-240">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-240">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="e7f8c-241">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-241">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="e7f8c-242">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-242">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="e7f8c-243">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-243">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="e7f8c-244">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-244">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="e7f8c-245">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-245">Don't use a wildcard binding.</span></span> <span data-ttu-id="e7f8c-246">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-246">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="e7f8c-247">URL 必須包含結尾斜線。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-247">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="e7f8c-248">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-248">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="e7f8c-249">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-249">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="e7f8c-250">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-250">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="e7f8c-251">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-251">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="e7f8c-252">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-252">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="e7f8c-253">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-253">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="e7f8c-254">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-254">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="e7f8c-255">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-255">Don't use a wildcard binding.</span></span> <span data-ttu-id="e7f8c-256">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-256">Use a valid IP address.</span></span>
   * <span data-ttu-id="e7f8c-257">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-257">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="e7f8c-258">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-258">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="e7f8c-259">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-259">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="e7f8c-260">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-260">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="e7f8c-261">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-261">In Visual Studio:</span></span>
     * <span data-ttu-id="e7f8c-262">在 [方案總管] 中的專案上按一下滑鼠右鍵，然後選取 [屬性]，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-262">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="e7f8c-263">選取 [套件] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-263">Select the **Package** tab.</span></span>
     * <span data-ttu-id="e7f8c-264">輸入您在 [標記] 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-264">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="e7f8c-265">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-265">When not using Visual Studio:</span></span>
     * <span data-ttu-id="e7f8c-266">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-266">Open the app's project file.</span></span>
     * <span data-ttu-id="e7f8c-267">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-267">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="e7f8c-268">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="e7f8c-268">In the following example:</span></span>

   * <span data-ttu-id="e7f8c-269">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-269">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="e7f8c-270">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-270">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="e7f8c-271">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-271">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="e7f8c-272">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-272">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="e7f8c-273">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-273">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="e7f8c-274">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="e7f8c-274">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="e7f8c-275">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="e7f8c-275">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="e7f8c-276">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-276">Run the app.</span></span>

   <span data-ttu-id="e7f8c-277">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-277">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="e7f8c-278">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-278">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="e7f8c-279">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-279">The app responds at the server's public IP address.</span></span> <span data-ttu-id="e7f8c-280">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-280">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="e7f8c-281">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-281">A development certificate is used in this example.</span></span> <span data-ttu-id="e7f8c-282">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-282">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e7f8c-284">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="e7f8c-284">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e7f8c-285">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-285">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="e7f8c-286">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-286">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="e7f8c-287">支援 gRPC 的 Advanced HTTP/2 功能</span><span class="sxs-lookup"><span data-stu-id="e7f8c-287">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="e7f8c-288">HTTP.sys 中的額外 HTTP/2 功能支援 gRPC，包括對回應結尾和傳送重設框架的支援。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-288">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="e7f8c-289">使用 HTTP.sys 執行 gRPC 的需求：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-289">Requirements to run gRPC with HTTP.sys:</span></span>

* <span data-ttu-id="e7f8c-290">Windows 10、OS 組建19041.508 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-290">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="e7f8c-291">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="e7f8c-291">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="e7f8c-292">預告片</span><span class="sxs-lookup"><span data-stu-id="e7f8c-292">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="e7f8c-293">重設</span><span class="sxs-lookup"><span data-stu-id="e7f8c-293">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="e7f8c-294">其他資源</span><span class="sxs-lookup"><span data-stu-id="e7f8c-294">Additional resources</span></span>

* <span data-ttu-id="e7f8c-295">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-295">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="e7f8c-296">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-296">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="e7f8c-297">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-297">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="e7f8c-298">主機</span><span class="sxs-lookup"><span data-stu-id="e7f8c-298">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="e7f8c-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-299">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="e7f8c-300">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-300">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e7f8c-301">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-301">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="e7f8c-302">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-302">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="e7f8c-303">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="e7f8c-303">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="e7f8c-304">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="e7f8c-304">Port sharing</span></span>
* <span data-ttu-id="e7f8c-305">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="e7f8c-305">HTTPS with SNI</span></span>
* <span data-ttu-id="e7f8c-306">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-306">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="e7f8c-307">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="e7f8c-307">Direct file transmission</span></span>
* <span data-ttu-id="e7f8c-308">回應快取</span><span class="sxs-lookup"><span data-stu-id="e7f8c-308">Response caching</span></span>
* <span data-ttu-id="e7f8c-309">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-309">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="e7f8c-310">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-310">Supported Windows versions:</span></span>

* <span data-ttu-id="e7f8c-311">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-311">Windows 7 or later</span></span>
* <span data-ttu-id="e7f8c-312">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-312">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e7f8c-313">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e7f8c-313">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="e7f8c-314">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="e7f8c-314">When to use HTTP.sys</span></span>

<span data-ttu-id="e7f8c-315">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-315">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="e7f8c-316">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-316">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="e7f8c-318">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-318">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="e7f8c-320">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-320">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="e7f8c-321">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-321">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e7f8c-322">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="e7f8c-322">HTTP/2 support</span></span>

<span data-ttu-id="e7f8c-323">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-323">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="e7f8c-324">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-324">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="e7f8c-325">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="e7f8c-325">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="e7f8c-326">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="e7f8c-326">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e7f8c-327">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-327">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="e7f8c-328">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-328">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e7f8c-329">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-329">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="e7f8c-330">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-330">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="e7f8c-331">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="e7f8c-331">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="e7f8c-332">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-332">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="e7f8c-333">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-333">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="e7f8c-334">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-334">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="e7f8c-335">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-335">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="e7f8c-336">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7f8c-336">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="e7f8c-337">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7f8c-337">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="e7f8c-338">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-338">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="e7f8c-339">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-339">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="e7f8c-340">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-340">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="e7f8c-341">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="e7f8c-341">**HTTP.sys options**</span></span>

| <span data-ttu-id="e7f8c-342">屬性</span><span class="sxs-lookup"><span data-stu-id="e7f8c-342">Property</span></span> | <span data-ttu-id="e7f8c-343">描述</span><span class="sxs-lookup"><span data-stu-id="e7f8c-343">Description</span></span> | <span data-ttu-id="e7f8c-344">預設</span><span class="sxs-lookup"><span data-stu-id="e7f8c-344">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="e7f8c-345">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="e7f8c-345">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="e7f8c-346">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-346">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="e7f8c-347">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="e7f8c-347">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="e7f8c-348">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-348">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="e7f8c-349">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="e7f8c-349">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="e7f8c-350">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-350">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="e7f8c-351">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-351">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="e7f8c-352">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-352">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="e7f8c-353">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="e7f8c-353">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="e7f8c-354">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-354">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="e7f8c-355">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-355">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="e7f8c-356">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-356">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="e7f8c-357">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-357">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="e7f8c-358">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-358">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="e7f8c-359">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-359">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="e7f8c-360">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-360">Use `-1` for infinite.</span></span> <span data-ttu-id="e7f8c-361">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-361">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="e7f8c-362"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="e7f8c-362">(machine-wide</span></span><br><span data-ttu-id="e7f8c-363">設定) </span><span class="sxs-lookup"><span data-stu-id="e7f8c-363">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="e7f8c-364">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-364">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="e7f8c-365">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="e7f8c-365">30000000 bytes</span></span><br><span data-ttu-id="e7f8c-366">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-366">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="e7f8c-367">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-367">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="e7f8c-368">1000</span><span class="sxs-lookup"><span data-stu-id="e7f8c-368">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="e7f8c-369">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-369">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="e7f8c-370">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-370">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="e7f8c-371">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-371">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="e7f8c-372">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-372">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="e7f8c-373">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-373">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="e7f8c-374">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-374">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="e7f8c-375">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-375">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="e7f8c-376">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-376">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="e7f8c-377">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-377">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="e7f8c-378">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-378">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="e7f8c-379">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-379">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="e7f8c-380">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-380">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="e7f8c-381">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-381">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="e7f8c-382">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="e7f8c-382">**MaxRequestBodySize**</span></span>

<span data-ttu-id="e7f8c-383">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-383">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="e7f8c-384">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-384">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="e7f8c-385">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-385">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="e7f8c-386">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-386">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="e7f8c-387">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-387">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="e7f8c-388">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-388">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="e7f8c-389">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-389">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="e7f8c-390">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-390">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="e7f8c-391">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-391">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="e7f8c-392">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-392">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="e7f8c-394">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="e7f8c-394">Configure Windows Server</span></span>

1. <span data-ttu-id="e7f8c-395">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-395">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="e7f8c-396">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-396">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e7f8c-397">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-397">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="e7f8c-398">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-398">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e7f8c-399">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-399">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="e7f8c-400">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-400">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="e7f8c-401">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-401">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="e7f8c-402">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** >  存放區中。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-402">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="e7f8c-403">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-403">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="e7f8c-404">**.Net core**：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-404">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="e7f8c-405">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-405">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="e7f8c-406">**.NET Framework**：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-406">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="e7f8c-407">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-407">Install the required .NET Framework.</span></span> <span data-ttu-id="e7f8c-408">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-408">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="e7f8c-409">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-409">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="e7f8c-410">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-410">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="e7f8c-411">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-411">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="e7f8c-412">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-412">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e7f8c-413">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-413">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="e7f8c-414">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="e7f8c-414">`urls` command-line argument</span></span>
   * <span data-ttu-id="e7f8c-415">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="e7f8c-415">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="e7f8c-416">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-416">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="e7f8c-417">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-417">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="e7f8c-418">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-418">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="e7f8c-419">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-419">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="e7f8c-420">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-420">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="e7f8c-421">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-421">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e7f8c-422">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-422">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="e7f8c-423">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-423">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e7f8c-424">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-424">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="e7f8c-425">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-425">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e7f8c-426">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-426">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="e7f8c-427">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-427">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="e7f8c-428">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-428">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="e7f8c-429">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-429">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="e7f8c-430">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-430">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="e7f8c-431">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-431">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="e7f8c-432">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-432">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="e7f8c-433">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-433">Don't use a wildcard binding.</span></span> <span data-ttu-id="e7f8c-434">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-434">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="e7f8c-435">URL 必須包含結尾斜線。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-435">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="e7f8c-436">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-436">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="e7f8c-437">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-437">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="e7f8c-438">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-438">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="e7f8c-439">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-439">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="e7f8c-440">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-440">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="e7f8c-441">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-441">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="e7f8c-442">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-442">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="e7f8c-443">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-443">Don't use a wildcard binding.</span></span> <span data-ttu-id="e7f8c-444">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-444">Use a valid IP address.</span></span>
   * <span data-ttu-id="e7f8c-445">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-445">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="e7f8c-446">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-446">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="e7f8c-447">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-447">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="e7f8c-448">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-448">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="e7f8c-449">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-449">In Visual Studio:</span></span>
     * <span data-ttu-id="e7f8c-450">在 [方案總管] 中的專案上按一下滑鼠右鍵，然後選取 [屬性]，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-450">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="e7f8c-451">選取 [套件] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-451">Select the **Package** tab.</span></span>
     * <span data-ttu-id="e7f8c-452">輸入您在 [標記] 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-452">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="e7f8c-453">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-453">When not using Visual Studio:</span></span>
     * <span data-ttu-id="e7f8c-454">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-454">Open the app's project file.</span></span>
     * <span data-ttu-id="e7f8c-455">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-455">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="e7f8c-456">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="e7f8c-456">In the following example:</span></span>

   * <span data-ttu-id="e7f8c-457">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-457">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="e7f8c-458">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-458">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="e7f8c-459">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-459">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="e7f8c-460">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-460">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="e7f8c-461">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-461">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="e7f8c-462">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="e7f8c-462">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="e7f8c-463">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="e7f8c-463">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="e7f8c-464">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-464">Run the app.</span></span>

   <span data-ttu-id="e7f8c-465">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-465">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="e7f8c-466">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-466">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="e7f8c-467">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-467">The app responds at the server's public IP address.</span></span> <span data-ttu-id="e7f8c-468">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-468">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="e7f8c-469">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-469">A development certificate is used in this example.</span></span> <span data-ttu-id="e7f8c-470">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-470">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e7f8c-472">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="e7f8c-472">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e7f8c-473">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-473">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="e7f8c-474">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-474">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7f8c-475">其他資源</span><span class="sxs-lookup"><span data-stu-id="e7f8c-475">Additional resources</span></span>

* <span data-ttu-id="e7f8c-476">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-476">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="e7f8c-477">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-477">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="e7f8c-478">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-478">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="e7f8c-479">主機</span><span class="sxs-lookup"><span data-stu-id="e7f8c-479">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="e7f8c-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-480">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="e7f8c-481">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-481">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e7f8c-482">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-482">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="e7f8c-483">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-483">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="e7f8c-484">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="e7f8c-484">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="e7f8c-485">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="e7f8c-485">Port sharing</span></span>
* <span data-ttu-id="e7f8c-486">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="e7f8c-486">HTTPS with SNI</span></span>
* <span data-ttu-id="e7f8c-487">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-487">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="e7f8c-488">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="e7f8c-488">Direct file transmission</span></span>
* <span data-ttu-id="e7f8c-489">回應快取</span><span class="sxs-lookup"><span data-stu-id="e7f8c-489">Response caching</span></span>
* <span data-ttu-id="e7f8c-490">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-490">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="e7f8c-491">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-491">Supported Windows versions:</span></span>

* <span data-ttu-id="e7f8c-492">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-492">Windows 7 or later</span></span>
* <span data-ttu-id="e7f8c-493">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-493">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e7f8c-494">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e7f8c-494">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="e7f8c-495">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="e7f8c-495">When to use HTTP.sys</span></span>

<span data-ttu-id="e7f8c-496">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-496">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="e7f8c-497">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-497">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="e7f8c-499">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-499">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="e7f8c-501">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-501">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="e7f8c-502">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-502">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e7f8c-503">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="e7f8c-503">HTTP/2 support</span></span>

<span data-ttu-id="e7f8c-504">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-504">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="e7f8c-505">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-505">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="e7f8c-506">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="e7f8c-506">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="e7f8c-507">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="e7f8c-507">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e7f8c-508">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-508">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="e7f8c-509">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-509">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e7f8c-510">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-510">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="e7f8c-511">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-511">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="e7f8c-512">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="e7f8c-512">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="e7f8c-513">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-513">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="e7f8c-514">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-514">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="e7f8c-515">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-515">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="e7f8c-516">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-516">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="e7f8c-517">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7f8c-517">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="e7f8c-518">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7f8c-518">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="e7f8c-519">使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 時，不需要專案檔中的套件參考。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-519">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="e7f8c-520">若不是使用 `Microsoft.AspNetCore.App` 中繼套件，請將套件參考加入 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-520">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="e7f8c-521">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-521">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="e7f8c-522">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-522">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="e7f8c-523">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-523">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="e7f8c-524">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="e7f8c-524">**HTTP.sys options**</span></span>

| <span data-ttu-id="e7f8c-525">屬性</span><span class="sxs-lookup"><span data-stu-id="e7f8c-525">Property</span></span> | <span data-ttu-id="e7f8c-526">描述</span><span class="sxs-lookup"><span data-stu-id="e7f8c-526">Description</span></span> | <span data-ttu-id="e7f8c-527">預設</span><span class="sxs-lookup"><span data-stu-id="e7f8c-527">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="e7f8c-528">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="e7f8c-528">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="e7f8c-529">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-529">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="e7f8c-530">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="e7f8c-530">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="e7f8c-531">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-531">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="e7f8c-532">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="e7f8c-532">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="e7f8c-533">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-533">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="e7f8c-534">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-534">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="e7f8c-535">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-535">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="e7f8c-536">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="e7f8c-536">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="e7f8c-537">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-537">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="e7f8c-538">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-538">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="e7f8c-539">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-539">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="e7f8c-540">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-540">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="e7f8c-541">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-541">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="e7f8c-542">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-542">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="e7f8c-543">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-543">Use `-1` for infinite.</span></span> <span data-ttu-id="e7f8c-544">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-544">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="e7f8c-545"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="e7f8c-545">(machine-wide</span></span><br><span data-ttu-id="e7f8c-546">設定) </span><span class="sxs-lookup"><span data-stu-id="e7f8c-546">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="e7f8c-547">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-547">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="e7f8c-548">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="e7f8c-548">30000000 bytes</span></span><br><span data-ttu-id="e7f8c-549">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-549">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="e7f8c-550">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-550">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="e7f8c-551">1000</span><span class="sxs-lookup"><span data-stu-id="e7f8c-551">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="e7f8c-552">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-552">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="e7f8c-553">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-553">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="e7f8c-554">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-554">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="e7f8c-555">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-555">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="e7f8c-556">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-556">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="e7f8c-557">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-557">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="e7f8c-558">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-558">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="e7f8c-559">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-559">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="e7f8c-560">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-560">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="e7f8c-561">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-561">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="e7f8c-562">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-562">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="e7f8c-563">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-563">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="e7f8c-564">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-564">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="e7f8c-565">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="e7f8c-565">**MaxRequestBodySize**</span></span>

<span data-ttu-id="e7f8c-566">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-566">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="e7f8c-567">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-567">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="e7f8c-568">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-568">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="e7f8c-569">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-569">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="e7f8c-570">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-570">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="e7f8c-571">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-571">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="e7f8c-572">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-572">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="e7f8c-573">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-573">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="e7f8c-574">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-574">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="e7f8c-575">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-575">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="e7f8c-577">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="e7f8c-577">Configure Windows Server</span></span>

1. <span data-ttu-id="e7f8c-578">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-578">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="e7f8c-579">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-579">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e7f8c-580">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-580">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="e7f8c-581">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-581">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e7f8c-582">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-582">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="e7f8c-583">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-583">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="e7f8c-584">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-584">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="e7f8c-585">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** >  存放區中。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-585">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="e7f8c-586">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-586">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="e7f8c-587">**.Net core**：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-587">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="e7f8c-588">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-588">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="e7f8c-589">**.NET Framework**：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-589">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="e7f8c-590">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-590">Install the required .NET Framework.</span></span> <span data-ttu-id="e7f8c-591">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-591">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="e7f8c-592">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-592">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="e7f8c-593">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-593">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="e7f8c-594">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-594">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="e7f8c-595">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-595">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e7f8c-596">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-596">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="e7f8c-597">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="e7f8c-597">`urls` command-line argument</span></span>
   * <span data-ttu-id="e7f8c-598">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="e7f8c-598">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="e7f8c-599">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-599">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="e7f8c-600">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-600">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="e7f8c-601">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-601">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="e7f8c-602">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-602">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="e7f8c-603">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-603">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="e7f8c-604">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-604">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e7f8c-605">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-605">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="e7f8c-606">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-606">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e7f8c-607">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-607">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="e7f8c-608">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-608">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e7f8c-609">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-609">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="e7f8c-610">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-610">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="e7f8c-611">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-611">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="e7f8c-612">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-612">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="e7f8c-613">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-613">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="e7f8c-614">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-614">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="e7f8c-615">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-615">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="e7f8c-616">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-616">Don't use a wildcard binding.</span></span> <span data-ttu-id="e7f8c-617">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-617">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="e7f8c-618">URL 必須包含結尾斜線。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-618">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="e7f8c-619">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-619">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="e7f8c-620">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-620">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="e7f8c-621">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-621">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="e7f8c-622">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-622">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="e7f8c-623">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-623">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="e7f8c-624">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-624">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="e7f8c-625">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-625">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="e7f8c-626">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-626">Don't use a wildcard binding.</span></span> <span data-ttu-id="e7f8c-627">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-627">Use a valid IP address.</span></span>
   * <span data-ttu-id="e7f8c-628">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-628">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="e7f8c-629">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-629">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="e7f8c-630">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-630">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="e7f8c-631">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-631">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="e7f8c-632">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-632">In Visual Studio:</span></span>
     * <span data-ttu-id="e7f8c-633">在 [方案總管] 中的專案上按一下滑鼠右鍵，然後選取 [屬性]，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-633">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="e7f8c-634">選取 [套件] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-634">Select the **Package** tab.</span></span>
     * <span data-ttu-id="e7f8c-635">輸入您在 [標記] 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-635">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="e7f8c-636">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-636">When not using Visual Studio:</span></span>
     * <span data-ttu-id="e7f8c-637">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-637">Open the app's project file.</span></span>
     * <span data-ttu-id="e7f8c-638">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-638">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="e7f8c-639">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="e7f8c-639">In the following example:</span></span>

   * <span data-ttu-id="e7f8c-640">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-640">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="e7f8c-641">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-641">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="e7f8c-642">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-642">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="e7f8c-643">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-643">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="e7f8c-644">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-644">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="e7f8c-645">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="e7f8c-645">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="e7f8c-646">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="e7f8c-646">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="e7f8c-647">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-647">Run the app.</span></span>

   <span data-ttu-id="e7f8c-648">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-648">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="e7f8c-649">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-649">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="e7f8c-650">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-650">The app responds at the server's public IP address.</span></span> <span data-ttu-id="e7f8c-651">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-651">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="e7f8c-652">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-652">A development certificate is used in this example.</span></span> <span data-ttu-id="e7f8c-653">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-653">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e7f8c-655">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="e7f8c-655">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e7f8c-656">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-656">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="e7f8c-657">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-657">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7f8c-658">其他資源</span><span class="sxs-lookup"><span data-stu-id="e7f8c-658">Additional resources</span></span>

* <span data-ttu-id="e7f8c-659">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-659">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="e7f8c-660">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-660">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="e7f8c-661">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-661">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="e7f8c-662">主機</span><span class="sxs-lookup"><span data-stu-id="e7f8c-662">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e7f8c-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-663">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="e7f8c-664">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-664">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e7f8c-665">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-665">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="e7f8c-666">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-666">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="e7f8c-667">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="e7f8c-667">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="e7f8c-668">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="e7f8c-668">Port sharing</span></span>
* <span data-ttu-id="e7f8c-669">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="e7f8c-669">HTTPS with SNI</span></span>
* <span data-ttu-id="e7f8c-670">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-670">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="e7f8c-671">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="e7f8c-671">Direct file transmission</span></span>
* <span data-ttu-id="e7f8c-672">回應快取</span><span class="sxs-lookup"><span data-stu-id="e7f8c-672">Response caching</span></span>
* <span data-ttu-id="e7f8c-673">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-673">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="e7f8c-674">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-674">Supported Windows versions:</span></span>

* <span data-ttu-id="e7f8c-675">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-675">Windows 7 or later</span></span>
* <span data-ttu-id="e7f8c-676">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-676">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="e7f8c-677">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e7f8c-677">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="e7f8c-678">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="e7f8c-678">When to use HTTP.sys</span></span>

<span data-ttu-id="e7f8c-679">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-679">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="e7f8c-680">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-680">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="e7f8c-682">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-682">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="e7f8c-684">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-684">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="e7f8c-685">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-685">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="e7f8c-686">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="e7f8c-686">HTTP/2 support</span></span>

<span data-ttu-id="e7f8c-687">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-687">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="e7f8c-688">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="e7f8c-688">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="e7f8c-689">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="e7f8c-689">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="e7f8c-690">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="e7f8c-690">TLS 1.2 or later connection</span></span>

<span data-ttu-id="e7f8c-691">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-691">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="e7f8c-692">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-692">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="e7f8c-693">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-693">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="e7f8c-694">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-694">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="e7f8c-695">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="e7f8c-695">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="e7f8c-696">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-696">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="e7f8c-697">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-697">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="e7f8c-698">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-698">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="e7f8c-699">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-699">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="e7f8c-700">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7f8c-700">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="e7f8c-701">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e7f8c-701">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="e7f8c-702">使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 時，不需要專案檔中的套件參考。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-702">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="e7f8c-703">若不是使用 `Microsoft.AspNetCore.App` 中繼套件，請將套件參考加入 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-703">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="e7f8c-704">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-704">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="e7f8c-705">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-705">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="e7f8c-706">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-706">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="e7f8c-707">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="e7f8c-707">**HTTP.sys options**</span></span>

| <span data-ttu-id="e7f8c-708">屬性</span><span class="sxs-lookup"><span data-stu-id="e7f8c-708">Property</span></span> | <span data-ttu-id="e7f8c-709">描述</span><span class="sxs-lookup"><span data-stu-id="e7f8c-709">Description</span></span> | <span data-ttu-id="e7f8c-710">預設</span><span class="sxs-lookup"><span data-stu-id="e7f8c-710">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="e7f8c-711">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="e7f8c-711">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="e7f8c-712">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-712">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="e7f8c-713">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="e7f8c-713">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="e7f8c-714">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-714">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="e7f8c-715">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="e7f8c-715">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="e7f8c-716">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-716">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="e7f8c-717">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-717">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="e7f8c-718">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-718">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="e7f8c-719">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="e7f8c-719">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="e7f8c-720">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-720">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="e7f8c-721">回應可能不包含 `Set-Cookie`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-721">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="e7f8c-722">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-722">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="e7f8c-723">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-723">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="e7f8c-724">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-724">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="e7f8c-725">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-725">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="e7f8c-726">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-726">Use `-1` for infinite.</span></span> <span data-ttu-id="e7f8c-727">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-727">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="e7f8c-728"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="e7f8c-728">(machine-wide</span></span><br><span data-ttu-id="e7f8c-729">設定) </span><span class="sxs-lookup"><span data-stu-id="e7f8c-729">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="e7f8c-730">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-730">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="e7f8c-731">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="e7f8c-731">30000000 bytes</span></span><br><span data-ttu-id="e7f8c-732">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-732">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="e7f8c-733">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-733">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="e7f8c-734">1000</span><span class="sxs-lookup"><span data-stu-id="e7f8c-734">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="e7f8c-735">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-735">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="e7f8c-736">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-736">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="e7f8c-737">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-737">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="e7f8c-738">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-738">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="e7f8c-739">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-739">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="e7f8c-740">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-740">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="e7f8c-741">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-741">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="e7f8c-742">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-742">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="e7f8c-743">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-743">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="e7f8c-744">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-744">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="e7f8c-745">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-745">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="e7f8c-746">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-746">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="e7f8c-747">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-747">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="e7f8c-748">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="e7f8c-748">**MaxRequestBodySize**</span></span>

<span data-ttu-id="e7f8c-749">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-749">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="e7f8c-750">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-750">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="e7f8c-751">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-751">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="e7f8c-752">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-752">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="e7f8c-753">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-753">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="e7f8c-754">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-754">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="e7f8c-755">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-755">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="e7f8c-756">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-756">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="e7f8c-757">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-757">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="e7f8c-758">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-758">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="e7f8c-760">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="e7f8c-760">Configure Windows Server</span></span>

1. <span data-ttu-id="e7f8c-761">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-761">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="e7f8c-762">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-762">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e7f8c-763">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-763">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="e7f8c-764">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-764">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="e7f8c-765">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-765">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="e7f8c-766">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-766">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="e7f8c-767">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-767">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="e7f8c-768">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]**個人** >  存放區中。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-768">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="e7f8c-769">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-769">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="e7f8c-770">**.Net core**：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-770">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="e7f8c-771">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-771">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="e7f8c-772">**.NET Framework**：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-772">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="e7f8c-773">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-773">Install the required .NET Framework.</span></span> <span data-ttu-id="e7f8c-774">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-774">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="e7f8c-775">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-775">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="e7f8c-776">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-776">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="e7f8c-777">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-777">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="e7f8c-778">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-778">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="e7f8c-779">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-779">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="e7f8c-780">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="e7f8c-780">`urls` command-line argument</span></span>
   * <span data-ttu-id="e7f8c-781">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="e7f8c-781">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="e7f8c-782">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-782">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="e7f8c-783">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-783">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="e7f8c-784">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-784">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="e7f8c-785">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-785">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="e7f8c-786">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-786">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="e7f8c-787">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-787">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="e7f8c-788">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-788">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="e7f8c-789">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-789">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="e7f8c-790">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-790">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="e7f8c-791">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-791">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="e7f8c-792">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-792">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="e7f8c-793">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-793">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="e7f8c-794">用來設定 HTTP.sys 的內建工具是 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-794">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="e7f8c-795">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-795">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="e7f8c-796">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-796">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="e7f8c-797">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-797">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="e7f8c-798">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-798">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="e7f8c-799">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-799">Don't use a wildcard binding.</span></span> <span data-ttu-id="e7f8c-800">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-800">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="e7f8c-801">URL 必須包含結尾斜線。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-801">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="e7f8c-802">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-802">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="e7f8c-803">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-803">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="e7f8c-804">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-804">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="e7f8c-805">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-805">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="e7f8c-806">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-806">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="e7f8c-807">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-807">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="e7f8c-808">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-808">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="e7f8c-809">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-809">Don't use a wildcard binding.</span></span> <span data-ttu-id="e7f8c-810">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-810">Use a valid IP address.</span></span>
   * <span data-ttu-id="e7f8c-811">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-811">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="e7f8c-812">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-812">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="e7f8c-813">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-813">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="e7f8c-814">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-814">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="e7f8c-815">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-815">In Visual Studio:</span></span>
     * <span data-ttu-id="e7f8c-816">在 [方案總管] 中的專案上按一下滑鼠右鍵，然後選取 [屬性]，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-816">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="e7f8c-817">選取 [套件] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-817">Select the **Package** tab.</span></span>
     * <span data-ttu-id="e7f8c-818">輸入您在 [標記] 欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-818">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="e7f8c-819">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-819">When not using Visual Studio:</span></span>
     * <span data-ttu-id="e7f8c-820">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-820">Open the app's project file.</span></span>
     * <span data-ttu-id="e7f8c-821">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-821">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="e7f8c-822">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="e7f8c-822">In the following example:</span></span>

   * <span data-ttu-id="e7f8c-823">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-823">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="e7f8c-824">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-824">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="e7f8c-825">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-825">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="e7f8c-826">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-826">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="e7f8c-827">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="e7f8c-827">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="e7f8c-828">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="e7f8c-828">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="e7f8c-829">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="e7f8c-829">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="e7f8c-830">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-830">Run the app.</span></span>

   <span data-ttu-id="e7f8c-831">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-831">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="e7f8c-832">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-832">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="e7f8c-833">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-833">The app responds at the server's public IP address.</span></span> <span data-ttu-id="e7f8c-834">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-834">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="e7f8c-835">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-835">A development certificate is used in this example.</span></span> <span data-ttu-id="e7f8c-836">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-836">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="e7f8c-838">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="e7f8c-838">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="e7f8c-839">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-839">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="e7f8c-840">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="e7f8c-840">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7f8c-841">其他資源</span><span class="sxs-lookup"><span data-stu-id="e7f8c-841">Additional resources</span></span>

* <span data-ttu-id="e7f8c-842">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-842">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="e7f8c-843">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-843">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="e7f8c-844">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="e7f8c-844">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="e7f8c-845">主機</span><span class="sxs-lookup"><span data-stu-id="e7f8c-845">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
