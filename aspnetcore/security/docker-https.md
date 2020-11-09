---
title: 使用 Docker over HTTPS 裝載 ASP.NET Core 映射
author: rick-anderson
description: 瞭解如何使用 Docker 透過 HTTPS 裝載 ASP.NET Core 映射
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/docker-https
ms.openlocfilehash: 63d6e220c0f28e552207039c1649041bfdf4a0d4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059672"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="f5cd1-103">使用 Docker over HTTPS 裝載 ASP.NET Core 映射</span><span class="sxs-lookup"><span data-stu-id="f5cd1-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="f5cd1-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f5cd1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f5cd1-105">ASP.NET Core 預設會使用 [HTTPS](./enforcing-ssl.md)。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="f5cd1-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) 依賴 [憑證](https://en.wikipedia.org/wiki/Public_key_certificate) 來進行信任、身分識別和加密。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="f5cd1-107">本檔說明如何使用 HTTPS 執行預先建立的容器映射。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="f5cd1-108">請參閱 [使用 Docker OVER HTTPS 開發](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) 適用于開發案例的 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="f5cd1-109">此範例需要 docker [17.06](https://docs.docker.com/release-notes/docker-ce) 或更新版本的 [docker 用戶端](https://www.docker.com/products/docker)。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f5cd1-110">必要條件</span><span class="sxs-lookup"><span data-stu-id="f5cd1-110">Prerequisites</span></span>

<span data-ttu-id="f5cd1-111">本檔中的部分指示需要 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download) 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="f5cd1-112">憑證</span><span class="sxs-lookup"><span data-stu-id="f5cd1-112">Certificates</span></span>

<span data-ttu-id="f5cd1-113">網域的[生產環境裝載](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)需要證書[頒發機構](https://wikipedia.org/wiki/Certificate_authority)單位的憑證。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="f5cd1-114">[Let's Encrypt](https://letsencrypt.org/) 是提供免費憑證的憑證授權單位單位。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="f5cd1-115">本檔使用 [自我簽署的開發憑證](https://en.wikipedia.org/wiki/Self-signed_certificate) 來裝載預先建立的映射 `localhost` 。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="f5cd1-116">這些指示與使用生產憑證類似。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="f5cd1-117">針對生產憑證：</span><span class="sxs-lookup"><span data-stu-id="f5cd1-117">For production certs:</span></span>

* <span data-ttu-id="f5cd1-118">`dotnet dev-certs`這是不必要的工具。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-118">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="f5cd1-119">憑證不需要儲存在指示中所使用的位置。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-119">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="f5cd1-120">任何位置都應該可以運作，但不建議將憑證儲存在您的網站目錄中。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-120">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="f5cd1-121">下列章節中包含的指示會使用 Docker 的命令列選項，將憑證掛接到容器中 `-v` 。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-121">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="f5cd1-122">您可以使用 Dockerfile 中的命令，將憑證新增至容器映射中 `COPY` ，但不建議這麼做。 *Dockerfile*</span><span class="sxs-lookup"><span data-stu-id="f5cd1-122">You could add certificates into container images with a `COPY` command in a *Dockerfile* , but it's not recommended.</span></span> <span data-ttu-id="f5cd1-123">基於下列原因，不建議將憑證複製到映射：</span><span class="sxs-lookup"><span data-stu-id="f5cd1-123">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="f5cd1-124">使用相同的映射測試開發人員憑證會很困難。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-124">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="f5cd1-125">使用相同的映射來裝載生產憑證會很困難。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-125">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="f5cd1-126">憑證洩漏有很大的風險。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-126">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="f5cd1-127">使用 HTTPS 執行預先建立的容器映射</span><span class="sxs-lookup"><span data-stu-id="f5cd1-127">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="f5cd1-128">針對您的作業系統設定，請使用下列指示。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-128">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="f5cd1-129">使用 Linux 容器的 Windows</span><span class="sxs-lookup"><span data-stu-id="f5cd1-129">Windows using Linux containers</span></span>

<span data-ttu-id="f5cd1-130">產生憑證並設定本機電腦：</span><span class="sxs-lookup"><span data-stu-id="f5cd1-130">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="f5cd1-131">在上述命令中，以 `{ password here }` 密碼取代。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-131">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="f5cd1-132">使用命令 shell 中針對 HTTPS 設定的 ASP.NET Core 來執行容器映射：</span><span class="sxs-lookup"><span data-stu-id="f5cd1-132">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="f5cd1-133">使用 [PowerShell](/powershell/scripting/overview)時，請將取代 `%USERPROFILE%` 為 `$env:USERPROFILE` 。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-133">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="f5cd1-134">密碼必須符合憑證所用的密碼。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-134">The password must match the password used for the certificate.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="f5cd1-135">macOS 或 Linux</span><span class="sxs-lookup"><span data-stu-id="f5cd1-135">macOS or Linux</span></span>

<span data-ttu-id="f5cd1-136">產生憑證並設定本機電腦：</span><span class="sxs-lookup"><span data-stu-id="f5cd1-136">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="f5cd1-137">`dotnet dev-certs https --trust` 只有在 macOS 和 Windows 上才支援。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-137">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="f5cd1-138">您必須以散發套件支援的方式來信任 Linux 上的憑證。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-138">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="f5cd1-139">您很可能需要信任您瀏覽器中的憑證。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-139">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="f5cd1-140">在上述命令中，以 `{ password here }` 密碼取代。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-140">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="f5cd1-141">使用針對 HTTPS 設定的 ASP.NET Core 來執行容器映射：</span><span class="sxs-lookup"><span data-stu-id="f5cd1-141">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="f5cd1-142">密碼必須符合憑證所用的密碼。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-142">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="f5cd1-143">使用 Windows 容器的 windows</span><span class="sxs-lookup"><span data-stu-id="f5cd1-143">Windows using Windows containers</span></span>

<span data-ttu-id="f5cd1-144">產生憑證並設定本機電腦：</span><span class="sxs-lookup"><span data-stu-id="f5cd1-144">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="f5cd1-145">在上述命令中，以 `{ password here }` 密碼取代。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-145">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="f5cd1-146">使用 [PowerShell](/powershell/scripting/overview)時，請將取代 `%USERPROFILE%` 為 `$env:USERPROFILE` 。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-146">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="f5cd1-147">使用針對 HTTPS 設定的 ASP.NET Core 來執行容器映射：</span><span class="sxs-lookup"><span data-stu-id="f5cd1-147">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="f5cd1-148">密碼必須符合憑證所用的密碼。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-148">The password must match the password used for the certificate.</span></span> <span data-ttu-id="f5cd1-149">使用 [PowerShell](/powershell/scripting/overview)時，請將取代 `%USERPROFILE%` 為 `$env:USERPROFILE` 。</span><span class="sxs-lookup"><span data-stu-id="f5cd1-149">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>