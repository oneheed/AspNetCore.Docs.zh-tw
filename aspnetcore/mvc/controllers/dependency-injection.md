---
title: ASP.NET Core 控制器的相依性插入
author: ardalis
description: 了解 ASP.NET Core MVC 控制器如何在 ASP.NET Core 中，透過其含有相依性插入的建構函式明確要求其相依性。
ms.author: riande
ms.date: 02/24/2019
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
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: f3654e008733c57b4cd4cd34a52b747af2258be1
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589071"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a>ASP.NET Core 控制器的相依性插入

::: moniker range=">= aspnetcore-3.0"

作者：[Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://github.com/ardalis)

ASP.NET Core MVC 控制器會透過建構函式明確地要求相依性。 ASP.NET Core 內建[相依性插入 (DI)](xref:fundamentals/dependency-injection) 支援。 DI 可讓您更輕鬆地測試和維護應用程式。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="constructor-injection"></a>建構函式插入

服務會新增來作為建構函式參數，而執行階段會解析來自服務容器的服務。 通常可以使用介面來定義服務。 例如，假設應用程式需要目前的時間。 下列介面會公開 `IDateTime` 服務：

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

下列程式碼會實作 `IDateTime` 介面：

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

將服務新增至服務容器：

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

如需 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> 的詳細資訊，請參閱 [DI 服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)。

下列程式碼會根據時段來向使用者顯示問候語：

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

執行應用程式，並根據時間顯示訊息。

## <a name="action-injection-with-fromservices"></a>使用 FromServices 進行動作插入

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> 能夠將服務直接插入至動作方法，而不需使用建構函式插入：

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>從控制器存取設定

從控制器存取應用程式或組態設定是常見的模式。 <xref:fundamentals/configuration/options> 中所述的「選項模式」是管理設定的慣用方法。 一般而言，不要將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 直接插入至控制器。

建立要代表選項的類別。 例如：

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

將設定類別新增至服務集合：

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

設定應用程式以從 JSON 格式的檔案中讀取設定：

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

下列程式碼會向服務容器要求 `IOptions<SampleWebSettings>` 設定，並在 `Index` 方法中使用它們：

[!code-csharp[](dependency-injection/3.1sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>其他資源

* 請參閱 <xref:mvc/controllers/testing>，以了解如何透過明確地要求控制器中的相依性，讓程式碼更容易測試。

* [使用協力廠商實作來取代預設的相依性插入容器](xref:fundamentals/dependency-injection#default-service-container-replacement)。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Shadi Namrouti](https://github.com/shadinamrouti)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://github.com/ardalis)

ASP.NET Core MVC 控制器會透過建構函式明確地要求相依性。 ASP.NET Core 內建[相依性插入 (DI)](xref:fundamentals/dependency-injection) 支援。 DI 可讓您更輕鬆地測試和維護應用程式。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/dependency-injection/sample) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="constructor-injection"></a>建構函式插入

服務會新增來作為建構函式參數，而執行階段會解析來自服務容器的服務。 通常可以使用介面來定義服務。 例如，假設應用程式需要目前的時間。 下列介面會公開 `IDateTime` 服務：

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

下列程式碼會實作 `IDateTime` 介面：

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

將服務新增至服務容器：

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

如需 <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> 的詳細資訊，請參閱 [DI 服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)。

下列程式碼會根據時段來向使用者顯示問候語：

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

執行應用程式，並根據時間顯示訊息。

## <a name="action-injection-with-fromservices"></a>使用 FromServices 進行動作插入

<xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> 能夠將服務直接插入至動作方法，而不需使用建構函式插入：

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a>從控制器存取設定

從控制器存取應用程式或組態設定是常見的模式。 <xref:fundamentals/configuration/options> 中所述的「選項模式」是管理設定的慣用方法。 一般而言，不要將 <xref:Microsoft.Extensions.Configuration.IConfiguration> 直接插入至控制器。

建立要代表選項的類別。 例如：

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

將設定類別新增至服務集合：

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

設定應用程式以從 JSON 格式的檔案中讀取設定：

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

下列程式碼會向服務容器要求 `IOptions<SampleWebSettings>` 設定，並在 `Index` 方法中使用它們：

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a>其他資源

* 請參閱 <xref:mvc/controllers/testing>，以了解如何透過明確地要求控制器中的相依性，讓程式碼更容易測試。

* [使用協力廠商實作來取代預設的相依性插入容器](xref:fundamentals/dependency-injection#default-service-container-replacement)。

::: moniker-end