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
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a>教學課程：在 ASP.NET Core 中建立 gRPC 用戶端和伺服器

作者：[John Luo](https://github.com/juntaoluo)

本教學課程示範如何建立 .NET Core [gRPC](https://grpc.io/docs/guides/) 用戶端，以及 ASP.NET Core gRPC 伺服器。

在結束時，您將擁有可與 gRPC Greeter 服務通訊的 gRPC 用戶端。

[檢視或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/grpc/grpc-start/sample) ([如何下載](xref:index#how-to-download-a-sample))。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立 gRPC 伺服器。
> * 建立 gRPC 用戶端。
> * 利用 gRPC Greeter 服務來測試 gRPC 用戶端。

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [Visual Studio for Mac 8.7 版或更新版本](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a>建立 gRPC 服務

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 啟動 Visual Studio，然後選取 [建立新專案]。 或者，從 Visual Studio 的 [檔案] 功能表中，選取 [新增] > [專案]。
* 在 [ **建立新專案** ] 對話方塊中，選取 [ **gRPC 服務** ]，然後選取 **[下一步]**：

  ![在 Visual Studio 中建立新的專案對話方塊](~/tutorials/grpc/grpc-start/static/cnp.png)

* 將專案命名為 **GrpcGreeter**。 請務必將專案命名為 *GrpcGreeter* ，如此當您複製並貼上程式碼時，命名空間才會相符。
* 選取 [建立]  。
* 在 [建立新的 gRPC 服務] 對話方塊中：
  * 已選取 [gRPC 服務] 範本。
  * 選取 [建立]  。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。
* 將目錄 (`cd`) 變更為專案的資料夾。
* 執行下列命令：

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * `dotnet new` 命令會在 [GrpcGreeter] 資料夾中建立一個新的 gRPC 服務。
  * `code` 命令會在新的 Visual Studio Code 執行個體中開啟 [GrpcGreeter] 資料夾。

  會出現一個對話方塊，其中包含 **建立和偵測所需的資產，但缺少 ' GrpcGreeter '。要新增它們嗎？**
* 選取 [是]  。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 啟動 Visual Studio for Mac，然後選取 [ **建立新專案**]。 或者，從 Visual Studio 的 [檔案] 功能表中，選取 [新增] > [專案]。
* 在 [**建立新專案**] 對話方塊中，選取 [ **Web] 和 [主控台**  >  **應用程式**  >  **gRPC 服務**] 並選取 **[下一步]**

  ![在 macOS 上建立新的專案對話方塊](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* 選取目標 framework 的 **.Net Core 3.1** ，然後選取 **[下一步]**。
* 將專案命名為 **GrpcGreeter**。 請務必將專案命名為 *GrpcGreeter* ，如此當您複製並貼上程式碼時，命名空間才會相符。
* 選取 [建立]  。
---

### <a name="run-the-service"></a>執行服務

  [!INCLUDE[](~/includes/run-the-app.md)]

該記錄會顯示正在接聽 `https://localhost:5001` 的服務。

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> gRPC 範本已設為使用[傳輸層安全性 (TLS)](https://tools.ietf.org/html/rfc5246)。 gRPC 用戶端需要使用 HTTPS 來呼叫伺服器。
>
> macOS 不支援具有 TLS 的 ASP.NET Core gRPC。 您需要額外的組態才能在 macOS 上成功執行 gRPC 服務。 如需詳細資訊，請參閱[無法在 macOS 上啟動 ASP.NET Core gRPC 應用程式](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。

### <a name="examine-the-project-files"></a>檢查專案檔

*GrpcGreeter* 專案檔：

* *歡迎*： *Protos/歡迎. proto* 檔案定義 `Greeter` gRPC，並用來產生 gRPC 伺服器資產。 如需詳細資訊，請參閱 [gRPC 簡介](xref:grpc/index)。
* *Services* 資料夾：包含服務的執行 `Greeter` 。
* *appSettings.json*：包含設定資料，例如 Kestrel 所使用的通訊協定。 如需詳細資訊，請參閱<xref:fundamentals/configuration/index>。
* *Program.cs*：包含 gRPC 服務的進入點。 如需詳細資訊，請參閱<xref:fundamentals/host/generic-host>。
* *Startup.cs*：包含設定應用程式行為的程式碼。 如需詳細資訊，請參閱[應用程式啟動](xref:fundamentals/startup)。

## <a name="create-the-grpc-client-in-a-net-console-app"></a>在 .NET 主控台應用程式中建立 gRPC 用戶端

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 開啟第二個 Visual Studio 執行個體，並選取 [建立新專案]。
* 在 [建立新專案] 對話方塊中，選取 [主控台應用程式 (.NET Core)] 並選取 [下一步]。
* 在 [ **專案名稱** ] 文字方塊中，輸入 **GrpcGreeterClient** ，然後選取 [ **建立**]。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 開啟 [整合式終端](https://code.visualstudio.com/docs/editor/integrated-terminal)機。
* 將目錄 (`cd`) 變更為專案的資料夾。
* 執行下列命令：

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

請遵循 [使用 Visual Studio for Mac 在 macOS 上建置完整的 .NET Core 方案](/dotnet/core/tutorials/using-on-mac-vs-full-solution)中的指示，以建立名為 *GrpcGreeterClient* 的主控台應用程式。

---

### <a name="add-required-packages"></a>新增必要套件

gRPC 用戶端專案需要下列套件：

* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client)，包含 .NET Core 用戶端。
* [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)，包含 C# 的 protobuf 訊息 API。
* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)，包含 protobuf 檔案的 C# 工具支援。 執行階段不需要工具套件，因此相依性會標示為 `PrivateAssets="All"`。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

使用套件管理員主控台 (PMC) 或管理 NuGet 套件來安裝套件。

#### <a name="pmc-option-to-install-packages"></a>安裝套件的 PMC 選項

* 從 Visual Studio 中，選取 [**工具**]  >  **NuGet 套件管理員**[  >  **套件管理員主控台**]
* 從 [套件管理員主控台] 視窗，執行 `cd GrpcGreeterClient` 以變更包含 *GrpcGreeterClient.csproj* 檔案之資料夾的目錄。
* 執行下列命令：

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a>管理 NuGet 套件選項來安裝套件

* 以滑鼠右鍵按一下 **方案 Explorer** 中的專案 [  >  **管理 NuGet 封裝**]。
* 選取 [瀏覽] 索引標籤。
* 在搜尋方塊中輸入 **Grpc.Net.Client**。
* 從 [瀏覽] 索引標籤選取 [Grpc.Net.Client] 套件，然後選取 [安裝]。
* 對 `Google.Protobuf` 與 `Grpc.Tools` 重複進行。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

從 [整合式終端] 執行下列命令：

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 在 **Solution Pad** 中，以滑鼠右鍵按一下 **GrpcGreeterClient** 專案，然後選取 [**管理 NuGet 套件**]。
* 在搜尋方塊中輸入 **Grpc.Net.Client**。
* 從結果窗格中選取 **Grpc .net. 用戶端** 封裝，然後選取 [ **加入封裝**]。
* 在 [**接受授權**] 對話方塊上，選取 [**接受**] 按鈕。
* 對 `Google.Protobuf` 與 `Grpc.Tools` 重複進行。

---

### <a name="add-greetproto"></a>新增 greet.proto

* 在 gRPC 用戶端專案中建立 [Protos] 資料夾。
* 將 *Protos\greet.proto* 檔案從 gRPC Greeter 服務複製到 gRPC 用戶端專案。
* 將檔案內的命名空間更新 `greet.proto` 為專案的命名空間：

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* 編輯 *GrpcGreeterClient.csproj* 專案檔：

  # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  以滑鼠右鍵按一下專案，然後選取 [編輯專案檔]。

  # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  選取 [GrpcGreeterClient.csproj] 檔案。

  # <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

  以滑鼠右鍵按一下專案，然後選取 [編輯專案檔]。

  ---

* 新增具有代表 *greet.proto* 檔案之 `<Protobuf>` 元素的項目群組：

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a>建立 Greeter 用戶端

建立用戶端專案，以在命名空間中建立類型 `GrpcGreeter` 。 `GrpcGreeter` 類型會自動由建置處理序產生。

利用下列程式碼，更新 gRPC 用戶端 *Program.cs* 檔案：

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

*Program.cs* 包含 gRPC 用戶端的進入點和邏輯。

Greeter 用戶端建立者：

* 具現化包含建立與 gRPC 服務連線資訊的 `GrpcChannel`。
* 使用 `GrpcChannel` 建構 Greeter 用戶端：

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

Greeter 用戶端會呼叫非同步的 `SayHello` 方法。 顯示 `SayHello` 呼叫的結果：

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a>使用 gRPC Greeter 服務測試 gRPC 用戶端

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在 Greeter 服務中按下 `Ctrl+F5`，在不啟動偵錯工具的情況下啟動伺服器。
* 在 `GrpcGreeterClient` 專案中按下 `Ctrl+F5`，在不啟動偵錯工具的情況下啟動用戶端。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 啟動 Greeter 服務。
* 啟動用戶端。


# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 由於上述 macOS 因應措施的 [HTTP/2 TLS 問題](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)，您必須將用戶端中的通道位址更新為 " http://localhost:5000 "。 將 **GrpcGreeterClient/Program** 的第13行更新為 read：
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* 啟動 Greeter 服務。
* 啟動用戶端。

---

用戶端會以包含其名稱 *GreeterClient* 的訊息，將問候語傳送至服務。 該服務會傳送 "Hello GreeterClient" 訊息做為回應。 "Hello GreeterClient" 回應會顯示在命令提示字元中：

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

gRPC 服務會將成功呼叫的詳細資料，記錄在會寫入命令提示字元的記錄中：

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
> 此文章中的程式碼需要 ASP.NET Core HTTPS 開發憑證來保護 gRPC 服務。 如果 .NET gRPC 用戶端因訊息 `The remote certificate is invalid according to the validation procedure.` 或而失敗 `The SSL connection could not be established.` ，則開發憑證不受信任。 若要修正此問題，請參閱 [使用未受信任/不正確憑證呼叫 gRPC 服務](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate)。

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a>後續步驟

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
