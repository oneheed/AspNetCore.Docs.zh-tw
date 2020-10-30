---
title: ASP.NET Core 中的 HTTP.sys 網頁伺服器實作
author: rick-anderson
description: 深入了解 HTTP.sys，這是 Windows 上的 ASP.NET Core 網頁伺服器。 HTTP.sys 建置在 HTTP.sys 核心模式驅動程式之上，是 Kestrel 的替代方式，可以用來直接連線到網際網路而不使用 IIS。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: ca8aa126a44ea417017f0be0372e818a95ad8413
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053744"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="b5c55-104">ASP.NET Core 中的 HTTP.sys 網頁伺服器實作</span><span class="sxs-lookup"><span data-stu-id="b5c55-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="b5c55-105">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="b5c55-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="b5c55-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="b5c55-107">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="b5c55-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b5c55-108">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="b5c55-109">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="b5c55-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="b5c55-110">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="b5c55-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="b5c55-111">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="b5c55-111">Port sharing</span></span>
* <span data-ttu-id="b5c55-112">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="b5c55-112">HTTPS with SNI</span></span>
* <span data-ttu-id="b5c55-113">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="b5c55-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="b5c55-114">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="b5c55-114">Direct file transmission</span></span>
* <span data-ttu-id="b5c55-115">回應快取</span><span class="sxs-lookup"><span data-stu-id="b5c55-115">Response caching</span></span>
* <span data-ttu-id="b5c55-116">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="b5c55-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="b5c55-117">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="b5c55-117">Supported Windows versions:</span></span>

* <span data-ttu-id="b5c55-118">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-118">Windows 7 or later</span></span>
* <span data-ttu-id="b5c55-119">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b5c55-120">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="b5c55-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="b5c55-121">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="b5c55-121">When to use HTTP.sys</span></span>

<span data-ttu-id="b5c55-122">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="b5c55-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="b5c55-123">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="b5c55-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="b5c55-125">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="b5c55-127">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="b5c55-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="b5c55-128">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="b5c55-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="b5c55-129">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="b5c55-129">HTTP/2 support</span></span>

<span data-ttu-id="b5c55-130">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="b5c55-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="b5c55-131">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="b5c55-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="b5c55-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="b5c55-133">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="b5c55-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="b5c55-134">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="b5c55-135">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="b5c55-136">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="b5c55-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="b5c55-137">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="b5c55-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="b5c55-138">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="b5c55-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="b5c55-139">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="b5c55-140">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="b5c55-141">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="b5c55-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="b5c55-142">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="b5c55-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="b5c55-143">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b5c55-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="b5c55-144">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b5c55-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="b5c55-145">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="b5c55-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="b5c55-146">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="b5c55-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="b5c55-147">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="b5c55-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="b5c55-148">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="b5c55-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="b5c55-149">屬性</span><span class="sxs-lookup"><span data-stu-id="b5c55-149">Property</span></span> | <span data-ttu-id="b5c55-150">描述</span><span class="sxs-lookup"><span data-stu-id="b5c55-150">Description</span></span> | <span data-ttu-id="b5c55-151">預設</span><span class="sxs-lookup"><span data-stu-id="b5c55-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="b5c55-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="b5c55-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="b5c55-153">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="b5c55-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="b5c55-154">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="b5c55-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="b5c55-155">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="b5c55-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="b5c55-156">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="b5c55-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="b5c55-157">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="b5c55-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="b5c55-158">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="b5c55-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="b5c55-159">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="b5c55-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="b5c55-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="b5c55-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="b5c55-161">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="b5c55-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="b5c55-162">回應可能不包含 `Set-:::no-loc(Cookie):::`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-162">The response may not include `Set-:::no-loc(Cookie):::`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="b5c55-163">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="b5c55-164">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="b5c55-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="b5c55-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="b5c55-166">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="b5c55-167">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="b5c55-167">Use `-1` for infinite.</span></span> <span data-ttu-id="b5c55-168">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="b5c55-169"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="b5c55-169">(machine-wide</span></span><br><span data-ttu-id="b5c55-170">設定) </span><span class="sxs-lookup"><span data-stu-id="b5c55-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="b5c55-171">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="b5c55-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="b5c55-172">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="b5c55-172">30000000 bytes</span></span><br><span data-ttu-id="b5c55-173">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="b5c55-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="b5c55-174">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="b5c55-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="b5c55-175">1000</span><span class="sxs-lookup"><span data-stu-id="b5c55-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="b5c55-176">這會指出伺服器是否負責建立和設定要求佇列，或是否應該附加至現有的佇列。</span><span class="sxs-lookup"><span data-stu-id="b5c55-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="b5c55-177">附加至現有的佇列時，大部分現有的設定選項都不適用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="b5c55-178">HTTP.sys 要求佇列的名稱。</span><span class="sxs-lookup"><span data-stu-id="b5c55-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="b5c55-179">`null` (匿名佇列) </span><span class="sxs-lookup"><span data-stu-id="b5c55-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="b5c55-180">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="b5c55-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="b5c55-181">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="b5c55-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="b5c55-182">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="b5c55-183">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="b5c55-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="b5c55-184">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="b5c55-185">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="b5c55-186">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="b5c55-187">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="b5c55-188">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="b5c55-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="b5c55-189">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="b5c55-190">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="b5c55-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="b5c55-191">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="b5c55-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="b5c55-192">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="b5c55-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="b5c55-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="b5c55-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="b5c55-194">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="b5c55-195">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="b5c55-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="b5c55-196">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="b5c55-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="b5c55-197">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="b5c55-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="b5c55-198">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b5c55-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="b5c55-199">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="b5c55-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="b5c55-200">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="b5c55-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="b5c55-201">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="b5c55-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="b5c55-202">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="b5c55-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="b5c55-203">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="b5c55-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="b5c55-205">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="b5c55-205">Configure Windows Server</span></span>

