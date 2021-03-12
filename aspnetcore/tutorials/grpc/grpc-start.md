---
title: 在 ASP.NET Core 中建立 .NET Core gRPC 用戶端與伺服器
author: juntaoluo
description: 此教學課程會示範如何在 ASP.NET Core 上，建立 gRPC 服務與 gRPC 用戶端。 了解如何建立 gRPC 服務專案、編輯通訊協定檔案，以及新增雙工資料流呼叫。
ms.author: johluo
ms.date: 10/23/2020
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
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: fed25eeacf57504810d41fcf002dcaa9927a21af
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588597"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="a4e0d-104">教學課程：在 ASP.NET Core 中建立 gRPC 用戶端和伺服器</span><span class="sxs-lookup"><span data-stu-id="a4e0d-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="a4e0d-105">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="a4e0d-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="a4e0d-106">本教學課程示範如何建立 .NET Core [gRPC](https://grpc.io/docs/guides/) 用戶端，以及 ASP.NET Core gRPC 伺服器。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="a4e0d-107">在結束時，您將擁有可與 gRPC Greeter 服務通訊的 gRPC 用戶端。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="a4e0d-108">[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/grpc/grpc-start/sample) ([如何下載](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a4e0d-109">在本教學課程中，您：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a4e0d-110">建立 gRPC 伺服器。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="a4e0d-111">建立 gRPC 用戶端。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="a4e0d-112">利用 gRPC Greeter 服務來測試 gRPC 用戶端。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a4e0d-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="a4e0d-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4e0d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e0d-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e0d-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e0d-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e0d-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e0d-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="a4e0d-117">Visual Studio for Mac 8.7 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="a4e0d-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="a4e0d-118">建立 gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="a4e0d-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4e0d-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e0d-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a4e0d-120">啟動 Visual Studio，然後選取 [建立新專案]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-120">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="a4e0d-121">或者，從 Visual Studio 的 [檔案] 功能表中，選取 [新增] > [專案]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a4e0d-122">在 [ **建立新專案** ] 對話方塊中，選取 [ **gRPC 服務** ]，然後選取 **[下一步]**：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next**:</span></span>

  ![在 Visual Studio 中建立新的專案對話方塊](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="a4e0d-124">將專案命名為 **GrpcGreeter**。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-124">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="a4e0d-125">請務必將專案命名為 *GrpcGreeter* ，如此當您複製並貼上程式碼時，命名空間才會相符。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-125">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="a4e0d-126">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-126">Select **Create**.</span></span>
* <span data-ttu-id="a4e0d-127">在 [建立新的 gRPC 服務] 對話方塊中：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="a4e0d-128">已選取 [gRPC 服務] 範本。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="a4e0d-129">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-129">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e0d-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e0d-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a4e0d-131">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a4e0d-132">將目錄 (`cd`) 變更為專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-132">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="a4e0d-133">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="a4e0d-134">`dotnet new` 命令會在 [GrpcGreeter] 資料夾中建立一個新的 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="a4e0d-135">`code` 命令會在新的 Visual Studio Code 執行個體中開啟 [GrpcGreeter] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="a4e0d-136">會出現一個對話方塊，其中包含 **建立和偵測所需的資產，但缺少 ' GrpcGreeter '。要新增它們嗎？**</span><span class="sxs-lookup"><span data-stu-id="a4e0d-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="a4e0d-137">選取 [是]  。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-137">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e0d-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e0d-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a4e0d-139">啟動 Visual Studio for Mac，然後選取 [ **建立新專案**]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-139">Start Visual Studio for Mac and select **Create a new project**.</span></span> <span data-ttu-id="a4e0d-140">或者，從 Visual Studio 的 [檔案] 功能表中，選取 [新增] > [專案]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a4e0d-141">在 [**建立新專案**] 對話方塊中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **gRPC 服務**] 並選取 **[下一步]**</span><span class="sxs-lookup"><span data-stu-id="a4e0d-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next**:</span></span>

  ![在 macOS 上建立新的專案對話方塊](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="a4e0d-143">選取目標 framework 的 **.Net Core 3.1** ，然後選取 **[下一步]**。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-143">Select **.NET Core 3.1** for the target framework and select **Next**.</span></span>
* <span data-ttu-id="a4e0d-144">將專案命名為 **GrpcGreeter**。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-144">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="a4e0d-145">請務必將專案命名為 *GrpcGreeter* ，如此當您複製並貼上程式碼時，命名空間才會相符。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-145">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="a4e0d-146">選取 [建立]  。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-146">Select **Create**.</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="a4e0d-147">執行服務</span><span class="sxs-lookup"><span data-stu-id="a4e0d-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="a4e0d-148">該記錄會顯示正在接聽 `https://localhost:5001` 的服務。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="a4e0d-149">gRPC 範本已設為使用[傳輸層安全性 (TLS)](https://tools.ietf.org/html/rfc5246)。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="a4e0d-150">gRPC 用戶端需要使用 HTTPS 來呼叫伺服器。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="a4e0d-151">macOS 不支援具有 TLS 的 ASP.NET Core gRPC。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="a4e0d-152">您需要額外的組態才能在 macOS 上成功執行 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="a4e0d-153">如需詳細資訊，請參閱[無法在 macOS 上啟動 ASP.NET Core gRPC 應用程式](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="a4e0d-154">檢查專案檔</span><span class="sxs-lookup"><span data-stu-id="a4e0d-154">Examine the project files</span></span>

<span data-ttu-id="a4e0d-155">*GrpcGreeter* 專案檔：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="a4e0d-156">*歡迎*： *Protos/歡迎. proto* 檔案定義 `Greeter` gRPC，並用來產生 gRPC 伺服器資產。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-156">*greet.proto*: The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="a4e0d-157">如需詳細資訊，請參閱 [gRPC 簡介](xref:grpc/index)。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="a4e0d-158">*Services* 資料夾：包含服務的執行 `Greeter` 。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="a4e0d-159">*appSettings.json*：包含設定資料，例如 Kestrel 所使用的通訊協定。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-159">*appSettings.json*: Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="a4e0d-160">如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="a4e0d-161">*Program.cs*：包含 gRPC 服務的進入點。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-161">*Program.cs*: Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="a4e0d-162">如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="a4e0d-163">*Startup.cs*：包含設定應用程式行為的程式碼。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-163">*Startup.cs*: Contains code that configures app behavior.</span></span> <span data-ttu-id="a4e0d-164">如需詳細資訊，請參閱[應用程式啟動](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="a4e0d-165">在 .NET 主控台應用程式中建立 gRPC 用戶端</span><span class="sxs-lookup"><span data-stu-id="a4e0d-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4e0d-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e0d-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a4e0d-167">開啟第二個 Visual Studio 執行個體，並選取 [建立新專案]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-167">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="a4e0d-168">在 [建立新專案] 對話方塊中，選取 [主控台應用程式 (.NET Core)] 並選取 [下一步]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="a4e0d-169">在 [ **專案名稱** ] 文字方塊中，輸入 **GrpcGreeterClient** ，然後選取 [ **建立**]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e0d-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e0d-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a4e0d-171">開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a4e0d-172">將目錄 (`cd`) 變更為專案的資料夾。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-172">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="a4e0d-173">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e0d-174">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e0d-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a4e0d-175">請遵循 [使用 Visual Studio for Mac 在 macOS 上建置完整的 .NET Core 方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)中的指示，以建立名為 *GrpcGreeterClient* 的主控台應用程式。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="a4e0d-176">新增必要套件</span><span class="sxs-lookup"><span data-stu-id="a4e0d-176">Add required packages</span></span>

<span data-ttu-id="a4e0d-177">gRPC 用戶端專案需要下列套件：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="a4e0d-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，包含 .NET Core 用戶端。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="a4e0d-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)，包含 C# 的 protobuf 訊息 API。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="a4e0d-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)，包含 protobuf 檔案的 C# 工具支援。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="a4e0d-181">執行階段不需要工具套件，因此相依性會標示為 `PrivateAssets="All"`。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4e0d-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e0d-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a4e0d-183">使用套件管理員主控台 (PMC) 或管理 NuGet 套件來安裝套件。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="a4e0d-184">安裝套件的 PMC 選項</span><span class="sxs-lookup"><span data-stu-id="a4e0d-184">PMC option to install packages</span></span>

* <span data-ttu-id="a4e0d-185">從 Visual Studio 中，選取 [**工具**]  >  **NuGet 套件管理員**[  >  **套件管理員主控台**]</span><span class="sxs-lookup"><span data-stu-id="a4e0d-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="a4e0d-186">從 [套件管理員主控台] 視窗，執行 `cd GrpcGreeterClient` 以變更包含 *GrpcGreeterClient.csproj* 檔案之資料夾的目錄。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="a4e0d-187">執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="a4e0d-188">管理 NuGet 套件選項來安裝套件</span><span class="sxs-lookup"><span data-stu-id="a4e0d-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="a4e0d-189">以滑鼠右鍵按一下 **方案 Explorer** 中的專案 [  >  **管理 NuGet 封裝**]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="a4e0d-190">選取 [瀏覽] 索引標籤。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="a4e0d-191">在搜尋方塊中輸入 **Grpc.Net.Client**。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="a4e0d-192">從 [瀏覽] 索引標籤選取 [Grpc.Net.Client] 套件，然後選取 [安裝]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="a4e0d-193">對 `Google.Protobuf` 與 `Grpc.Tools` 重複進行。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e0d-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e0d-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a4e0d-195">從 [整合式終端] 執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-195">Run the following commands from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e0d-196">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e0d-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a4e0d-197">在 **Solution Pad** 中，以滑鼠右鍵按一下 **GrpcGreeterClient** 專案，然後選取 [**管理 NuGet 套件**]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="a4e0d-198">在搜尋方塊中輸入 **Grpc.Net.Client**。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="a4e0d-199">從結果窗格中選取 **Grpc .net. 用戶端** 封裝，然後選取 [ **加入封裝**]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**.</span></span>
* <span data-ttu-id="a4e0d-200">在 [**接受授權**] 對話方塊上，選取 [**接受**] 按鈕。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-200">Select the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="a4e0d-201">對 `Google.Protobuf` 與 `Grpc.Tools` 重複進行。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="a4e0d-202">新增 greet.proto</span><span class="sxs-lookup"><span data-stu-id="a4e0d-202">Add greet.proto</span></span>

* <span data-ttu-id="a4e0d-203">在 gRPC 用戶端專案中建立 [Protos] 資料夾。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="a4e0d-204">將 *Protos\greet.proto* 檔案從 gRPC Greeter 服務複製到 gRPC 用戶端專案。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="a4e0d-205">將檔案內的命名空間更新 `greet.proto` 為專案的命名空間：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="a4e0d-206">編輯 *GrpcGreeterClient.csproj* 專案檔：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="a4e0d-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e0d-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="a4e0d-208">以滑鼠右鍵按一下專案，然後選取 [編輯專案檔]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="a4e0d-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e0d-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="a4e0d-210">選取 [GrpcGreeterClient.csproj] 檔案。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e0d-211">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e0d-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="a4e0d-212">以滑鼠右鍵按一下專案，然後選取 [編輯專案檔]。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-212">Right-click the project and select **Edit Project File**.</span></span>

  ---

* <span data-ttu-id="a4e0d-213">新增具有代表 *greet.proto* 檔案之 `<Protobuf>` 元素的項目群組：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="a4e0d-214">建立 Greeter 用戶端</span><span class="sxs-lookup"><span data-stu-id="a4e0d-214">Create the Greeter client</span></span>

<span data-ttu-id="a4e0d-215">建立用戶端專案，以在命名空間中建立類型 `GrpcGreeter` 。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="a4e0d-216">`GrpcGreeter` 類型會自動由建置處理序產生。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="a4e0d-217">利用下列程式碼，更新 gRPC 用戶端 *Program.cs* 檔案：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="a4e0d-218">*Program.cs* 包含 gRPC 用戶端的進入點和邏輯。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="a4e0d-219">Greeter 用戶端建立者：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="a4e0d-220">具現化包含建立與 gRPC 服務連線資訊的 `GrpcChannel`。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="a4e0d-221">使用 `GrpcChannel` 建構 Greeter 用戶端：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="a4e0d-222">Greeter 用戶端會呼叫非同步的 `SayHello` 方法。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="a4e0d-223">顯示 `SayHello` 呼叫的結果：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="a4e0d-224">使用 gRPC Greeter 服務測試 gRPC 用戶端</span><span class="sxs-lookup"><span data-stu-id="a4e0d-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a4e0d-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a4e0d-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a4e0d-226">在 Greeter 服務中按下 `Ctrl+F5`，在不啟動偵錯工具的情況下啟動伺服器。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="a4e0d-227">在 `GrpcGreeterClient` 專案中按下 `Ctrl+F5`，在不啟動偵錯工具的情況下啟動用戶端。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a4e0d-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a4e0d-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a4e0d-229">啟動 Greeter 服務。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-229">Start the Greeter service.</span></span>
* <span data-ttu-id="a4e0d-230">啟動用戶端。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a4e0d-231">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a4e0d-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a4e0d-232">由於上述 macOS 因應措施的 [HTTP/2 TLS 問題](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)，您必須將用戶端中的通道位址更新為 " http://localhost:5000 "。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="a4e0d-233">將 **GrpcGreeterClient/Program** 的第13行更新為 read：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="a4e0d-234">啟動 Greeter 服務。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-234">Start the Greeter service.</span></span>
* <span data-ttu-id="a4e0d-235">啟動用戶端。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-235">Start the client.</span></span>

---

<span data-ttu-id="a4e0d-236">用戶端會以包含其名稱 *GreeterClient* 的訊息，將問候語傳送至服務。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-236">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="a4e0d-237">該服務會傳送 "Hello GreeterClient" 訊息做為回應。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="a4e0d-238">"Hello GreeterClient" 回應會顯示在命令提示字元中：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="a4e0d-239">gRPC 服務會將成功呼叫的詳細資料，記錄在會寫入命令提示字元的記錄中：</span><span class="sxs-lookup"><span data-stu-id="a4e0d-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="a4e0d-240">此文章中的程式碼需要 ASP.NET Core HTTPS 開發憑證來保護 gRPC 服務。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="a4e0d-241">如果 .NET gRPC 用戶端因訊息 `The remote certificate is invalid according to the validation procedure.` 或而失敗 `The SSL connection could not be established.` ，則開發憑證不受信任。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="a4e0d-242">若要修正此問題，請參閱 [使用未受信任/不正確憑證呼叫 gRPC 服務](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate)。</span><span class="sxs-lookup"><span data-stu-id="a4e0d-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="a4e0d-243">後續步驟</span><span class="sxs-lookup"><span data-stu-id="a4e0d-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
