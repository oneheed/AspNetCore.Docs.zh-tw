---
title: ASP.NET Core 全球化和當地語系化
author: rick-anderson
description: 了解 ASP.NET Core 如何提供服務與中介軟體，以將內容當地語系化成不同的語言與文化特性。
ms.author: riande
ms.date: 11/30/2019
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
uid: fundamentals/localization
ms.openlocfilehash: 8212025bbf778334045b8fbe7a5dae312524b426
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588018"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="e4c3d-103">ASP.NET Core 全球化和當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-103">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="e4c3d-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya) 提供</span><span class="sxs-lookup"><span data-stu-id="e4c3d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="e4c3d-105">多語系網站可讓網站觸及更廣大的物件。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-105">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="e4c3d-106">ASP.NET Core 提供服務與中介軟體，可將網站當地語系化成不同的語言與文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="e4c3d-107">國際化包含[全球化](/dotnet/api/system.globalization)和[當地語系化](/dotnet/standard/globalization-localization/localization)這兩部分。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="e4c3d-108">全球化是指設計出可支援不同文化特性之應用程式的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="e4c3d-109">透過全球化，您可新增支援與特定地區相關之已定義語言指令碼的輸入、顯示及輸出作業。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="e4c3d-110">當地語系化是指針對全球化應用程式進行調整的程序，且您已順應特定文化特性/地區設定對這些全球化應用程式進行可當地語系化處理。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="e4c3d-111">如需詳細資訊，請參閱本文件結尾處的 **全球化和當地語系化詞彙**。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="e4c3d-112">應用程式當地語系化包含下列作業：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-112">App localization involves the following:</span></span>

1. <span data-ttu-id="e4c3d-113">讓應用程式的內容可當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-113">Make the app's content localizable</span></span>
1. <span data-ttu-id="e4c3d-114">針對您支援的語言和文化特性提供當地語系化資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-114">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="e4c3d-115">實作可依據每項要求選取語言/文化特性的策略</span><span class="sxs-lookup"><span data-stu-id="e4c3d-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="e4c3d-116">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e4c3d-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="e4c3d-117">讓應用程式的內容可當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-117">Make the app's content localizable</span></span>