1. <span data-ttu-id="b5c55-206">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="b5c55-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="b5c55-207">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="b5c55-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="b5c55-208">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="b5c55-209">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="b5c55-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="b5c55-210">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="b5c55-211">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="b5c55-212">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="b5c55-213">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]  存放區中。</span><span class="sxs-lookup"><span data-stu-id="b5c55-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="b5c55-214">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="b5c55-215">**.Net core** ：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-215">**.NET Core** : If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="b5c55-216">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="b5c55-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="b5c55-217">**.NET Framework** ：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-217">**.NET Framework** : If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="b5c55-218">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="b5c55-218">Install the required .NET Framework.</span></span> <span data-ttu-id="b5c55-219">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="b5c55-220">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="b5c55-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="b5c55-221">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="b5c55-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="b5c55-222">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="b5c55-223">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="b5c55-224">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="b5c55-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="b5c55-225">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="b5c55-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="b5c55-226">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="b5c55-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="b5c55-227">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="b5c55-228">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="b5c55-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="b5c55-229">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="b5c55-230">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="b5c55-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="b5c55-231">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="b5c55-232">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="b5c55-233">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="b5c55-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="b5c55-234">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="b5c55-235">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="b5c55-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="b5c55-236">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="b5c55-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="b5c55-237">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="b5c55-238">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="b5c55-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="b5c55-239">用來設定 HTTP.sys 的內建工具是 *netsh.exe* 。</span><span class="sxs-lookup"><span data-stu-id="b5c55-239">The built-in tool for configuring HTTP.sys is *netsh.exe* .</span></span> <span data-ttu-id="b5c55-240">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="b5c55-241">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="b5c55-242">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="b5c55-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="b5c55-243">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="b5c55-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="b5c55-244">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="b5c55-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="b5c55-245">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="b5c55-246">URL 必須包含結尾斜線。 </span><span class="sxs-lookup"><span data-stu-id="b5c55-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="b5c55-247">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="b5c55-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="b5c55-248">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="b5c55-249">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="b5c55-250">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="b5c55-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="b5c55-251">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="b5c55-252">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="b5c55-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="b5c55-253">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="b5c55-254">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="b5c55-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="b5c55-255">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="b5c55-256">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="b5c55-257">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="b5c55-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="b5c55-258">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="b5c55-259">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="b5c55-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="b5c55-260">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="b5c55-260">In Visual Studio:</span></span>
     * <span data-ttu-id="b5c55-261">在 [方案總管]  中的專案上按一下滑鼠右鍵，然後選取 [屬性]  ，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="b5c55-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties** .</span></span>
     * <span data-ttu-id="b5c55-262">選取 [套件]  索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b5c55-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="b5c55-263">輸入您在 [標記]  欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="b5c55-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="b5c55-264">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="b5c55-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="b5c55-265">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="b5c55-265">Open the app's project file.</span></span>
     * <span data-ttu-id="b5c55-266">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="b5c55-267">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="b5c55-267">In the following example:</span></span>

   * <span data-ttu-id="b5c55-268">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="b5c55-269">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="b5c55-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="b5c55-270">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="b5c55-271">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="b5c55-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="b5c55-272">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="b5c55-272">Reference documentation for *netsh.exe* :</span></span>

   * <span data-ttu-id="b5c55-273">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="b5c55-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="b5c55-274">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="b5c55-274">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="b5c55-275">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-275">Run the app.</span></span>

   <span data-ttu-id="b5c55-276">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="b5c55-277">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="b5c55-278">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="b5c55-279">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="b5c55-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="b5c55-280">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-280">A development certificate is used in this example.</span></span> <span data-ttu-id="b5c55-281">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="b5c55-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b5c55-283">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="b5c55-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b5c55-284">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="b5c55-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="b5c55-285">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="b5c55-286">支援 gRPC 的 Advanced HTTP/2 功能</span><span class="sxs-lookup"><span data-stu-id="b5c55-286">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="b5c55-287">HTTP.sys 中的額外 HTTP/2 功能支援 gRPC，包括對回應結尾和傳送重設框架的支援。</span><span class="sxs-lookup"><span data-stu-id="b5c55-287">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="b5c55-288">使用 HTTP.SYS 執行 gRPC 的需求：</span><span class="sxs-lookup"><span data-stu-id="b5c55-288">Requirements to run gRPC with HTTP.SYS:</span></span>

* <span data-ttu-id="b5c55-289">Windows 10、OS 組建19041.508 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-289">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="b5c55-290">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="b5c55-290">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="b5c55-291">預告片</span><span class="sxs-lookup"><span data-stu-id="b5c55-291">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="b5c55-292">重設</span><span class="sxs-lookup"><span data-stu-id="b5c55-292">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="b5c55-293">其他資源</span><span class="sxs-lookup"><span data-stu-id="b5c55-293">Additional resources</span></span>

* <span data-ttu-id="b5c55-294">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-294">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="b5c55-295">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-295">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="b5c55-296">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-296">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="b5c55-297">主機</span><span class="sxs-lookup"><span data-stu-id="b5c55-297">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="b5c55-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="b5c55-299">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="b5c55-299">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b5c55-300">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-300">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="b5c55-301">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="b5c55-301">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="b5c55-302">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="b5c55-302">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="b5c55-303">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="b5c55-303">Port sharing</span></span>
* <span data-ttu-id="b5c55-304">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="b5c55-304">HTTPS with SNI</span></span>
* <span data-ttu-id="b5c55-305">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="b5c55-305">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="b5c55-306">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="b5c55-306">Direct file transmission</span></span>
* <span data-ttu-id="b5c55-307">回應快取</span><span class="sxs-lookup"><span data-stu-id="b5c55-307">Response caching</span></span>
* <span data-ttu-id="b5c55-308">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="b5c55-308">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="b5c55-309">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="b5c55-309">Supported Windows versions:</span></span>

* <span data-ttu-id="b5c55-310">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-310">Windows 7 or later</span></span>
* <span data-ttu-id="b5c55-311">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-311">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b5c55-312">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="b5c55-312">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="b5c55-313">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="b5c55-313">When to use HTTP.sys</span></span>

<span data-ttu-id="b5c55-314">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="b5c55-314">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="b5c55-315">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="b5c55-315">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="b5c55-317">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-317">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="b5c55-319">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="b5c55-319">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="b5c55-320">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="b5c55-320">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="b5c55-321">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="b5c55-321">HTTP/2 support</span></span>

<span data-ttu-id="b5c55-322">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="b5c55-322">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="b5c55-323">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-323">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="b5c55-324">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="b5c55-324">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="b5c55-325">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="b5c55-325">TLS 1.2 or later connection</span></span>

<span data-ttu-id="b5c55-326">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-326">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="b5c55-327">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-327">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="b5c55-328">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="b5c55-328">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="b5c55-329">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="b5c55-329">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="b5c55-330">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="b5c55-330">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="b5c55-331">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-331">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="b5c55-332">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-332">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="b5c55-333">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="b5c55-333">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="b5c55-334">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="b5c55-334">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="b5c55-335">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b5c55-335">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="b5c55-336">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b5c55-336">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="b5c55-337">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="b5c55-337">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="b5c55-338">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="b5c55-338">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="b5c55-339">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="b5c55-339">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="b5c55-340">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="b5c55-340">**HTTP.sys options**</span></span>

