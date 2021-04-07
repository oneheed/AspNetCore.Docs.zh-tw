---
title: 在 ASP.NET Core 中使用應用程式模型
author: rick-anderson
description: 了解如何讀取及操作應用程式模型，來修改 ASP.NET Core 中 MVC 項目的行為方式。
ms.author: riande
ms.date: 04/05/2021
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
uid: mvc/controllers/application-model
ms.openlocfilehash: 8772a432f137fd6d0278208963bee03f6e8a715b
ms.sourcegitcommit: 0abfe496fed8e9470037c8128efa8a50069ccd52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2021
ms.locfileid: "106564037"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>在 ASP.NET Core 中使用應用程式模型

作者：[Steve Smith](https://ardalis.com/)

ASP.NET Core MVC 定義了一個「應用程式模型」，代表 MVC 應用程式的元件。 讀取和操作此模型，以修改 MVC 元素的行為。 根據預設，MVC 會遵循特定慣例來判斷哪些類別被視為控制器、哪些類別上的方法是動作，以及參數和路由的行為。 藉由建立自訂慣例並全域套用或作為屬性，自訂此行為以符合應用程式的需求。

## <a name="models-and-providers-iapplicationmodelprovider"></a> () 模型和提供者 `IApplicationModelProvider`

ASP.NET Core MVC 應用程式模型包含抽象介面和描述 MVC 應用程式的具體實作為類別。 此模型是 MVC 根據預設慣例來探索應用程式控制器、動作、動作參數、路由和篩選條件的結果。 使用應用程式模型，修改應用程式以遵循預設 MVC 行為中的不同慣例。 參數、名稱、路由和篩選條件全都用作動作與控制器的組態資料。

ASP.NET Core MVC 應用程式模型具有下列結構：

* ApplicationModel
  * 控制器 (ControllerModel)
    * 動作 (ActionModel)
      * 參數 (ParameterModel)

模型的每個層級都可存取共同的 `Properties` 集合，而較低層級可以存取並覆寫階層架構中較高層級所設定的屬性值。 屬性會在建立動作時保存到 <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType>。 然後當處理要求時，可以透過 <xref:Microsoft.AspNetCore.Mvc.ActionContext.ActionDescriptor?displayProperty=nameWithType> 來存取慣例所新增或修改的任何屬性。 使用屬性是根據每個動作來設定篩選、模型系結器和其他應用程式模型層面的絕佳方式。

> [!NOTE]
> 在 <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType> 應用程式啟動之後，集合不會 (寫入) 的安全線程。 慣例是安全地將資料新增至此集合的最佳方式。

ASP.NET Core MVC 會使用介面所定義的提供者模式來載入應用程式模型 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> 。 本節涵蓋此提供者運作方式的一些內部實作詳細資料。 使用提供者模式是先進的主旨，主要用於架構用途。 大部分的應用程式都應該使用慣例，而不是提供者模式。

介面的實作為「 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> 包裝」，其中每個實 <xref:Microsoft.AspNetCore.Mvc.Abstractions.IActionInvokerProvider.OnProvidersExecuting%2A> 根據其屬性以遞增順序呼叫 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider.Order> 。 然後以相反順序呼叫 <xref:Microsoft.AspNetCore.Mvc.Abstractions.IActionInvokerProvider.OnProvidersExecuted%2A> 方法。 架構會定義數個提供者：

先是 (`Order=-1000`)：

* `DefaultApplicationModelProvider`

然後 (`Order=-990`)：

* `AuthorizationApplicationModelProvider`
* `CorsApplicationModelProvider`

> [!NOTE]
> 呼叫兩個具有相同值的提供者的順序 `Order` 是未定義的，而且不應該依賴。

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelProvider> 是讓架構作者擴充的進階概念。 一般情況下，應用程式應該使用慣例，而架構應該使用提供者。 主要的差別是提供者一律在慣例之前先執行。

`DefaultApplicationModelProvider` 建立了 ASP.NET Core MVC 使用的許多預設行為。 其負責的部分包括：

* 將全域篩選條件新增至內容
* 將控制器新增至內容
* 將公用控制器方法新增作為動作
* 將動作方法參數新增至內容
* 套用路由和其他屬性

某些內建行為由 `DefaultApplicationModelProvider` 實作。 此提供者負責建立，而 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ControllerModel> 後者接著參考 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ActionModel> 、 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PropertyModel> 和 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ParameterModel> 實例。 `DefaultApplicationModelProvider`類別是內部架構的執行詳細資料，未來可能會變更。

`AuthorizationApplicationModelProvider` 負責套用與 <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> 屬性建立關聯的行為。 如需詳細資訊，請參閱<xref:security/authorization/simple>。

`CorsApplicationModelProvider`與和相關聯的實行為 <xref:Microsoft.AspNetCore.Cors.Infrastructure.IEnableCorsAttribute> <xref:Microsoft.AspNetCore.Cors.Infrastructure.IDisableCorsAttribute> 。 如需詳細資訊，請參閱<xref:security/cors>。

本節所述架構內部提供者的相關資訊無法透過 [.NET API 瀏覽器](https://docs.microsoft.com/dotnet/api/)取得。 不過，您可以在 ASP.NET Core 參考來源中檢查提供者 [ (dotnet/Aspnetcore GitHub 存放庫) ](https://github.com/dotnet/aspnetcore)。 使用 GitHub 搜尋依名稱尋找提供者，然後使用 [ **切換分支/標記** ] 下拉式清單選取來源的版本。

## <a name="conventions"></a>慣例

應用程式模型定義了慣例抽象化，提供比覆寫整個模型或提供者更簡單的方式，來自訂模型的行為。 這些抽象概念是修改應用程式行為的建議方式。 慣例提供一種方法來撰寫可動態套用自訂的程式碼。 雖然 [篩選器](xref:mvc/controllers/filters) 會提供修改架構行為的方法，但自訂可讓您控制整個應用程式如何一起運作。

可用的慣例如下：

* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelConvention>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IControllerModelConvention>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IActionModelConvention>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IParameterModelConvention>

套用慣例的方式是將它們新增至 MVC 選項，或藉由執行屬性並將其套用至控制器、動作或動作參數， (類似于 [篩選](xref:mvc/controllers/filters)) 。不同于篩選準則，只有在應用程式啟動時才會執行慣例，而不會在每個要求中執行。

> [!NOTE]
> 如需 Razor 頁面路由和應用程式模型提供者慣例的詳細資訊，請參閱 <xref:razor-pages/razor-pages-conventions> 。

## <a name="modify-the-applicationmodel"></a>修改 `ApplicationModel`

下列慣例是用來將屬性新增至應用程式模型：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

在中加入 MVC 時，應用程式模型慣例會套用為選項 `Startup.ConfigureServices` ：

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

您可以從 <xref:Microsoft.AspNetCore.Mvc.Abstractions.ActionDescriptor.Properties?displayProperty=nameWithType> 控制器動作內的集合存取屬性：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

## <a name="modify-the-controllermodel-description"></a>修改 `ControllerModel` 描述

控制器模型也可以包含自訂屬性。 自訂屬性會以應用程式模型中指定的相同名稱覆寫現有的屬性。 下列慣例屬性會在控制器層級新增描述：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

此慣例會套用為控制器上的屬性：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

## <a name="modify-the-actionmodel-description"></a>修改 `ActionModel` 描述

個別的屬性慣例可以套用至個別的動作，而覆寫應用程式或控制器層級已套用的行為：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

將此套用到控制器內的動作，會示範它如何覆寫控制器層級慣例：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

## <a name="modify-the-parametermodel"></a>修改 `ParameterModel`

下列的慣例可以套用至動作參數，以修改其 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindingInfo>。 下列慣例要求參數必須是路由參數。 系統會忽略其他潛在的系結來源，例如查詢字串值：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

屬性可套用至任何動作參數：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

若要將慣例套用至所有動作參數，請將加入 `MustBeInRouteParameterModelConvention` 至 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> `Startup.ConfigureServices` ：

```csharp
options.Conventions.Add(new MustBeInRouteParameterModelConvention());
```

## <a name="modify-the-actionmodel-name"></a>修改 `ActionModel` 名稱

下列慣例會修改 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ActionModel> 以更新其所套用之動作的「名稱」。 新的名稱會當作傳給屬性的參數。 路由會使用這個新名稱，因此它會影響用來達到此動作方法的路由：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

這個屬性會套用至 `HomeController` 中的動作方法：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

即使方法名稱是 `SomeName`，屬性仍會覆寫使用方法名稱的 MVC 慣例，並將動作名稱取代為 `MyCoolAction`。 因此，用來連線到此動作的路由是 `/Home/MyCoolAction`。

> [!NOTE]
> 此區段中的這個範例基本上與使用內建相同 <xref:Microsoft.AspNetCore.Mvc.ActionNameAttribute> 。

## <a name="custom-routing-convention"></a>自訂路由慣例

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IApplicationModelConvention> 自訂路由的運作方式。 例如，下列慣例會將控制器的命名空間併入其路由中， `.` 並以路由中的命名空間取代 `/` ：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

慣例會新增為中的選項 `Startup.ConfigureServices` ：

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> 使用下列方法，將慣例新增至 [中介軟體](xref:fundamentals/middleware/index) <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 。 `{CONVENTION}`預留位置是要加入的慣例：
>
> ```csharp
> services.Configure<MvcOptions>(c => c.Conventions.Add({CONVENTION}));
> ```

下列範例會將慣例套用至未使用控制器  `Namespace` 在其名稱中之屬性路由的路由：

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

::: moniker range="<= aspnetcore-2.2"

## <a name="application-model-usage-in-webapicompatshim"></a>中的應用程式模型使用方式 `WebApiCompatShim`

ASP.NET Core MVC 會使用與 ASP.NET Web API 2 不同的一組慣例。 您可以使用自訂慣例，修改 ASP.NET Core MVC 應用程式的行為，使其與 web API 應用程式的行為一致。 Microsoft 針對此用途特別提供[ `WebApiCompatShim` NuGet 套件](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。

> [!NOTE]
> 如需從 ASP.NET Web API 遷移的詳細資訊，請參閱 <xref:migration/webapi> 。

使用 Web API 相容性填充碼：

* 將 `Microsoft.AspNetCore.Mvc.WebApiCompatShim` 封裝加入至專案。
* 藉由呼叫，將慣例新增至 MVC <xref:Microsoft.Extensions.DependencyInjection.WebApiCompatShimMvcBuilderExtensions.AddWebApiConventions%2A> `Startup.ConfigureServices` ：

```csharp
services.AddMvc().AddWebApiConventions();
```

填充碼提供的慣例僅會套用到已套用特定屬性的應用程式組件。 下列四個屬性用來控制哪些控制器應該讓其慣例由填充碼修改：

* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiActionConventionsAttribute>
* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiOverloadingAttribute>
* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiParameterConventionsAttribute>
* <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiRoutesAttribute>

### <a name="action-conventions"></a>動作慣例

<xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiActionConventionsAttribute> 用來根據 (實例的名稱將 HTTP 方法對應至動作， `Get` 會對應至 `HttpGet`) 。 它只適用於不使用屬性路由的動作。

### <a name="overloading"></a>多載化

<xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiOverloadingAttribute> 用來套用 <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.WebApiOverloadingApplicationModelConvention> 慣例。 這個慣例會將 <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.OverloadActionConstraint> 新增至動作選取程序，這將候選項目動作限制為要求符合其所有非選擇性參數的動作。

### <a name="parameter-conventions"></a>參數慣例

<xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiParameterConventionsAttribute> 用來套用 <xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.WebApiParameterConventionsApplicationModelConvention> 動作慣例。 這個慣例指定用來作為動作參數的簡單類型預設會從 URL 繫結，而複雜類型則會從要求主體繫結。

### <a name="routes"></a>路由

<xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim.UseWebApiRoutesAttribute> 控制是否套用 `WebApiApplicationModelConvention` 控制器慣例。 啟用時，此慣例會用來將 [區域](xref:mvc/controllers/areas) 支援新增至路由，並指出控制器位於 `api` 區域中。

除了一組慣例之外，相容性套件還包含一個 <xref:System.Web.Http.ApiController?displayProperty=fullName> 基類，可取代 WEB API 所提供的類別。 這可讓您為 web API 撰寫的 web API 控制器，並在 ASP.NET Core MVC 上執行時，從其繼承 `ApiController` 以運作。 稍 [`UseWebApi*`](xref:Microsoft.AspNetCore.Mvc.WebApiCompatShim) 早列出的所有屬性都會套用至基底控制器類別。 `ApiController`會公開與 WEB API 中找到的屬性、方法和結果類型相容的屬性、方法和結果型別。

::: moniker-end

## <a name="use-apiexplorer-to-document-an-app"></a>用 `ApiExplorer` 來記錄應用程式

應用程式模型會在可用來周遊應用程式結構的每個層級公開 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ApiExplorerModel> 屬性。 這可以用來 [使用 Swagger 之類的工具來產生 Web api 的說明頁面](xref:tutorials/web-api-help-pages-using-swagger)。 `ApiExplorer`屬性 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.ApiExplorerModel.IsVisible> 會公開可以設定的屬性，以指定應該公開應用程式模型的哪些部分。 使用慣例設定此設定：

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

使用此方法 (和其他慣例（如有必要）) ，在應用程式內的任何層級啟用或停用 API 可見度。
