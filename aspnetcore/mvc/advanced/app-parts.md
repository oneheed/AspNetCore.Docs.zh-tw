---
title: RazorASP.NET Core 中的應用程式元件共用控制器、視圖、頁面和其他
author: rick-anderson
description: RazorASP.NET Core 中的應用程式元件共用控制器、視圖、頁面和其他
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 2e86025eaf98c4e2cbbd86a5a353664204c35594
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630415"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a>使用應用程式元件共用控制器、視圖、 Razor 頁面和其他

::: moniker range=">= aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([如何下載](xref:index#how-to-download-a-sample)) 

*應用程式元件*是應用程式資源的抽象概念。 應用程式元件可讓 ASP.NET Core 探索控制器、視圖元件、標籤協助程式、 Razor 頁面、razor 編譯來源等等。 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 是應用程式元件。 `AssemblyPart` 封裝元件參考，並公開類型和編譯參考。

[功能提供者](#fp) 會使用應用程式元件，以填入 ASP.NET Core 應用程式的功能。 應用程式元件的主要使用案例是設定應用程式以探索 (，或避免從元件載入) ASP.NET Core 功能。 例如，您可能會想要在多個應用程式之間共用通用功能。 使用應用程式元件時，您可以透過多個應用程式，共用包含控制器、視圖、 Razor 頁面、razor 編譯來源、標記協助程式等元件 (DLL) 。 建議您共用元件，以便在多個專案中複製程式碼。

ASP.NET Core apps 從中載入功能 <xref:System.Web.WebPages.ApplicationPart> 。 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>類別代表元件所支援的應用程式元件。

## <a name="load-aspnet-core-features"></a>載入 ASP.NET Core 功能

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> 和 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> 類別來探索和載入 ASP.NET Core 的功能 (控制器、view 元件等 ) 。 會 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> 追蹤可用的應用程式元件和功能提供者。 `ApplicationPartManager` 設定于 `Startup.ConfigureServices` ：

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

下列程式碼提供使用設定的替代方法 `ApplicationPartManager` `AssemblyPart` ：

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

上述兩個程式碼範例會 `SharedController` 從元件載入。 `SharedController`不在應用程式的專案中。 請參閱 [WebAppParts 解決方案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) 範例下載。

### <a name="include-views"></a>包含視圖

使用[ Razor 類別庫](xref:razor-pages/ui-class)將視圖包含在元件中。

### <a name="prevent-loading-resources"></a>防止載入資源

應用程式元件可以用來 *避免* 在特定元件或位置中載入資源。 新增或移除集合的成員  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> ，以隱藏或建立可用的資源。 `ApplicationParts` 集合中的項目順序並不重要。 先設定， `ApplicationPartManager` 再使用它來設定容器中的服務。 例如，在叫用 `ApplicationPartManager` 之前設定 `AddControllersAsServices` 。 `Remove`在集合上呼叫 `ApplicationParts` 以移除資源。

`ApplicationPartManager`包含下列元件：

* 應用程式的元件和相依元件。
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

<a name="fp"></a>

## <a name="feature-providers"></a>功能提供者

應用程式功能提供者會檢查應用程式元件，並為這些元件提供功能。 下列 ASP.NET Core 功能有內建功能提供者：

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* `internal class`[ Razor CompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)

繼承自 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> 的功能提供者，其中 `T` 是功能的類型。 您可以針對任何先前列出的功能類型來執行功能提供者。 中的功能提供者順序 `ApplicationPartManager.FeatureProviders` 可能會影響執行時間行為。 稍後新增的提供者可以回應先前新增的提供者所採取的動作。

### <a name="display-available-features"></a>顯示可用的功能

藉由透過相依性插入要求來列舉應用程式可用的功能 `ApplicationPartManager` ： [dependency injection](../../fundamentals/dependency-injection.md)

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)會使用上述程式碼來顯示應用程式功能：

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>應用程式元件中的探索

使用應用程式元件進行開發時，HTTP 404 錯誤並不常見。 這些錯誤通常是因為遺漏應用程式元件的基本需求所造成。 如果您的應用程式傳回 HTTP 404 錯誤，請確認已符合下列需求：