| <span data-ttu-id="b5c55-341">屬性</span><span class="sxs-lookup"><span data-stu-id="b5c55-341">Property</span></span> | <span data-ttu-id="b5c55-342">描述</span><span class="sxs-lookup"><span data-stu-id="b5c55-342">Description</span></span> | <span data-ttu-id="b5c55-343">預設</span><span class="sxs-lookup"><span data-stu-id="b5c55-343">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="b5c55-344">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="b5c55-344">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="b5c55-345">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="b5c55-345">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="b5c55-346">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="b5c55-346">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="b5c55-347">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="b5c55-347">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="b5c55-348">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="b5c55-348">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="b5c55-349">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="b5c55-349">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="b5c55-350">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="b5c55-350">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="b5c55-351">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="b5c55-351">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="b5c55-352">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="b5c55-352">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="b5c55-353">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="b5c55-353">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="b5c55-354">回應可能不包含 `Set-:::no-loc(Cookie):::`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-354">The response may not include `Set-:::no-loc(Cookie):::`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="b5c55-355">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-355">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="b5c55-356">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-356">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="b5c55-357">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="b5c55-357">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="b5c55-358">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-358">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="b5c55-359">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="b5c55-359">Use `-1` for infinite.</span></span> <span data-ttu-id="b5c55-360">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-360">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="b5c55-361"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="b5c55-361">(machine-wide</span></span><br><span data-ttu-id="b5c55-362">設定) </span><span class="sxs-lookup"><span data-stu-id="b5c55-362">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="b5c55-363">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="b5c55-363">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="b5c55-364">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="b5c55-364">30000000 bytes</span></span><br><span data-ttu-id="b5c55-365">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="b5c55-365">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="b5c55-366">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="b5c55-366">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="b5c55-367">1000</span><span class="sxs-lookup"><span data-stu-id="b5c55-367">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="b5c55-368">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="b5c55-368">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="b5c55-369">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="b5c55-369">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="b5c55-370">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-370">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="b5c55-371">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="b5c55-371">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="b5c55-372">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="b5c55-373">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="b5c55-374">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="b5c55-375">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="b5c55-376">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="b5c55-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="b5c55-377">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="b5c55-378">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="b5c55-378">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="b5c55-379">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="b5c55-379">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="b5c55-380">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="b5c55-380">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="b5c55-381">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="b5c55-381">**MaxRequestBodySize**</span></span>

<span data-ttu-id="b5c55-382">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-382">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="b5c55-383">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="b5c55-383">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="b5c55-384">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="b5c55-384">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="b5c55-385">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="b5c55-385">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="b5c55-386">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b5c55-386">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="b5c55-387">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="b5c55-387">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="b5c55-388">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="b5c55-388">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="b5c55-389">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="b5c55-389">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="b5c55-390">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="b5c55-390">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="b5c55-391">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="b5c55-391">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="b5c55-393">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="b5c55-393">Configure Windows Server</span></span>

1. <span data-ttu-id="b5c55-394">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="b5c55-394">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="b5c55-395">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="b5c55-395">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="b5c55-396">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-396">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="b5c55-397">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="b5c55-397">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="b5c55-398">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-398">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="b5c55-399">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-399">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="b5c55-400">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-400">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="b5c55-401">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]  存放區中。</span><span class="sxs-lookup"><span data-stu-id="b5c55-401">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="b5c55-402">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-402">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="b5c55-403">**.Net core** ：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-403">**.NET Core** : If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="b5c55-404">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="b5c55-404">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="b5c55-405">**.NET Framework** ：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-405">**.NET Framework** : If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="b5c55-406">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="b5c55-406">Install the required .NET Framework.</span></span> <span data-ttu-id="b5c55-407">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-407">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="b5c55-408">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="b5c55-408">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="b5c55-409">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="b5c55-409">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="b5c55-410">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-410">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="b5c55-411">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-411">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="b5c55-412">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="b5c55-412">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="b5c55-413">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="b5c55-413">`urls` command-line argument</span></span>
   * <span data-ttu-id="b5c55-414">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="b5c55-414">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="b5c55-415">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-415">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="b5c55-416">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="b5c55-416">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="b5c55-417">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-417">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="b5c55-418">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="b5c55-418">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="b5c55-419">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-419">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="b5c55-420">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-420">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="b5c55-421">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="b5c55-421">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="b5c55-422">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-422">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="b5c55-423">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="b5c55-423">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="b5c55-424">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="b5c55-424">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="b5c55-425">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-425">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="b5c55-426">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="b5c55-426">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="b5c55-427">用來設定 HTTP.sys 的內建工具是 *netsh.exe* 。</span><span class="sxs-lookup"><span data-stu-id="b5c55-427">The built-in tool for configuring HTTP.sys is *netsh.exe* .</span></span> <span data-ttu-id="b5c55-428">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-428">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="b5c55-429">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-429">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="b5c55-430">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="b5c55-430">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="b5c55-431">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="b5c55-431">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="b5c55-432">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="b5c55-432">Don't use a wildcard binding.</span></span> <span data-ttu-id="b5c55-433">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-433">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="b5c55-434">URL 必須包含結尾斜線。 </span><span class="sxs-lookup"><span data-stu-id="b5c55-434">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="b5c55-435">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="b5c55-435">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="b5c55-436">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-436">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="b5c55-437">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-437">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="b5c55-438">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="b5c55-438">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="b5c55-439">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-439">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="b5c55-440">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="b5c55-440">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="b5c55-441">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-441">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="b5c55-442">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="b5c55-442">Don't use a wildcard binding.</span></span> <span data-ttu-id="b5c55-443">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-443">Use a valid IP address.</span></span>
   * <span data-ttu-id="b5c55-444">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-444">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="b5c55-445">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="b5c55-445">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="b5c55-446">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-446">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="b5c55-447">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="b5c55-447">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="b5c55-448">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="b5c55-448">In Visual Studio:</span></span>
     * <span data-ttu-id="b5c55-449">在 [方案總管]  中的專案上按一下滑鼠右鍵，然後選取 [屬性]  ，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="b5c55-449">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties** .</span></span>
     * <span data-ttu-id="b5c55-450">選取 [套件]  索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b5c55-450">Select the **Package** tab.</span></span>
     * <span data-ttu-id="b5c55-451">輸入您在 [標記]  欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="b5c55-451">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="b5c55-452">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="b5c55-452">When not using Visual Studio:</span></span>
     * <span data-ttu-id="b5c55-453">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="b5c55-453">Open the app's project file.</span></span>
     * <span data-ttu-id="b5c55-454">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-454">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="b5c55-455">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="b5c55-455">In the following example:</span></span>

   * <span data-ttu-id="b5c55-456">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-456">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="b5c55-457">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="b5c55-457">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="b5c55-458">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-458">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="b5c55-459">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="b5c55-459">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="b5c55-460">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="b5c55-460">Reference documentation for *netsh.exe* :</span></span>

   * <span data-ttu-id="b5c55-461">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="b5c55-461">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="b5c55-462">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="b5c55-462">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="b5c55-463">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-463">Run the app.</span></span>

   <span data-ttu-id="b5c55-464">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-464">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="b5c55-465">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-465">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="b5c55-466">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-466">The app responds at the server's public IP address.</span></span> <span data-ttu-id="b5c55-467">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="b5c55-467">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="b5c55-468">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-468">A development certificate is used in this example.</span></span> <span data-ttu-id="b5c55-469">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="b5c55-469">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b5c55-471">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="b5c55-471">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b5c55-472">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="b5c55-472">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="b5c55-473">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-473">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5c55-474">其他資源</span><span class="sxs-lookup"><span data-stu-id="b5c55-474">Additional resources</span></span>

