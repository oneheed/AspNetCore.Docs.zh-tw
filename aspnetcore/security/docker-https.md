---
title: 使用 Docker over HTTPS 裝載 ASP.NET Core 映射
author: rick-anderson
description: 瞭解如何使用 Docker 透過 HTTPS 裝載 ASP.NET Core 映射
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
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
uid: security/docker-https
ms.openlocfilehash: 3201eac9128e30e63a34b2e1b8736ac69fb59d8f
ms.sourcegitcommit: 7354c2029164702d075fd3786d96a92c6d49bc6e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106164301"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="070e4-103">使用 Docker over HTTPS 裝載 ASP.NET Core 映射</span><span class="sxs-lookup"><span data-stu-id="070e4-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="070e4-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="070e4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="070e4-105">ASP.NET Core 預設會使用 [HTTPS](./enforcing-ssl.md)。</span><span class="sxs-lookup"><span data-stu-id="070e4-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="070e4-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) 依賴 [憑證](https://en.wikipedia.org/wiki/Public_key_certificate) 來進行信任、身分識別和加密。</span><span class="sxs-lookup"><span data-stu-id="070e4-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="070e4-107">本檔說明如何使用 [.net 命令列介面 (CLI) ](/dotnet/core/tools/)，以 HTTPS 執行預先建立的容器映射。</span><span class="sxs-lookup"><span data-stu-id="070e4-107">This document explains how to run pre-built container images with HTTPS using the [.NET command-line interface (CLI)](/dotnet/core/tools/).</span></span> <span data-ttu-id="070e4-108">如需有關如何使用 Visual Studio 在開發環境中執行 Docker 的指示，請參閱 [使用 docker OVER HTTPS 開發 ASP.NET Core 應用程式](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)。</span><span class="sxs-lookup"><span data-stu-id="070e4-108">For instructions on how to run Docker in development with Visual Studio, see [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md).</span></span>

<span data-ttu-id="070e4-109">此範例需要 docker [17.06](https://docs.docker.com/release-notes/docker-ce) 或更新版本的 [docker 用戶端](https://www.docker.com/products/docker)。</span><span class="sxs-lookup"><span data-stu-id="070e4-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="070e4-110">必要條件</span><span class="sxs-lookup"><span data-stu-id="070e4-110">Prerequisites</span></span>

<span data-ttu-id="070e4-111">本檔中的部分指示需要 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download) 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="070e4-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="070e4-112">憑證</span><span class="sxs-lookup"><span data-stu-id="070e4-112">Certificates</span></span>

<span data-ttu-id="070e4-113">網域的[生產環境裝載](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)需要證書[頒發機構](https://wikipedia.org/wiki/Certificate_authority)單位的憑證。</span><span class="sxs-lookup"><span data-stu-id="070e4-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="070e4-114">[Let's Encrypt](https://letsencrypt.org/) 是提供免費憑證的憑證授權單位單位。</span><span class="sxs-lookup"><span data-stu-id="070e4-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="070e4-115">本檔使用 [自我簽署的開發憑證](https://en.wikipedia.org/wiki/Self-signed_certificate) 來裝載預先建立的映射 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="070e4-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="070e4-116">這些指示與使用生產憑證類似。</span><span class="sxs-lookup"><span data-stu-id="070e4-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="070e4-117">使用 [dotnet dev](/dotnet/core/additional-tools/self-signed-certificates-guide) 憑證來建立自我簽署憑證，以進行開發和測試。</span><span class="sxs-lookup"><span data-stu-id="070e4-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="070e4-118">針對生產憑證：</span><span class="sxs-lookup"><span data-stu-id="070e4-118">For production certs:</span></span>

* <span data-ttu-id="070e4-119">`dotnet dev-certs`這是不必要的工具。</span><span class="sxs-lookup"><span data-stu-id="070e4-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="070e4-120">憑證不需要儲存在指示中所使用的位置。</span><span class="sxs-lookup"><span data-stu-id="070e4-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="070e4-121">任何位置都應該可以運作，但不建議將憑證儲存在您的網站目錄中。</span><span class="sxs-lookup"><span data-stu-id="070e4-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="070e4-122">下列章節中包含的指示會使用 Docker 的命令列選項，將憑證掛接到容器中 `-v` 。</span><span class="sxs-lookup"><span data-stu-id="070e4-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="070e4-123">您可以使用 Dockerfile 中的命令，將憑證新增至容器映射中 `COPY` ，但不建議這麼做。 </span><span class="sxs-lookup"><span data-stu-id="070e4-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="070e4-124">基於下列原因，不建議將憑證複製到映射：</span><span class="sxs-lookup"><span data-stu-id="070e4-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="070e4-125">使用相同的映射測試開發人員憑證會很困難。</span><span class="sxs-lookup"><span data-stu-id="070e4-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="070e4-126">使用相同的映射來裝載生產憑證會很困難。</span><span class="sxs-lookup"><span data-stu-id="070e4-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="070e4-127">憑證洩漏有很大的風險。</span><span class="sxs-lookup"><span data-stu-id="070e4-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="070e4-128">使用 HTTPS 執行預先建立的容器映射</span><span class="sxs-lookup"><span data-stu-id="070e4-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="070e4-129">針對您的作業系統設定，請使用下列指示。</span><span class="sxs-lookup"><span data-stu-id="070e4-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="070e4-130">使用 Linux 容器的 Windows</span><span class="sxs-lookup"><span data-stu-id="070e4-130">Windows using Linux containers</span></span>

<span data-ttu-id="070e4-131">產生憑證並設定本機電腦：</span><span class="sxs-lookup"><span data-stu-id="070e4-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="070e4-132">在上述命令中，以 `{ password here }` 密碼取代。</span><span class="sxs-lookup"><span data-stu-id="070e4-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="070e4-133">使用命令 shell 中針對 HTTPS 設定的 ASP.NET Core 來執行容器映射：</span><span class="sxs-lookup"><span data-stu-id="070e4-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="070e4-134">使用 [PowerShell](/powershell/scripting/overview)時，請將取代 `%USERPROFILE%` 為 `$env:USERPROFILE` 。</span><span class="sxs-lookup"><span data-stu-id="070e4-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="070e4-135">密碼必須符合憑證所用的密碼。</span><span class="sxs-lookup"><span data-stu-id="070e4-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="070e4-136">注意：此案例中的憑證必須是檔案 `.pfx` 。</span><span class="sxs-lookup"><span data-stu-id="070e4-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="070e4-137">`.crt` `.key` 範例容器不支援利用或不含密碼的檔案。</span><span class="sxs-lookup"><span data-stu-id="070e4-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="070e4-138">例如，在指定檔案時 `.crt` ，容器可能會傳回錯誤訊息，例如「伺服器模式 SSL 必須使用具有相關私密金鑰的憑證」。</span><span class="sxs-lookup"><span data-stu-id="070e4-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="070e4-139">使用 [WSL](/windows/wsl/about)時，請驗證掛接路徑以確保憑證正確載入。</span><span class="sxs-lookup"><span data-stu-id="070e4-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="070e4-140">macOS 或 Linux</span><span class="sxs-lookup"><span data-stu-id="070e4-140">macOS or Linux</span></span>

<span data-ttu-id="070e4-141">產生憑證並設定本機電腦：</span><span class="sxs-lookup"><span data-stu-id="070e4-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="070e4-142">`dotnet dev-certs https --trust` 只有在 macOS 和 Windows 上才支援。</span><span class="sxs-lookup"><span data-stu-id="070e4-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="070e4-143">您必須以散發套件支援的方式來信任 Linux 上的憑證。</span><span class="sxs-lookup"><span data-stu-id="070e4-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="070e4-144">您很可能需要信任您瀏覽器中的憑證。</span><span class="sxs-lookup"><span data-stu-id="070e4-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="070e4-145">在上述命令中，以 `{ password here }` 密碼取代。</span><span class="sxs-lookup"><span data-stu-id="070e4-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="070e4-146">使用針對 HTTPS 設定的 ASP.NET Core 來執行容器映射：</span><span class="sxs-lookup"><span data-stu-id="070e4-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="070e4-147">密碼必須符合憑證所用的密碼。</span><span class="sxs-lookup"><span data-stu-id="070e4-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="070e4-148">使用 Windows 容器的 windows</span><span class="sxs-lookup"><span data-stu-id="070e4-148">Windows using Windows containers</span></span>

<span data-ttu-id="070e4-149">產生憑證並設定本機電腦：</span><span class="sxs-lookup"><span data-stu-id="070e4-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="070e4-150">在上述命令中，以 `{ password here }` 密碼取代。</span><span class="sxs-lookup"><span data-stu-id="070e4-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="070e4-151">使用 [PowerShell](/powershell/scripting/overview)時，請將取代 `%USERPROFILE%` 為 `$env:USERPROFILE` 。</span><span class="sxs-lookup"><span data-stu-id="070e4-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="070e4-152">使用針對 HTTPS 設定的 ASP.NET Core 來執行容器映射：</span><span class="sxs-lookup"><span data-stu-id="070e4-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="070e4-153">密碼必須符合憑證所用的密碼。</span><span class="sxs-lookup"><span data-stu-id="070e4-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="070e4-154">使用 [PowerShell](/powershell/scripting/overview)時，請將取代 `%USERPROFILE%` 為 `$env:USERPROFILE` 。</span><span class="sxs-lookup"><span data-stu-id="070e4-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
