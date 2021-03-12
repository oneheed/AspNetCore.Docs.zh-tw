---
title: 使用 Docker over HTTPS 裝載 ASP.NET 核心映射
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
ms.openlocfilehash: 3af2aff477604eb19ac211753f848d08d0c67c72
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588636"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a>使用 Docker over HTTPS 裝載 ASP.NET 核心映射

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 預設會使用 [HTTPS](./enforcing-ssl.md)。 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 依賴 [憑證](https://en.wikipedia.org/wiki/Public_key_certificate) 來進行信任、身分識別和加密。

本檔說明如何使用 HTTPS 執行預先建立的容器映射。

請參閱 [使用 Docker OVER HTTPS 開發 ASP.NET Core 應用程式](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md) ，以進行開發案例。

此範例需要 docker [17.06](https://docs.docker.com/release-notes/docker-ce) 或更新版本的 [docker 用戶端](https://www.docker.com/products/docker)。

## <a name="prerequisites"></a>必要條件

本檔中的部分指示需要 [.Net Core 2.2 SDK](https://dotnet.microsoft.com/download) 或更新版本。

## <a name="certificates"></a>憑證

網域的[生產環境裝載](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/)需要證書[頒發機構](https://wikipedia.org/wiki/Certificate_authority)單位的憑證。 [Let's Encrypt](https://letsencrypt.org/) 是提供免費憑證的憑證授權單位單位。

本檔使用 [自我簽署的開發憑證](https://en.wikipedia.org/wiki/Self-signed_certificate) 來裝載預先建立的映射 `localhost` 。 這些指示與使用生產憑證類似。

使用 [dotnet dev](/dotnet/core/additional-tools/self-signed-certificates-guide) 憑證來建立自我簽署憑證，以進行開發和測試。

針對生產憑證：

* `dotnet dev-certs`這是不必要的工具。
* 憑證不需要儲存在指示中所使用的位置。 任何位置都應該可以運作，但不建議將憑證儲存在您的網站目錄中。

下列章節中包含的指示會使用 Docker 的命令列選項，將憑證掛接到容器中 `-v` 。 您可以使用 Dockerfile 中的命令，將憑證新增至容器映射中 `COPY` ，但不建議這麼做。  基於下列原因，不建議將憑證複製到映射：

* 使用相同的映射測試開發人員憑證會很困難。
* 使用相同的映射來裝載生產憑證會很困難。
* 憑證洩漏有很大的風險。

## <a name="running-pre-built-container-images-with-https"></a>使用 HTTPS 執行預先建立的容器映射

針對您的作業系統設定，請使用下列指示。

### <a name="windows-using-linux-containers"></a>使用 Linux 容器的 Windows

產生憑證並設定本機電腦：

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

在上述命令中，以 `{ password here }` 密碼取代。

使用命令 shell 中為 HTTPS 設定的 ASP.NET Core 執行容器映射：

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

使用 [PowerShell](/powershell/scripting/overview)時，請將取代 `%USERPROFILE%` 為 `$env:USERPROFILE` 。

密碼必須符合憑證所用的密碼。


注意：此案例中的憑證必須是檔案 `.pfx` 。  `.crt` `.key` 範例容器不支援利用或不含密碼的檔案。  例如，在指定檔案時 `.crt` ，容器可能會傳回錯誤訊息，例如「伺服器模式 SSL 必須使用具有相關私密金鑰的憑證」。 使用 [WSL](/windows/wsl/about)時，請驗證掛接路徑以確保憑證正確載入。

### <a name="macos-or-linux"></a>macOS 或 Linux

產生憑證並設定本機電腦：

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust` 只有在 macOS 和 Windows 上才支援。 您必須以散發套件支援的方式來信任 Linux 上的憑證。 您很可能需要信任您瀏覽器中的憑證。

在上述命令中，以 `{ password here }` 密碼取代。

使用針對 HTTPS 設定的 ASP.NET Core 執行容器映射：

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

密碼必須符合憑證所用的密碼。

### <a name="windows-using-windows-containers"></a>使用 Windows 容器的 windows

產生憑證並設定本機電腦：

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

在上述命令中，以 `{ password here }` 密碼取代。 使用 [PowerShell](/powershell/scripting/overview)時，請將取代 `%USERPROFILE%` 為 `$env:USERPROFILE` 。

使用針對 HTTPS 設定的 ASP.NET Core 執行容器映射：

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

密碼必須符合憑證所用的密碼。 使用 [PowerShell](/powershell/scripting/overview)時，請將取代 `%USERPROFILE%` 為 `$env:USERPROFILE` 。