* <span data-ttu-id="b5c55-475">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-475">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="b5c55-476">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-476">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="b5c55-477">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-477">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="b5c55-478">主機</span><span class="sxs-lookup"><span data-stu-id="b5c55-478">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b5c55-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="b5c55-480">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="b5c55-480">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b5c55-481">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-481">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="b5c55-482">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="b5c55-482">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="b5c55-483">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="b5c55-483">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="b5c55-484">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="b5c55-484">Port sharing</span></span>
* <span data-ttu-id="b5c55-485">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="b5c55-485">HTTPS with SNI</span></span>
* <span data-ttu-id="b5c55-486">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="b5c55-486">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="b5c55-487">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="b5c55-487">Direct file transmission</span></span>
* <span data-ttu-id="b5c55-488">回應快取</span><span class="sxs-lookup"><span data-stu-id="b5c55-488">Response caching</span></span>
* <span data-ttu-id="b5c55-489">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="b5c55-489">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="b5c55-490">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="b5c55-490">Supported Windows versions:</span></span>

* <span data-ttu-id="b5c55-491">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-491">Windows 7 or later</span></span>
* <span data-ttu-id="b5c55-492">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-492">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b5c55-493">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="b5c55-493">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="b5c55-494">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="b5c55-494">When to use HTTP.sys</span></span>

<span data-ttu-id="b5c55-495">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="b5c55-495">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="b5c55-496">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="b5c55-496">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="b5c55-498">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-498">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="b5c55-500">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="b5c55-500">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="b5c55-501">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="b5c55-501">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="b5c55-502">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="b5c55-502">HTTP/2 support</span></span>

<span data-ttu-id="b5c55-503">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="b5c55-503">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="b5c55-504">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-504">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="b5c55-505">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="b5c55-505">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="b5c55-506">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="b5c55-506">TLS 1.2 or later connection</span></span>

<span data-ttu-id="b5c55-507">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-507">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="b5c55-508">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-508">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="b5c55-509">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="b5c55-509">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="b5c55-510">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="b5c55-510">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="b5c55-511">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="b5c55-511">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="b5c55-512">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-512">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="b5c55-513">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-513">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="b5c55-514">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="b5c55-514">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="b5c55-515">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="b5c55-515">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="b5c55-516">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b5c55-516">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="b5c55-517">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b5c55-517">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="b5c55-518">使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 時，不需要專案檔中的套件參考。</span><span class="sxs-lookup"><span data-stu-id="b5c55-518">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="b5c55-519">若不是使用 `Microsoft.AspNetCore.App` 中繼套件，請將套件參考加入 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-519">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="b5c55-520">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="b5c55-520">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="b5c55-521">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="b5c55-521">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="b5c55-522">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="b5c55-522">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="b5c55-523">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="b5c55-523">**HTTP.sys options**</span></span>

| <span data-ttu-id="b5c55-524">屬性</span><span class="sxs-lookup"><span data-stu-id="b5c55-524">Property</span></span> | <span data-ttu-id="b5c55-525">描述</span><span class="sxs-lookup"><span data-stu-id="b5c55-525">Description</span></span> | <span data-ttu-id="b5c55-526">預設</span><span class="sxs-lookup"><span data-stu-id="b5c55-526">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="b5c55-527">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="b5c55-527">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="b5c55-528">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="b5c55-528">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="b5c55-529">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="b5c55-529">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="b5c55-530">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="b5c55-530">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="b5c55-531">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="b5c55-531">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="b5c55-532">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="b5c55-532">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="b5c55-533">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="b5c55-533">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="b5c55-534">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="b5c55-534">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="b5c55-535">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="b5c55-535">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="b5c55-536">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="b5c55-536">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="b5c55-537">回應可能不包含 `Set-:::no-loc(Cookie):::`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-537">The response may not include `Set-:::no-loc(Cookie):::`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="b5c55-538">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-538">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="b5c55-539">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-539">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="b5c55-540">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="b5c55-540">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="b5c55-541">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-541">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="b5c55-542">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="b5c55-542">Use `-1` for infinite.</span></span> <span data-ttu-id="b5c55-543">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-543">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="b5c55-544"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="b5c55-544">(machine-wide</span></span><br><span data-ttu-id="b5c55-545">設定) </span><span class="sxs-lookup"><span data-stu-id="b5c55-545">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="b5c55-546">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="b5c55-546">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="b5c55-547">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="b5c55-547">30000000 bytes</span></span><br><span data-ttu-id="b5c55-548">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="b5c55-548">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="b5c55-549">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="b5c55-549">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="b5c55-550">1000</span><span class="sxs-lookup"><span data-stu-id="b5c55-550">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="b5c55-551">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="b5c55-551">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="b5c55-552">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="b5c55-552">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="b5c55-553">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-553">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="b5c55-554">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="b5c55-554">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="b5c55-555">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="b5c55-556">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="b5c55-557">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="b5c55-558">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="b5c55-559">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="b5c55-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="b5c55-560">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="b5c55-561">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="b5c55-561">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="b5c55-562">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="b5c55-562">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="b5c55-563">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="b5c55-563">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="b5c55-564">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="b5c55-564">**MaxRequestBodySize**</span></span>

<span data-ttu-id="b5c55-565">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-565">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="b5c55-566">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="b5c55-566">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="b5c55-567">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="b5c55-567">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="b5c55-568">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="b5c55-568">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="b5c55-569">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b5c55-569">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="b5c55-570">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="b5c55-570">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="b5c55-571">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="b5c55-571">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="b5c55-572">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="b5c55-572">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="b5c55-573">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="b5c55-573">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="b5c55-574">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="b5c55-574">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="b5c55-576">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="b5c55-576">Configure Windows Server</span></span>

