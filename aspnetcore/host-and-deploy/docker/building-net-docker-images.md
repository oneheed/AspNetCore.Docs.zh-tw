---
title: ASP.NET Core 的 Docker 映像
author: rick-anderson
description: 瞭解如何使用 Docker 登錄中已發佈的 ASP.NET Core Docker 映射。 提取並建立您自己的映射。
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: b29ce03366e5c0e815de0874f5b96efb9ba5326c
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585951"
---
# <a name="docker-images-for-aspnet-core"></a>ASP.NET Core 的 Docker 映像

本教學課程示範如何在 Docker 容器中執行 ASP.NET Core 應用程式。

在本教學課程中，您：
> [!div class="checklist"]
> * 瞭解 ASP.NET Core Docker 映射
> * 下載 ASP.NET Core 範例應用程式
> * 在本機執行範例應用程式
> * 在 Linux 容器中執行範例應用程式
> * 在 Windows 容器中執行範例應用程式
> * 手動建置並部署

## <a name="aspnet-core-docker-images"></a>ASP.NET Core Docker 映像

針對本教學課程，您可以下載 ASP.NET Core 範例應用程式，並在 Docker 容器中執行它。 該範例可與 Linux 或 Windows 容器搭配使用。

範例 Dockerfile 會使用 [Docker 多階段建置功能](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) \(英文\)，在不同容器中建置並執行。 建置和執行容器會從 Docker Hub 中 Microsoft 所提供的映像來建置：

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  範例會使用此映像來建置應用程式。 映射包含 .NET SDK，其中包含 (CLI) 的命令列工具。 此映像會最佳化來進行本機開發、偵錯和單元測試。 針對開發和編譯所安裝的工具會讓映射相對較大。

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  範例會使用此映像來建置應用程式。 此映像包含隨附命令列工具 (CLI) 的 .NET Core SDK。 此映像會最佳化來進行本機開發、偵錯和單元測試。 針對開發和編譯所安裝的工具會讓映射相對較大。

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   範例會使用此映像來執行應用程式。 此映像包含 ASP.NET Core 執行階段和程式庫，並會進行最佳化，以在生產環境中執行應用程式。 專為部署和應用程式啟動速度而設計的映像相對較小，因此，已將從 Docker 登錄到 Docker 主機的網路效能最佳化。 只會將執行應用程式所需的程式庫和內容複製到容器中。 內容已準備好執行，可用最短的時間從 `docker run` 到應用程式啟動。 在 Docker 模型中，不需要動態程式碼編譯。

## <a name="prerequisites"></a>必要條件

::: moniker range=">= aspnetcore-5.0"