* `applicationName`設定必須設定為用於探索的根元件。 用於探索的根元件通常是進入點元件。
* 根元件必須參考用於探索的元件。 參考可以是直接或可轉移的。
* 根元件必須參考 Web SDK。 架構具有將屬性戳記至用於探索之根元件的邏輯。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([如何下載](xref:index#how-to-download-a-sample)) 

*應用程式元件*是應用程式資源的抽象概念。 應用程式元件可讓 ASP.NET Core 探索控制器、視圖元件、標籤協助程式、 Razor 頁面、razor 編譯來源等等。 [AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) 是一個應用程式元件。 `AssemblyPart` 封裝元件參考，並公開類型和編譯參考。

*功能提供者* 會使用應用程式元件，以填入 ASP.NET Core 應用程式的功能。 應用程式元件的主要使用案例是設定應用程式以探索 (，或避免從元件載入) ASP.NET Core 功能。 例如，您可能會想要在多個應用程式之間共用通用功能。 使用應用程式元件時，您可以透過多個應用程式，共用包含控制器、視圖、 Razor 頁面、razor 編譯來源、標記協助程式等元件 (DLL) 。 建議您共用元件，以便在多個專案中複製程式碼。

ASP.NET Core apps 從中載入功能 <xref:System.Web.WebPages.ApplicationPart> 。 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>類別代表元件所支援的應用程式元件。

## <a name="load-aspnet-core-features"></a>載入 ASP.NET Core 功能

使用 `ApplicationPart` 和 `AssemblyPart` 類別來探索和載入 ASP.NET Core 的功能 (控制器、view 元件等 ) 。 會 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> 追蹤可用的應用程式元件和功能提供者。 `ApplicationPartManager` 設定于 `Startup.ConfigureServices` ：

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

下列程式碼提供使用設定的替代方法 `ApplicationPartManager` `AssemblyPart` ：

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

上述兩個程式碼範例會 `SharedController` 從元件載入。 `SharedController`不在應用程式的專案中。 請參閱 [WebAppParts 解決方案](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) 範例下載。

### <a name="include-views"></a>包含視圖

使用[ Razor 類別庫](xref:razor-pages/ui-class)將視圖包含在元件中。

### <a name="prevent-loading-resources"></a>防止載入資源

應用程式元件可以用來 *避免* 在特定元件或位置中載入資源。 新增或移除集合的成員  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> ，以隱藏或建立可用的資源。 `ApplicationParts` 集合中的項目順序並不重要。 先設定， `ApplicationPartManager` 再使用它來設定容器中的服務。 例如，在叫用 `ApplicationPartManager` 之前設定 `AddControllersAsServices` 。 `Remove`在集合上呼叫 `ApplicationParts` 以移除資源。

下列程式碼會使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> `MyDependentLibrary` 從應用程式移除： [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]

`ApplicationPartManager`包含下列元件：

* 應用程式的元件和相依元件。
* `Microsoft.AspNetCore.Mvc.TagHelpers`.
* `Microsoft.AspNetCore.Mvc.Razor`.

## <a name="application-feature-providers"></a>應用程式功能提供者

應用程式功能提供者會檢查應用程式元件，並為這些元件提供功能。 下列 ASP.NET Core 功能有內建功能提供者：

* [控制器](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [標籤協助程式](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [視圖元件](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

繼承自 <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1> 的功能提供者，其中 `T` 是功能的類型。 您可以針對任何先前列出的功能類型來執行功能提供者。 中的功能提供者順序 `ApplicationPartManager.FeatureProviders` 可能會影響執行時間行為。 稍後新增的提供者可以回應先前新增的提供者所採取的動作。

### <a name="display-available-features"></a>顯示可用的功能

藉由透過相依性插入要求來列舉應用程式可用的功能 `ApplicationPartManager` ： [dependency injection](../../fundamentals/dependency-injection.md)

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

[下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2)會使用上述程式碼來顯示應用程式功能：

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a>應用程式元件中的探索

使用應用程式元件進行開發時，HTTP 404 錯誤並不常見。 這些錯誤通常是因為遺漏應用程式元件的基本需求所造成。 如果您的應用程式傳回 HTTP 404 錯誤，請確認已符合下列需求：

* `applicationName`設定必須設定為用於探索的根元件。 用於探索的根元件通常是進入點元件。
* 根元件必須參考用於探索的元件。 參考可以是直接或可轉移的。
* 根元件必須參考 Web SDK。
  * ASP.NET Core framework 的自訂群組建邏輯會將屬性戳記至用於探索的根元件中。

::: moniker-end
