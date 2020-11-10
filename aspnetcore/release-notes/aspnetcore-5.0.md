---
title: ASP.NET Core 5.0 的新功能
author: rick-anderson
description: 瞭解 ASP.NET Core 5.0 中的新功能。
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
- 'Kestrel'
uid: aspnetcore-5.0
ms.openlocfilehash: 1f377f3be54ed8837d2857aed64c2d055ed9f582
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422583"
---
# <a name="whats-new-in-aspnet-core-50"></a><span data-ttu-id="7b2ac-103">ASP.NET Core 5.0 的新功能</span><span class="sxs-lookup"><span data-stu-id="7b2ac-103">What's new in ASP.NET Core 5.0</span></span>

<span data-ttu-id="7b2ac-104">本文將重點說明 ASP.NET Core 5.0 中最重要的變更，以及相關檔的連結。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-104">This article highlights the most significant changes in ASP.NET Core 5.0 with links to relevant documentation.</span></span>

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a><span data-ttu-id="7b2ac-105">ASP.NET Core MVC 和 Razor 改進</span><span class="sxs-lookup"><span data-stu-id="7b2ac-105">ASP.NET Core MVC and Razor improvements</span></span>

### <a name="model-binding-datetime-as-utc"></a><span data-ttu-id="7b2ac-106">模型系結日期時間（UTC）</span><span class="sxs-lookup"><span data-stu-id="7b2ac-106">Model binding DateTime as UTC</span></span>

<span data-ttu-id="7b2ac-107">模型系結現在支援將 UTC 時間字串系結至 `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-107">Model binding now supports binding UTC time strings to `DateTime`.</span></span> <span data-ttu-id="7b2ac-108">如果要求包含 UTC 時間字串，則模型系結會將它系結至 UTC `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-108">If the request contains a UTC time string, model binding binds it to a UTC `DateTime`.</span></span> <span data-ttu-id="7b2ac-109">例如，下列時間字串系結 UTC `DateTime` ： `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span><span class="sxs-lookup"><span data-stu-id="7b2ac-109">For example, the following time string is bound the UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span></span>

### <a name="model-binding-and-validation-with-c-9-record-types"></a><span data-ttu-id="7b2ac-110">使用 c # 9 記錄類型進行模型系結和驗證</span><span class="sxs-lookup"><span data-stu-id="7b2ac-110">Model binding and validation with C# 9 record types</span></span>

