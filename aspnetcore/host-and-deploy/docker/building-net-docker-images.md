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
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="79083-104">ASP.NET Core 的 Docker 映像</span><span class="sxs-lookup"><span data-stu-id="79083-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="79083-105">本教學課程示範如何在 Docker 容器中執行 ASP.NET Core 應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="79083-106">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="79083-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="79083-107">瞭解 ASP.NET Core Docker 映射</span><span class="sxs-lookup"><span data-stu-id="79083-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="79083-108">下載 ASP.NET Core 範例應用程式</span><span class="sxs-lookup"><span data-stu-id="79083-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="79083-109">在本機執行範例應用程式</span><span class="sxs-lookup"><span data-stu-id="79083-109">Run the sample app locally</span></span>
> * <span data-ttu-id="79083-110">在 Linux 容器中執行範例應用程式</span><span class="sxs-lookup"><span data-stu-id="79083-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="79083-111">在 Windows 容器中執行範例應用程式</span><span class="sxs-lookup"><span data-stu-id="79083-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="79083-112">手動建置並部署</span><span class="sxs-lookup"><span data-stu-id="79083-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="79083-113">ASP.NET Core Docker 映像</span><span class="sxs-lookup"><span data-stu-id="79083-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="79083-114">針對本教學課程，您可以下載 ASP.NET Core 範例應用程式，並在 Docker 容器中執行它。</span><span class="sxs-lookup"><span data-stu-id="79083-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="79083-115">該範例可與 Linux 或 Windows 容器搭配使用。</span><span class="sxs-lookup"><span data-stu-id="79083-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="79083-116">範例 Dockerfile 會使用 [Docker 多階段建置功能](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) \(英文\)，在不同容器中建置並執行。</span><span class="sxs-lookup"><span data-stu-id="79083-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="79083-117">建置和執行容器會從 Docker Hub 中 Microsoft 所提供的映像來建置：</span><span class="sxs-lookup"><span data-stu-id="79083-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="79083-118">範例會使用此映像來建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="79083-119">映射包含 .NET SDK，其中包含 (CLI) 的命令列工具。</span><span class="sxs-lookup"><span data-stu-id="79083-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="79083-120">此映像會最佳化來進行本機開發、偵錯和單元測試。</span><span class="sxs-lookup"><span data-stu-id="79083-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="79083-121">針對開發和編譯所安裝的工具會讓映射相對較大。</span><span class="sxs-lookup"><span data-stu-id="79083-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="79083-122">範例會使用此映像來建置應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="79083-123">此映像包含隨附命令列工具 (CLI) 的 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="79083-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="79083-124">此映像會最佳化來進行本機開發、偵錯和單元測試。</span><span class="sxs-lookup"><span data-stu-id="79083-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="79083-125">針對開發和編譯所安裝的工具會讓映射相對較大。</span><span class="sxs-lookup"><span data-stu-id="79083-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="79083-126">範例會使用此映像來執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="79083-127">此映像包含 ASP.NET Core 執行階段和程式庫，並會進行最佳化，以在生產環境中執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="79083-128">專為部署和應用程式啟動速度而設計的映像相對較小，因此，已將從 Docker 登錄到 Docker 主機的網路效能最佳化。</span><span class="sxs-lookup"><span data-stu-id="79083-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="79083-129">只會將執行應用程式所需的程式庫和內容複製到容器中。</span><span class="sxs-lookup"><span data-stu-id="79083-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="79083-130">內容已準備好執行，可用最短的時間從 `docker run` 到應用程式啟動。</span><span class="sxs-lookup"><span data-stu-id="79083-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="79083-131">在 Docker 模型中，不需要動態程式碼編譯。</span><span class="sxs-lookup"><span data-stu-id="79083-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="79083-132">必要條件</span><span class="sxs-lookup"><span data-stu-id="79083-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="79083-133">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="79083-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="79083-134">.NET Core SDK 3.1</span><span class="sxs-lookup"><span data-stu-id="79083-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="79083-135">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="79083-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="79083-136">Docker 用戶端 18.03 或更新版本</span><span class="sxs-lookup"><span data-stu-id="79083-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="79083-137">Linux 散發套件</span><span class="sxs-lookup"><span data-stu-id="79083-137">Linux distributions</span></span>
    * [<span data-ttu-id="79083-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="79083-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="79083-139">Debian</span><span class="sxs-lookup"><span data-stu-id="79083-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="79083-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="79083-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * <span data-ttu-id="79083-141">[Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="79083-141">[Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)</span></span>
  * [<span data-ttu-id="79083-142">macOS</span><span class="sxs-lookup"><span data-stu-id="79083-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="79083-143">Windows</span><span class="sxs-lookup"><span data-stu-id="79083-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="79083-144">Git</span><span class="sxs-lookup"><span data-stu-id="79083-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="79083-145">下載範例應用程式</span><span class="sxs-lookup"><span data-stu-id="79083-145">Download the sample app</span></span>

* <span data-ttu-id="79083-146">複製 [.Net Docker 存放庫](https://github.com/dotnet/dotnet-docker)以下載範例：</span><span class="sxs-lookup"><span data-stu-id="79083-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="79083-147">在本機執行應用程式</span><span class="sxs-lookup"><span data-stu-id="79083-147">Run the app locally</span></span>

* <span data-ttu-id="79083-148">瀏覽至 *dotnet-docker/samples/aspnetapp/aspnetapp* 中的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="79083-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="79083-149">執行下列命令，在本機建置並執行應用程式：</span><span class="sxs-lookup"><span data-stu-id="79083-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="79083-150">在瀏覽器中移至 `http://localhost:5000` 以測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="79083-151">在命令提示字元中按 Ctrl + C 以停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="79083-152">在 Linux 容器中執行</span><span class="sxs-lookup"><span data-stu-id="79083-152">Run in a Linux container</span></span>

* <span data-ttu-id="79083-153">在 Docker 用戶端中， [切換至 Linux 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。</span><span class="sxs-lookup"><span data-stu-id="79083-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="79083-154">瀏覽至 *dotnet-docker/samples/aspnetapp* 中的 Dockerfile 資料夾。</span><span class="sxs-lookup"><span data-stu-id="79083-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="79083-155">執行下列命令，在 Docker 中建置並執行範例：</span><span class="sxs-lookup"><span data-stu-id="79083-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="79083-156">`build` 命令引數：</span><span class="sxs-lookup"><span data-stu-id="79083-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="79083-157">將映像命名為 aspnetapp。</span><span class="sxs-lookup"><span data-stu-id="79083-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="79083-158">在目前的資料夾 (結束期間) 中尋找 Dockerfile。</span><span class="sxs-lookup"><span data-stu-id="79083-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="79083-159">run 命令引數：</span><span class="sxs-lookup"><span data-stu-id="79083-159">The run command arguments:</span></span>
  * <span data-ttu-id="79083-160">配置虛擬 TTY，即使未連接，還是要使其保持開啟</span><span class="sxs-lookup"><span data-stu-id="79083-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="79083-161">(與 `--interactive --tty` 的效果相同)。</span><span class="sxs-lookup"><span data-stu-id="79083-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="79083-162">結束時自動移除容器。</span><span class="sxs-lookup"><span data-stu-id="79083-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="79083-163">將本機電腦上的連接埠 5000 對應至容器中的連接埠 80。</span><span class="sxs-lookup"><span data-stu-id="79083-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="79083-164">將容器命名為 aspnetcore_sample。</span><span class="sxs-lookup"><span data-stu-id="79083-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="79083-165">指定 aspnetapp 映像。</span><span class="sxs-lookup"><span data-stu-id="79083-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="79083-166">在瀏覽器中移至 `http://localhost:5000` 以測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="79083-167">在 Windows 容器中執行</span><span class="sxs-lookup"><span data-stu-id="79083-167">Run in a Windows container</span></span>

* <span data-ttu-id="79083-168">在 Docker 用戶端中， [切換至 Windows 容器](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)。</span><span class="sxs-lookup"><span data-stu-id="79083-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="79083-169">瀏覽至 `dotnet-docker/samples/aspnetapp` 中的 docker 檔案資料夾。</span><span class="sxs-lookup"><span data-stu-id="79083-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="79083-170">執行下列命令，在 Docker 中建置並執行範例：</span><span class="sxs-lookup"><span data-stu-id="79083-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="79083-171">針對 Windows 容器，您需要容器的 IP 位址 (瀏覽至 `http://localhost:5000` 將無法運作)：</span><span class="sxs-lookup"><span data-stu-id="79083-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="79083-172">開啟另一個命令提示字元。</span><span class="sxs-lookup"><span data-stu-id="79083-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="79083-173">執行 `docker ps` 以查看執行中的容器。</span><span class="sxs-lookup"><span data-stu-id="79083-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="79083-174">確認其中有 "aspnetcore_sample" 容器。</span><span class="sxs-lookup"><span data-stu-id="79083-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="79083-175">執行 `docker exec aspnetcore_sample ipconfig` 來顯示容器的 IP 位址。</span><span class="sxs-lookup"><span data-stu-id="79083-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="79083-176">此命令的輸出看起來就像下列範例：</span><span class="sxs-lookup"><span data-stu-id="79083-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="79083-177">複製容器 IPv4 位址 (例如 172.29.245.43) 並貼入瀏覽器網址列，以測試應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="79083-178">手動建置並部署</span><span class="sxs-lookup"><span data-stu-id="79083-178">Build and deploy manually</span></span>

<span data-ttu-id="79083-179">在某些情況下，您可能會想要將應用程式部署至容器，方法是複製其在執行時間所需的資產。</span><span class="sxs-lookup"><span data-stu-id="79083-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="79083-180">本節示範如何手動部署。</span><span class="sxs-lookup"><span data-stu-id="79083-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="79083-181">瀏覽至 *dotnet-docker/samples/aspnetapp/aspnetapp* 中的專案資料夾。</span><span class="sxs-lookup"><span data-stu-id="79083-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="79083-182">執行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令：</span><span class="sxs-lookup"><span data-stu-id="79083-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="79083-183">命令引數：</span><span class="sxs-lookup"><span data-stu-id="79083-183">The command arguments:</span></span>
  * <span data-ttu-id="79083-184">在 [發行] 模式中建立應用程式 (預設值為 [) 的 debug 模式]。</span><span class="sxs-lookup"><span data-stu-id="79083-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="79083-185">在 *已發佈* 的資料夾中建立資產。</span><span class="sxs-lookup"><span data-stu-id="79083-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="79083-186">執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="79083-186">Run the app.</span></span>

  * <span data-ttu-id="79083-187">Windows：</span><span class="sxs-lookup"><span data-stu-id="79083-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="79083-188">Linux：</span><span class="sxs-lookup"><span data-stu-id="79083-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="79083-189">瀏覽至 `http://localhost:5000` 以查看首頁。</span><span class="sxs-lookup"><span data-stu-id="79083-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="79083-190">若要在 Docker 容器內使用手動發佈的應用程式，請建立新的 *Dockerfile* ，並使用 `docker build .` 命令來建立容器。</span><span class="sxs-lookup"><span data-stu-id="79083-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="79083-191">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="79083-191">The Dockerfile</span></span>

<span data-ttu-id="79083-192">以下是您稍早執行的命令所使用的 *Dockerfile* `docker build` 。</span><span class="sxs-lookup"><span data-stu-id="79083-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="79083-193">它會以您在本節所做的相同方式，使用 `dotnet publish` 進行建置及部署。</span><span class="sxs-lookup"><span data-stu-id="79083-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="79083-194">在上述 *Dockerfile* 中，檔案 `*.csproj` 會複製並還原為不同的 *層* 級。</span><span class="sxs-lookup"><span data-stu-id="79083-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="79083-195">當 `docker build` 命令建立映射時，它會使用內建的快取。</span><span class="sxs-lookup"><span data-stu-id="79083-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="79083-196">如果 `*.csproj` 自 `docker build` 命令上次執行之後，檔案尚未變更，則 `dotnet restore` 不需要再次執行命令。</span><span class="sxs-lookup"><span data-stu-id="79083-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="79083-197">相反地，會重複使用對應層的內建快取 `dotnet restore` 。</span><span class="sxs-lookup"><span data-stu-id="79083-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="79083-198">如需詳細資訊，請參閱 [撰寫 dockerfile 的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)。</span><span class="sxs-lookup"><span data-stu-id="79083-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="79083-199">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="79083-199">The Dockerfile</span></span>

<span data-ttu-id="79083-200">以下是您稍早執行的命令所使用的 *Dockerfile* `docker build` 。</span><span class="sxs-lookup"><span data-stu-id="79083-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="79083-201">它會以您在本節所做的相同方式，使用 `dotnet publish` 進行建置及部署。</span><span class="sxs-lookup"><span data-stu-id="79083-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="79083-202">如上述 Dockerfile 所述，檔案 `*.csproj` 會複製並還原為不同的 *層* 級。</span><span class="sxs-lookup"><span data-stu-id="79083-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="79083-203">當 `docker build` 命令建立映射時，它會使用內建的快取。</span><span class="sxs-lookup"><span data-stu-id="79083-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="79083-204">如果 `*.csproj` 自 `docker build` 命令上次執行之後，檔案尚未變更，則 `dotnet restore` 不需要再次執行命令。</span><span class="sxs-lookup"><span data-stu-id="79083-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="79083-205">相反地，會重複使用對應層的內建快取 `dotnet restore` 。</span><span class="sxs-lookup"><span data-stu-id="79083-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="79083-206">如需詳細資訊，請參閱 [撰寫 dockerfile 的最佳做法](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)。</span><span class="sxs-lookup"><span data-stu-id="79083-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="79083-207">Dockerfile</span><span class="sxs-lookup"><span data-stu-id="79083-207">The Dockerfile</span></span>

<span data-ttu-id="79083-208">以下是您稍早執行的命令所使用的 *Dockerfile* `docker build` 。</span><span class="sxs-lookup"><span data-stu-id="79083-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="79083-209">它會以您在本節所做的相同方式，使用 `dotnet publish` 進行建置及部署。</span><span class="sxs-lookup"><span data-stu-id="79083-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

## <a name="additional-resources"></a><span data-ttu-id="79083-210">其他資源</span><span class="sxs-lookup"><span data-stu-id="79083-210">Additional resources</span></span>

* <span data-ttu-id="79083-211">[Docker build 命令列](https://docs.docker.com/engine/reference/commandline/build) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="79083-211">[Docker build command](https://docs.docker.com/engine/reference/commandline/build)</span></span>
* <span data-ttu-id="79083-212">[Docker run 命令列](https://docs.docker.com/engine/reference/commandline/run) \(英文\)</span><span class="sxs-lookup"><span data-stu-id="79083-212">[Docker run command](https://docs.docker.com/engine/reference/commandline/run)</span></span>
* <span data-ttu-id="79083-213">[ASP.NET Core Docker 範例](https://github.com/dotnet/dotnet-docker) \(英文\) (本教學課程中所使用的範例。)</span><span class="sxs-lookup"><span data-stu-id="79083-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="79083-214">設定 ASP.NET Core 以處理 Proxy 伺服器和負載平衡器</span><span class="sxs-lookup"><span data-stu-id="79083-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="79083-215">使用 Visual Studio Docker 工具</span><span class="sxs-lookup"><span data-stu-id="79083-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="79083-216">使用 Visual Studio Code 偵錯</span><span class="sxs-lookup"><span data-stu-id="79083-216">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="79083-217">使用 Docker 和小型容器的 GC</span><span class="sxs-lookup"><span data-stu-id="79083-217">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="79083-218">下一步</span><span class="sxs-lookup"><span data-stu-id="79083-218">Next steps</span></span>

<span data-ttu-id="79083-219">包含範例應用程式的 Git 存放庫也會包含文件。</span><span class="sxs-lookup"><span data-stu-id="79083-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="79083-220">如需存放庫中可用資源的概觀，請參閱[讀我檔案](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md) \(英文\)。</span><span class="sxs-lookup"><span data-stu-id="79083-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/main/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="79083-221">特別是了解如何實作 HTTPS：</span><span class="sxs-lookup"><span data-stu-id="79083-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="79083-222">透過 HTTPS 使用 Docker 開發 ASP.NET Core 應用程式</span><span class="sxs-lookup"><span data-stu-id="79083-222">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/main/samples/run-aspnetcore-https-development.md)
