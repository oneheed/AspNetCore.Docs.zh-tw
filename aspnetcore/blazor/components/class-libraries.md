---
title: ASP.NET Core Razor 元件類別庫
author: guardrex
description: 探索如何將元件包含在 Blazor 來自外部元件程式庫的應用程式中。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components/class-libraries
ms.openlocfilehash: 8293d61f88f53e55d94b114ca2143fdfb6fd8468
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87819063"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="84bf9-103">ASP.NET Core Razor 元件類別庫</span><span class="sxs-lookup"><span data-stu-id="84bf9-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="84bf9-104">依[Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="84bf9-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="84bf9-105">元件可以在類別庫中共用， (跨專案的[ Razor RCL) ](xref:razor-pages/ui-class) 。</span><span class="sxs-lookup"><span data-stu-id="84bf9-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="84bf9-106">\* Razor 元件類別庫\*可以包含在：</span><span class="sxs-lookup"><span data-stu-id="84bf9-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="84bf9-107">方案中的另一個專案。</span><span class="sxs-lookup"><span data-stu-id="84bf9-107">Another project in the solution.</span></span>
* <span data-ttu-id="84bf9-108">NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="84bf9-108">A NuGet package.</span></span>
* <span data-ttu-id="84bf9-109">參考的 .NET 程式庫。</span><span class="sxs-lookup"><span data-stu-id="84bf9-109">A referenced .NET library.</span></span>

<span data-ttu-id="84bf9-110">就像元件是一般的 .NET 類型，RCL 所提供的元件是一般的 .NET 元件。</span><span class="sxs-lookup"><span data-stu-id="84bf9-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="84bf9-111">建立 RCL</span><span class="sxs-lookup"><span data-stu-id="84bf9-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84bf9-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84bf9-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="84bf9-113">建立新專案。</span><span class="sxs-lookup"><span data-stu-id="84bf9-113">Create a new project.</span></span>
1. <span data-ttu-id="84bf9-114">選取 [ \*\* Razor 類別庫\*\*]。</span><span class="sxs-lookup"><span data-stu-id="84bf9-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="84bf9-115">選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="84bf9-115">Select **Next**.</span></span>
1. <span data-ttu-id="84bf9-116">在 [**建立新的 Razor 類別庫**] 對話方塊中，選取 [**建立**]。</span><span class="sxs-lookup"><span data-stu-id="84bf9-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="84bf9-117">在 [專案名稱]\*\*\*\* 欄位中提供專案名稱，或接受預設專案名稱。</span><span class="sxs-lookup"><span data-stu-id="84bf9-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="84bf9-118">本主題中的範例會使用專案名稱 `ComponentLibrary` 。</span><span class="sxs-lookup"><span data-stu-id="84bf9-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="84bf9-119">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="84bf9-119">Select **Create**.</span></span>
1. <span data-ttu-id="84bf9-120">將 RCL 新增至方案：</span><span class="sxs-lookup"><span data-stu-id="84bf9-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="84bf9-121">以滑鼠右鍵按一下方案。</span><span class="sxs-lookup"><span data-stu-id="84bf9-121">Right-click the solution.</span></span> <span data-ttu-id="84bf9-122">選取 [**加入**  >  **現有專案**]。</span><span class="sxs-lookup"><span data-stu-id="84bf9-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="84bf9-123">流覽至 RCL 的專案檔。</span><span class="sxs-lookup"><span data-stu-id="84bf9-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="84bf9-124">選取 RCL 的專案檔案 (`.csproj`) ]。</span><span class="sxs-lookup"><span data-stu-id="84bf9-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="84bf9-125">從應用程式新增參考 RCL：</span><span class="sxs-lookup"><span data-stu-id="84bf9-125">Add a reference the RCL from the app:</span></span>
   1. <span data-ttu-id="84bf9-126">以滑鼠右鍵按一下應用程式專案。</span><span class="sxs-lookup"><span data-stu-id="84bf9-126">Right-click the app project.</span></span> <span data-ttu-id="84bf9-127">選取 [**新增**  >  **參考**]。</span><span class="sxs-lookup"><span data-stu-id="84bf9-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="84bf9-128">選取 [RCL] 專案。</span><span class="sxs-lookup"><span data-stu-id="84bf9-128">Select the RCL project.</span></span> <span data-ttu-id="84bf9-129">選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="84bf9-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="84bf9-130">從範本產生 RCL 時，如果已選取 [**支援頁面和視圖**] 核取方塊，則也會 `_Imports.razor` 使用下列內容將檔案新增至所產生專案的根目錄，以啟用 Razor 元件撰寫：</span><span class="sxs-lookup"><span data-stu-id="84bf9-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="84bf9-131">以手動方式將檔案新增至所產生專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="84bf9-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="84bf9-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="84bf9-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="84bf9-133">使用 [ \*\* Razor 類別庫\*\*] 範本 (`razorclasslib` [`dotnet new`](/dotnet/core/tools/dotnet-new) 在命令 shell 中使用命令) 。</span><span class="sxs-lookup"><span data-stu-id="84bf9-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="84bf9-134">在下列範例中，會建立名為的 RCL `ComponentLibrary` 。</span><span class="sxs-lookup"><span data-stu-id="84bf9-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="84bf9-135">`ComponentLibrary`執行命令時，會自動建立保存的資料夾：</span><span class="sxs-lookup"><span data-stu-id="84bf9-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="84bf9-136">如果 `-s|--support-pages-and-views` 從範本產生 RCL 時使用參數，則也會使用下列內容將檔案新增 `_Imports.razor` 至所產生專案的根目錄，以啟用 Razor 元件撰寫：</span><span class="sxs-lookup"><span data-stu-id="84bf9-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="84bf9-137">以手動方式將檔案新增至所產生專案的根目錄。</span><span class="sxs-lookup"><span data-stu-id="84bf9-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="84bf9-138">若要將程式庫新增至現有的專案，請 [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) 在命令 shell 中使用命令。</span><span class="sxs-lookup"><span data-stu-id="84bf9-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="84bf9-139">在下列範例中，會將 RCL 新增至應用程式。</span><span class="sxs-lookup"><span data-stu-id="84bf9-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="84bf9-140">從應用程式的專案資料夾中，使用程式庫的路徑執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="84bf9-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="84bf9-141">使用程式庫元件</span><span class="sxs-lookup"><span data-stu-id="84bf9-141">Consume a library component</span></span>

<span data-ttu-id="84bf9-142">若要使用另一個專案中的程式庫中所定義的元件，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="84bf9-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="84bf9-143">使用命名空間的完整類型名稱。</span><span class="sxs-lookup"><span data-stu-id="84bf9-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="84bf9-144">使用 Razor 的指示詞 [`@using`](xref:mvc/views/razor#using) 。</span><span class="sxs-lookup"><span data-stu-id="84bf9-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="84bf9-145">個別元件可以依名稱新增。</span><span class="sxs-lookup"><span data-stu-id="84bf9-145">Individual components can be added by name.</span></span>

<span data-ttu-id="84bf9-146">在下列範例中， `ComponentLibrary` 是包含 `Component1` 元件 () 的元件庫 `Component1.razor` 。</span><span class="sxs-lookup"><span data-stu-id="84bf9-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="84bf9-147">`Component1`元件是 RCL 專案範本在建立程式庫時自動新增的範例元件。</span><span class="sxs-lookup"><span data-stu-id="84bf9-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="84bf9-148">參考 `Component1` 使用其命名空間的元件：</span><span class="sxs-lookup"><span data-stu-id="84bf9-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="84bf9-149">或者，將程式庫帶入具有指示詞的範圍， [`@using`](xref:mvc/views/razor#using) 並使用不含其命名空間的元件：</span><span class="sxs-lookup"><span data-stu-id="84bf9-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="84bf9-150">（選擇性）在最上層檔案中包含指示詞， `@using ComponentLibrary` `_Import.razor` 讓程式庫的元件可供整個專案使用。</span><span class="sxs-lookup"><span data-stu-id="84bf9-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="84bf9-151">將指示詞新增至 `_Import.razor` 任何層級的檔案，以將命名空間套用至資料夾內的單一元件或元件集。</span><span class="sxs-lookup"><span data-stu-id="84bf9-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="84bf9-152">若要將 `Component1` 的 `my-component` CSS 類別提供給元件，請在中使用架構的[ `Link` 元件](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements)連結至程式庫的樣式表單 `Component1.razor` ：</span><span class="sxs-lookup"><span data-stu-id="84bf9-152">To provide `Component1`'s `my-component` CSS class to the component, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:</span></span>

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

<span data-ttu-id="84bf9-153">若要在應用程式中提供樣式表單，您也可以在應用程式的檔案中連結至程式庫的樣式表單 `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` (Blazor Server) ：</span><span class="sxs-lookup"><span data-stu-id="84bf9-153">To provide the stylesheet across the app, you can alternatively link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

<span data-ttu-id="84bf9-154">當 `Link` 元件在子元件中使用時，只要轉譯具有元件的子系，連結的資產也會提供給父元件的任何其他子元件 `Link` 。</span><span class="sxs-lookup"><span data-stu-id="84bf9-154">When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered.</span></span> <span data-ttu-id="84bf9-155">在子元件中使用 `Link` 元件以及將 `<link>` HTML 標籤放在或中的區別，在於 `wwwroot/index.html` `Pages/_Host.cshtml` 架構元件的轉譯 HTML 標籤：</span><span class="sxs-lookup"><span data-stu-id="84bf9-155">The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="84bf9-156">可由應用程式狀態修改。</span><span class="sxs-lookup"><span data-stu-id="84bf9-156">Can be modified by application state.</span></span> <span data-ttu-id="84bf9-157">`<link>`應用程式狀態無法修改硬式編碼的 HTML 標籤。</span><span class="sxs-lookup"><span data-stu-id="84bf9-157">A hard-coded `<link>` HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="84bf9-158">當父元件不再呈現時，會從 HTML 中移除 `<head>` 。</span><span class="sxs-lookup"><span data-stu-id="84bf9-158">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="84bf9-159">若要提供 `Component1` 的 `my-component` CSS 類別，請連結至應用程式檔案中文件庫的樣式表單 `wwwroot/index.html` (Blazor WebAssembly) 或檔案 `Pages/_Host.cshtml` (Blazor Server) ：</span><span class="sxs-lookup"><span data-stu-id="84bf9-159">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="84bf9-160">建立 Razor 具有靜態資產的元件類別庫</span><span class="sxs-lookup"><span data-stu-id="84bf9-160">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="84bf9-161">RCL 可以包含靜態資產。</span><span class="sxs-lookup"><span data-stu-id="84bf9-161">An RCL can include static assets.</span></span> <span data-ttu-id="84bf9-162">使用該程式庫的任何應用程式都可以使用靜態資產。</span><span class="sxs-lookup"><span data-stu-id="84bf9-162">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="84bf9-163">如需詳細資訊，請參閱<xref:razor-pages/ui-class#create-an-rcl-with-static-assets>。</span><span class="sxs-lookup"><span data-stu-id="84bf9-163">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="84bf9-164">將元件和靜態資產提供給多個託管 Blazor 應用程式</span><span class="sxs-lookup"><span data-stu-id="84bf9-164">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="84bf9-165">如需詳細資訊，請參閱<xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>。</span><span class="sxs-lookup"><span data-stu-id="84bf9-165">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="84bf9-166">組建、封裝和寄送至 NuGet</span><span class="sxs-lookup"><span data-stu-id="84bf9-166">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="84bf9-167">因為元件程式庫是標準的 .NET 程式庫，所以封裝和傳送至 NuGet 的方式與將任何程式庫封裝和傳送至 NuGet 的方式並無不同。</span><span class="sxs-lookup"><span data-stu-id="84bf9-167">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="84bf9-168">封裝是使用命令 [`dotnet pack`](/dotnet/core/tools/dotnet-pack) shell 中的命令來執行：</span><span class="sxs-lookup"><span data-stu-id="84bf9-168">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="84bf9-169">使用命令 shell 中的命令，將封裝上傳至 NuGet [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) 。</span><span class="sxs-lookup"><span data-stu-id="84bf9-169">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84bf9-170">其他資源</span><span class="sxs-lookup"><span data-stu-id="84bf9-170">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="84bf9-171">將 XML 連結器設定檔加入至程式庫</span><span class="sxs-lookup"><span data-stu-id="84bf9-171">Add an XML linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)