1. <span data-ttu-id="b5c55-577">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="b5c55-577">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="b5c55-578">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="b5c55-578">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="b5c55-579">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-579">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="b5c55-580">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="b5c55-580">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="b5c55-581">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-581">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="b5c55-582">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-582">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="b5c55-583">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-583">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="b5c55-584">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]  存放區中。</span><span class="sxs-lookup"><span data-stu-id="b5c55-584">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="b5c55-585">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-585">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="b5c55-586">**.Net core** ：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-586">**.NET Core** : If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="b5c55-587">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="b5c55-587">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="b5c55-588">**.NET Framework** ：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-588">**.NET Framework** : If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="b5c55-589">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="b5c55-589">Install the required .NET Framework.</span></span> <span data-ttu-id="b5c55-590">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-590">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="b5c55-591">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="b5c55-591">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="b5c55-592">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="b5c55-592">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="b5c55-593">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-593">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="b5c55-594">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-594">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="b5c55-595">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="b5c55-595">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="b5c55-596">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="b5c55-596">`urls` command-line argument</span></span>
   * <span data-ttu-id="b5c55-597">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="b5c55-597">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="b5c55-598">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-598">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="b5c55-599">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="b5c55-599">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="b5c55-600">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-600">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="b5c55-601">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="b5c55-601">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="b5c55-602">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-602">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="b5c55-603">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-603">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="b5c55-604">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="b5c55-604">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="b5c55-605">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-605">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="b5c55-606">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="b5c55-606">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="b5c55-607">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="b5c55-607">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="b5c55-608">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-608">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="b5c55-609">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="b5c55-609">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="b5c55-610">用來設定 HTTP.sys 的內建工具是 *netsh.exe* 。</span><span class="sxs-lookup"><span data-stu-id="b5c55-610">The built-in tool for configuring HTTP.sys is *netsh.exe* .</span></span> <span data-ttu-id="b5c55-611">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-611">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="b5c55-612">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-612">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="b5c55-613">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="b5c55-613">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="b5c55-614">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="b5c55-614">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="b5c55-615">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="b5c55-615">Don't use a wildcard binding.</span></span> <span data-ttu-id="b5c55-616">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-616">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="b5c55-617">URL 必須包含結尾斜線。 </span><span class="sxs-lookup"><span data-stu-id="b5c55-617">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="b5c55-618">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="b5c55-618">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="b5c55-619">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-619">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="b5c55-620">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-620">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="b5c55-621">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="b5c55-621">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="b5c55-622">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-622">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="b5c55-623">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="b5c55-623">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="b5c55-624">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-624">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="b5c55-625">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="b5c55-625">Don't use a wildcard binding.</span></span> <span data-ttu-id="b5c55-626">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-626">Use a valid IP address.</span></span>
   * <span data-ttu-id="b5c55-627">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-627">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="b5c55-628">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="b5c55-628">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="b5c55-629">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-629">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="b5c55-630">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="b5c55-630">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="b5c55-631">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="b5c55-631">In Visual Studio:</span></span>
     * <span data-ttu-id="b5c55-632">在 [方案總管]  中的專案上按一下滑鼠右鍵，然後選取 [屬性]  ，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="b5c55-632">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties** .</span></span>
     * <span data-ttu-id="b5c55-633">選取 [套件]  索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b5c55-633">Select the **Package** tab.</span></span>
     * <span data-ttu-id="b5c55-634">輸入您在 [標記]  欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="b5c55-634">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="b5c55-635">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="b5c55-635">When not using Visual Studio:</span></span>
     * <span data-ttu-id="b5c55-636">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="b5c55-636">Open the app's project file.</span></span>
     * <span data-ttu-id="b5c55-637">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-637">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="b5c55-638">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="b5c55-638">In the following example:</span></span>

   * <span data-ttu-id="b5c55-639">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-639">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="b5c55-640">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="b5c55-640">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="b5c55-641">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-641">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="b5c55-642">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="b5c55-642">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="b5c55-643">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="b5c55-643">Reference documentation for *netsh.exe* :</span></span>

   * <span data-ttu-id="b5c55-644">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="b5c55-644">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="b5c55-645">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="b5c55-645">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="b5c55-646">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-646">Run the app.</span></span>

   <span data-ttu-id="b5c55-647">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-647">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="b5c55-648">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-648">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="b5c55-649">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-649">The app responds at the server's public IP address.</span></span> <span data-ttu-id="b5c55-650">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="b5c55-650">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="b5c55-651">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-651">A development certificate is used in this example.</span></span> <span data-ttu-id="b5c55-652">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="b5c55-652">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b5c55-654">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="b5c55-654">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b5c55-655">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="b5c55-655">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="b5c55-656">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-656">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5c55-657">其他資源</span><span class="sxs-lookup"><span data-stu-id="b5c55-657">Additional resources</span></span>