<span data-ttu-id="e4c3d-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 而且 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 是為了改善開發當地語系化應用程式時的生產力而設計。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="e4c3d-119">`IStringLocalizer` 使用 <xref:System.Resources.ResourceManager> 和， <xref:System.Resources.ResourceReader> 在執行時間提供特定文化特性的資源。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-119">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="e4c3d-120">介面具有可傳回當地語系化字串的索引子和 `IEnumerable` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-120">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="e4c3d-121">`IStringLocalizer` 不需要將預設語言字串儲存在資源檔中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-121">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="e4c3d-122">您不必在開發初期建立資源檔，即可開發以當地語系化為目標的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="e4c3d-123">下列程式碼會示範如何包裝 "About Title" 字串以進行當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="e4c3d-124">在上述程式碼中， `IStringLocalizer<T>` 執行是來自相依性 [插入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-124">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="e4c3d-125">如果找不到 "About Title" 的當地語系化值，即會傳回索引子的索引鍵，也就是 "About Title" 字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="e4c3d-126">您可以保留應用程式中的預設語言常值字串，並將其包裝在當地語系化工具中，以便專注於開發應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="e4c3d-127">您不用先建立預設資源檔，即可使用預設語言來開發應用程式，並針對當地語系化步驟進行應用程式的準備。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="e4c3d-128">或者，您可以使用傳統方法，並提供索引鍵以擷取預設語言字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="e4c3d-129">對許多開發人員來說，新的工作流程 (單純包裝字串常值而不使用預設語言的 *.resx* 檔案) 可以降低當地語系化應用程式的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="e4c3d-130">其他開發人員則偏好傳統的工作流程，因為這種方法更便於使用較長的字串常值，且更易於更新當地語系化的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="e4c3d-131">若是包含 HTML 的資源，請使用 `IHtmlLocalizer<T>` 實作。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="e4c3d-132">`IHtmlLocalizer` 會對資源字串中經過格式化的引數進行 HTML 編碼，但不會對資源字串本身進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="e4c3d-133">在下列醒目提示的範例中，只有 `name` 參數的值經過 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="e4c3d-134">一般而言，只會當地語系化文字，而非 HTML。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-134">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="e4c3d-135">您可以在最底層的[相依性插入](dependency-injection.md)中，將 `IStringLocalizerFactory` 移出：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="e4c3d-136">上述程式碼會示範這兩個 Factory Create 方法。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="e4c3d-137">您可以依據控制器或區域來分割當地語系化的字串，也可以只用一個容器。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="e4c3d-138">在範例應用程式中，會針對共用資源使用名為 `SharedResource` 的虛擬類別。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="e4c3d-139">有些開發人員會使用 `Startup` 類別來包含全域或共用字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="e4c3d-140">下列範例會使用 `InfoController` 和 `SharedResource` 當地語系化工具：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="e4c3d-141">檢視當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-141">View localization</span></span>

<span data-ttu-id="e4c3d-142">`IViewLocalizer` 服務可提供[檢視](xref:mvc/views/overview)的當地語系化字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="e4c3d-143">`ViewLocalizer` 類別會實作這個介面，並透過檢視檔案路徑來找出資源的位置。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="e4c3d-144">下列程式碼示範如何使用 `IViewLocalizer` 的預設實作：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="e4c3d-145">`IViewLocalizer` 的預設實作會依據檢視的檔案名稱來找出資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="e4c3d-146">其中並沒有任何選項可以使用全域共用的資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="e4c3d-147">`ViewLocalizer` 使用來執行當地語系化工具 `IHtmlLocalizer` ，因此 Razor 不會對當地語系化的字串進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="e4c3d-148">您可以參數化資源字串，`IViewLocalizer` 即會對參數 (而不是資源字串) 進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="e4c3d-149">請考慮下列 Razor 標記：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="e4c3d-150">法文資源檔可能包含下列內容：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="e4c3d-151">Key</span><span class="sxs-lookup"><span data-stu-id="e4c3d-151">Key</span></span> | <span data-ttu-id="e4c3d-152">值</span><span class="sxs-lookup"><span data-stu-id="e4c3d-152">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="e4c3d-153">轉譯的檢視內容可能包含來自資源檔的 HTML 標記。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-153">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="e4c3d-154">一般而言，只會當地語系化文字，而非 HTML。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-154">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="e4c3d-155">若要在檢視中使用共用的資源檔，請插入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="e4c3d-156">DataAnnotations 當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-156">DataAnnotations localization</span></span>

<span data-ttu-id="e4c3d-157">DataAnnotations 錯誤訊息會使用 `IStringLocalizer<T>` 來當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="e4c3d-158">使用 `ResourcesPath = "Resources"` 選項時，`RegisterViewModel` 中的錯誤訊息會儲存在下列路徑之一：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="e4c3d-159">*Resources/Viewmodel. Account. RegisterViewModel fr-fr*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="e4c3d-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="e4c3d-161">在 ASP.NET Core MVC 1.1.0 和更高版本中，系統會將非驗證屬性當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="e4c3d-162">ASP.NET Core MVC 1.0 **不會** 查閱非驗證屬性的當地語系化字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="e4c3d-163">針對多個類別使用同一個資源字串</span><span class="sxs-lookup"><span data-stu-id="e4c3d-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="e4c3d-164">下列程式碼會示範如何針對含有多個類別的驗證屬性使用同一個資源字串：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="e4c3d-165">在先前的程式碼中，`SharedResource` 是與儲存驗證訊息的 resx 對應的類別。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="e4c3d-166">使用這個方法時，DataAnnotations 只會使用 `SharedResource`，而不是每個類別的資源。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="e4c3d-167">針對您支援的語言和文化特性提供當地語系化資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="e4c3d-168">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="e4c3d-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="e4c3d-169">ASP.NET Core 可讓您指定 <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedCultures> 和 <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedUICultures> 這兩個文化特性值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-169">ASP.NET Core allows you to specify two culture values, <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedCultures> and <xref:Microsoft.AspNetCore.Builder.RequestLocalizationOptions.SupportedUICultures>.</span></span> <span data-ttu-id="e4c3d-170">的 <xref:System.Globalization.CultureInfo> 物件可 `SupportedCultures` 決定文化特性相依函數的結果，例如日期、時間、數位和貨幣格式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-170">The <xref:System.Globalization.CultureInfo> object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="e4c3d-171">`SupportedCultures` 也可決定文字排列順序、大小寫慣例和字串比較。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="e4c3d-172">如需有關伺服器如何取得文化特性的詳細資訊，請參閱 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=nameWithType> 和 <xref:System.Globalization.CultureInfo.CurrentUICulture?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-172">For more information on how the server obtains the culture, see <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.CurrentUICulture?displayProperty=nameWithType>.</span></span> <span data-ttu-id="e4c3d-173">`SupportedUICultures` `.resx` 會決定所查閱) 所 (的翻譯字串 <xref:System.Resources.ResourceManager> 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-173">The `SupportedUICultures` determines which translated strings (from `.resx` files) are looked up by the <xref:System.Resources.ResourceManager>.</span></span> <span data-ttu-id="e4c3d-174">`ResourceManager`會查閱由所決定的文化特性特定字串 `CurrentUICulture` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-174">The `ResourceManager` looks up culture-specific strings that are determined by `CurrentUICulture`.</span></span> <span data-ttu-id="e4c3d-175">.NET 中的每個執行緒都有 `CurrentCulture` 和 `CurrentUICulture` 物件。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="e4c3d-176">架構會在轉譯與文化特性相依的函式時，檢查這些值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-176">The framework inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="e4c3d-177">如果目前線程的文化特性設定為 `en-US` (英文、美國) ，則會 `DateTime.Now.ToLongDateString()` 顯示 `Thursday, February 18, 2016` ; 但是，如果 `CurrentCulture` 設為 `es-ES` (西班牙文（西班牙）) ，則輸出為 `jueves, 18 de febrero de 2016` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-177">If the current thread's culture is set to `en-US` (English, United States), `DateTime.Now.ToLongDateString()` displays `Thursday, February 18, 2016`; but if `CurrentCulture` is set to `es-ES` (Spanish, Spain), the output is `jueves, 18 de febrero de 2016`.</span></span>

## <a name="resource-files"></a><span data-ttu-id="e4c3d-178">資源檔</span><span class="sxs-lookup"><span data-stu-id="e4c3d-178">Resource files</span></span>

<span data-ttu-id="e4c3d-179">資源檔是一種實用的機制，可讓您將可當地語系化的字串與代碼區隔開來。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="e4c3d-180">非預設語言的翻譯字串會隔離在 *.resx* 資源檔中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="e4c3d-181">例如，您可以建立名為 *Welcome.es.resx* 的西班牙文資源檔，以包含翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="e4c3d-182">"es" 是西班牙文的語言代碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="e4c3d-183">若要在 Visual Studio 中建立這個資源檔：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="e4c3d-184">在方案總管中，以滑鼠右鍵按一下要放置資源檔的資料夾 > [新增]**[新增項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![巢狀特色選單：方案總管會開啟 [資源] 的特色選單，](localization/_static/newi.png)

1. <span data-ttu-id="e4c3d-187">在 [Search installed templates] (搜尋已安裝的範本) 方塊中，輸入「資源」，並命名檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![[新增項目] 對話方塊](localization/_static/res.png)

1. <span data-ttu-id="e4c3d-189">在 [名稱] 資料行中輸入索引鍵值 (原生字串)，並在 [值] 資料行中輸入已翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx 檔案 (西班牙文的「歡迎使用」資源檔)，其中 [名稱] 資料行的文字為 Hello，而 [值] 資料行的文字為 Hola (Hello 的西班牙文)](localization/_static/hola.png)

   <span data-ttu-id="e4c3d-191">Visual Studio 會顯示 *Welcome.es.resx* 檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![方案總管，其中顯示「歡迎使用」的西班牙文 (es) 資源檔](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="e4c3d-193">資源檔命名</span><span class="sxs-lookup"><span data-stu-id="e4c3d-193">Resource file naming</span></span>

<span data-ttu-id="e4c3d-194">資源的命名方式是以其類別的完整類型名稱去掉組件名稱而得。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="e4c3d-195">例如，假設專案中的法文資源是 `LocalizationWebsite.Web.Startup` 類別、主要組件為 `LocalizationWebsite.Web.dll`，就會命名為 *Startup.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="e4c3d-196">若是 `LocalizationWebsite.Web.Controllers.HomeController` 類別的資源，則應命名為 *Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-197">如果目標類別的命名空間和組件名稱不相同，則需要使用完整類型名稱。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="e4c3d-198">比方說，範例專案中 `ExtraNamespace.Tools` 類型的資源會命名為 *ExtraNamespace.Tools.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="e4c3d-199">在範例專案中，`ConfigureServices` 方法會將 `ResourcesPath` 設為 "Resources"，因此首頁控制器的法文資源檔專案相對路徑即為 *Resources/Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-200">或者，您可以使用資料夾來收集資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="e4c3d-201">若是首頁控制器，路徑就是 *Resources/Controllers/HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-202">如果您不使用 `ResourcesPath` 選項，*.resx* 檔案即會放置在專案的基底目錄中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="e4c3d-203">`HomeController` 的資源檔會命名為 *Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-204">您可依據自己的資源檔收集方式，來選擇要使用點或路徑的命名慣例。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="e4c3d-205">資源名稱</span><span class="sxs-lookup"><span data-stu-id="e4c3d-205">Resource name</span></span> | <span data-ttu-id="e4c3d-206">點或路徑命名</span><span class="sxs-lookup"><span data-stu-id="e4c3d-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="e4c3d-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="e4c3d-208">點</span><span class="sxs-lookup"><span data-stu-id="e4c3d-208">Dot</span></span>  |
| <span data-ttu-id="e4c3d-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="e4c3d-210">路徑</span><span class="sxs-lookup"><span data-stu-id="e4c3d-210">Path</span></span> |

<span data-ttu-id="e4c3d-211">使用於 views 的資源檔會 `@inject IViewLocalizer` Razor 遵循類似的模式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="e4c3d-212">您可以使用點命名或路徑命名方式，來命名檢視的資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="e4c3d-213">Razor 查看資源檔，模仿其相關聯的視圖檔案的路徑。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="e4c3d-214">假設我們將 `ResourcesPath` 設為 "Resources"，與 *Views/Home/About.cshtml* 檢視建立關聯的法文資源檔可為下列其一：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="e4c3d-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="e4c3d-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="e4c3d-217">如果您不使用 `ResourcesPath` 選項，則檢視的 *.resx* 檔案會與檢視位於相同資料夾中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="e4c3d-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="e4c3d-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="e4c3d-219"><xref:Microsoft.Extensions.Localization.RootNamespaceAttribute>當元件的根命名空間與元件名稱不同時，屬性會提供元件的根命名空間。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-219">The <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="e4c3d-220">當專案的名稱不是有效的 .NET 識別碼時，就會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="e4c3d-221">例如， `my-project-name.csproj` 會使用根命名空間 `my_project_name` 和 `my-project-name` 導致此錯誤的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="e4c3d-222">如果組件的根命名空間與組件名稱不同：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="e4c3d-223">根據預設，當地語系化無法運作。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-223">Localization does not work by default.</span></span>
* <span data-ttu-id="e4c3d-224">在組件中搜尋資源的方式造成當地語系化失敗。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="e4c3d-225">`RootNamespace` 是建置時間值，無法用於執行處理序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="e4c3d-226">如果 `RootNamespace` 與 `AssemblyName` 不同，請將下列內容納入 *AssemblyInfo.cs* (參數值取代為實際值)：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="e4c3d-227">上述程式碼可成功解析 resx 檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="e4c3d-228">文化特性後援行為</span><span class="sxs-lookup"><span data-stu-id="e4c3d-228">Culture fallback behavior</span></span>

<span data-ttu-id="e4c3d-229">搜尋資源時，當地語系化會使用「文化特性後援」。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="e4c3d-230">從所要求的文化特性開始，如果找不到，就會還原成該文化特性的父文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="e4c3d-231">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 屬性代表父文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="e4c3d-232">這通常 (但並非一定) 表示從 ISO 中移除國家意符 (Signifier)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="e4c3d-233">例如，墨西哥的西班牙文方言是 "es-MX"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="e4c3d-234">它具有父系 "es" &mdash; 西班牙文不是任何國家/地區的特定項目。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="e4c3d-235">假設您的網站收到使用文化特性 "fr-CA" 之 "Welcome" 資源的要求。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="e4c3d-236">當地語系化系統會依照順序尋找下列資源，並選取第一個相符項目：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="e4c3d-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="e4c3d-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="e4c3d-239">*Welcome.resx* (如果 `NeutralResourcesLanguage` 是 "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="e4c3d-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="e4c3d-240">舉例來說，如果您移除 ".fr" 文化特性指示項，並將文化特性設定為法文，則系統會讀取預設資源檔，並將字串當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="e4c3d-241">當沒有任何項目符合您要求的文化特性時，資源管理員即會指定預設資源或後援資源。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="e4c3d-242">如果您只想在要求的文化特性缺少資源時傳回索引鍵，就不能使用預設資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="e4c3d-243">使用 Visual Studio 產生資源檔</span><span class="sxs-lookup"><span data-stu-id="e4c3d-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="e4c3d-244">如果您在 Visual Studio 中建立資源檔，但檔案名稱中不含文化特性 (例如 *Welcome.resx*)，則 Visual Studio 會針對每個字串的屬性建立 C# 類別。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="e4c3d-245">但這通常不是您使用 ASP.NET Core 的初衷。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="e4c3d-246">一般來說，您不會有預設的 *.resx* 資源檔 (不含文化特性名稱的 *.resx* 檔案)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="e4c3d-247">因此，建議您建立含有文化特性名稱的 *.resx* 檔案 (例如 *Welcome.fr.resx*)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="e4c3d-248">當您建立含有文化特性名稱的 *.resx* 檔案時，Visual Studio 就不會產生類別檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="e4c3d-249">新增其他文化特性</span><span class="sxs-lookup"><span data-stu-id="e4c3d-249">Add other cultures</span></span>

<span data-ttu-id="e4c3d-250">每種語言和文化特性的組合 (非預設語言) 都需要唯一的資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="e4c3d-251">若要建立不同文化特性和地區設定的資源檔，您可以建立新的資源檔並將 ISO 語言代碼作為檔名的一部分 (例如 **en-us**、**fr-ca** 和 **en-gb**)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="e4c3d-252">您應將 ISO 代碼置於檔案名稱和 *.resx* 副檔名之間，例如 *Welcome.es-MX.resx* (西班牙文/墨西哥)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="e4c3d-253">實作可依據每項要求選取語言/文化特性的策略</span><span class="sxs-lookup"><span data-stu-id="e4c3d-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="e4c3d-254">設定當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-254">Configure localization</span></span>

<span data-ttu-id="e4c3d-255">您可以在 `Startup.ConfigureServices` 方法中設定當地語系化：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="e4c3d-256">`AddLocalization` 將當地語系化服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-256">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="e4c3d-257">上方的程式碼也會將資源路徑設為 "Resources"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="e4c3d-258">`AddViewLocalization` 新增當地語系化視圖檔案的支援。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-258">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="e4c3d-259">在此範例中，檢視的當地語系化會以檢視檔案的後置詞為依據。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="e4c3d-260">例如 *Index.fr.cshtml* 檔案中的 "fr"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="e4c3d-261">`AddDataAnnotationsLocalization` 透過抽象新增當地語系化 `DataAnnotations` 驗證訊息的支援 `IStringLocalizer` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-261">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="e4c3d-262">當地語系化中介軟體</span><span class="sxs-lookup"><span data-stu-id="e4c3d-262">Localization middleware</span></span>

<span data-ttu-id="e4c3d-263">您可以在當地語系化[中介軟體](xref:fundamentals/middleware/index)中，設定要求目前的文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="e4c3d-264">已在 `Startup.Configure` 方法中啟用當地語系化中介軟體。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="e4c3d-265">您必須在可能檢查要求文化特性的任何中介軟體之前，設定當地語系化中介軟體 (例如 `app.UseMvcWithDefaultRoute()`)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e4c3d-266">`UseRequestLocalization` 會初始化 `RequestLocalizationOptions` 物件。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="e4c3d-267">在每次要求時，系統會列舉 `RequestLocalizationOptions` 中的 `RequestCultureProvider` 清單，並使用能成功判斷要求的文化特性的第一個提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="e4c3d-268">預設的提供者是來自 `RequestLocalizationOptions` 類別：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="e4c3d-269">預設清單會由針對性高到低來排列。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="e4c3d-270">在本文稍後，我們會說明如何變更順序，甚至新增自訂的文化特性提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="e4c3d-271">如果沒有任何提供者可以判斷要求的文化特性，即會使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="e4c3d-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e4c3d-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="e4c3d-273">有些應用程式會使用查詢字串來設定 <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-273">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="e4c3d-274">針對使用 cookie 或 Accept-Language 標頭方法的應用程式，將查詢字串新增至 URL 可用於偵錯工具代碼和測試程式碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="e4c3d-275">系統預設會將 `QueryStringRequestCultureProvider` 登錄為 `RequestCultureProvider` 清單中的第一個當地語系化提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="e4c3d-276">您應傳遞查詢字串參數 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="e4c3d-277">下列範例會設定西班牙文/墨西哥的特定文化特性 (語言和地區)：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="e4c3d-278">如果您只傳入兩者其一 (`culture` 或 `ui-culture`)，查詢字串提供者就會使用您傳入的項目來設定這兩個值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="e4c3d-279">例如，若只設定文化特性，即會同時設定 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="e4c3d-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e4c3d-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="e4c3d-281">生產環境應用程式通常會提供一種機制，以 ASP.NET 核心文化特性設定文化特性 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="e4c3d-282">使用 `MakeCookieValue` 方法來建立 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="e4c3d-283">會傳回 `CookieRequestCultureProvider` `DefaultCookieName` cookie 用來追蹤使用者慣用文化特性資訊的預設名稱。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="e4c3d-284">預設 cookie 名稱為 `.AspNetCore.Culture` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="e4c3d-285">cookie格式為 `c=%LANGCODE%|uic=%LANGCODE%` ，其中 `c` 是 `Culture` 和，例如 `uic` `UICulture` ：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="e4c3d-286">如果您只指定文化特性資訊和 UI 文化特性其一，系統就會將您所指定的文化特性用於文化特性資訊和 UI 文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="e4c3d-287">Accept-Language HTTP 標頭</span><span class="sxs-lookup"><span data-stu-id="e4c3d-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="e4c3d-288">您可在大多數的瀏覽器中設定 [Accept-Language 標頭](https://www.w3.org/International/questions/qa-accept-lang-locales)，其最初的設計目的是用來指定使用者的語言。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="e4c3d-289">這項設定可指出瀏覽器已設好要傳送哪些項目，或已從基礎作業系統繼承哪些項目。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="e4c3d-290">透過瀏覽器要求的 Accept-Language HTTP 標頭來偵測使用者的慣用語言，並非萬無一失 (請參閱 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) (在瀏覽器中設定語言喜好設定)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="e4c3d-291">生產環境應用程式應該包含可讓使用者自訂文化特性的選擇方式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="e4c3d-292">在 IE 中設定 Accept-Language HTTP 標頭</span><span class="sxs-lookup"><span data-stu-id="e4c3d-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="e4c3d-293">從齒輪圖示，點選 [網際網路選項]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-293">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="e4c3d-294">點選 [語言]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-294">Tap **Languages**.</span></span>

   ![網際網路選項](localization/_static/lang.png)

1. <span data-ttu-id="e4c3d-296">點選 [設定語言喜好設定]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-296">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="e4c3d-297">點選 [新增語言]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-297">Tap **Add a language**.</span></span>

1. <span data-ttu-id="e4c3d-298">新增語言。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-298">Add the language.</span></span>

1. <span data-ttu-id="e4c3d-299">點選語言，然後點選 [上移]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-299">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="e4c3d-300">使用自訂提供者</span><span class="sxs-lookup"><span data-stu-id="e4c3d-300">Use a custom provider</span></span>

<span data-ttu-id="e4c3d-301">假設您想要讓客戶將他們的語言和文化特性儲存在您的資料庫中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-301">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="e4c3d-302">您可以撰寫提供者，以供使用者查詢這些值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-302">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="e4c3d-303">下列程式碼會示範如何新增自訂提供者：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-303">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="e4c3d-304">使用 `RequestLocalizationOptions` 新增或移除當地語系化提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-304">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="e4c3d-305">以程式設計方式來設定文化特性</span><span class="sxs-lookup"><span data-stu-id="e4c3d-305">Set the culture programmatically</span></span>

<span data-ttu-id="e4c3d-306">[GitHub](https://github.com/aspnet/entropy) 上的這個範例 **Localization.StarterWeb** 專案包含可設定 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-306">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="e4c3d-307">*Views/Shared/_SelectLanguagePartial.cshtml* 檔可讓您從支援的文化特性清單中選取文化特性：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-307">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="e4c3d-308">系統會將 *Views/Shared/_SelectLanguagePartial.cshtml* 檔案新增至配置檔案的 `footer` 區段，以供所有檢視使用：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-308">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="e4c3d-309">`SetLanguage`方法會設定文化特性 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-309">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="e4c3d-310">您無法將 *_SelectLanguagePartial.cshtml* 插入這個專案的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-310">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="e4c3d-311">[GitHub](https://github.com/aspnet/entropy)上的 **>localization.starterweb** 專案有程式碼，可透過相依性 `RequestLocalizationOptions` 插入容器將傳送至 Razor 部分。 [](dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="e4c3d-311">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="e4c3d-312">模型系結路由資料和查詢字串</span><span class="sxs-lookup"><span data-stu-id="e4c3d-312">Model binding route data and query strings</span></span>

<span data-ttu-id="e4c3d-313">請參閱模型系結 [路由資料和查詢字串的全球化行為](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-313">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="e4c3d-314">全球化和當地語系化詞彙</span><span class="sxs-lookup"><span data-stu-id="e4c3d-314">Globalization and localization terms</span></span>

<span data-ttu-id="e4c3d-315">在進行應用程式的當地語系化程序時，您也需要具備現代軟體開發中常用的相關字元集基本知識，並了解與它們建立關聯的問題。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-315">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="e4c3d-316">雖然所有電腦都會將文字儲存為數字 (程式碼)，但不同系統會使用不同數字來儲存相同的文字。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-316">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="e4c3d-317">當地語系化是指針對特定文化特性/地區設定，轉譯應用程式使用者介面 (UI) 的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-317">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="e4c3d-318">[可當地語系化](/dotnet/standard/globalization-localization/localizability-review)是確認全球化應用程式已準備好進行當地語系化的中繼程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-318">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="e4c3d-319">文化特性名稱的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式是 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是語言代碼，而 `<country/regioncode2>` 是子文化特性代碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-319">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="e4c3d-320">例如，`es-CL` 是指西班牙文 (智利)，`en-US` 是指英文 (美國)，而 `en-AU` 是指英文 (澳大利亞)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-320">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="e4c3d-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是 ISO 639 (兩個小寫字母，為與某個語言建立關聯的文化特性代碼) 及 ISO 3166 (兩個大寫字母，為與某個國家或地區建立關聯的子文化特性代碼) 的組合。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="e4c3d-322">請參閱 <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-322">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="e4c3d-323">國際化通常縮寫為 "I18N"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-323">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="e4c3d-324">這個縮寫是採用該詞彙的第一個和最後一個字母，以及這兩個字母間的字母數組成，因此 18 代表第一個字母 "I" 及最後 "N" 中間的字母數。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-324">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="e4c3d-325">同樣的原則也適用於全球化 (G11N) 與當地語系化 (L10N) 的縮寫。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-325">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="e4c3d-326">詞彙：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-326">Terms:</span></span>

* <span data-ttu-id="e4c3d-327">全球化 (G11N)：讓應用程式支援不同語言和區域的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-327">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="e4c3d-328">當地語系化 (L10N)：針對特定語言和區域，自訂應用程式的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-328">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="e4c3d-329">國際化 (I18N)：包含全球化和當地語系化這兩部分的描述。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-329">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="e4c3d-330">文化特性：它是一種語言和/或地區。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-330">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="e4c3d-331">中性文化特性：具有指定的語言但不限地區的文化特性 </span><span class="sxs-lookup"><span data-stu-id="e4c3d-331">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="e4c3d-332">(例如 "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-332">(for example "en", "es")</span></span>
* <span data-ttu-id="e4c3d-333">特定文化特性：具有指定的語言和地區的文化特性 </span><span class="sxs-lookup"><span data-stu-id="e4c3d-333">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="e4c3d-334">(例如 "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-334">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="e4c3d-335">父文化特性：包含特定文化特性的中性文化特性 </span><span class="sxs-lookup"><span data-stu-id="e4c3d-335">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="e4c3d-336">(例如，"en" 是 "en-US" 和 "en-GB" 的父文化特性)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-336">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="e4c3d-337">地區設定：地區設定與文化特性相同。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-337">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="e4c3d-338">其他資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-338">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="e4c3d-339">本文使用的 [Localization.StarterWeb 專案](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-339">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="e4c3d-340">全球化與當地語系化 .NET 應用程式</span><span class="sxs-lookup"><span data-stu-id="e4c3d-340">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="e4c3d-341">.Resx 檔案中的資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-341">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="e4c3d-342">Microsoft 多語應用程式工具組</span><span class="sxs-lookup"><span data-stu-id="e4c3d-342">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="e4c3d-343">當地語系化和泛型</span><span class="sxs-lookup"><span data-stu-id="e4c3d-343">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e4c3d-344">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya) 提供</span><span class="sxs-lookup"><span data-stu-id="e4c3d-344">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="e4c3d-345">多語系網站可讓網站觸及更廣大的物件。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-345">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="e4c3d-346">ASP.NET Core 提供服務與中介軟體，可將網站當地語系化成不同的語言與文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-346">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="e4c3d-347">國際化包含[全球化](/dotnet/api/system.globalization)和[當地語系化](/dotnet/standard/globalization-localization/localization)這兩部分。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-347">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="e4c3d-348">全球化是指設計出可支援不同文化特性之應用程式的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-348">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="e4c3d-349">透過全球化，您可新增支援與特定地區相關之已定義語言指令碼的輸入、顯示及輸出作業。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-349">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="e4c3d-350">當地語系化是指針對全球化應用程式進行調整的程序，且您已順應特定文化特性/地區設定對這些全球化應用程式進行可當地語系化處理。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-350">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="e4c3d-351">如需詳細資訊，請參閱本文件結尾處的 **全球化和當地語系化詞彙**。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-351">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="e4c3d-352">應用程式當地語系化包含下列作業：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-352">App localization involves the following:</span></span>

1. <span data-ttu-id="e4c3d-353">讓應用程式的內容可當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-353">Make the app's content localizable</span></span>
1. <span data-ttu-id="e4c3d-354">針對您支援的語言和文化特性提供當地語系化資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-354">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="e4c3d-355">實作可依據每項要求選取語言/文化特性的策略</span><span class="sxs-lookup"><span data-stu-id="e4c3d-355">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="e4c3d-356">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/Localization) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e4c3d-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="e4c3d-357">讓應用程式的內容可當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-357">Make the app's content localizable</span></span>

<span data-ttu-id="e4c3d-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 而且 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 是為了改善開發當地語系化應用程式時的生產力而設計。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="e4c3d-359">`IStringLocalizer` 使用 <xref:System.Resources.ResourceManager> 和， <xref:System.Resources.ResourceReader> 在執行時間提供特定文化特性的資源。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-359">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="e4c3d-360">介面具有可傳回當地語系化字串的索引子和 `IEnumerable` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-360">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="e4c3d-361">`IStringLocalizer` 不需要將預設語言字串儲存在資源檔中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-361">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="e4c3d-362">您不必在開發初期建立資源檔，即可開發以當地語系化為目標的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-362">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="e4c3d-363">下列程式碼會示範如何包裝 "About Title" 字串以進行當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-363">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="e4c3d-364">在上述程式碼中， `IStringLocalizer<T>` 執行是來自相依性 [插入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-364">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="e4c3d-365">如果找不到 "About Title" 的當地語系化值，即會傳回索引子的索引鍵，也就是 "About Title" 字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-365">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="e4c3d-366">您可以保留應用程式中的預設語言常值字串，並將其包裝在當地語系化工具中，以便專注於開發應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-366">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="e4c3d-367">您不用先建立預設資源檔，即可使用預設語言來開發應用程式，並針對當地語系化步驟進行應用程式的準備。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-367">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="e4c3d-368">或者，您可以使用傳統方法，並提供索引鍵以擷取預設語言字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-368">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="e4c3d-369">對許多開發人員來說，新的工作流程 (單純包裝字串常值而不使用預設語言的 *.resx* 檔案) 可以降低當地語系化應用程式的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-369">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="e4c3d-370">其他開發人員則偏好傳統的工作流程，因為這種方法更便於使用較長的字串常值，且更易於更新當地語系化的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-370">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="e4c3d-371">若是包含 HTML 的資源，請使用 `IHtmlLocalizer<T>` 實作。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-371">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="e4c3d-372">`IHtmlLocalizer` 會對資源字串中經過格式化的引數進行 HTML 編碼，但不會對資源字串本身進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-372">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="e4c3d-373">在下列醒目提示的範例中，只有 `name` 參數的值經過 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-373">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="e4c3d-374">一般而言，只會當地語系化文字，而非 HTML。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-374">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="e4c3d-375">您可以在最底層的[相依性插入](dependency-injection.md)中，將 `IStringLocalizerFactory` 移出：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-375">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="e4c3d-376">上述程式碼會示範這兩個 Factory Create 方法。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-376">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="e4c3d-377">您可以依據控制器或區域來分割當地語系化的字串，也可以只用一個容器。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-377">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="e4c3d-378">在範例應用程式中，會針對共用資源使用名為 `SharedResource` 的虛擬類別。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-378">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="e4c3d-379">有些開發人員會使用 `Startup` 類別來包含全域或共用字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-379">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="e4c3d-380">下列範例會使用 `InfoController` 和 `SharedResource` 當地語系化工具：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-380">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="e4c3d-381">檢視當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-381">View localization</span></span>

<span data-ttu-id="e4c3d-382">`IViewLocalizer` 服務可提供[檢視](xref:mvc/views/overview)的當地語系化字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-382">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="e4c3d-383">`ViewLocalizer` 類別會實作這個介面，並透過檢視檔案路徑來找出資源的位置。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-383">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="e4c3d-384">下列程式碼示範如何使用 `IViewLocalizer` 的預設實作：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-384">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="e4c3d-385">`IViewLocalizer` 的預設實作會依據檢視的檔案名稱來找出資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-385">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="e4c3d-386">其中並沒有任何選項可以使用全域共用的資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-386">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="e4c3d-387">`ViewLocalizer` 使用來執行當地語系化工具 `IHtmlLocalizer` ，因此 Razor 不會對當地語系化的字串進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-387">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="e4c3d-388">您可以參數化資源字串，`IViewLocalizer` 即會對參數 (而不是資源字串) 進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-388">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="e4c3d-389">請考慮下列 Razor 標記：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-389">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="e4c3d-390">法文資源檔可能包含下列內容：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-390">A French resource file could contain the following:</span></span>

| <span data-ttu-id="e4c3d-391">Key</span><span class="sxs-lookup"><span data-stu-id="e4c3d-391">Key</span></span> | <span data-ttu-id="e4c3d-392">值</span><span class="sxs-lookup"><span data-stu-id="e4c3d-392">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="e4c3d-393">轉譯的檢視內容可能包含來自資源檔的 HTML 標記。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-393">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="e4c3d-394">一般而言，只會當地語系化文字，而非 HTML。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-394">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="e4c3d-395">若要在檢視中使用共用的資源檔，請插入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-395">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="e4c3d-396">DataAnnotations 當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-396">DataAnnotations localization</span></span>

<span data-ttu-id="e4c3d-397">DataAnnotations 錯誤訊息會使用 `IStringLocalizer<T>` 來當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-397">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="e4c3d-398">使用 `ResourcesPath = "Resources"` 選項時，`RegisterViewModel` 中的錯誤訊息會儲存在下列路徑之一：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-398">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="e4c3d-399">*Resources/Viewmodel. Account. RegisterViewModel fr-fr*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="e4c3d-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="e4c3d-401">在 ASP.NET Core MVC 1.1.0 和更高版本中，系統會將非驗證屬性當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-401">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="e4c3d-402">ASP.NET Core MVC 1.0 **不會** 查閱非驗證屬性的當地語系化字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-402">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="e4c3d-403">針對多個類別使用同一個資源字串</span><span class="sxs-lookup"><span data-stu-id="e4c3d-403">Using one resource string for multiple classes</span></span>

<span data-ttu-id="e4c3d-404">下列程式碼會示範如何針對含有多個類別的驗證屬性使用同一個資源字串：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-404">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="e4c3d-405">在先前的程式碼中，`SharedResource` 是與儲存驗證訊息的 resx 對應的類別。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-405">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="e4c3d-406">使用這個方法時，DataAnnotations 只會使用 `SharedResource`，而不是每個類別的資源。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-406">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="e4c3d-407">針對您支援的語言和文化特性提供當地語系化資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-407">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="e4c3d-408">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="e4c3d-408">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="e4c3d-409">ASP.NET Core 可讓您指定 `SupportedCultures` 和 `SupportedUICultures` 這兩個文化特性值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-409">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="e4c3d-410">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 物件可決定文化特性相依函式的結果，例如日期、時間、數字及貨幣格式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-410">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="e4c3d-411">`SupportedCultures` 也可決定文字排列順序、大小寫慣例和字串比較。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-411">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="e4c3d-412">如需伺服器如何取得文化特性的詳細資訊，請參閱 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-412">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="e4c3d-413">`SupportedUICultures`會判斷 [ResourceManager](/dotnet/api/system.resources.resourcemanager) (從 *.resx* 檔) 的翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-413">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="e4c3d-414">`ResourceManager` 僅會查閱 `CurrentUICulture` 所決定之文化特性特有的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-414">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="e4c3d-415">.NET 中的每個執行緒都有 `CurrentCulture` 和 `CurrentUICulture` 物件。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-415">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="e4c3d-416">ASP.NET Core 會在轉譯文化特性相依函式時檢查這些值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-416">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="e4c3d-417">比方說，如果目前執行緒的文化特性設定為 "en-US" (英文 - 美國)，`DateTime.Now.ToLongDateString()` 會顯示 "Thursday, February 18, 2016"，但如果 `CurrentCulture` 設定為 "es-ES" (西班牙文 - 西班牙)，則輸出會是 "jueves, 18 de febrero de 2016"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-417">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="e4c3d-418">資源檔</span><span class="sxs-lookup"><span data-stu-id="e4c3d-418">Resource files</span></span>

<span data-ttu-id="e4c3d-419">資源檔是一種實用的機制，可讓您將可當地語系化的字串與代碼區隔開來。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-419">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="e4c3d-420">非預設語言的翻譯字串會隔離在 *.resx* 資源檔中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-420">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="e4c3d-421">例如，您可以建立名為 *Welcome.es.resx* 的西班牙文資源檔，以包含翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-421">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="e4c3d-422">"es" 是西班牙文的語言代碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-422">"es" is the language code for Spanish.</span></span> <span data-ttu-id="e4c3d-423">若要在 Visual Studio 中建立這個資源檔：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-423">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="e4c3d-424">在方案總管中，以滑鼠右鍵按一下要放置資源檔的資料夾 > [新增]**[新增項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-424">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![巢狀特色選單：方案總管會開啟 [資源] 的特色選單，](localization/_static/newi.png)

1. <span data-ttu-id="e4c3d-427">在 [Search installed templates] (搜尋已安裝的範本) 方塊中，輸入「資源」，並命名檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-427">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![[新增項目] 對話方塊](localization/_static/res.png)

1. <span data-ttu-id="e4c3d-429">在 [名稱] 資料行中輸入索引鍵值 (原生字串)，並在 [值] 資料行中輸入已翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-429">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx 檔案 (西班牙文的「歡迎使用」資源檔)，其中 [名稱] 資料行的文字為 Hello，而 [值] 資料行的文字為 Hola (Hello 的西班牙文)](localization/_static/hola.png)

   <span data-ttu-id="e4c3d-431">Visual Studio 會顯示 *Welcome.es.resx* 檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-431">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![方案總管，其中顯示「歡迎使用」的西班牙文 (es) 資源檔](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="e4c3d-433">資源檔命名</span><span class="sxs-lookup"><span data-stu-id="e4c3d-433">Resource file naming</span></span>

<span data-ttu-id="e4c3d-434">資源的命名方式是以其類別的完整類型名稱去掉組件名稱而得。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-434">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="e4c3d-435">例如，假設專案中的法文資源是 `LocalizationWebsite.Web.Startup` 類別、主要組件為 `LocalizationWebsite.Web.dll`，就會命名為 *Startup.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-435">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="e4c3d-436">若是 `LocalizationWebsite.Web.Controllers.HomeController` 類別的資源，則應命名為 *Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-436">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-437">如果目標類別的命名空間和組件名稱不相同，則需要使用完整類型名稱。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-437">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="e4c3d-438">比方說，範例專案中 `ExtraNamespace.Tools` 類型的資源會命名為 *ExtraNamespace.Tools.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-438">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="e4c3d-439">在範例專案中，`ConfigureServices` 方法會將 `ResourcesPath` 設為 "Resources"，因此首頁控制器的法文資源檔專案相對路徑即為 *Resources/Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-439">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-440">或者，您可以使用資料夾來收集資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-440">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="e4c3d-441">若是首頁控制器，路徑就是 *Resources/Controllers/HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-441">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-442">如果您不使用 `ResourcesPath` 選項，*.resx* 檔案即會放置在專案的基底目錄中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-442">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="e4c3d-443">`HomeController` 的資源檔會命名為 *Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-443">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-444">您可依據自己的資源檔收集方式，來選擇要使用點或路徑的命名慣例。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-444">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="e4c3d-445">資源名稱</span><span class="sxs-lookup"><span data-stu-id="e4c3d-445">Resource name</span></span> | <span data-ttu-id="e4c3d-446">點或路徑命名</span><span class="sxs-lookup"><span data-stu-id="e4c3d-446">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="e4c3d-447">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-447">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="e4c3d-448">點</span><span class="sxs-lookup"><span data-stu-id="e4c3d-448">Dot</span></span>  |
| <span data-ttu-id="e4c3d-449">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-449">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="e4c3d-450">路徑</span><span class="sxs-lookup"><span data-stu-id="e4c3d-450">Path</span></span> |

<span data-ttu-id="e4c3d-451">使用於 views 的資源檔會 `@inject IViewLocalizer` Razor 遵循類似的模式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-451">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="e4c3d-452">您可以使用點命名或路徑命名方式，來命名檢視的資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-452">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="e4c3d-453">Razor 查看資源檔，模仿其相關聯的視圖檔案的路徑。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-453">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="e4c3d-454">假設我們將 `ResourcesPath` 設為 "Resources"，與 *Views/Home/About.cshtml* 檢視建立關聯的法文資源檔可為下列其一：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-454">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="e4c3d-455">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-455">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="e4c3d-456">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-456">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="e4c3d-457">如果您不使用 `ResourcesPath` 選項，則檢視的 *.resx* 檔案會與檢視位於相同資料夾中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-457">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="e4c3d-458">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="e4c3d-458">RootNamespaceAttribute</span></span> 

<span data-ttu-id="e4c3d-459"><xref:Microsoft.Extensions.Localization.RootNamespaceAttribute>當元件的根命名空間與元件名稱不同時，屬性會提供元件的根命名空間。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-459">The <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="e4c3d-460">當專案的名稱不是有效的 .NET 識別碼時，就會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-460">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="e4c3d-461">例如， `my-project-name.csproj` 會使用根命名空間 `my_project_name` 和 `my-project-name` 導致此錯誤的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-461">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="e4c3d-462">如果組件的根命名空間與組件名稱不同：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-462">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="e4c3d-463">根據預設，當地語系化無法運作。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-463">Localization does not work by default.</span></span>
* <span data-ttu-id="e4c3d-464">在組件中搜尋資源的方式造成當地語系化失敗。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-464">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="e4c3d-465">`RootNamespace` 是建置時間值，無法用於執行處理序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-465">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="e4c3d-466">如果 `RootNamespace` 與 `AssemblyName` 不同，請將下列內容納入 *AssemblyInfo.cs* (參數值取代為實際值)：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-466">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="e4c3d-467">上述程式碼可成功解析 resx 檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-467">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="e4c3d-468">文化特性後援行為</span><span class="sxs-lookup"><span data-stu-id="e4c3d-468">Culture fallback behavior</span></span>

<span data-ttu-id="e4c3d-469">搜尋資源時，當地語系化會使用「文化特性後援」。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-469">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="e4c3d-470">從所要求的文化特性開始，如果找不到，就會還原成該文化特性的父文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-470">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="e4c3d-471">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 屬性代表父文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-471">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="e4c3d-472">這通常 (但並非一定) 表示從 ISO 中移除國家意符 (Signifier)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-472">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="e4c3d-473">例如，墨西哥的西班牙文方言是 "es-MX"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-473">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="e4c3d-474">它具有父系 "es" &mdash; 西班牙文不是任何國家/地區的特定項目。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-474">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="e4c3d-475">假設您的網站收到使用文化特性 "fr-CA" 之 "Welcome" 資源的要求。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-475">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="e4c3d-476">當地語系化系統會依照順序尋找下列資源，並選取第一個相符項目：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-476">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="e4c3d-477">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-477">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="e4c3d-478">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-478">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="e4c3d-479">*Welcome.resx* (如果 `NeutralResourcesLanguage` 是 "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="e4c3d-479">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="e4c3d-480">舉例來說，如果您移除 ".fr" 文化特性指示項，並將文化特性設定為法文，則系統會讀取預設資源檔，並將字串當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-480">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="e4c3d-481">當沒有任何項目符合您要求的文化特性時，資源管理員即會指定預設資源或後援資源。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-481">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="e4c3d-482">如果您只想在要求的文化特性缺少資源時傳回索引鍵，就不能使用預設資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-482">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="e4c3d-483">使用 Visual Studio 產生資源檔</span><span class="sxs-lookup"><span data-stu-id="e4c3d-483">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="e4c3d-484">如果您在 Visual Studio 中建立資源檔，但檔案名稱中不含文化特性 (例如 *Welcome.resx*)，則 Visual Studio 會針對每個字串的屬性建立 C# 類別。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-484">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="e4c3d-485">但這通常不是您使用 ASP.NET Core 的初衷。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-485">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="e4c3d-486">一般來說，您不會有預設的 *.resx* 資源檔 (不含文化特性名稱的 *.resx* 檔案)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-486">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="e4c3d-487">因此，建議您建立含有文化特性名稱的 *.resx* 檔案 (例如 *Welcome.fr.resx*)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-487">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="e4c3d-488">當您建立含有文化特性名稱的 *.resx* 檔案時，Visual Studio 就不會產生類別檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-488">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="e4c3d-489">新增其他文化特性</span><span class="sxs-lookup"><span data-stu-id="e4c3d-489">Add other cultures</span></span>

<span data-ttu-id="e4c3d-490">每種語言和文化特性的組合 (非預設語言) 都需要唯一的資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-490">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="e4c3d-491">若要建立不同文化特性和地區設定的資源檔，您可以建立新的資源檔並將 ISO 語言代碼作為檔名的一部分 (例如 **en-us**、**fr-ca** 和 **en-gb**)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-491">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="e4c3d-492">您應將 ISO 代碼置於檔案名稱和 *.resx* 副檔名之間，例如 *Welcome.es-MX.resx* (西班牙文/墨西哥)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-492">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="e4c3d-493">實作可依據每項要求選取語言/文化特性的策略</span><span class="sxs-lookup"><span data-stu-id="e4c3d-493">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="e4c3d-494">設定當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-494">Configure localization</span></span>

<span data-ttu-id="e4c3d-495">您可以在 `Startup.ConfigureServices` 方法中設定當地語系化：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-495">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="e4c3d-496">`AddLocalization` 將當地語系化服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-496">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="e4c3d-497">上方的程式碼也會將資源路徑設為 "Resources"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-497">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="e4c3d-498">`AddViewLocalization` 新增當地語系化視圖檔案的支援。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-498">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="e4c3d-499">在此範例中，檢視的當地語系化會以檢視檔案的後置詞為依據。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-499">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="e4c3d-500">例如 *Index.fr.cshtml* 檔案中的 "fr"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-500">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="e4c3d-501">`AddDataAnnotationsLocalization` 透過抽象新增當地語系化 `DataAnnotations` 驗證訊息的支援 `IStringLocalizer` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-501">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="e4c3d-502">當地語系化中介軟體</span><span class="sxs-lookup"><span data-stu-id="e4c3d-502">Localization middleware</span></span>

<span data-ttu-id="e4c3d-503">您可以在當地語系化[中介軟體](xref:fundamentals/middleware/index)中，設定要求目前的文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-503">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="e4c3d-504">已在 `Startup.Configure` 方法中啟用當地語系化中介軟體。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-504">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="e4c3d-505">您必須在可能檢查要求文化特性的任何中介軟體之前，設定當地語系化中介軟體 (例如 `app.UseMvcWithDefaultRoute()`)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-505">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e4c3d-506">`UseRequestLocalization` 會初始化 `RequestLocalizationOptions` 物件。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-506">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="e4c3d-507">在每次要求時，系統會列舉 `RequestLocalizationOptions` 中的 `RequestCultureProvider` 清單，並使用能成功判斷要求的文化特性的第一個提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-507">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="e4c3d-508">預設的提供者是來自 `RequestLocalizationOptions` 類別：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-508">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="e4c3d-509">預設清單會由針對性高到低來排列。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-509">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="e4c3d-510">在本文稍後，我們會說明如何變更順序，甚至新增自訂的文化特性提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-510">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="e4c3d-511">如果沒有任何提供者可以判斷要求的文化特性，即會使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-511">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="e4c3d-512">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e4c3d-512">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="e4c3d-513">有些應用程式會使用查詢字串來設定 <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1> 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-513">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="e4c3d-514">針對使用 cookie 或 Accept-Language 標頭方法的應用程式，將查詢字串新增至 URL 可用於偵錯工具代碼和測試程式碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-514">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="e4c3d-515">系統預設會將 `QueryStringRequestCultureProvider` 登錄為 `RequestCultureProvider` 清單中的第一個當地語系化提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-515">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="e4c3d-516">您應傳遞查詢字串參數 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-516">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="e4c3d-517">下列範例會設定西班牙文/墨西哥的特定文化特性 (語言和地區)：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-517">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="e4c3d-518">如果您只傳入兩者其一 (`culture` 或 `ui-culture`)，查詢字串提供者就會使用您傳入的項目來設定這兩個值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-518">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="e4c3d-519">例如，若只設定文化特性，即會同時設定 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-519">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="e4c3d-520">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e4c3d-520">CookieRequestCultureProvider</span></span>

<span data-ttu-id="e4c3d-521">生產環境應用程式通常會提供一種機制，以 ASP.NET 核心文化特性設定文化特性 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-521">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="e4c3d-522">使用 `MakeCookieValue` 方法來建立 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-522">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="e4c3d-523">會傳回 `CookieRequestCultureProvider` `DefaultCookieName` cookie 用來追蹤使用者慣用文化特性資訊的預設名稱。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-523">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="e4c3d-524">預設 cookie 名稱為 `.AspNetCore.Culture` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-524">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="e4c3d-525">cookie格式為 `c=%LANGCODE%|uic=%LANGCODE%` ，其中 `c` 是 `Culture` 和，例如 `uic` `UICulture` ：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-525">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="e4c3d-526">如果您只指定文化特性資訊和 UI 文化特性其一，系統就會將您所指定的文化特性用於文化特性資訊和 UI 文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-526">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="e4c3d-527">Accept-Language HTTP 標頭</span><span class="sxs-lookup"><span data-stu-id="e4c3d-527">The Accept-Language HTTP header</span></span>

<span data-ttu-id="e4c3d-528">您可在大多數的瀏覽器中設定 [Accept-Language 標頭](https://www.w3.org/International/questions/qa-accept-lang-locales)，其最初的設計目的是用來指定使用者的語言。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-528">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="e4c3d-529">這項設定可指出瀏覽器已設好要傳送哪些項目，或已從基礎作業系統繼承哪些項目。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-529">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="e4c3d-530">透過瀏覽器要求的 Accept-Language HTTP 標頭來偵測使用者的慣用語言，並非萬無一失 (請參閱 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) (在瀏覽器中設定語言喜好設定)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-530">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="e4c3d-531">生產環境應用程式應該包含可讓使用者自訂文化特性的選擇方式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-531">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="e4c3d-532">在 IE 中設定 Accept-Language HTTP 標頭</span><span class="sxs-lookup"><span data-stu-id="e4c3d-532">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="e4c3d-533">從齒輪圖示，點選 [網際網路選項]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-533">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="e4c3d-534">點選 [語言]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-534">Tap **Languages**.</span></span>

   ![網際網路選項](localization/_static/lang.png)

1. <span data-ttu-id="e4c3d-536">點選 [設定語言喜好設定]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-536">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="e4c3d-537">點選 [新增語言]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-537">Tap **Add a language**.</span></span>

1. <span data-ttu-id="e4c3d-538">新增語言。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-538">Add the language.</span></span>

1. <span data-ttu-id="e4c3d-539">點選語言，然後點選 [上移]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-539">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="e4c3d-540">使用自訂提供者</span><span class="sxs-lookup"><span data-stu-id="e4c3d-540">Use a custom provider</span></span>

<span data-ttu-id="e4c3d-541">假設您想要讓客戶將他們的語言和文化特性儲存在您的資料庫中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-541">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="e4c3d-542">您可以撰寫提供者，以供使用者查詢這些值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-542">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="e4c3d-543">下列程式碼會示範如何新增自訂提供者：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-543">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="e4c3d-544">使用 `RequestLocalizationOptions` 新增或移除當地語系化提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-544">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="e4c3d-545">以程式設計方式來設定文化特性</span><span class="sxs-lookup"><span data-stu-id="e4c3d-545">Set the culture programmatically</span></span>

<span data-ttu-id="e4c3d-546">[GitHub](https://github.com/aspnet/entropy) 上的這個範例 **Localization.StarterWeb** 專案包含可設定 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-546">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="e4c3d-547">*Views/Shared/_SelectLanguagePartial.cshtml* 檔可讓您從支援的文化特性清單中選取文化特性：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-547">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="e4c3d-548">系統會將 *Views/Shared/_SelectLanguagePartial.cshtml* 檔案新增至配置檔案的 `footer` 區段，以供所有檢視使用：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-548">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="e4c3d-549">`SetLanguage`方法會設定文化特性 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-549">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="e4c3d-550">您無法將 *_SelectLanguagePartial.cshtml* 插入這個專案的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-550">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="e4c3d-551">[GitHub](https://github.com/aspnet/entropy)上的 **>localization.starterweb** 專案有程式碼，可透過相依性 `RequestLocalizationOptions` 插入容器將傳送至 Razor 部分。 [](dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="e4c3d-551">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="e4c3d-552">模型系結路由資料和查詢字串</span><span class="sxs-lookup"><span data-stu-id="e4c3d-552">Model binding route data and query strings</span></span>

<span data-ttu-id="e4c3d-553">請參閱模型系結 [路由資料和查詢字串的全球化行為](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-553">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="e4c3d-554">全球化和當地語系化詞彙</span><span class="sxs-lookup"><span data-stu-id="e4c3d-554">Globalization and localization terms</span></span>

<span data-ttu-id="e4c3d-555">在進行應用程式的當地語系化程序時，您也需要具備現代軟體開發中常用的相關字元集基本知識，並了解與它們建立關聯的問題。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-555">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="e4c3d-556">雖然所有電腦都會將文字儲存為數字 (程式碼)，但不同系統會使用不同數字來儲存相同的文字。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-556">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="e4c3d-557">當地語系化是指針對特定文化特性/地區設定，轉譯應用程式使用者介面 (UI) 的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-557">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="e4c3d-558">[可當地語系化](/dotnet/standard/globalization-localization/localizability-review)是確認全球化應用程式已準備好進行當地語系化的中繼程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-558">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="e4c3d-559">文化特性名稱的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式是 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是語言代碼，而 `<country/regioncode2>` 是子文化特性代碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-559">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="e4c3d-560">例如，`es-CL` 是指西班牙文 (智利)，`en-US` 是指英文 (美國)，而 `en-AU` 是指英文 (澳大利亞)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-560">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="e4c3d-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是 ISO 639 (兩個小寫字母，為與某個語言建立關聯的文化特性代碼) 及 ISO 3166 (兩個大寫字母，為與某個國家或地區建立關聯的子文化特性代碼) 的組合。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="e4c3d-562">請參閱 <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-562">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="e4c3d-563">國際化通常縮寫為 "I18N"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-563">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="e4c3d-564">這個縮寫是採用該詞彙的第一個和最後一個字母，以及這兩個字母間的字母數組成，因此 18 代表第一個字母 "I" 及最後 "N" 中間的字母數。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-564">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="e4c3d-565">同樣的原則也適用於全球化 (G11N) 與當地語系化 (L10N) 的縮寫。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-565">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="e4c3d-566">詞彙：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-566">Terms:</span></span>

* <span data-ttu-id="e4c3d-567">全球化 (G11N)：讓應用程式支援不同語言和區域的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-567">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="e4c3d-568">當地語系化 (L10N)：針對特定語言和區域，自訂應用程式的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-568">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="e4c3d-569">國際化 (I18N)：包含全球化和當地語系化這兩部分的描述。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-569">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="e4c3d-570">文化特性：它是一種語言和/或地區。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-570">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="e4c3d-571">中性文化特性：具有指定的語言但不限地區的文化特性 </span><span class="sxs-lookup"><span data-stu-id="e4c3d-571">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="e4c3d-572">(例如 "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-572">(for example "en", "es")</span></span>
* <span data-ttu-id="e4c3d-573">特定文化特性：具有指定的語言和地區的文化特性 </span><span class="sxs-lookup"><span data-stu-id="e4c3d-573">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="e4c3d-574">(例如 "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-574">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="e4c3d-575">父文化特性：包含特定文化特性的中性文化特性 </span><span class="sxs-lookup"><span data-stu-id="e4c3d-575">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="e4c3d-576">(例如，"en" 是 "en-US" 和 "en-GB" 的父文化特性)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-576">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="e4c3d-577">地區設定：地區設定與文化特性相同。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-577">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="e4c3d-578">其他資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-578">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="e4c3d-579">本文使用的 [Localization.StarterWeb 專案](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-579">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="e4c3d-580">全球化與當地語系化 .NET 應用程式</span><span class="sxs-lookup"><span data-stu-id="e4c3d-580">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="e4c3d-581">.Resx 檔案中的資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-581">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="e4c3d-582">Microsoft 多語應用程式工具組</span><span class="sxs-lookup"><span data-stu-id="e4c3d-582">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="e4c3d-583">當地語系化和泛型</span><span class="sxs-lookup"><span data-stu-id="e4c3d-583">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="e4c3d-584">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya) 提供</span><span class="sxs-lookup"><span data-stu-id="e4c3d-584">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="e4c3d-585">多語系網站可讓網站觸及更廣大的物件。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-585">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="e4c3d-586">ASP.NET Core 提供服務與中介軟體，可將網站當地語系化成不同的語言與文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-586">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="e4c3d-587">國際化包含[全球化](/dotnet/api/system.globalization)和[當地語系化](/dotnet/standard/globalization-localization/localization)這兩部分。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-587">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="e4c3d-588">全球化是指設計出可支援不同文化特性之應用程式的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-588">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="e4c3d-589">透過全球化，您可新增支援與特定地區相關之已定義語言指令碼的輸入、顯示及輸出作業。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-589">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="e4c3d-590">當地語系化是指針對全球化應用程式進行調整的程序，且您已順應特定文化特性/地區設定對這些全球化應用程式進行可當地語系化處理。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-590">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="e4c3d-591">如需詳細資訊，請參閱本文件結尾處的 **全球化和當地語系化詞彙**。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-591">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="e4c3d-592">應用程式當地語系化包含下列作業：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-592">App localization involves the following:</span></span>

1. <span data-ttu-id="e4c3d-593">讓應用程式的內容可當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-593">Make the app's content localizable</span></span>
1. <span data-ttu-id="e4c3d-594">針對您支援的語言和文化特性提供當地語系化資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-594">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="e4c3d-595">實作可依據每項要求選取語言/文化特性的策略</span><span class="sxs-lookup"><span data-stu-id="e4c3d-595">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="e4c3d-596">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="e4c3d-596">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="e4c3d-597">讓應用程式的內容可當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-597">Make the app's content localizable</span></span>

<span data-ttu-id="e4c3d-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> 而且 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 是為了改善開發當地語系化應用程式時的生產力而設計。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="e4c3d-599">`IStringLocalizer` 使用 <xref:System.Resources.ResourceManager> 和， <xref:System.Resources.ResourceReader> 在執行時間提供特定文化特性的資源。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-599">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="e4c3d-600">介面具有可傳回當地語系化字串的索引子和 `IEnumerable` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-600">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="e4c3d-601">`IStringLocalizer` 不需要將預設語言字串儲存在資源檔中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-601">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="e4c3d-602">您不必在開發初期建立資源檔，即可開發以當地語系化為目標的應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-602">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="e4c3d-603">下列程式碼會示範如何包裝 "About Title" 字串以進行當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-603">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="e4c3d-604">在上述程式碼中， `IStringLocalizer<T>` 執行是來自相依性 [插入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-604">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="e4c3d-605">如果找不到 "About Title" 的當地語系化值，即會傳回索引子的索引鍵，也就是 "About Title" 字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-605">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="e4c3d-606">您可以保留應用程式中的預設語言常值字串，並將其包裝在當地語系化工具中，以便專注於開發應用程式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-606">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="e4c3d-607">您不用先建立預設資源檔，即可使用預設語言來開發應用程式，並針對當地語系化步驟進行應用程式的準備。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-607">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="e4c3d-608">或者，您可以使用傳統方法，並提供索引鍵以擷取預設語言字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-608">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="e4c3d-609">對許多開發人員來說，新的工作流程 (單純包裝字串常值而不使用預設語言的 *.resx* 檔案) 可以降低當地語系化應用程式的額外負荷。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-609">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="e4c3d-610">其他開發人員則偏好傳統的工作流程，因為這種方法更便於使用較長的字串常值，且更易於更新當地語系化的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-610">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="e4c3d-611">若是包含 HTML 的資源，請使用 `IHtmlLocalizer<T>` 實作。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-611">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="e4c3d-612">`IHtmlLocalizer` 會對資源字串中經過格式化的引數進行 HTML 編碼，但不會對資源字串本身進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-612">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="e4c3d-613">在下列醒目提示的範例中，只有 `name` 參數的值經過 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-613">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="e4c3d-614">一般而言，只會當地語系化文字，而非 HTML。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-614">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="e4c3d-615">您可以在最底層的[相依性插入](dependency-injection.md)中，將 `IStringLocalizerFactory` 移出：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-615">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="e4c3d-616">上述程式碼會示範這兩個 Factory Create 方法。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-616">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="e4c3d-617">您可以依據控制器或區域來分割當地語系化的字串，也可以只用一個容器。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-617">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="e4c3d-618">在範例應用程式中，會針對共用資源使用名為 `SharedResource` 的虛擬類別。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-618">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="e4c3d-619">有些開發人員會使用 `Startup` 類別來包含全域或共用字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-619">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="e4c3d-620">下列範例會使用 `InfoController` 和 `SharedResource` 當地語系化工具：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-620">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="e4c3d-621">檢視當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-621">View localization</span></span>

<span data-ttu-id="e4c3d-622">`IViewLocalizer` 服務可提供[檢視](xref:mvc/views/overview)的當地語系化字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-622">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="e4c3d-623">`ViewLocalizer` 類別會實作這個介面，並透過檢視檔案路徑來找出資源的位置。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-623">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="e4c3d-624">下列程式碼示範如何使用 `IViewLocalizer` 的預設實作：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-624">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="e4c3d-625">`IViewLocalizer` 的預設實作會依據檢視的檔案名稱來找出資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-625">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="e4c3d-626">其中並沒有任何選項可以使用全域共用的資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-626">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="e4c3d-627">`ViewLocalizer` 使用來執行當地語系化工具 `IHtmlLocalizer` ，因此 Razor 不會對當地語系化的字串進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-627">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="e4c3d-628">您可以參數化資源字串，`IViewLocalizer` 即會對參數 (而不是資源字串) 進行 HTML 編碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-628">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="e4c3d-629">請考慮下列 Razor 標記：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-629">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="e4c3d-630">法文資源檔可能包含下列內容：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-630">A French resource file could contain the following:</span></span>

| <span data-ttu-id="e4c3d-631">Key</span><span class="sxs-lookup"><span data-stu-id="e4c3d-631">Key</span></span> | <span data-ttu-id="e4c3d-632">值</span><span class="sxs-lookup"><span data-stu-id="e4c3d-632">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="e4c3d-633">轉譯的檢視內容可能包含來自資源檔的 HTML 標記。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-633">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="e4c3d-634">一般而言，只會當地語系化文字，而非 HTML。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-634">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="e4c3d-635">若要在檢視中使用共用的資源檔，請插入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-635">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="e4c3d-636">DataAnnotations 當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-636">DataAnnotations localization</span></span>

<span data-ttu-id="e4c3d-637">DataAnnotations 錯誤訊息會使用 `IStringLocalizer<T>` 來當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-637">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="e4c3d-638">使用 `ResourcesPath = "Resources"` 選項時，`RegisterViewModel` 中的錯誤訊息會儲存在下列路徑之一：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-638">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="e4c3d-639">*Resources/Viewmodel. Account. RegisterViewModel fr-fr*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="e4c3d-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="e4c3d-641">在 ASP.NET Core MVC 1.1.0 和更高版本中，系統會將非驗證屬性當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-641">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="e4c3d-642">ASP.NET Core MVC 1.0 **不會** 查閱非驗證屬性的當地語系化字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-642">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="e4c3d-643">針對多個類別使用同一個資源字串</span><span class="sxs-lookup"><span data-stu-id="e4c3d-643">Using one resource string for multiple classes</span></span>

<span data-ttu-id="e4c3d-644">下列程式碼會示範如何針對含有多個類別的驗證屬性使用同一個資源字串：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-644">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="e4c3d-645">在先前的程式碼中，`SharedResource` 是與儲存驗證訊息的 resx 對應的類別。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-645">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="e4c3d-646">使用這個方法時，DataAnnotations 只會使用 `SharedResource`，而不是每個類別的資源。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-646">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="e4c3d-647">針對您支援的語言和文化特性提供當地語系化資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-647">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="e4c3d-648">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="e4c3d-648">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="e4c3d-649">ASP.NET Core 可讓您指定 `SupportedCultures` 和 `SupportedUICultures` 這兩個文化特性值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-649">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="e4c3d-650">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 物件可決定文化特性相依函式的結果，例如日期、時間、數字及貨幣格式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-650">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="e4c3d-651">`SupportedCultures` 也可決定文字排列順序、大小寫慣例和字串比較。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-651">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="e4c3d-652">如需伺服器如何取得文化特性的詳細資訊，請參閱 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-652">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="e4c3d-653">`SupportedUICultures`會判斷 [ResourceManager](/dotnet/api/system.resources.resourcemanager) (從 *.resx* 檔) 的翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-653">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="e4c3d-654">`ResourceManager` 僅會查閱 `CurrentUICulture` 所決定之文化特性特有的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-654">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="e4c3d-655">.NET 中的每個執行緒都有 `CurrentCulture` 和 `CurrentUICulture` 物件。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-655">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="e4c3d-656">ASP.NET Core 會在轉譯文化特性相依函式時檢查這些值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-656">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="e4c3d-657">比方說，如果目前執行緒的文化特性設定為 "en-US" (英文 - 美國)，`DateTime.Now.ToLongDateString()` 會顯示 "Thursday, February 18, 2016"，但如果 `CurrentCulture` 設定為 "es-ES" (西班牙文 - 西班牙)，則輸出會是 "jueves, 18 de febrero de 2016"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-657">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="e4c3d-658">資源檔</span><span class="sxs-lookup"><span data-stu-id="e4c3d-658">Resource files</span></span>

<span data-ttu-id="e4c3d-659">資源檔是一種實用的機制，可讓您將可當地語系化的字串與代碼區隔開來。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-659">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="e4c3d-660">非預設語言的翻譯字串會隔離在 *.resx* 資源檔中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-660">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="e4c3d-661">例如，您可以建立名為 *Welcome.es.resx* 的西班牙文資源檔，以包含翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-661">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="e4c3d-662">"es" 是西班牙文的語言代碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-662">"es" is the language code for Spanish.</span></span> <span data-ttu-id="e4c3d-663">若要在 Visual Studio 中建立這個資源檔：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-663">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="e4c3d-664">在方案總管中，以滑鼠右鍵按一下要放置資源檔的資料夾 > [新增]**[新增項目]** > 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-664">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![巢狀特色選單：方案總管會開啟 [資源] 的特色選單，](localization/_static/newi.png)

1. <span data-ttu-id="e4c3d-667">在 [Search installed templates] (搜尋已安裝的範本) 方塊中，輸入「資源」，並命名檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-667">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![[新增項目] 對話方塊](localization/_static/res.png)

1. <span data-ttu-id="e4c3d-669">在 [名稱] 資料行中輸入索引鍵值 (原生字串)，並在 [值] 資料行中輸入已翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-669">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx 檔案 (西班牙文的「歡迎使用」資源檔)，其中 [名稱] 資料行的文字為 Hello，而 [值] 資料行的文字為 Hola (Hello 的西班牙文)](localization/_static/hola.png)

   <span data-ttu-id="e4c3d-671">Visual Studio 會顯示 *Welcome.es.resx* 檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-671">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![方案總管，其中顯示「歡迎使用」的西班牙文 (es) 資源檔](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="e4c3d-673">資源檔命名</span><span class="sxs-lookup"><span data-stu-id="e4c3d-673">Resource file naming</span></span>

<span data-ttu-id="e4c3d-674">資源的命名方式是以其類別的完整類型名稱去掉組件名稱而得。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-674">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="e4c3d-675">例如，假設專案中的法文資源是 `LocalizationWebsite.Web.Startup` 類別、主要組件為 `LocalizationWebsite.Web.dll`，就會命名為 *Startup.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-675">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="e4c3d-676">若是 `LocalizationWebsite.Web.Controllers.HomeController` 類別的資源，則應命名為 *Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-676">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-677">如果目標類別的命名空間和組件名稱不相同，則需要使用完整類型名稱。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-677">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="e4c3d-678">比方說，範例專案中 `ExtraNamespace.Tools` 類型的資源會命名為 *ExtraNamespace.Tools.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-678">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="e4c3d-679">在範例專案中，`ConfigureServices` 方法會將 `ResourcesPath` 設為 "Resources"，因此首頁控制器的法文資源檔專案相對路徑即為 *Resources/Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-679">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-680">或者，您可以使用資料夾來收集資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-680">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="e4c3d-681">若是首頁控制器，路徑就是 *Resources/Controllers/HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-681">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-682">如果您不使用 `ResourcesPath` 選項，*.resx* 檔案即會放置在專案的基底目錄中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-682">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="e4c3d-683">`HomeController` 的資源檔會命名為 *Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-683">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="e4c3d-684">您可依據自己的資源檔收集方式，來選擇要使用點或路徑的命名慣例。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-684">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="e4c3d-685">資源名稱</span><span class="sxs-lookup"><span data-stu-id="e4c3d-685">Resource name</span></span> | <span data-ttu-id="e4c3d-686">點或路徑命名</span><span class="sxs-lookup"><span data-stu-id="e4c3d-686">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="e4c3d-687">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-687">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="e4c3d-688">點</span><span class="sxs-lookup"><span data-stu-id="e4c3d-688">Dot</span></span>  |
| <span data-ttu-id="e4c3d-689">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-689">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="e4c3d-690">路徑</span><span class="sxs-lookup"><span data-stu-id="e4c3d-690">Path</span></span> |

<span data-ttu-id="e4c3d-691">使用於 views 的資源檔會 `@inject IViewLocalizer` Razor 遵循類似的模式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-691">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="e4c3d-692">您可以使用點命名或路徑命名方式，來命名檢視的資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-692">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="e4c3d-693">Razor 查看資源檔，模仿其相關聯的視圖檔案的路徑。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-693">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="e4c3d-694">假設我們將 `ResourcesPath` 設為 "Resources"，與 *Views/Home/About.cshtml* 檢視建立關聯的法文資源檔可為下列其一：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-694">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="e4c3d-695">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-695">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="e4c3d-696">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="e4c3d-696">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="e4c3d-697">如果您不使用 `ResourcesPath` 選項，則檢視的 *.resx* 檔案會與檢視位於相同資料夾中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-697">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="e4c3d-698">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="e4c3d-698">RootNamespaceAttribute</span></span> 

<span data-ttu-id="e4c3d-699"><xref:Microsoft.Extensions.Localization.RootNamespaceAttribute>當元件的根命名空間與元件名稱不同時，屬性會提供元件的根命名空間。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-699">The <xref:Microsoft.Extensions.Localization.RootNamespaceAttribute> attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="e4c3d-700">當專案的名稱不是有效的 .NET 識別碼時，就會發生這種情況。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-700">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="e4c3d-701">例如， `my-project-name.csproj` 會使用根命名空間 `my_project_name` 和 `my-project-name` 導致此錯誤的元件名稱。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-701">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="e4c3d-702">如果組件的根命名空間與組件名稱不同：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-702">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="e4c3d-703">根據預設，當地語系化無法運作。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-703">Localization does not work by default.</span></span>
* <span data-ttu-id="e4c3d-704">在組件中搜尋資源的方式造成當地語系化失敗。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-704">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="e4c3d-705">`RootNamespace` 是建置時間值，無法用於執行處理序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-705">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="e4c3d-706">如果 `RootNamespace` 與 `AssemblyName` 不同，請將下列內容納入 *AssemblyInfo.cs* (參數值取代為實際值)：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-706">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="e4c3d-707">上述程式碼可成功解析 resx 檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-707">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="e4c3d-708">文化特性後援行為</span><span class="sxs-lookup"><span data-stu-id="e4c3d-708">Culture fallback behavior</span></span>

<span data-ttu-id="e4c3d-709">搜尋資源時，當地語系化會使用「文化特性後援」。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-709">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="e4c3d-710">從所要求的文化特性開始，如果找不到，就會還原成該文化特性的父文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-710">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="e4c3d-711">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 屬性代表父文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-711">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="e4c3d-712">這通常 (但並非一定) 表示從 ISO 中移除國家意符 (Signifier)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-712">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="e4c3d-713">例如，墨西哥的西班牙文方言是 "es-MX"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-713">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="e4c3d-714">它具有父系 "es" &mdash; 西班牙文不是任何國家/地區的特定項目。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-714">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="e4c3d-715">假設您的網站收到使用文化特性 "fr-CA" 之 "Welcome" 資源的要求。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-715">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="e4c3d-716">當地語系化系統會依照順序尋找下列資源，並選取第一個相符項目：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-716">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="e4c3d-717">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-717">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="e4c3d-718">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="e4c3d-718">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="e4c3d-719">*Welcome.resx* (如果 `NeutralResourcesLanguage` 是 "fr-CA")</span><span class="sxs-lookup"><span data-stu-id="e4c3d-719">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="e4c3d-720">舉例來說，如果您移除 ".fr" 文化特性指示項，並將文化特性設定為法文，則系統會讀取預設資源檔，並將字串當地語系化。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-720">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="e4c3d-721">當沒有任何項目符合您要求的文化特性時，資源管理員即會指定預設資源或後援資源。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-721">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="e4c3d-722">如果您只想在要求的文化特性缺少資源時傳回索引鍵，就不能使用預設資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-722">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="e4c3d-723">使用 Visual Studio 產生資源檔</span><span class="sxs-lookup"><span data-stu-id="e4c3d-723">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="e4c3d-724">如果您在 Visual Studio 中建立資源檔，但檔案名稱中不含文化特性 (例如 *Welcome.resx*)，則 Visual Studio 會針對每個字串的屬性建立 C# 類別。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-724">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="e4c3d-725">但這通常不是您使用 ASP.NET Core 的初衷。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-725">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="e4c3d-726">一般來說，您不會有預設的 *.resx* 資源檔 (不含文化特性名稱的 *.resx* 檔案)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-726">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="e4c3d-727">因此，建議您建立含有文化特性名稱的 *.resx* 檔案 (例如 *Welcome.fr.resx*)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-727">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="e4c3d-728">當您建立含有文化特性名稱的 *.resx* 檔案時，Visual Studio 就不會產生類別檔案。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-728">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="e4c3d-729">新增其他文化特性</span><span class="sxs-lookup"><span data-stu-id="e4c3d-729">Add other cultures</span></span>

<span data-ttu-id="e4c3d-730">每種語言和文化特性的組合 (非預設語言) 都需要唯一的資源檔。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-730">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="e4c3d-731">若要建立不同文化特性和地區設定的資源檔，您可以建立新的資源檔並將 ISO 語言代碼作為檔名的一部分 (例如 **en-us**、**fr-ca** 和 **en-gb**)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-731">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="e4c3d-732">您應將 ISO 代碼置於檔案名稱和 *.resx* 副檔名之間，例如 *Welcome.es-MX.resx* (西班牙文/墨西哥)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-732">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="e4c3d-733">實作可依據每項要求選取語言/文化特性的策略</span><span class="sxs-lookup"><span data-stu-id="e4c3d-733">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="e4c3d-734">設定當地語系化</span><span class="sxs-lookup"><span data-stu-id="e4c3d-734">Configure localization</span></span>

<span data-ttu-id="e4c3d-735">您可以在 `Startup.ConfigureServices` 方法中設定當地語系化：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-735">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="e4c3d-736">`AddLocalization` 將當地語系化服務新增至服務容器。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-736">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="e4c3d-737">上方的程式碼也會將資源路徑設為 "Resources"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-737">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="e4c3d-738">`AddViewLocalization` 新增當地語系化視圖檔案的支援。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-738">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="e4c3d-739">在此範例中，檢視的當地語系化會以檢視檔案的後置詞為依據。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-739">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="e4c3d-740">例如 *Index.fr.cshtml* 檔案中的 "fr"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-740">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="e4c3d-741">`AddDataAnnotationsLocalization` 透過抽象新增當地語系化 `DataAnnotations` 驗證訊息的支援 `IStringLocalizer` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-741">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="e4c3d-742">當地語系化中介軟體</span><span class="sxs-lookup"><span data-stu-id="e4c3d-742">Localization middleware</span></span>

<span data-ttu-id="e4c3d-743">您可以在當地語系化[中介軟體](xref:fundamentals/middleware/index)中，設定要求目前的文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-743">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="e4c3d-744">已在 `Startup.Configure` 方法中啟用當地語系化中介軟體。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-744">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="e4c3d-745">您必須在可能檢查要求文化特性的任何中介軟體之前，設定當地語系化中介軟體 (例如 `app.UseMvcWithDefaultRoute()`)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-745">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="e4c3d-746">`UseRequestLocalization` 會初始化 `RequestLocalizationOptions` 物件。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-746">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="e4c3d-747">在每次要求時，系統會列舉 `RequestLocalizationOptions` 中的 `RequestCultureProvider` 清單，並使用能成功判斷要求的文化特性的第一個提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-747">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="e4c3d-748">預設的提供者是來自 `RequestLocalizationOptions` 類別：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-748">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="e4c3d-749">預設清單會由針對性高到低來排列。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-749">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="e4c3d-750">在本文稍後，我們會說明如何變更順序，甚至新增自訂的文化特性提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-750">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="e4c3d-751">如果沒有任何提供者可以判斷要求的文化特性，即會使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-751">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="e4c3d-752">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e4c3d-752">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="e4c3d-753">有些應用程式會使用查詢字串來設定 <xref:System.Globalization.CultureInfo> 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-753">Some apps will use a query string to set the <xref:System.Globalization.CultureInfo>.</span></span> <span data-ttu-id="e4c3d-754">針對使用 cookie 或 Accept-Language 標頭方法的應用程式，將查詢字串新增至 URL 可用於偵錯工具代碼和測試程式碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-754">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="e4c3d-755">系統預設會將 `QueryStringRequestCultureProvider` 登錄為 `RequestCultureProvider` 清單中的第一個當地語系化提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-755">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="e4c3d-756">您應傳遞查詢字串參數 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-756">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="e4c3d-757">下列範例會設定西班牙文/墨西哥的特定文化特性 (語言和地區)：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-757">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="e4c3d-758">如果您只傳入兩者其一 (`culture` 或 `ui-culture`)，查詢字串提供者就會使用您傳入的項目來設定這兩個值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-758">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="e4c3d-759">例如，若只設定文化特性，即會同時設定 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-759">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="e4c3d-760">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="e4c3d-760">CookieRequestCultureProvider</span></span>

<span data-ttu-id="e4c3d-761">生產環境應用程式通常會提供一種機制，以 ASP.NET 核心文化特性設定文化特性 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-761">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="e4c3d-762">使用 `MakeCookieValue` 方法來建立 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-762">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="e4c3d-763">會傳回 `CookieRequestCultureProvider` `DefaultCookieName` cookie 用來追蹤使用者慣用文化特性資訊的預設名稱。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-763">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="e4c3d-764">預設 cookie 名稱為 `.AspNetCore.Culture` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-764">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="e4c3d-765">cookie格式為 `c=%LANGCODE%|uic=%LANGCODE%` ，其中 `c` 是 `Culture` 和，例如 `uic` `UICulture` ：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-765">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="e4c3d-766">如果您只指定文化特性資訊和 UI 文化特性其一，系統就會將您所指定的文化特性用於文化特性資訊和 UI 文化特性。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-766">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="e4c3d-767">Accept-Language HTTP 標頭</span><span class="sxs-lookup"><span data-stu-id="e4c3d-767">The Accept-Language HTTP header</span></span>

<span data-ttu-id="e4c3d-768">您可在大多數的瀏覽器中設定 [Accept-Language 標頭](https://www.w3.org/International/questions/qa-accept-lang-locales)，其最初的設計目的是用來指定使用者的語言。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-768">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="e4c3d-769">這項設定可指出瀏覽器已設好要傳送哪些項目，或已從基礎作業系統繼承哪些項目。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-769">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="e4c3d-770">透過瀏覽器要求的 Accept-Language HTTP 標頭來偵測使用者的慣用語言，並非萬無一失 (請參閱 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php) (在瀏覽器中設定語言喜好設定)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-770">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="e4c3d-771">生產環境應用程式應該包含可讓使用者自訂文化特性的選擇方式。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-771">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="e4c3d-772">在 IE 中設定 Accept-Language HTTP 標頭</span><span class="sxs-lookup"><span data-stu-id="e4c3d-772">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="e4c3d-773">從齒輪圖示，點選 [網際網路選項]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-773">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="e4c3d-774">點選 [語言]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-774">Tap **Languages**.</span></span>

   ![網際網路選項](localization/_static/lang.png)

1. <span data-ttu-id="e4c3d-776">點選 [設定語言喜好設定]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-776">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="e4c3d-777">點選 [新增語言]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-777">Tap **Add a language**.</span></span>

1. <span data-ttu-id="e4c3d-778">新增語言。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-778">Add the language.</span></span>

1. <span data-ttu-id="e4c3d-779">點選語言，然後點選 [上移]。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-779">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="e4c3d-780">Content-type HTTP 標頭</span><span class="sxs-lookup"><span data-stu-id="e4c3d-780">The Content-Language HTTP header</span></span>

<span data-ttu-id="e4c3d-781">[Content Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) entity 標頭：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-781">The [Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

* <span data-ttu-id="e4c3d-782">用來描述) 適用于物件的語言 (。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-782">Is used to describe the language(s) intended for the audience.</span></span>
* <span data-ttu-id="e4c3d-783">可讓使用者根據使用者的慣用語言來區分。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-783">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="e4c3d-784">HTTP 要求和回應中都會使用實體標頭。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-784">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="e4c3d-785">您 `Content-Language` 可以藉由設定屬性來新增標頭 `ApplyCurrentCultureToResponseHeaders` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-785">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="e4c3d-786">新增 `Content-Language` 標頭：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-786">Adding the `Content-Language` header:</span></span>

* <span data-ttu-id="e4c3d-787">允許 RequestLocalizationMiddleware `Content-Language` 使用設定標頭 `CurrentUICulture` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-787">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
* <span data-ttu-id="e4c3d-788">無須明確設定回應標頭 `Content-Language` 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-788">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="e4c3d-789">使用自訂提供者</span><span class="sxs-lookup"><span data-stu-id="e4c3d-789">Use a custom provider</span></span>

<span data-ttu-id="e4c3d-790">假設您想要讓客戶將他們的語言和文化特性儲存在您的資料庫中。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-790">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="e4c3d-791">您可以撰寫提供者，以供使用者查詢這些值。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-791">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="e4c3d-792">下列程式碼會示範如何新增自訂提供者：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-792">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="e4c3d-793">使用 `RequestLocalizationOptions` 新增或移除當地語系化提供者。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-793">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="e4c3d-794">以程式設計方式來設定文化特性</span><span class="sxs-lookup"><span data-stu-id="e4c3d-794">Set the culture programmatically</span></span>

<span data-ttu-id="e4c3d-795">[GitHub](https://github.com/aspnet/entropy) 上的這個範例 **Localization.StarterWeb** 專案包含可設定 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-795">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="e4c3d-796">*Views/Shared/_SelectLanguagePartial.cshtml* 檔可讓您從支援的文化特性清單中選取文化特性：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-796">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="e4c3d-797">系統會將 *Views/Shared/_SelectLanguagePartial.cshtml* 檔案新增至配置檔案的 `footer` 區段，以供所有檢視使用：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-797">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="e4c3d-798">`SetLanguage`方法會設定文化特性 cookie 。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-798">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="e4c3d-799">您無法將 *_SelectLanguagePartial.cshtml* 插入這個專案的範例程式碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-799">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="e4c3d-800">[GitHub](https://github.com/aspnet/entropy)上的 **>localization.starterweb** 專案有程式碼，可透過相依性 `RequestLocalizationOptions` 插入容器將傳送至 Razor 部分。 [](dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="e4c3d-800">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="e4c3d-801">模型系結路由資料和查詢字串</span><span class="sxs-lookup"><span data-stu-id="e4c3d-801">Model binding route data and query strings</span></span>

<span data-ttu-id="e4c3d-802">請參閱模型系結 [路由資料和查詢字串的全球化行為](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-802">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="e4c3d-803">全球化和當地語系化詞彙</span><span class="sxs-lookup"><span data-stu-id="e4c3d-803">Globalization and localization terms</span></span>

<span data-ttu-id="e4c3d-804">在進行應用程式的當地語系化程序時，您也需要具備現代軟體開發中常用的相關字元集基本知識，並了解與它們建立關聯的問題。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-804">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="e4c3d-805">雖然所有電腦都會將文字儲存為數字 (程式碼)，但不同系統會使用不同數字來儲存相同的文字。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-805">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="e4c3d-806">當地語系化是指針對特定文化特性/地區設定，轉譯應用程式使用者介面 (UI) 的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-806">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="e4c3d-807">[可當地語系化](/dotnet/standard/globalization-localization/localizability-review)是確認全球化應用程式已準備好進行當地語系化的中繼程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-807">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="e4c3d-808">文化特性名稱的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式是 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是語言代碼，而 `<country/regioncode2>` 是子文化特性代碼。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-808">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="e4c3d-809">例如，`es-CL` 是指西班牙文 (智利)，`en-US` 是指英文 (美國)，而 `en-AU` 是指英文 (澳大利亞)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-809">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="e4c3d-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是 ISO 639 (兩個小寫字母，為與某個語言建立關聯的文化特性代碼) 及 ISO 3166 (兩個大寫字母，為與某個國家或地區建立關聯的子文化特性代碼) 的組合。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="e4c3d-811">請參閱 <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-811">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="e4c3d-812">國際化通常縮寫為 "I18N"。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-812">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="e4c3d-813">這個縮寫是採用該詞彙的第一個和最後一個字母，以及這兩個字母間的字母數組成，因此 18 代表第一個字母 "I" 及最後 "N" 中間的字母數。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-813">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="e4c3d-814">同樣的原則也適用於全球化 (G11N) 與當地語系化 (L10N) 的縮寫。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-814">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="e4c3d-815">詞彙：</span><span class="sxs-lookup"><span data-stu-id="e4c3d-815">Terms:</span></span>

* <span data-ttu-id="e4c3d-816">全球化 (G11N)：讓應用程式支援不同語言和區域的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-816">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="e4c3d-817">當地語系化 (L10N)：針對特定語言和區域，自訂應用程式的程序。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-817">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="e4c3d-818">國際化 (I18N)：包含全球化和當地語系化這兩部分的描述。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-818">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="e4c3d-819">文化特性：它是一種語言和/或地區。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-819">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="e4c3d-820">中性文化特性：具有指定的語言但不限地區的文化特性 </span><span class="sxs-lookup"><span data-stu-id="e4c3d-820">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="e4c3d-821">(例如 "en"、"es")。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-821">(for example "en", "es")</span></span>
* <span data-ttu-id="e4c3d-822">特定文化特性：具有指定的語言和地區的文化特性 </span><span class="sxs-lookup"><span data-stu-id="e4c3d-822">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="e4c3d-823">(例如 "en-US"、"en-GB"、"es-CL")。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-823">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="e4c3d-824">父文化特性：包含特定文化特性的中性文化特性 </span><span class="sxs-lookup"><span data-stu-id="e4c3d-824">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="e4c3d-825">(例如，"en" 是 "en-US" 和 "en-GB" 的父文化特性)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-825">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="e4c3d-826">地區設定：地區設定與文化特性相同。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-826">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="e4c3d-827">其他資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-827">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="e4c3d-828">本文使用的 [Localization.StarterWeb 專案](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="e4c3d-828">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="e4c3d-829">全球化與當地語系化 .NET 應用程式</span><span class="sxs-lookup"><span data-stu-id="e4c3d-829">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="e4c3d-830">.Resx 檔案中的資源</span><span class="sxs-lookup"><span data-stu-id="e4c3d-830">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="e4c3d-831">Microsoft 多語應用程式工具組</span><span class="sxs-lookup"><span data-stu-id="e4c3d-831">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="e4c3d-832">當地語系化和泛型</span><span class="sxs-lookup"><span data-stu-id="e4c3d-832">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
