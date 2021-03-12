---
title: Visual Studio 容器工具搭配 ASP.NET Core
author: spboyer
description: 了解如何使用適用於 Windows 的 Visual Studio 工具和 Docker 對 ASP.NET Core 應用程式進行容器化。
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
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
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: c11261fef84234a9caaf872ae9871932d02617fa
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102585990"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>Visual Studio 容器工具搭配 ASP.NET Core

Visual Studio 2017 及更新版本支援建置、偵錯和執行以 .NET Core 為目標的容器化 ASP.NET Core 應用程式。 同時支援 Windows 和 Linux 容器。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

* [Docker for Windows](https://docs.docker.com/docker-for-windows/install/)
* 已安裝 **.NET Core 跨平台開發** 工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="installation-and-setup"></a>安裝與設定

若要進行 Docker 安裝，請先檢閱 [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install) 中的資訊。 接下來，安裝 [適用於 Windows 的 Docker](https://docs.docker.com/docker-for-windows/install/)。

Docker for Windows 中的 **[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** (共用磁碟機) 必須設定為支援磁碟區對應和偵錯。 以滑鼠右鍵按一下系統匣的 Docker 圖示，並選取 [ **設定**]，然後選取 [ **共用磁片磁碟機**]。 選取 Docker 儲存檔案的磁碟機。 按一下 [套用]。

![為容器選取共用本機 C 磁碟機的對話方塊](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> 未設定 [共用磁碟機] 時，Visual Studio 2017 15.6 版和更新版本會顯示提示。

## <a name="add-a-project-to-a-docker-container"></a>將專案新增至 Docker 容器

若要容器化 ASP.NET Core 專案，該專案必須以 .NET Core 為目標。 同時支援 Linux 和 Windows 容器。

將 Docker 支援新增至專案時，請選擇 Windows 或 Linux 容器。 Docker 主機必須執行相同的容器類型。 若要變更執行中 Docker 執行個體中的容器類型，請以滑鼠右鍵按一下系統匣的 Docker 圖示，然後選擇 [Switch to Windows containers...] (切換至 Windows 容器...) 或 [Switch to Linux containers] (切換至 Linux 容器...)。

### <a name="new-app"></a>新增應用程式

使用 **ASP.NET Core Web 應用程式** 專案範本來建立新的應用程式時，請選取 [啟用 Docker 支援] 核取方塊：

![啟用 Docker 支援核取方塊](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

如果目標架構是 .NET Core，則 [OS] 下拉式清單會允許選取容器類型。

### <a name="existing-app"></a>現有的應用程式

針對以 .NET Core 為目標的 ASP.NET Core 專案，有兩個選項可透過工具來新增 Docker 支援。 在 Visual Studio 中開啟專案，然後選擇下列其中一個選項：

* 選取 [專案] 功能表的 [Docker 支援]。
* 以滑鼠右鍵按一下 **方案總管** 中的專案，然後選取 [新增] > [Docker 支援]。

Visual Studio 容器工具不支援將 Docker 新增至以 .NET Framework 為目標的現有 ASP.NET Core 專案。

## <a name="dockerfile-overview"></a>Dockerfile 概觀

*Dockerfile*，是用於建立最終 Docker 映像的配方，會新增至專案根目錄。 請參閱 [Dockerfile 參考](https://docs.docker.com/engine/reference/builder/) ，以瞭解其內的命令。 此特定 *Dockerfile* 使用 [多階段建置](https://docs.docker.com/engine/userguide/eng-image/multistage-build/)，包含四個不同的具名建置階段：

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

上述的 *Dockerfile* 以 [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) 映像為基礎。 此基底映像包含 ASP.NET Core 執行階段與 NuGet 套件。 套件會進行 Just-in-Time (JIT) 編譯，以改善啟動效能。

核取新專案對話方塊的 [設定 HTTPS] 核取方塊時，*Dockerfile* 會提供兩個連接埠。 其中一個連接埠用於 HTTP 流量，另一個連接埠則用於 HTTPS。 如果未選取該核取方塊，則會為 HTTP 流量提供單一連接埠 (80)。

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

上述的 *Dockerfile* 以 [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) 映像為基礎。 此基底映像包含 ASP.NET Core NuGet 套件，其進行 Just-in-Time (JIT) 編譯，以改善啟動效能。

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>為應用程式新增容器協調器支援

Visual Studio 2017 版本 15.7 或更早的版本支援將 [Docker Compose](https://docs.docker.com/compose/overview/) 作為唯一的容器協調流程解決方案。 Docker 撰寫成品是 **透過新增**  >  **docker 支援** 新增的。

Visual Studio 2017 版本 15.8 或更新版本只有在指示進行時，才會新增協調流程解決方案。 以滑鼠右鍵按一下 **方案總管** 中的專案，然後選取 [新增] > [容器協調器支援]。 可用的選項如下： 

* [Docker 撰寫](#docker-compose)
* [Service Fabric](#service-fabric)
* [Kubernetes/Helm ](https://helm.sh/)

### <a name="docker-compose"></a>Docker Compose

Visual Studio 容器工具會將 *docker-compose* 專案新增至包含下列檔案的解決方案：

* *docker-compose.dcproj*：代表專案的檔案。 包含 `<DockerTargetOS>` 項目，指定要使用的 OS。
* *>.dockerignore*：列出產生組建內容時要排除的檔案和目錄模式。
* *>docker-compose.yml. yml*：基底 [docker 撰寫](https://docs.docker.com/compose/overview/) 檔，用來定義與和一起建立和執行的映射集合 `docker-compose build` `docker-compose run` 。
* *>docker-compose.yml。 yml*：由 docker 撰寫所讀取的選擇性檔案，以及服務的設定覆寫。 Visual Studio 會執行 `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` 來合併這些檔案。

*docker-compose.yml* 檔案會參考執行專案時所建立映像的名稱：

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

在上述範例中，`image: hellodockertools` 會在以 **偵錯** 模式執行應用程式時產生 `hellodockertools:dev` 映像。 以 **發行** 模式執行應用程式時，會產生 `hellodockertools:latest` 映像。

如果映像會推送至登錄，會在映像名稱前加上 [Docker Hub](https://hub.docker.com/) 使用者名稱 (例如，`dockerhubusername/hellodockertools`)。 或者，根據設定將映像名稱變更為包含私人登錄 URL (例如，`privateregistry.domain.com/hellodockertools`)。

如果您需要基於組建設定的不同行為 (例如偵錯或發行)，請新增特定於設定的 *docker-compose* 檔案。 應根據組建設定來命名檔案 (例如 *docker-compose.vs.debug.yml* 和 *docker-compose.vs.release.yml*) 並放在與 *docker-compose-override.yml* 檔案相同的位置。 

使用特定於設定的覆寫檔案，可以為偵錯和發行組建設定指定不同的組態設定 (例如環境變數或進入點)。

Docker 專案必須是啟始專案，才能讓 Docker 撰寫用來顯示在 Visual Studio 中執行的選項。

### <a name="service-fabric"></a>Service Fabric

除了基礎[必要條件](#prerequisites)之外，[Service Fabric](/azure/service-fabric/) 協調流程解決方案還需要下列必要條件：

* [Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) 版本 2.6 或更新版本
* Visual Studio 的 **Azure 開發** 工作負載

Service Fabric 不支援在 Windows 上的本機開發叢集中執行 Linux 容器。 如果專案已在使用 Linux 容器，Visual Studio 會提示您切換至 Windows 容器。

Visual Studio 容器工具會執行下列工作：

* 將 *&lt; project_name &gt; Application* **Service Fabric 應用程式** 專案新增至方案。
* 將 *Dockerfile* 與 *.dockerignore* 檔案，新增至 ASP.NET Core 專案。 如果 ASP.NET Core 專案中已存在 *Dockerfile*，則會重新命名為 *Dockerfile.original*。 會建立類似如下的新 *Dockerfile*：

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* 會將 `<IsServiceFabricServiceProject>` 項目新增至 ASP.NET Core 專案的 *.csproj* 檔案：

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* 會將 *PackageRoot* 資料夾新增至 ASP.NET Core 專案。 該資料夾包含服務資訊清單與新服務的設定。

如需詳細資訊，請參閱[將 Windows 容器中的 .NET 應用程式部署至 Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)。

## <a name="debug"></a>偵錯

從工具列的偵錯下拉式清單中選取 [Docker]，然後開始對應用程式進行偵錯。 [輸出] 視窗的 **Docker** 檢視會顯示下列將採取的動作：

::: moniker range=">= aspnetcore-2.1"

* 取得 *microsoft/dotnet* 執行階段映像的 *2.1-aspnetcore-runtime* 標籤 (若尚未存在於快取中)。 映像會安裝 ASP.NET Core 與 .NET Core 執行階段，以及相關聯的程式庫。 在生產環境中最好是執行 ASP.NET Core 應用程式。
* 在容器內，`ASPNETCORE_ENVIRONMENT` 環境變數設定為 `Development`。
* 會提供兩個動態指派的連接埠：一個用於 HTTP，另一個用於 HTTPS。 可使用 `docker ps` 命令，查詢指派到 localhost 的連接埠。
* 應用程式會複製至容器。
* 預設瀏覽器會在偵錯工具使用動態指派的連接埠附加至容器的情況下啟動。

產生的應用程式 Docker 映像，會標記為 *dev*。 此映像以 *microsoft/dotnet* 基底映像的 *2.1-aspnetcore-runtime* 標籤為基礎。 在 [套件管理員主控台] (PMC) 視窗中，執行 `docker images` 命令。 這會顯示電腦上的映像：

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* 取得 *microsoft/aspnetcore* 執行階段映像 (若尚未存在於快取中)。
* 在容器內，`ASPNETCORE_ENVIRONMENT` 環境變數設定為 `Development`。
* 連接埠 80 會公開並對應至本機主機的動態指派連接埠。 連接埠是由 Docker 主機所決定，並且可以使用 `docker ps` 命令進行查詢。
* 應用程式會複製至容器。
* 預設瀏覽器會在偵錯工具使用動態指派的連接埠附加至容器的情況下啟動。

產生的應用程式 Docker 映像，會標記為 *dev*。 映像以 *microsoft/aspnetcore* 基底映像為基礎。 在 [套件管理員主控台] (PMC) 視窗中，執行 `docker images` 命令。 這會顯示電腦上的映像：

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> 因為 **偵錯** 組態會使用磁碟區掛接來提供重複的體驗，所以 *dev* 映像不會有應用程式內容。 若要推送映射，請使用 **發行** 設定。

在 PMC 中執行 `docker ps` 命令。 請注意是使用容器來執行應用程式：

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>編輯後繼續

靜態檔案和 Razor views 的變更會自動更新，而不需要編譯步驟。 進行變更並儲存，然後重新整理瀏覽器來檢視更新。

程式碼檔案的修改需要編譯以及重新啟動容器內的 Kestrel。 完成變更之後，請使用 `CTRL+F5` 來執行程序，並啟動容器內的應用程式。 Docker 容器不會進行重建或停止。 在 PMC 中執行 `docker ps` 命令。 請注意，原始容器在 10 分鐘前仍在執行：

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>發行 Docker 映像

當應用程式的開發和偵錯循環完畢之後，Visual Studio 容器工具就會協助建立應用程式的實際執行映像。 將組態下拉式清單變更為 [發行] 並建置應用程式。 工具會從 Docker Hub (若尚未在快取中) 取得編譯/發行映像。 映像會使用 *最新* 的標籤產生，其可推送至私人登錄或 Docker Hub。

在 PMC 中執行 `docker images` 命令，可查看映像清單。 會顯示類似下列的輸出：

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

自 .NET Core 2.1 起，上述輸出中所列出的 `microsoft/aspnetcore-build` 和 `microsoft/aspnetcore` 映像，會取代為 `microsoft/dotnet` 映像。 如需詳細資訊，請參閱 [Docker 存放庫移轉公告](https://github.com/aspnet/Announcements/issues/298)。

::: moniker-end

> [!NOTE]
> 此 `docker images` 命令會傳回具有存放庫名稱和標記的中繼映射（ *\<none>* (未列于上述) ）。 這些未命名映像是由 [多階段建置](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile* 所產生。 它們可以改善最終映像的建置效率 &mdash; 發生變更時只會重建必要層。 當不再需要中繼映像時，請使用 [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) \(英文\) 命令予以刪除。

相較於 *dev* 映像，生產或發行映像的大小可能需要更小。 基於磁碟區對應，偵錯工具和應用程式是從本機電腦執行，而不是在容器內執行。 「最新」映像已封裝在主機上執行應用程式所需的應用程式碼。 因此，差異是應用程式碼的大小。

## <a name="additional-resources"></a>其他資源

* [使用 Visual Studio 進行容器開發](/visualstudio/containers)
* [Azure Service Fabric：準備您的開發環境](/azure/service-fabric/service-fabric-get-started)
* [將 Windows 容器中的 .NET 應用程式部署至 Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [對使用 Docker 進行的 Visual Studio 開發進行疑難排解](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [GitHub 存放庫上的 Visual Studio 容器工具](https://github.com/Microsoft/DockerTools)
* [使用 Docker 和小型容器的 GC](xref:performance/memory#sc)