* <span data-ttu-id="b5c55-658">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-658">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="b5c55-659">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-659">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="b5c55-660">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-660">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="b5c55-661">主機</span><span class="sxs-lookup"><span data-stu-id="b5c55-661">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b5c55-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是只在 Windows 上執行的 [ASP.NET Core 網頁伺服器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="b5c55-663">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 伺服器的替代方式，提供了一些 Kestrel 未提供的功能。</span><span class="sxs-lookup"><span data-stu-id="b5c55-663">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b5c55-664">HTTP.sys 與 [ASP.NET Core 模組](xref:host-and-deploy/aspnet-core-module)不相容，且不能與 IIS 或 IIS Express 搭配使用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-664">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="b5c55-665">HTTP.sys 支援下列功能：</span><span class="sxs-lookup"><span data-stu-id="b5c55-665">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="b5c55-666">Windows 驗證</span><span class="sxs-lookup"><span data-stu-id="b5c55-666">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="b5c55-667">連接埠共用</span><span class="sxs-lookup"><span data-stu-id="b5c55-667">Port sharing</span></span>
* <span data-ttu-id="b5c55-668">使用 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="b5c55-668">HTTPS with SNI</span></span>
* <span data-ttu-id="b5c55-669">透過 TLS 的 HTTP/2 (Windows 10 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="b5c55-669">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="b5c55-670">直接檔案傳輸</span><span class="sxs-lookup"><span data-stu-id="b5c55-670">Direct file transmission</span></span>
* <span data-ttu-id="b5c55-671">回應快取</span><span class="sxs-lookup"><span data-stu-id="b5c55-671">Response caching</span></span>
* <span data-ttu-id="b5c55-672">WebSocket (Windows 8 或更新版本)</span><span class="sxs-lookup"><span data-stu-id="b5c55-672">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="b5c55-673">支援的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="b5c55-673">Supported Windows versions:</span></span>

* <span data-ttu-id="b5c55-674">Windows 7 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-674">Windows 7 or later</span></span>
* <span data-ttu-id="b5c55-675">Windows Server 2008 R2 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-675">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="b5c55-676">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="b5c55-676">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="b5c55-677">使用 HTTP.sys 的時機</span><span class="sxs-lookup"><span data-stu-id="b5c55-677">When to use HTTP.sys</span></span>

<span data-ttu-id="b5c55-678">HTTP.sys 在下列部署環境中非常有用：</span><span class="sxs-lookup"><span data-stu-id="b5c55-678">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="b5c55-679">需要直接向網際網路公開伺服器而不使用 IIS。</span><span class="sxs-lookup"><span data-stu-id="b5c55-679">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="b5c55-681">內部部署需要的功能無法在 Kestrel 中使用，例如 [Windows 驗證](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-681">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="b5c55-683">HTTP.sys 是成熟的技術，可抵禦許多種類的攻擊，並提供功能完整之網頁伺服器的穩固性、安全性及延展性。</span><span class="sxs-lookup"><span data-stu-id="b5c55-683">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="b5c55-684">IIS 本身在 HTTP.sys 之上以 HTTP 接聽程式的形式執行。</span><span class="sxs-lookup"><span data-stu-id="b5c55-684">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="b5c55-685">HTTP/2 支援</span><span class="sxs-lookup"><span data-stu-id="b5c55-685">HTTP/2 support</span></span>

<span data-ttu-id="b5c55-686">如果符合下列基本需求，則可以針對 ASP.NET Core 應用程式啟用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="b5c55-686">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="b5c55-687">Windows Server 2016/Windows 10 或更新版本</span><span class="sxs-lookup"><span data-stu-id="b5c55-687">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="b5c55-688">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 連線</span><span class="sxs-lookup"><span data-stu-id="b5c55-688">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="b5c55-689">TLS 1.2 或更新版本連線</span><span class="sxs-lookup"><span data-stu-id="b5c55-689">TLS 1.2 or later connection</span></span>

<span data-ttu-id="b5c55-690">如果已建立 HTTP/2 連線，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 會報告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-690">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="b5c55-691">HTTP/2 預設為啟用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-691">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="b5c55-692">如果 HTTP/2 連線尚未建立，連線會退為 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="b5c55-692">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="b5c55-693">Windows 的未來版本會推出 HTTP/2 設定旗標，包括使用 HTTP.sys 來停用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="b5c55-693">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="b5c55-694">使用 Kerberos 的核心模式驗證</span><span class="sxs-lookup"><span data-stu-id="b5c55-694">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="b5c55-695">HTTP.sys 使用 Kerberos 驗證通訊協定委派給核心模式驗證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-695">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="b5c55-696">Kerberos 和 HTTP.sys 不支援使用者模式驗證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-696">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="b5c55-697">必須使用電腦帳戶來解密 Kerberos 權杖/票證，該權杖/票證取自 Active Directory，並由用戶端將其轉送至伺服器來驗證使用者。</span><span class="sxs-lookup"><span data-stu-id="b5c55-697">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="b5c55-698">請註冊主機的服務主體名稱 (SPN)，而非應用程式的使用者。</span><span class="sxs-lookup"><span data-stu-id="b5c55-698">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="b5c55-699">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b5c55-699">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="b5c55-700">設定 ASP.NET Core 應用程式使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b5c55-700">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="b5c55-701">使用 [AspNetCore 中繼套件](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 時，不需要專案檔中的套件參考。</span><span class="sxs-lookup"><span data-stu-id="b5c55-701">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="b5c55-702">若不是使用 `Microsoft.AspNetCore.App` 中繼套件，請將套件參考加入 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-702">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="b5c55-703">建置主機時，呼叫 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 擴充方法，並指定任何必要的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="b5c55-703">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="b5c55-704">下列範例會將選項設定為它們的預設值：</span><span class="sxs-lookup"><span data-stu-id="b5c55-704">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="b5c55-705">其他的 HTTP.sys 設定則透過[登錄設定](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)處理。</span><span class="sxs-lookup"><span data-stu-id="b5c55-705">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="b5c55-706">**HTTP.sys 選項**</span><span class="sxs-lookup"><span data-stu-id="b5c55-706">**HTTP.sys options**</span></span>

| <span data-ttu-id="b5c55-707">屬性</span><span class="sxs-lookup"><span data-stu-id="b5c55-707">Property</span></span> | <span data-ttu-id="b5c55-708">描述</span><span class="sxs-lookup"><span data-stu-id="b5c55-708">Description</span></span> | <span data-ttu-id="b5c55-709">預設</span><span class="sxs-lookup"><span data-stu-id="b5c55-709">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="b5c55-710">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="b5c55-710">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="b5c55-711">控制是否允許 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 同步輸出/輸入。</span><span class="sxs-lookup"><span data-stu-id="b5c55-711">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="b5c55-712">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="b5c55-712">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="b5c55-713">允許匿名要求。</span><span class="sxs-lookup"><span data-stu-id="b5c55-713">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="b5c55-714">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="b5c55-714">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="b5c55-715">指定允許的驗證配置。</span><span class="sxs-lookup"><span data-stu-id="b5c55-715">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="b5c55-716">處置接聽程式之前可隨時修改。</span><span class="sxs-lookup"><span data-stu-id="b5c55-716">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="b5c55-717">值是由 [AuthenticationSchemes 列舉](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)提供： `Basic` 、、 `Kerberos` `Negotiate` 、 `None` 和 `NTLM` 。</span><span class="sxs-lookup"><span data-stu-id="b5c55-717">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="b5c55-718">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="b5c55-718">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="b5c55-719">針對含有合格標頭的回應嘗試[核心模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)快取。</span><span class="sxs-lookup"><span data-stu-id="b5c55-719">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="b5c55-720">回應可能不包含 `Set-:::no-loc(Cookie):::`、`Vary` 或 `Pragma` 標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-720">The response may not include `Set-:::no-loc(Cookie):::`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="b5c55-721">它必須包含為 `public` 的 `Cache-Control` 標頭，且有 `shared-max-age` 或 `max-age` 值，或是 `Expires` 標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-721">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="b5c55-722">可同時接受的數目上限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-722">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="b5c55-723">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="b5c55-723">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="b5c55-724">可接受的同時連線數量上限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-724">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="b5c55-725">使用 `-1` 為無限多個。</span><span class="sxs-lookup"><span data-stu-id="b5c55-725">Use `-1` for infinite.</span></span> <span data-ttu-id="b5c55-726">使用 `null` 以使用登錄之整個電腦的設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-726">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="b5c55-727"> (全電腦</span><span class="sxs-lookup"><span data-stu-id="b5c55-727">(machine-wide</span></span><br><span data-ttu-id="b5c55-728">設定) </span><span class="sxs-lookup"><span data-stu-id="b5c55-728">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="b5c55-729">請參閱 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 小節。</span><span class="sxs-lookup"><span data-stu-id="b5c55-729">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="b5c55-730">30000000 位元組</span><span class="sxs-lookup"><span data-stu-id="b5c55-730">30000000 bytes</span></span><br><span data-ttu-id="b5c55-731">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="b5c55-731">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="b5c55-732">可以加入佇列的最大要求數目。</span><span class="sxs-lookup"><span data-stu-id="b5c55-732">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="b5c55-733">1000</span><span class="sxs-lookup"><span data-stu-id="b5c55-733">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="b5c55-734">指出若回應本文因為用戶端中斷連線而寫入失敗時，應擲回例外狀況或正常完成。</span><span class="sxs-lookup"><span data-stu-id="b5c55-734">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="b5c55-735">(正常完成)</span><span class="sxs-lookup"><span data-stu-id="b5c55-735">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="b5c55-736">公開 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 設定，這也可在登錄中設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-736">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="b5c55-737">API 連結可提供包括預設值在內每個設定的詳細資訊：</span><span class="sxs-lookup"><span data-stu-id="b5c55-737">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="b5c55-738">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)： HTTP 伺服器 API 在 Keep-Alive 連接上清空實體主體所允許的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="b5c55-739">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：要求實體內容到達的允許時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="b5c55-740">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)： HTTP 伺服器 API 允許的時間剖析要求標頭。</span><span class="sxs-lookup"><span data-stu-id="b5c55-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="b5c55-741">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允許閒置連接的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="b5c55-742">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：回應的最小傳送速率。</span><span class="sxs-lookup"><span data-stu-id="b5c55-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="b5c55-743">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：允許要求在應用程式拾取之前保留在要求佇列中的時間。</span><span class="sxs-lookup"><span data-stu-id="b5c55-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="b5c55-744">指定 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> 以向 HTTP.sys 註冊。</span><span class="sxs-lookup"><span data-stu-id="b5c55-744">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="b5c55-745">最實用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，可用來將前置詞加入集合。</span><span class="sxs-lookup"><span data-stu-id="b5c55-745">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="b5c55-746">處置接聽程式之前可隨時修改這些內容。</span><span class="sxs-lookup"><span data-stu-id="b5c55-746">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="b5c55-747">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="b5c55-747">**MaxRequestBodySize**</span></span>

<span data-ttu-id="b5c55-748">任何要求所允許的大小上限 (以位元組為單位)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-748">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="b5c55-749">當設定為 `null` 時，要求主體大小上限為無限制。</span><span class="sxs-lookup"><span data-stu-id="b5c55-749">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="b5c55-750">此限制對升級連線不會有任何影響，因為其一律為無限制。</span><span class="sxs-lookup"><span data-stu-id="b5c55-750">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="b5c55-751">在 ASP.NET Core MVC 應用程式中針對單一 `IActionResult` 覆寫限制的建議方式，是在動作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 屬性：</span><span class="sxs-lookup"><span data-stu-id="b5c55-751">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="b5c55-752">如果應用程式已開始讀取要求之後，才嘗試設定要求的限制，則會擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="b5c55-752">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="b5c55-753">`IsReadOnly` 屬性可用來指出 `MaxRequestBodySize` 屬性是否處於唯讀狀態，代表要設定限制已經太遲。</span><span class="sxs-lookup"><span data-stu-id="b5c55-753">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="b5c55-754">如果應用程式應該覆寫每個要求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，請使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="b5c55-754">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="b5c55-755">如果您使用 Visual Studio，請確定應用程式未設定為執行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="b5c55-755">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="b5c55-756">在 Visual Studio 中，預設啟動設定檔適用於 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="b5c55-756">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="b5c55-757">若要執行專案作為主控台應用程式，請手動變更選取的設定檔，如下列螢幕擷取畫面所示：</span><span class="sxs-lookup"><span data-stu-id="b5c55-757">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![選取主控台應用程式設定檔](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="b5c55-759">設定 Windows Server</span><span class="sxs-lookup"><span data-stu-id="b5c55-759">Configure Windows Server</span></span>

1. <span data-ttu-id="b5c55-760">判斷要為應用程式開啟的連接埠，然後使用 [Windows 防火牆](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell Cmdlet 來開啟防火牆連接埠，以允許流量到達 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="b5c55-760">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="b5c55-761">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="b5c55-761">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="b5c55-762">部署至 Azure VM 時，請在[網路安全性群組](/azure/virtual-machines/windows/nsg-quickstart-portal)中開啟連接埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-762">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="b5c55-763">在下列命令和應用程式設定中，會使用連接埠 443。</span><span class="sxs-lookup"><span data-stu-id="b5c55-763">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="b5c55-764">視需要取得並安裝 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-764">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="b5c55-765">在 Windows 上，請使用 [New-SelfSignedCertificate PowerShell Cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 來建立自我簽署的憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-765">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="b5c55-766">如需不支援的範例，請參閱 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-766">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="b5c55-767">將自我簽署的憑證或 CA 簽署的憑證安裝在伺服器的 [本機電腦]  存放區中。</span><span class="sxs-lookup"><span data-stu-id="b5c55-767">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="b5c55-768">如果應用程式是[與架構相依的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，請安裝 .NET Core、.NET Framework 或兩者 (如果應用程式是以 .NET Framework 為目標的 .NET Core 應用程式)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-768">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="b5c55-769">**.Net core** ：如果應用程式需要 .net core，請從 [.net core 下載](https://dotnet.microsoft.com/download)取得並執行 **.net core 運行** 時間安裝程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-769">**.NET Core** : If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="b5c55-770">請勿在伺服器上安裝完整的 SDK。</span><span class="sxs-lookup"><span data-stu-id="b5c55-770">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="b5c55-771">**.NET Framework** ：如果應用程式需要 .NET Framework，請參閱 [.NET Framework 安裝指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-771">**.NET Framework** : If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="b5c55-772">安裝必要的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="b5c55-772">Install the required .NET Framework.</span></span> <span data-ttu-id="b5c55-773">您可以從 [.NET Core 下載](https://dotnet.microsoft.com/download)頁面取得最新 .NET Framework 的安裝程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-773">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="b5c55-774">如果應用程式是[自封式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，則應用程式的部署中會包含執行階段。</span><span class="sxs-lookup"><span data-stu-id="b5c55-774">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="b5c55-775">不需要在伺服器上安裝任何架構。</span><span class="sxs-lookup"><span data-stu-id="b5c55-775">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="b5c55-776">設定應用程式中的 URL 和連接埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-776">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="b5c55-777">ASP.NET Core 預設會繫結至 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-777">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="b5c55-778">若要設定 URL 首碼和連接埠，選項包括：</span><span class="sxs-lookup"><span data-stu-id="b5c55-778">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="b5c55-779">`urls` 命令列引數</span><span class="sxs-lookup"><span data-stu-id="b5c55-779">`urls` command-line argument</span></span>
   * <span data-ttu-id="b5c55-780">`ASPNETCORE_URLS` 環境變數</span><span class="sxs-lookup"><span data-stu-id="b5c55-780">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="b5c55-781">下列程式碼範例示範如何使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 搭配位於連接埠 443 的伺服器本機 IP 位址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-781">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="b5c55-782">`UrlPrefixes` 的優點是針對錯誤格式的前置詞會立即產生錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="b5c55-782">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="b5c55-783">`UrlPrefixes` 中的設定會覆寫 `UseUrls`/`urls`/`ASPNETCORE_URLS` 設定。</span><span class="sxs-lookup"><span data-stu-id="b5c55-783">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="b5c55-784">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 環境變數的優點，是能更輕鬆地在 Kestrel 和 HTTP.sys 之間切換。</span><span class="sxs-lookup"><span data-stu-id="b5c55-784">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="b5c55-785">HTTP.sys 使用 [HTTP Server API UrlPrefix 字串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-785">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="b5c55-786">請 **勿** 使用最上層萬用字元繫結 (`http://*:80/`與 `http://+:80`)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-786">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="b5c55-787">最上層萬用字元繫結會導致應用程式安全性弱點。</span><span class="sxs-lookup"><span data-stu-id="b5c55-787">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="b5c55-788">這對強式與弱式萬用字元皆適用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-788">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="b5c55-789">請使用明確的主機名稱或 IP 位址，而不要使用萬用字元。</span><span class="sxs-lookup"><span data-stu-id="b5c55-789">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="b5c55-790">若您擁有整個父網域 (相對於有弱點的 `*.com`) 的控制權，則子網域萬用字元繫結 (例如 `*.mysub.com`) 便不構成安全性風險。</span><span class="sxs-lookup"><span data-stu-id="b5c55-790">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="b5c55-791">如需詳細資訊，請參閱 [RFC 7230：第5.4 節： Host](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-791">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="b5c55-792">在伺服器上預先註冊 URL 首碼。</span><span class="sxs-lookup"><span data-stu-id="b5c55-792">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="b5c55-793">用來設定 HTTP.sys 的內建工具是 *netsh.exe* 。</span><span class="sxs-lookup"><span data-stu-id="b5c55-793">The built-in tool for configuring HTTP.sys is *netsh.exe* .</span></span> <span data-ttu-id="b5c55-794">*netsh.exe* 是用來保留 URL 前置詞，並指派 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-794">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="b5c55-795">此工具需要系統管理員權限。</span><span class="sxs-lookup"><span data-stu-id="b5c55-795">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="b5c55-796">使用 *netsh.exe* 工具來為應用程式註冊 URL：</span><span class="sxs-lookup"><span data-stu-id="b5c55-796">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="b5c55-797">`<URL>`： (URL) 的完整統一資源定位器。</span><span class="sxs-lookup"><span data-stu-id="b5c55-797">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="b5c55-798">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="b5c55-798">Don't use a wildcard binding.</span></span> <span data-ttu-id="b5c55-799">請使用有效的主機名稱或本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-799">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="b5c55-800">URL 必須包含結尾斜線。 </span><span class="sxs-lookup"><span data-stu-id="b5c55-800">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="b5c55-801">`<USER>`：指定使用者或使用者組名。</span><span class="sxs-lookup"><span data-stu-id="b5c55-801">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="b5c55-802">在以下範例中，伺服器的本機 IP 位址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-802">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="b5c55-803">已註冊 URL 時，此工具會以 `URL reservation successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-803">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="b5c55-804">若要刪除已註冊的 URL，請使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="b5c55-804">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="b5c55-805">在伺服器上註冊 X.509 憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-805">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="b5c55-806">使用 *netsh.exe* 工具來為應用程式註冊憑證：</span><span class="sxs-lookup"><span data-stu-id="b5c55-806">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="b5c55-807">`<IP>`：指定系結的本機 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-807">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="b5c55-808">請勿使用萬用字元繫結。</span><span class="sxs-lookup"><span data-stu-id="b5c55-808">Don't use a wildcard binding.</span></span> <span data-ttu-id="b5c55-809">請使用有效的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="b5c55-809">Use a valid IP address.</span></span>
   * <span data-ttu-id="b5c55-810">`<PORT>`：指定系結的埠。</span><span class="sxs-lookup"><span data-stu-id="b5c55-810">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="b5c55-811">`<THUMBPRINT>`： X.509 憑證指紋。</span><span class="sxs-lookup"><span data-stu-id="b5c55-811">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="b5c55-812">`<GUID>`：開發人員產生的 GUID，用來代表應用程式，以供參考之用。</span><span class="sxs-lookup"><span data-stu-id="b5c55-812">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="b5c55-813">為了便於參考，請將 GUID 以套件標記的形式儲存在應用程式中：</span><span class="sxs-lookup"><span data-stu-id="b5c55-813">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="b5c55-814">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="b5c55-814">In Visual Studio:</span></span>
     * <span data-ttu-id="b5c55-815">在 [方案總管]  中的專案上按一下滑鼠右鍵，然後選取 [屬性]  ，以開啟應用程式的專案屬性。</span><span class="sxs-lookup"><span data-stu-id="b5c55-815">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties** .</span></span>
     * <span data-ttu-id="b5c55-816">選取 [套件]  索引標籤。</span><span class="sxs-lookup"><span data-stu-id="b5c55-816">Select the **Package** tab.</span></span>
     * <span data-ttu-id="b5c55-817">輸入您在 [標記]  欄位中建立的 GUID。</span><span class="sxs-lookup"><span data-stu-id="b5c55-817">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="b5c55-818">不是使用 Visual Studio 時：</span><span class="sxs-lookup"><span data-stu-id="b5c55-818">When not using Visual Studio:</span></span>
     * <span data-ttu-id="b5c55-819">開啟應用程式的專案檔。</span><span class="sxs-lookup"><span data-stu-id="b5c55-819">Open the app's project file.</span></span>
     * <span data-ttu-id="b5c55-820">將 `<PackageTags>` 屬性搭配您所建立的 GUID 新增至新的或現有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="b5c55-820">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="b5c55-821">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="b5c55-821">In the following example:</span></span>

   * <span data-ttu-id="b5c55-822">伺服器的本機 IP 位址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="b5c55-822">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="b5c55-823">線上隨機 GUID 產生器會提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="b5c55-823">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="b5c55-824">已註冊憑證時，此工具會以 `SSL Certificate successfully added` 回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-824">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="b5c55-825">若要刪除憑證註冊，請使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="b5c55-825">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="b5c55-826">以下是 *netsh.exe* 的參考文件：</span><span class="sxs-lookup"><span data-stu-id="b5c55-826">Reference documentation for *netsh.exe* :</span></span>

   * <span data-ttu-id="b5c55-827">[超文字傳輸通訊協定 (HTTP) 的 netsh 命令](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="b5c55-827">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="b5c55-828">UrlPrefix 字串</span><span class="sxs-lookup"><span data-stu-id="b5c55-828">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="b5c55-829">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-829">Run the app.</span></span>

   <span data-ttu-id="b5c55-830">使用 HTTP (不是 HTTPS) 搭配大於 1024 的連接埠號碼來繫結至 localhost 時，不需要系統管理員權限即可執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-830">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="b5c55-831">針對其他設定 (例如，使用本機 IP 位址或繫結至連接埠 443)，請使用系統管理員權限來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="b5c55-831">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="b5c55-832">應用程式會在伺服器的公用 IP 位址回應。</span><span class="sxs-lookup"><span data-stu-id="b5c55-832">The app responds at the server's public IP address.</span></span> <span data-ttu-id="b5c55-833">在此範例中，是從網際網路透過伺服器的公用 IP 位址 `104.214.79.47` 連線至伺服器。</span><span class="sxs-lookup"><span data-stu-id="b5c55-833">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="b5c55-834">在此範例中使用的是開發憑證。</span><span class="sxs-lookup"><span data-stu-id="b5c55-834">A development certificate is used in this example.</span></span> <span data-ttu-id="b5c55-835">在略過瀏覽器的未受信任憑證警告之後，頁面會安全地載入。</span><span class="sxs-lookup"><span data-stu-id="b5c55-835">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![顯示已載入應用程式索引頁面的瀏覽器視窗](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="b5c55-837">Proxy 伺服器和負載平衡器案例</span><span class="sxs-lookup"><span data-stu-id="b5c55-837">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="b5c55-838">如果是 HTTP.sys 所裝載且與來自網際網路或公司網路的要求進行互動的應用程式，在裝載於 Proxy 伺服器和負載平衡器後方時，可能需要額外的組態。</span><span class="sxs-lookup"><span data-stu-id="b5c55-838">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="b5c55-839">如需詳細資訊，請參閱[設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="b5c55-839">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b5c55-840">其他資源</span><span class="sxs-lookup"><span data-stu-id="b5c55-840">Additional resources</span></span>

* <span data-ttu-id="b5c55-841">[使用 HTTP.sys 來啟用 Windows 驗證](xref:security/authentication/windowsauth#httpsys) \(機器翻譯\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-841">[Enable Windows Authentication with HTTP.sys](xref:security/authentication/windowsauth#httpsys)</span></span>
* <span data-ttu-id="b5c55-842">[HTTP 伺服器 API](/windows/win32/http/http-api-start-page) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-842">[HTTP Server API](/windows/win32/http/http-api-start-page)</span></span>
* <span data-ttu-id="b5c55-843">[aspnet/HttpSysServer GitHub 存放庫 (原始程式碼)](https://github.com/aspnet/HttpSysServer/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="b5c55-843">[aspnet/HttpSysServer GitHub repository (source code)](https://github.com/aspnet/HttpSysServer/)</span></span>
* [<span data-ttu-id="b5c55-844">主機</span><span class="sxs-lookup"><span data-stu-id="b5c55-844">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