<span data-ttu-id="7b2ac-111">[C # 9 記錄類型](/dotnet/csharp/whats-new/csharp-9#record-types) 可以搭配 MVC 控制器或頁面中的模型系結使用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-111">[C# 9 record types](/dotnet/csharp/whats-new/csharp-9#record-types) can be used with model binding in an MVC controller or a Razor Page.</span></span> <span data-ttu-id="7b2ac-112">記錄類型是在網路上傳送資料模型的好方式。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-112">Record types are a good way to model data being transmitted over the network.</span></span>

<span data-ttu-id="7b2ac-113">例如，下列 `PersonController` 使用 `Person` 具有模型系結和表單驗證的記錄類型：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-113">For example, the following `PersonController` uses the `Person` record type with model binding and form validation:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

<span data-ttu-id="7b2ac-114">`Person/Index.cshtml` 檔案：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-114">The `Person/Index.cshtml` file:</span></span>

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a><span data-ttu-id="7b2ac-115">DynamicRouteValueTransformer 的改善</span><span class="sxs-lookup"><span data-stu-id="7b2ac-115">Improvements to DynamicRouteValueTransformer</span></span>

<span data-ttu-id="7b2ac-116">ASP.NET Core 3.1 引進 <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> 了一種方式，可讓您使用自訂端點，以動態方式選取 MVC 控制器動作或 Razor 頁面。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-116">ASP.NET Core 3.1 introduced <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> as a way to use custom endpoint to dynamically select an MVC controller action or a Razor page.</span></span> <span data-ttu-id="7b2ac-117">ASP.NET Core 5.0 應用程式可以將狀態傳遞至 `DynamicRouteValueTransformer` ，並篩選所選擇的端點集合。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-117">ASP.NET Core 5.0 apps can pass state to a `DynamicRouteValueTransformer` and filter the set of endpoints chosen.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="7b2ac-118">其他</span><span class="sxs-lookup"><span data-stu-id="7b2ac-118">Miscellaneous</span></span>

* <span data-ttu-id="7b2ac-119">[[比較]](xref:System.ComponentModel.DataAnnotations.CompareAttribute)屬性可以套用至頁面模型上的屬性 Razor 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-119">The [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute can be applied to properties on a Razor Page model.</span></span>
* <span data-ttu-id="7b2ac-120">預設會將系結的參數和屬性視為必要。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-120">Parameters and properties bound from the body are considered required by default.</span></span> <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a><span data-ttu-id="7b2ac-121">Web API</span><span class="sxs-lookup"><span data-stu-id="7b2ac-121">Web API</span></span>

### <a name="openapi-specification-on-by-default"></a><span data-ttu-id="7b2ac-122">預設為開啟 OpenAPI 規格</span><span class="sxs-lookup"><span data-stu-id="7b2ac-122">OpenAPI Specification on by default</span></span>

<span data-ttu-id="7b2ac-123">[OpenAPI 規格](http://spec.openapis.org/oas/v3.0.3) 是一種業界標準，用於描述 HTTP api，並將其整合至複雜的商務程式或協力廠商。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-123">[OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3) is an industry standard for describing HTTP APIs and integrating them into complex business processes or with third parties.</span></span> <span data-ttu-id="7b2ac-124">所有雲端提供者和許多 API 登錄都廣泛支援 OpenAPI。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-124">OpenAPI is widely supported by all cloud providers and many API registries.</span></span> <span data-ttu-id="7b2ac-125">從 web Api 發出 OpenAPI 檔的應用程式有各種新的機會，可讓您使用這些 Api。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-125">Apps that emit OpenAPI documents from web APIs have a variety of new opportunities in which those APIs can be used.</span></span> <span data-ttu-id="7b2ac-126">與開放原始碼專案 [Swashbuckle](https://www.nuget.org/packages/Swashbuckle.AspNetCore/)的維護人員合作，ASP.NET Core API 範本包含 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore)的 NuGet 相依性。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-126">In partnership with the maintainers of the open-source project [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), the ASP.NET Core API template contains a NuGet dependency on [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="7b2ac-127">Swashbuckle 是常用的開放原始碼 NuGet 套件，可動態發出 OpenAPI 的檔。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-127">Swashbuckle is a popular open-source NuGet package that emits OpenAPI documents dynamically.</span></span> <span data-ttu-id="7b2ac-128">Swashbuckle 藉由 introspecting API 控制器，並在執行時間產生 OpenAPI 檔，或在組建階段使用 Swashbuckle CLI 來執行此工作。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-128">Swashbuckle does this by introspecting over the API controllers and generating the OpenAPI document at run-time, or at build time using the Swashbuckle CLI.</span></span>

<span data-ttu-id="7b2ac-129">在 ASP.NET Core 5.0 中，web API 範本預設會啟用 OpenAPI 支援。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-129">In ASP.NET Core 5.0, the web API templates enable the OpenAPI support by default.</span></span> <span data-ttu-id="7b2ac-130">若要停用 OpenAPI：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-130">To disable OpenAPI:</span></span>

* <span data-ttu-id="7b2ac-131">從命令列：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-131">From the command line:</span></span>

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* <span data-ttu-id="7b2ac-132">從 Visual Studio：取消核取 [ **啟用 OpenAPI 支援** ]。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-132">From Visual Studio: Uncheck **Enable OpenAPI support**.</span></span>

<span data-ttu-id="7b2ac-133">針對 web API 專案所建立的所有 *.csproj* 檔案都包含 [Swashbuckle. AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet 套件參考。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-133">All *.csproj* files created for web API projects contain the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet package reference.</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

<span data-ttu-id="7b2ac-134">範本產生的程式碼包含中 `Startup.ConfigureServices` 的程式碼，可啟用 OpenAPI 檔產生：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-134">The template generated code contains code in `Startup.ConfigureServices` that activates OpenAPI document generation:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

<span data-ttu-id="7b2ac-135">`Startup.Configure`方法會新增 Swashbuckle 中介軟體，以啟用：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-135">The `Startup.Configure` method adds the Swashbuckle middleware, which enables the:</span></span>

* <span data-ttu-id="7b2ac-136">檔產生程式。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-136">Document generation process.</span></span>
* <span data-ttu-id="7b2ac-137">在開發模式中，預設為 Swagger UI 頁面。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-137">Swagger UI page by default in development mode.</span></span>

<span data-ttu-id="7b2ac-138">範本產生的程式碼在發行至生產環境時，不會意外地公開 API 的描述。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-138">The template generated code won't accidentally expose the API's description when publishing to production.</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a><span data-ttu-id="7b2ac-139">Azure API 管理匯入</span><span class="sxs-lookup"><span data-stu-id="7b2ac-139">Azure API Management Import</span></span>

<span data-ttu-id="7b2ac-140">ASP.NET Core API 專案啟用 OpenAPI 時，Visual Studio 2019 16.8 版和更新版本的發行會自動在發佈流程中提供額外的步驟。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-140">When ASP.NET Core API projects enable OpenAPI, the Visual Studio 2019 version 16.8 and later publishing automatically offer an additional step in the publishing flow.</span></span> <span data-ttu-id="7b2ac-141">使用 [AZURE API 管理](xref:tutorials/publish-to-azure-api-management-using-vs) 的開發人員有機會在發佈流程期間，自動將 api 匯入至 Azure api 管理：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-141">Developers who use [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) have an opportunity to automatically import the APIs into Azure API Management during the publish flow:</span></span>

![Azure API 管理匯入與發佈](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a><span data-ttu-id="7b2ac-143">更好的 web API 專案啟動體驗</span><span class="sxs-lookup"><span data-stu-id="7b2ac-143">Better launch experience for web API projects</span></span>

<span data-ttu-id="7b2ac-144">OpenAPI 預設為啟用，web API 開發人員的應用程式啟動體驗 (F5) 可大幅改善。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-144">With OpenAPI enabled by default, the app launching experience (F5) for web API developers significantly improves.</span></span> <span data-ttu-id="7b2ac-145">使用 ASP.NET Core 5.0 時，會預先設定 web API 範本以載入 Swagger UI 頁面。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-145">With ASP.NET Core 5.0, the web API template comes pre-configured to load up the Swagger UI page.</span></span> <span data-ttu-id="7b2ac-146">Swagger UI 頁面提供針對已發佈 API 新增的檔，並可讓您按一下就能測試 Api。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-146">The Swagger UI page provides both the documentation added for the published API, and enables testing the APIs with a single click.</span></span>

![swagger/index.html 視圖](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a><span data-ttu-id="7b2ac-148">效能改進</span><span class="sxs-lookup"><span data-stu-id="7b2ac-148">Performance improvements</span></span>

<span data-ttu-id="7b2ac-149">針對 .NET 5，我們對執行時間效能進行了大幅改善， Blazor WebAssembly 並將焦點放在複雜的 UI 轉譯和 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-149">For .NET 5, we made significant improvements to Blazor WebAssembly runtime performance with a specific focus on complex UI rendering and JSON serialization.</span></span> <span data-ttu-id="7b2ac-150">在我們的效能測試中， Blazor WebAssembly 在大部分情況下，.net 5 的速度會快二到三倍。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-150">In our performance tests, Blazor WebAssembly in .NET 5 is two to three times faster for most scenarios.</span></span> <span data-ttu-id="7b2ac-151">如需詳細資訊，請參閱 [ASP.NET Blog： .net 5 候選版1中的 ASP.NET Core 更新](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements)。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-151">For more information, see [ASP.NET Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span></span>

### <a name="css-isolation"></a><span data-ttu-id="7b2ac-152">CSS 隔離</span><span class="sxs-lookup"><span data-stu-id="7b2ac-152">CSS isolation</span></span>

<span data-ttu-id="7b2ac-153">Blazor 現在支援定義範圍設定為特定元件的 CSS 樣式。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-153">Blazor now supports defining CSS styles that are scoped to a given component.</span></span> <span data-ttu-id="7b2ac-154">元件專屬的 CSS 樣式可讓您更輕鬆地瞭解應用程式中的樣式，並避免全域樣式的意外副作用。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-154">Component-specific CSS styles make it easier to reason about the styles in an app and to avoid unintentional side effects of global styles.</span></span> <span data-ttu-id="7b2ac-155">如需詳細資訊，請參閱<xref:blazor/components/css-isolation>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-155">For more information, see <xref:blazor/components/css-isolation>.</span></span>

### <a name="new-inputfile-component"></a><span data-ttu-id="7b2ac-156">新增 `InputFile` 元件</span><span class="sxs-lookup"><span data-stu-id="7b2ac-156">New `InputFile` component</span></span>

<span data-ttu-id="7b2ac-157">`InputFile`元件允許讀取使用者所選取的一或多個檔案進行上傳。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-157">The `InputFile` component allows reading one or more files selected by a user for upload.</span></span> <span data-ttu-id="7b2ac-158">如需詳細資訊，請參閱<xref:blazor/file-uploads>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-158">For more information, see <xref:blazor/file-uploads>.</span></span>

### <a name="new-inputradio-and-inputradiogroup-components"></a><span data-ttu-id="7b2ac-159">新 `InputRadio` 的和 `InputRadioGroup` 元件</span><span class="sxs-lookup"><span data-stu-id="7b2ac-159">New `InputRadio` and `InputRadioGroup` components</span></span>

<span data-ttu-id="7b2ac-160">Blazor 具有內建 `InputRadio` 和 `InputRadioGroup` 元件，可簡化資料系結至具有整合式驗證的選項按鈕群組。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-160">Blazor has built-in `InputRadio` and `InputRadioGroup` components that simplify data binding to radio button groups with integrated validation.</span></span> <span data-ttu-id="7b2ac-161">如需詳細資訊，請參閱<xref:blazor/forms-validation>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-161">For more information, see <xref:blazor/forms-validation>.</span></span>

### <a name="component-virtualization"></a><span data-ttu-id="7b2ac-162">元件虛擬化</span><span class="sxs-lookup"><span data-stu-id="7b2ac-162">Component virtualization</span></span>

<span data-ttu-id="7b2ac-163">使用 Blazor 架構內建的虛擬化支援，改善元件轉譯的認知效能。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-163">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="7b2ac-164">如需詳細資訊，請參閱<xref:blazor/forms-validation#radio-buttons>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-164">For more information, see <xref:blazor/forms-validation#radio-buttons>.</span></span>

### <a name="ontoggle-event-support"></a><span data-ttu-id="7b2ac-165">`ontoggle` 事件支援</span><span class="sxs-lookup"><span data-stu-id="7b2ac-165">`ontoggle` event support</span></span>

<span data-ttu-id="7b2ac-166">Blazor 事件現在支援 `ontoggle` DOM 事件。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-166">Blazor events now support the `ontoggle` DOM event.</span></span> <span data-ttu-id="7b2ac-167">如需詳細資訊，請參閱<xref:blazor/components/event-handling#event-argument-types>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-167">For more information, see <xref:blazor/components/event-handling#event-argument-types>.</span></span>

### <a name="set-ui-focus-in-no-locblazor-apps"></a><span data-ttu-id="7b2ac-168">在應用程式中設定 UI 焦點 Blazor</span><span class="sxs-lookup"><span data-stu-id="7b2ac-168">Set UI focus in Blazor apps</span></span>

<span data-ttu-id="7b2ac-169">`FocusAsync`在元素參考上使用便利方法，以將 UI 焦點設定為該專案。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-169">Use the `FocusAsync` convenience method on element references to set the UI focus to that element.</span></span> <span data-ttu-id="7b2ac-170">如需詳細資訊，請參閱<xref:blazor/components/event-handling#focus-an-element>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-170">For more information, see <xref:blazor/components/event-handling#focus-an-element>.</span></span>

### <a name="custom-validation-class-attributes"></a><span data-ttu-id="7b2ac-171">自訂驗證類別屬性</span><span class="sxs-lookup"><span data-stu-id="7b2ac-171">Custom validation class attributes</span></span>

<span data-ttu-id="7b2ac-172">在與 CSS 架構（例如啟動程式）整合時，自訂驗證類別名稱很有用。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-172">Custom validation class names are useful when integrating with CSS frameworks, such as Bootstrap.</span></span> <span data-ttu-id="7b2ac-173">如需詳細資訊，請參閱<xref:blazor/forms-validation#custom-validation-class-attributes>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-173">For more information, see <xref:blazor/forms-validation#custom-validation-class-attributes>.</span></span>

### <a name="iasyncdisposable-support"></a><span data-ttu-id="7b2ac-174">System.iasyncdisposable 支援</span><span class="sxs-lookup"><span data-stu-id="7b2ac-174">IAsyncDisposable support</span></span>

<span data-ttu-id="7b2ac-175">Blazor 元件現在支援已配置 <xref:System.IAsyncDisposable> 資源之非同步版本的介面。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-175">Blazor components now support the <xref:System.IAsyncDisposable> interface for the asynchronous release of allocated resources.</span></span>

### <a name="javascript-isolation-and-object-references"></a><span data-ttu-id="7b2ac-176">JavaScript 隔離和物件參考</span><span class="sxs-lookup"><span data-stu-id="7b2ac-176">JavaScript isolation and object references</span></span>

<span data-ttu-id="7b2ac-177">Blazor 啟用標準 [javascript 模組](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules)中的 JavaScript 隔離。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-177">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="7b2ac-178">如需詳細資訊，請參閱<xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-178">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

### <a name="form-components-support-display-name"></a><span data-ttu-id="7b2ac-179">表單元件支援顯示名稱</span><span class="sxs-lookup"><span data-stu-id="7b2ac-179">Form components support display name</span></span>

<span data-ttu-id="7b2ac-180">下列內建元件支援使用參數顯示名稱 `DisplayName` ：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-180">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* `InputDate`
* `InputNumber`
* `InputSelect`

<span data-ttu-id="7b2ac-181">如需詳細資訊，請參閱<xref:blazor/forms-validation#display-name-support>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-181">For more information, see <xref:blazor/forms-validation#display-name-support>.</span></span>

### <a name="catch-all-route-parameters"></a><span data-ttu-id="7b2ac-182">Catch-all 路由參數</span><span class="sxs-lookup"><span data-stu-id="7b2ac-182">Catch-all route parameters</span></span>

<span data-ttu-id="7b2ac-183">元件中支援攔截所有路由參數，這些參數會跨多個資料夾界限來捕捉路徑。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-183">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="7b2ac-184">如需詳細資訊，請參閱<xref:blazor/fundamentals/routing#catch-all-route-parameters>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-184">For more information, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="debugging-improvements"></a><span data-ttu-id="7b2ac-185">偵錯改進</span><span class="sxs-lookup"><span data-stu-id="7b2ac-185">Debugging improvements</span></span>

<span data-ttu-id="7b2ac-186">Blazor WebAssemblyASP.NET Core 5.0 中已改善偵錯工具的功能。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-186">Debugging Blazor WebAssembly apps is improved in ASP.NET Core 5.0.</span></span> <span data-ttu-id="7b2ac-187">此外，Visual Studio for Mac 上現在支援偵錯工具。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-187">Additionally, debugging is now supported on Visual Studio for Mac.</span></span> <span data-ttu-id="7b2ac-188">如需詳細資訊，請參閱<xref:blazor/debug>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-188">For more information, see <xref:blazor/debug>.</span></span>

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a><span data-ttu-id="7b2ac-189">Microsoft v2.0 Identity 和 MSAL 2.0 版</span><span class="sxs-lookup"><span data-stu-id="7b2ac-189">Microsoft Identity v2.0 and MSAL v2.0</span></span>

<span data-ttu-id="7b2ac-190">Blazor 安全性現在使用 Microsoft v2.0 Identity ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) 以及 [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) 和 MSAL 2.0 版。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-190">Blazor security now uses Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) and [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) and MSAL v2.0.</span></span> <span data-ttu-id="7b2ac-191">如需詳細資訊，請參閱[ Blazor 安全性和 Identity 節點](xref:blazor/security/index)中的主題。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-191">For more information, see the topics in the [Blazor Security and Identity node](xref:blazor/security/index).</span></span>

### <a name="protected-browser-storage-for-no-locblazor-server"></a><span data-ttu-id="7b2ac-192">受保護的瀏覽器存放裝置 Blazor Server</span><span class="sxs-lookup"><span data-stu-id="7b2ac-192">Protected Browser Storage for Blazor Server</span></span>

<span data-ttu-id="7b2ac-193">Blazor Server 應用程式現在可以使用內建的支援，在瀏覽器中儲存應用程式狀態，以防止使用 ASP.NET Core 資料保護進行篡改。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-193">Blazor Server apps can now use built-in support for storing app state in the browser that has been protected from tampering using ASP.NET Core data protection.</span></span> <span data-ttu-id="7b2ac-194">資料可以儲存在本機瀏覽器儲存體或會話儲存體中。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-194">Data can be stored in either local browser storage or session storage.</span></span> <span data-ttu-id="7b2ac-195">如需詳細資訊，請參閱<xref:blazor/state-management>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-195">For more information, see <xref:blazor/state-management>.</span></span>

### <a name="no-locblazor-webassembly-prerendering"></a><span data-ttu-id="7b2ac-196">Blazor WebAssembly 預</span><span class="sxs-lookup"><span data-stu-id="7b2ac-196">Blazor WebAssembly prerendering</span></span>

<span data-ttu-id="7b2ac-197">跨裝載模型改善了元件整合，而 Blazor WebAssembly 應用程式現在可以在伺服器上呈現輸出。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-197">Component integration is improved across hosting models, and Blazor WebAssembly apps can now prerender output on the server.</span></span> <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a><span data-ttu-id="7b2ac-198">修剪/連結改進</span><span class="sxs-lookup"><span data-stu-id="7b2ac-198">Trimming/linking improvements</span></span>

<span data-ttu-id="7b2ac-199">Blazor WebAssembly 在組建期間執行中繼語言 (IL) 修剪/連結，以從應用程式的輸出元件中修剪不必要的 IL。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-199">Blazor WebAssembly performs Intermediate Language (IL) trimming/linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="7b2ac-200">在 ASP.NET Core 5.0 的版本中，會 Blazor WebAssembly 使用其他設定選項來執行改良的修剪。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-200">With the release of ASP.NET Core 5.0, Blazor WebAssembly performs improved trimming with additional configuration options.</span></span> <span data-ttu-id="7b2ac-201">如需詳細資訊，請參閱 <xref:blazor/host-and-deploy/configure-trimmer> 和 [修剪選項](/dotnet/core/deploying/trimming-options)。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-201">For more information, see <xref:blazor/host-and-deploy/configure-trimmer> and [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

### <a name="browser-compatibility-analyzer"></a><span data-ttu-id="7b2ac-202">瀏覽器相容性分析器</span><span class="sxs-lookup"><span data-stu-id="7b2ac-202">Browser compatibility analyzer</span></span>

<span data-ttu-id="7b2ac-203">Blazor WebAssembly 應用程式會以完整的 .NET API 介面區為目標，但由於瀏覽器沙箱條件約束，WebAssembly 並不支援所有的 .NET Api。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-203">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="7b2ac-204"><xref:System.PlatformNotSupportedException>在 WebAssembly 上執行時，不支援的 api 會擲回。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-204">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="7b2ac-205">當應用程式使用應用程式的目標平臺不支援的 Api 時，平臺相容性分析器會警告開發人員。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-205">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="7b2ac-206">如需詳細資訊，請參閱<xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-206">For more information, see <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="7b2ac-207">延遲載入元件</span><span class="sxs-lookup"><span data-stu-id="7b2ac-207">Lazy load assemblies</span></span>

<span data-ttu-id="7b2ac-208">Blazor WebAssembly 應用程式啟動效能可透過延後載入部分應用程式元件來改善，直到需要它們為止。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-208">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they're required.</span></span> <span data-ttu-id="7b2ac-209">如需詳細資訊，請參閱<xref:blazor/webassembly-lazy-load-assemblies>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-209">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="updated-globalization-support"></a><span data-ttu-id="7b2ac-210">更新的全球化支援</span><span class="sxs-lookup"><span data-stu-id="7b2ac-210">Updated globalization support</span></span>

<span data-ttu-id="7b2ac-211">全球化支援可 Blazor WebAssembly 根據 Unicode (ICU) 的國際元件來提供。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-211">Globalization support is available for Blazor WebAssembly based on International Components for Unicode (ICU).</span></span> <span data-ttu-id="7b2ac-212">如需詳細資訊，請參閱<xref:blazor/globalization-localization>。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-212">For more information, see <xref:blazor/globalization-localization>.</span></span>

## <a name="grpc"></a><span data-ttu-id="7b2ac-213">gRPC</span><span class="sxs-lookup"><span data-stu-id="7b2ac-213">gRPC</span></span>

<span data-ttu-id="7b2ac-214">已在 [gRPC](https://grpc.io/)中進行許多效能改進。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-214">Many preformance improvements have been made in [gRPC](https://grpc.io/).</span></span> <span data-ttu-id="7b2ac-215">如需詳細資訊，請參閱 [.net 5 中的 gRPC 效能改進](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/)。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-215">For more information, see [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span></span>

<span data-ttu-id="7b2ac-216">如需 gRPC 的詳細資訊，請參閱 <xref:grpc/index> 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-216">For more gRPC information, see <xref:grpc/index>.</span></span>

## SignalR

### <a name="no-locsignalr-hub-filters"></a><span data-ttu-id="7b2ac-217">SignalR 中樞篩選</span><span class="sxs-lookup"><span data-stu-id="7b2ac-217">SignalR Hub filters</span></span>

<span data-ttu-id="7b2ac-218">SignalR 中樞篩選（在 ASP.NET 中稱為中樞管線 SignalR ）是一項功能，可讓程式碼在呼叫中樞方法之前和之後執行。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-218">SignalR Hub filters, called Hub pipelines in ASP.NET SignalR, is a feature that allows code to run before and after Hub methods are called.</span></span> <span data-ttu-id="7b2ac-219">呼叫中樞方法之前和之後執行程式碼，類似于中介軟體在 HTTP 要求之前和之後執行程式碼的能力。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-219">Running code before and after Hub methods are called is similar to how middleware has the ability to run code before and after an HTTP request.</span></span> <span data-ttu-id="7b2ac-220">常見用途包括記錄、錯誤處理和引數驗證。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-220">Common uses include logging, error handling, and argument validation.</span></span>

<span data-ttu-id="7b2ac-221">如需詳細資訊，請參閱[使用 ASP.NET Core SignalR 中的中樞篩選](xref:signalr/hub-filters)。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-221">For more information, see [Use hub filters in ASP.NET Core SignalR](xref:signalr/hub-filters).</span></span>

### <a name="no-locsignalr-parallel-hub-invocations"></a><span data-ttu-id="7b2ac-222">SignalR 平行中樞調用</span><span class="sxs-lookup"><span data-stu-id="7b2ac-222">SignalR parallel hub invocations</span></span>

<span data-ttu-id="7b2ac-223">ASP.NET Core SignalR 現在能夠處理平行中樞調用。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-223">ASP.NET Core SignalR is now capable of handling parallel hub invocations.</span></span> <span data-ttu-id="7b2ac-224">您可以變更預設行為，讓用戶端一次叫用一個以上的中樞方法：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-224">The default behavior can be changed to allow clients to invoke more than one hub method at a time:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a><span data-ttu-id="7b2ac-225">已新增 SignalR JAVA 用戶端的 Messagepack 支援</span><span class="sxs-lookup"><span data-stu-id="7b2ac-225">Added Messagepack support in SignalR Java client</span></span>

<span data-ttu-id="7b2ac-226">新的封裝 [signalr messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack)會將 messagepack 支援新增至 SignalR JAVA 用戶端。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-226">A new package, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adds MessagePack support to the SignalR Java client.</span></span> <span data-ttu-id="7b2ac-227">若要使用 MessagePack 中樞通訊協定，請新增 `.withHubProtocol(new MessagePackHubProtocol())` 至連接產生器：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-227">To use the MessagePack hub protocol, add `.withHubProtocol(new MessagePackHubProtocol())` to the connection builder:</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* <span data-ttu-id="7b2ac-228">透過設定可重載端點： Kestrel 可以偵測傳遞給[ KestrelServerOptions.Configu) ](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A)的設定變更，以及系結至新的端點，而不需要在參數為時重新開機應用程式 `reloadOnChange` `true` 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-228">Reloadable endpoints via configuration: Kestrel can detect changes to configuration passed to [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) and unbind from existing endpoints and bind to new endpoints without requiring an application restart when the `reloadOnChange` parameter is `true`.</span></span> <span data-ttu-id="7b2ac-229">使用或時，預設會系結 <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> Kestrel 至 [ Kestrel ](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) `reloadOnChange` 已啟用的「」設定子區段。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-229">By default when using <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> or <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, Kestrel binds to the ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) configuration subsection with `reloadOnChange` enabled.</span></span> <span data-ttu-id="7b2ac-230">應用程式必須 `reloadOnChange: true` 在 `KestrelServerOptions.Configure` 手動呼叫以取得可重載端點時傳遞。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-230">Apps must pass `reloadOnChange: true` when calling `KestrelServerOptions.Configure` manually to get reloadable endpoints.</span></span>
* <span data-ttu-id="7b2ac-231">HTTP/2 回應標頭改善。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-231">HTTP/2 response headers improvements.</span></span> <span data-ttu-id="7b2ac-232">如需詳細資訊，請參閱下一節中的 [效能改進](#performance-improvements) 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-232">For more information, see [Performance improvements](#performance-improvements) in the next section.</span></span>
* <span data-ttu-id="7b2ac-233">支援通訊端傳輸中的其他端點類型：加入至引進的新 API <xref:System.Net.Sockets?displayProperty=nameWithType> ，中的通訊端預設傳輸 [Kestrel](xref:fundamentals/servers/kestrel) 允許系結至現有的檔案控制代碼和 Unix 網域通訊端。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-233">Support for additional endpoints types in the sockets transport: Adding to the new API introduced in <xref:System.Net.Sockets?displayProperty=nameWithType>, the sockets default transport in [Kestrel](xref:fundamentals/servers/kestrel) allows binding to both existing file handles and Unix domain sockets.</span></span> <span data-ttu-id="7b2ac-234">支援系結至現有的檔案控制代碼可讓您使用現有的 `Systemd` 整合，而不需要 `libuv` 傳輸。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-234">Support for binding to existing file handles enables using the existing `Systemd` integration without requiring the `libuv` transport.</span></span>
* <span data-ttu-id="7b2ac-235">中的自訂標頭解碼 Kestrel ：應用程式可以指定要 <xref:System.Text.Encoding> 根據標頭名稱（而非預設為 utf-8）來解讀傳入標頭。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-235">Custom header decoding in Kestrel: Apps can specify which <xref:System.Text.Encoding> to use to interpret incoming headers based on the header name instead of defaulting to UTF-8.</span></span> <span data-ttu-id="7b2ac-236">設定</span><span class="sxs-lookup"><span data-stu-id="7b2ac-236">Set the</span></span> <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> <span data-ttu-id="7b2ac-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` 屬性，指定要使用的編碼：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` property to specify which encoding to use:</span></span>
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a><span data-ttu-id="7b2ac-238">Kestrel 透過設定的端點特定選項</span><span class="sxs-lookup"><span data-stu-id="7b2ac-238">Kestrel endpoint-specific options via configuration</span></span>

<span data-ttu-id="7b2ac-239">已新增支援，可透過設定來設定 Kestrel 的端點特定[configuration](xref:fundamentals/configuration/index)選項。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-239">Support has been added for configuring Kestrel’s endpoint-specific options via [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="7b2ac-240">端點特定的設定包括：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-240">The endpoint-specific configurations includes the:</span></span>

* <span data-ttu-id="7b2ac-241">使用的 HTTP 通訊協定</span><span class="sxs-lookup"><span data-stu-id="7b2ac-241">HTTP protocols used</span></span>
* <span data-ttu-id="7b2ac-242">使用的 TLS 通訊協定</span><span class="sxs-lookup"><span data-stu-id="7b2ac-242">TLS protocols used</span></span>
* <span data-ttu-id="7b2ac-243">選取的憑證</span><span class="sxs-lookup"><span data-stu-id="7b2ac-243">Certificate selected</span></span>
* <span data-ttu-id="7b2ac-244">用戶端憑證模式</span><span class="sxs-lookup"><span data-stu-id="7b2ac-244">Cient certificate mode</span></span>

<span data-ttu-id="7b2ac-245">設定可讓您根據指定的伺服器名稱來指定所選取的憑證。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-245">Configuration allows specifying which certificate is selected based on the specified server name.</span></span> <span data-ttu-id="7b2ac-246">伺服器名稱屬於 TLS 通訊協定伺服器名稱指示 (SNI) 擴充功能的一部分，如用戶端所指示。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-246">The server name is part of the Server Name Indication (SNI) extension to the TLS protocol as indicated by the client.</span></span> <span data-ttu-id="7b2ac-247">Kestrel的設定也支援主機名稱中的萬用字元前置詞。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-247">Kestrel's configuration also supports a wildcard prefix in the host name.</span></span>

<span data-ttu-id="7b2ac-248">下列範例示範如何使用設定檔來指定端點專用：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-248">The following example shows how to specify endpoint-specific using a configuration file:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

<span data-ttu-id="7b2ac-249">伺服器名稱指示 (SNI) 是 TLS 延伸模組，可將虛擬網域納入 SSL 協商的一部分。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-249">Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="7b2ac-250">這實際上是指虛擬功能變數名稱（或主機名稱）可以用來識別網路端點。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-250">What this effectively means is that the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="7b2ac-251">效能改進</span><span class="sxs-lookup"><span data-stu-id="7b2ac-251">Performance improvements</span></span>

### <a name="http2"></a><span data-ttu-id="7b2ac-252">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="7b2ac-252">HTTP/2</span></span>

* <span data-ttu-id="7b2ac-253">大幅減少 HTTP/2 程式碼路徑中的配置。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-253">Significant reductions in allocations in the HTTP/2 code path.</span></span>
* <span data-ttu-id="7b2ac-254">支援在中 HPack HTTP/2 回應標頭的 [動態壓縮](https://tools.ietf.org/html/rfc7541) [Kestrel](xref:fundamentals/servers/kestrel) 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-254">Support for [HPack dynamic compression](https://tools.ietf.org/html/rfc7541) of HTTP/2 response headers in [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="7b2ac-255">如需詳細資訊，請參閱 [標頭資料表大小](xref:fundamentals/servers/kestrel#header-table-size) 和 [HPACK： HTTP/2 的無訊息無訊息 (功能) ](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/)。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-255">For more information, see [Header table size](xref:fundamentals/servers/kestrel#header-table-size) and [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span></span>
* <span data-ttu-id="7b2ac-256">傳送 HTTP/2 偵測框架： HTTP/2 有傳送偵測框架的機制，以確保閒置的連接仍可正常運作。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-256">Sending HTTP/2 PING frames: HTTP/2 has a mechanism for sending PING frames to ensure an idle connection is still functional.</span></span> <span data-ttu-id="7b2ac-257">當處理經常閒置但只間歇性看到活動（例如 gRPC 串流）的長期資料流程時，確保可行的連接特別有用。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-257">Ensuring a viable connection is especially useful when working with long-lived streams that are often idle but only intermittently see activity, for example, gRPC streams.</span></span> <span data-ttu-id="7b2ac-258">應用程式可以在中設定限制來傳送定期偵測框架 [Kestrel](xref:fundamentals/servers/kestrel) <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> ：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-258">Apps can send periodic PING frames in [Kestrel](xref:fundamentals/servers/kestrel) by setting limits on <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>:</span></span>

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a><span data-ttu-id="7b2ac-259">容器</span><span class="sxs-lookup"><span data-stu-id="7b2ac-259">Containers</span></span>

<span data-ttu-id="7b2ac-260">在 .NET 5.0 之前，為 ASP.NET Core 應用程式建立和發佈 *Dockerfile* 需要提取整個 .NET Core SDK 和 ASP.NET Core 映射。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-260">Prior to .NET 5.0, building and publishing a *Dockerfile* for an ASP.NET Core app required pulling the entire .NET Core SDK and the ASP.NET Core image.</span></span> <span data-ttu-id="7b2ac-261">在此版本中，會減少提取 SDK 映射位元組，並大幅消除 ASP.NET Core 映射提取的位元組。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-261">With this release, pulling the SDK images bytes is reduced and the bytes pulled for the ASP.NET Core image is largely eliminated.</span></span> <span data-ttu-id="7b2ac-262">如需詳細資訊，請參閱 [此 GitHub 問題批註](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750)。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-262">For more information, see [this GitHub issue comment](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="7b2ac-263">驗證和授權</span><span class="sxs-lookup"><span data-stu-id="7b2ac-263">Authentication and authorization</span></span>

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a><span data-ttu-id="7b2ac-264">使用 Microsoft 驗證 Identity Azure Active Directory。Web</span><span class="sxs-lookup"><span data-stu-id="7b2ac-264">Azure Active Directory authentication with Microsoft.Identity.Web</span></span>

<span data-ttu-id="7b2ac-265">ASP.NET Core 的專案範本現在會與整合， <xref:Microsoft.Identity.Web?displayProperty=fullName> 以處理 [Azure 活動目錄](/azure/active-directory/fundamentals/active-directory-whatis) 的驗證， (Azure AD) 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-265">The ASP.NET Core project templates now integrate with <xref:Microsoft.Identity.Web?displayProperty=fullName> to handle authentication with [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span></span> <span data-ttu-id="7b2ac-266">[Microsoft ... IdentityWeb 套件](https://www.nuget.org/packages/Microsoft.Identity.Web/)提供：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-266">The [Microsoft.Identity.Web package](https://www.nuget.org/packages/Microsoft.Identity.Web/) provides:</span></span>

* <span data-ttu-id="7b2ac-267">透過 Azure AD 進行驗證的更佳體驗。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-267">A better experience for authentication through Azure AD.</span></span>
* <span data-ttu-id="7b2ac-268">代表您的使用者輕鬆存取 Azure 資源的方式，包括 [Microsoft Graph](/graph/overview)。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-268">An easier way to access Azure resources on behalf of your users, including [Microsoft Graph](/graph/overview).</span></span> <span data-ttu-id="7b2ac-269">請參閱 [Microsoft ... IdentityWeb 範例](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)，使用 Azure api、使用 Microsoft Graph 和保護您自己的 api，從基本登入開始，並透過多租使用者前進。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-269">See the [Microsoft.Identity.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), which starts with a basic login and advances through multi-tenancy, using Azure APIs, using Microsoft Graph, and protecting your own APIs.</span></span> <span data-ttu-id="7b2ac-270">`Microsoft.Identity.Web` 可與 .NET 5 一起使用。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-270">`Microsoft.Identity.Web` is available alongside .NET 5.</span></span>

### <a name="allow-anonymous-access-to-an-endpoint"></a><span data-ttu-id="7b2ac-271">允許匿名存取端點</span><span class="sxs-lookup"><span data-stu-id="7b2ac-271">Allow anonymous access to an endpoint</span></span>

<span data-ttu-id="7b2ac-272">`AllowAnonymous`擴充方法允許匿名存取端點：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-272">The `AllowAnonymous` extension method allows anonymous access to an endpoint:</span></span>

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a><span data-ttu-id="7b2ac-273">授權失敗的自訂處理</span><span class="sxs-lookup"><span data-stu-id="7b2ac-273">Custom handling of authorization failures</span></span>

<span data-ttu-id="7b2ac-274">透過[授權](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A)[中介軟體](xref:fundamentals/middleware/index)所叫用的新[IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs)介面，現在可以更輕鬆地自訂授權失敗的處理。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-274">Custom handling of authorization failures is now easier with the new [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) interface that is invoked by the [authorization](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="7b2ac-275">預設的執行會維持不變，但自訂處理常式可以在 [相依性插入] 中註冊，以根據授權失敗的原因來允許自訂 HTTP 回應。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-275">The default implementation remains the same, but a custom handler can be registered in [Dependency injection, which allows custom HTTP responses based on why authorization failed.</span></span> <span data-ttu-id="7b2ac-276">請參閱 [此範例](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) ，以示範的使用方式 `IAuthorizationMiddlewareResultHandler` 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-276">See [this sample](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) that demonstrates usage of the `IAuthorizationMiddlewareResultHandler`.</span></span>

### <a name="authorization-when-using-endpoint-routing"></a><span data-ttu-id="7b2ac-277">使用端點路由時的授權</span><span class="sxs-lookup"><span data-stu-id="7b2ac-277">Authorization when using endpoint routing</span></span>

<span data-ttu-id="7b2ac-278">使用端點路由時的授權，現在會接收 `HttpContext` 而不是端點實例。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-278">Authorization when using endpoint routing now receives the `HttpContext` rather than the endpoint instance.</span></span> <span data-ttu-id="7b2ac-279">這可讓授權中介軟體存取 `RouteData` 無法透過類別存取之的和其他屬性 `HttpContext` <xref:Microsoft.AspNetCore.Http.Endpoint> 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-279">This allows the authorization middleware to access the `RouteData` and other properties of the `HttpContext` that were not accessible though the <xref:Microsoft.AspNetCore.Http.Endpoint> class.</span></span> <span data-ttu-id="7b2ac-280">您可以使用內容從內容提取端點 [。GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A)。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-280">The endpoint can be fetched from the context using [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span></span>

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a><span data-ttu-id="7b2ac-281">以角色為基礎的存取控制與 Kerberos 驗證，以及 Linux 上的 LDAP</span><span class="sxs-lookup"><span data-stu-id="7b2ac-281">Role-based access control with Kerberos authentication and LDAP on Linux</span></span>

<span data-ttu-id="7b2ac-282">請參閱 [Kerberos 驗證和角色型存取控制 (RBAC) ](xref:security/authentication/windowsauth#rbac)</span><span class="sxs-lookup"><span data-stu-id="7b2ac-282">See [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac)</span></span>

## <a name="api-improvements"></a><span data-ttu-id="7b2ac-283">API 改進</span><span class="sxs-lookup"><span data-stu-id="7b2ac-283">API improvements</span></span>

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a><span data-ttu-id="7b2ac-284">HttpRequest 和 HttpResponse 的 JSON 擴充方法</span><span class="sxs-lookup"><span data-stu-id="7b2ac-284">JSON extension methods for HttpRequest and HttpResponse</span></span>

<span data-ttu-id="7b2ac-285">您可以從 `HttpRequest` 和 `HttpResponse` 使用新的 <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> 和 `WriteAsJsonAsync` 擴充方法，讀取和寫入 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-285">JSON data can be read and written to from an `HttpRequest` and `HttpResponse` using the new <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> and `WriteAsJsonAsync` extension methods.</span></span> <span data-ttu-id="7b2ac-286">這些擴充方法會使用序列化程式 [ 上的System.Text.Js](xref:System.Text.Json) 來處理 JSON 資料。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-286">These extension methods use the [System.Text.Json](xref:System.Text.Json) serializer to handle the JSON data.</span></span> <span data-ttu-id="7b2ac-287">新的 `HasJsonContentType` 擴充方法也可以檢查要求是否有 JSON 內容類型。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-287">The new `HasJsonContentType` extension method can also check if a request has a JSON content type.</span></span>

<span data-ttu-id="7b2ac-288">JSON 擴充方法可以與 [端點路由](xref:fundamentals/routing) 結合，以我們呼叫 \* **route 至 code** _ 的程式設計風格來建立 JSON api。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-288">The JSON extension methods can be combined with [endpoint routing](xref:fundamentals/routing) to create JSON APIs in a style of programming we call \* **route to code** _.</span></span> <span data-ttu-id="7b2ac-289">如果開發人員想要以輕量方式建立基本的 JSON Api，這是新的選項。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-289">It is a new option for developers who want to create basic JSON APIs in a lightweight way.</span></span> <span data-ttu-id="7b2ac-290">例如，只有少數幾個端點的 web 應用程式，可能會選擇使用路由傳送至程式碼，而不是 ASP.NET Core MVC 的完整功能：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-290">For example, a web app that has only a handful of endpoints might choose to use route to code rather than the full functionality of ASP.NET Core MVC:</span></span>

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

<span data-ttu-id="7b2ac-291">如需有關新的 JSON 擴充方法和程式碼路由的詳細資訊，請參閱 [這個 .net 節目](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code)。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-291">For more information on the new JSON extension methods and route to code, see [this .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span></span>

### <a name="systemdiagnosticsactivity"></a><span data-ttu-id="7b2ac-292">系統診斷。活動</span><span class="sxs-lookup"><span data-stu-id="7b2ac-292">System.Diagnostics.Activity</span></span>

<span data-ttu-id="7b2ac-293">預設格式現在預設 <xref:System.Diagnostics.Activity?displayProperty=fullName> 為 W3C 格式。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-293">The default format for <xref:System.Diagnostics.Activity?displayProperty=fullName> now defaults to the W3C format.</span></span> <span data-ttu-id="7b2ac-294">這使得 ASP.NET Core 中的分散式追蹤支援，在預設情況下可與更多架構互通。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-294">This makes distributed tracing support in ASP.NET Core interoperable with more frameworks by default.</span></span>

### <a name="frombodyattribute"></a><span data-ttu-id="7b2ac-295">FromBodyAttribute</span><span class="sxs-lookup"><span data-stu-id="7b2ac-295">FromBodyAttribute</span></span>

<span data-ttu-id="7b2ac-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> 允許支援設定允許將這些參數或屬性視為選擇性的選項：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> ow supports configuring an option that allows these parameters or properties to be considered optional:</span></span>

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                           MyModel model) {
     ...
     }
```

## <a name="miscellaneous-improvements"></a><span data-ttu-id="7b2ac-297">其他改進</span><span class="sxs-lookup"><span data-stu-id="7b2ac-297">Miscellaneous improvements</span></span>

<span data-ttu-id="7b2ac-298">我們已開始將 [可為 null 的注釋](/dotnet/csharp/nullable-references#attributes-describe-apis) 套用至 ASP.NET Core 元件。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-298">We’ve started applying [nullable annotations](/dotnet/csharp/nullable-references#attributes-describe-apis) to ASP.NET Core assemblies.</span></span> <span data-ttu-id="7b2ac-299">我們計畫對大部分的 .NET 5 framework 公用 API 介面加上批註。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-299">We plan to annotate most of the common public API surface of the .NET 5 framework.</span></span> <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a><span data-ttu-id="7b2ac-300">控制啟動類別啟用</span><span class="sxs-lookup"><span data-stu-id="7b2ac-300">Control Startup class activation</span></span>

<span data-ttu-id="7b2ac-301">已新增額外的多載 <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> ，可讓應用程式提供 factory 方法來控制 `Startup` 類別啟用。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-301">An additional <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> overload has been added that lets an app provide a factory method for controlling `Startup` class activation.</span></span> <span data-ttu-id="7b2ac-302">控制 `Startup` 類別啟用有助於傳遞與 `Startup` 主機一起初始化的其他參數：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-302">Controlling `Startup` class activation is useful to pass additional parameters to `Startup` that are initialized along with the host:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a><span data-ttu-id="7b2ac-303">使用 dotnet 監看自動重新整理</span><span class="sxs-lookup"><span data-stu-id="7b2ac-303">Auto refresh with dotnet watch</span></span>

<span data-ttu-id="7b2ac-304">在 .NET 5 中，在 ASP.NET Core 專案上執行 [dotnet watch](xref:tutorials/dotnet-watch) 都會啟動預設的瀏覽器，並在對程式碼進行變更時自動重新整理瀏覽器。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-304">In .NET 5, running [dotnet watch](xref:tutorials/dotnet-watch) on an ASP.NET Core project both launches the default browser and auto refreshes the browser as changes are made to the code.</span></span> <span data-ttu-id="7b2ac-305">這表示您可以：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-305">This means you can:</span></span>

<span data-ttu-id="7b2ac-306">_ 在文字編輯器中開啟 ASP.NET Core 專案。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-306">_ Open an ASP.NET Core project in a text editor.</span></span>
* <span data-ttu-id="7b2ac-307">執行 `dotnet watch`。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-307">Run `dotnet watch`.</span></span>
* <span data-ttu-id="7b2ac-308">將焦點放在程式碼變更時，工具會處理重建、重新開機和重載應用程式。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-308">Focus on the code changes while the tooling handles rebuilding, restarting, and reloading the app.</span></span>

<span data-ttu-id="7b2ac-309">我們希望未來能 Visual Studio 自動重新整理功能。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-309">We hope to bring the auto refresh functionality to Visual Studio in the future.</span></span>

### <a name="console-logger-formatter"></a><span data-ttu-id="7b2ac-310">主控台記錄器格式器</span><span class="sxs-lookup"><span data-stu-id="7b2ac-310">Console Logger Formatter</span></span>

<span data-ttu-id="7b2ac-311">程式庫中的主控台記錄提供者已進行改進 `Microsoft.Extensions.Logging` 。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-311">Improvements have been made to the console log provider in the `Microsoft.Extensions.Logging` library.</span></span> <span data-ttu-id="7b2ac-312">開發人員現在可以執行自訂的 `ConsoleFormatter` ，以對主控台輸出的格式化和顏色標示進行完整的控制。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-312">Developers can now implement a custom `ConsoleFormatter` to exercise complete control over formatting and colorization of the console output.</span></span> <span data-ttu-id="7b2ac-313">格式器 Api 可執行 VT-100 escape 序列的子集，以提供豐富的格式設定。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-313">The formatter APIs allow for rich formatting by implementing a subset of the VT-100 escape sequences.</span></span> <span data-ttu-id="7b2ac-314">大部分新式終端都支援 VT-100。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-314">VT-100 is supported by most modern terminals.</span></span> <span data-ttu-id="7b2ac-315">主控台記錄器可以在不支援的終端上剖析出 escape 序列，讓開發人員能夠為所有終端機撰寫單一格式器。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-315">The console logger can parse out escape sequences on unsupported terminals allowing developers to author a single formatter for all terminals.</span></span>

### <a name="json-console-logger"></a><span data-ttu-id="7b2ac-316">JSON 主控台記錄器</span><span class="sxs-lookup"><span data-stu-id="7b2ac-316">JSON Console Logger</span></span>

<span data-ttu-id="7b2ac-317">除了支援自訂格式器之外，我們也新增了內建的 JSON 格式器，可將結構化的 JSON 記錄發出至主控台。</span><span class="sxs-lookup"><span data-stu-id="7b2ac-317">In addition to support for custom formatters, we’ve also added a built-in JSON formatter that emits structured JSON logs to the console.</span></span> <span data-ttu-id="7b2ac-318">下列程式碼示範如何從預設記錄器切換至 JSON：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-318">The following code shows how to switch from the default logger to JSON:</span></span>

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

<span data-ttu-id="7b2ac-319">發出至主控台的記錄訊息是 JSON 格式：</span><span class="sxs-lookup"><span data-stu-id="7b2ac-319">Log messages emitted to the console are JSON formatted:</span></span>

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