* [.NET SDK 5.0](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [.NET Core SDK 3.1](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* Docker 用戶端 18.03 或更新版本

  * Linux 散發套件
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/) \(英文\)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>下載範例應用程式

* 複製 [.Net Docker 存放庫](https://github.com/dotnet/dotnet-docker)以下載範例： 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>在本機執行應用程式

* 瀏覽至 *dotnet-docker/samples/aspnetapp/aspnetapp* 中的專案資料夾。

* 執行下列命令，在本機建置並執行應用程式：

  ```dotnetcli
  dotnet run
  ```

* 在瀏覽器中移至 `http://localhost:5000` 以測試應用程式。

* 在命令提示字元中按 Ctrl + C 以停止應用程式。

## <a name="run-in-a-linux-container"></a>在 Linux 容器中執行

* 在 Docker 用戶端中， [切換至 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。

* 瀏覽至 *dotnet-docker/samples/aspnetapp* 中的 Dockerfile 資料夾。

* 執行下列命令，在 Docker 中建置並執行範例：

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  `build` 命令引數：
  * 將映像命名為 aspnetapp。
  * 在目前的資料夾 (結束期間) 中尋找 Dockerfile。

  run 命令引數：
  * 配置虛擬 TTY，即使未連接，還是要使其保持開啟 (與 `--interactive --tty` 的效果相同)。
  * 結束時自動移除容器。
  * 將本機電腦上的連接埠 5000 對應至容器中的連接埠 80。
  * 將容器命名為 aspnetcore_sample。
  * 指定 aspnetapp 映像。

* 在瀏覽器中移至 `http://localhost:5000` 以測試應用程式。

## <a name="run-in-a-windows-container"></a>在 Windows 容器中執行

* 在 Docker 用戶端中， [切換至 Windows 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。

瀏覽至 `dotnet-docker/samples/aspnetapp` 中的 docker 檔案資料夾。

* 執行下列命令，在 Docker 中建置並執行範例：

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* 針對 Windows 容器，您需要容器的 IP 位址 (瀏覽至 `http://localhost:5000` 將無法運作)：
  * 開啟另一個命令提示字元。
  * 執行 `docker ps` 以查看執行中的容器。 確認其中有 "aspnetcore_sample" 容器。
  * 執行 `docker exec aspnetcore_sample ipconfig` 來顯示容器的 IP 位址。 此命令的輸出看起來就像下列範例：

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* 複製容器 IPv4 位址 (例如 172.29.245.43) 並貼入瀏覽器網址列，以測試應用程式。

## <a name="build-and-deploy-manually"></a>手動建置並部署

在某些情況下，您可能會想要將應用程式部署至容器，方法是複製其在執行時間所需的資產。 本節示範如何手動部署。

* 瀏覽至 *dotnet-docker/samples/aspnetapp/aspnetapp* 中的專案資料夾。

* 執行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令：

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  命令引數：
  * 在 [發行] 模式中建立應用程式 (預設值為 [) 的 debug 模式]。
  * 在 *已發佈* 的資料夾中建立資產。

* 執行應用程式。

  * Windows：

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux：

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* 瀏覽至 `http://localhost:5000` 以查看首頁。

若要在 Docker 容器內使用手動發佈的應用程式，請建立新的 *Dockerfile* ，並使用 `docker build .` 命令來建立容器。

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerfile

以下是您稍早執行的命令所使用的 *Dockerfile* `docker build` 。  它會以您在本節所做的相同方式，使用 `dotnet publish` 進行建置及部署。  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

在上述 *Dockerfile* 中，檔案 `*.csproj` 會複製並還原為不同的 *層* 級。 當 `docker build` 命令建立映射時，它會使用內建的快取。 如果 `*.csproj` 自 `docker build` 命令上次執行之後，檔案尚未變更，則 `dotnet restore` 不需要再次執行命令。 相反地，會重複使用對應層的內建快取 `dotnet restore` 。 如需詳細資訊，請參閱 [撰寫 dockerfile 的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)。

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerfile

以下是您稍早執行的命令所使用的 *Dockerfile* `docker build` 。  它會以您在本節所做的相同方式，使用 `dotnet publish` 進行建置及部署。  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

如上述 Dockerfile 所述，檔案 `*.csproj` 會複製並還原為不同的 *層* 級。 當 `docker build` 命令建立映射時，它會使用內建的快取。 如果 `*.csproj` 自 `docker build` 命令上次執行之後，檔案尚未變更，則 `dotnet restore` 不需要再次執行命令。 相反地，會重複使用對應層的內建快取 `dotnet restore` 。 如需詳細資訊，請參閱 [撰寫 dockerfile 的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Dockerfile

以下是您稍早執行的命令所使用的 *Dockerfile* `docker build` 。 它會以您在本節所做的相同方式，使用 `dotnet publish` 進行建置及部署。  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a>其他資源

* [Docker build 命令列](https://docs.docker.com/engine/reference/commandline/build) \(英文\)
* [Docker run 命令列](https://docs.docker.com/engine/reference/commandline/run) \(英文\)
* [ASP.NET Core Docker 範例](https://github.com/dotnet/dotnet-docker) \(英文\) (本教學課程中所使用的範例。)
* [設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器](../proxy-load-balancer.md)
* [使用 Visual Studio Docker 工具](./visual-studio-tools-for-docker.md)
* [使用 Visual Studio Code 偵錯](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [使用 Docker 和小型容器的 GC](xref:performance/memory#sc)

## <a name="next-steps"></a>下一步

包含範例應用程式的 Git 存放庫也會包含文件。 如需存放庫中可用資源的概觀，請參閱[讀我檔案](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md) \(英文\)。 特別是了解如何實作 HTTPS：

> [!div class="nextstepaction"]
> [透過 HTTPS 使用 Docker 開發 ASP.NET Core 應用程式](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)
