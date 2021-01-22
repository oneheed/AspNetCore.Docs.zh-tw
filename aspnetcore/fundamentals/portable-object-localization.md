---
title: 使用 ASP.NET Core 設定可攜式物件當地語系化
author: sebastienros
description: 本文介紹可攜式物件檔案，並概述在具有 Orchard Core 架構的 ASP.NET Core 應用程式中使用它們的步驟。
ms.author: scaddie
ms.date: 09/26/2017
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 175614c426c564ce91068e18035ce05311432698
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689236"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="0eb25-103">使用 ASP.NET Core 設定可攜式物件當地語系化</span><span class="sxs-lookup"><span data-stu-id="0eb25-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0eb25-104">依 [Sébastien Ros](https://github.com/sebastienros)、 [Scott Addie](https://twitter.com/Scott_Addie) 和 [>hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="0eb25-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="0eb25-105">本文將逐步說明在具有 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 架構的 ASP.NET Core 應用程式中使用可攜式物件 (PO) 檔案的步驟。</span><span class="sxs-lookup"><span data-stu-id="0eb25-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="0eb25-106">**注意：** Orchard Core 不是 Microsoft 產品。</span><span class="sxs-lookup"><span data-stu-id="0eb25-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="0eb25-107">因此，Microsoft 不提供這項功能的支援。</span><span class="sxs-lookup"><span data-stu-id="0eb25-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="0eb25-108">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="0eb25-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="0eb25-109">什麼是 PO 檔案？</span><span class="sxs-lookup"><span data-stu-id="0eb25-109">What is a PO file?</span></span>

<span data-ttu-id="0eb25-110">PO 檔案以文字檔的形式散發，其中包含給定語言的翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="0eb25-111">使用 PO 檔案而非 *.resx* 檔的一些優點包括：</span><span class="sxs-lookup"><span data-stu-id="0eb25-111">Some advantages of using PO files instead of *.resx* files include:</span></span>
- <span data-ttu-id="0eb25-112">PO 檔案支援複數表示；*.resx* 檔案不支援複數表示。</span><span class="sxs-lookup"><span data-stu-id="0eb25-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="0eb25-113">PO 檔案不會像 *.resx* 檔案一樣進行編譯。</span><span class="sxs-lookup"><span data-stu-id="0eb25-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="0eb25-114">因此，不需要特殊化工具與建置步驟。</span><span class="sxs-lookup"><span data-stu-id="0eb25-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="0eb25-115">PO 檔案適用於共同作業的線上編輯工具。</span><span class="sxs-lookup"><span data-stu-id="0eb25-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="0eb25-116">範例</span><span class="sxs-lookup"><span data-stu-id="0eb25-116">Example</span></span>

<span data-ttu-id="0eb25-117">以下是範例 PO 檔案，其中包含兩個字串的法文翻譯，包括其複數形式的字串：</span><span class="sxs-lookup"><span data-stu-id="0eb25-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="0eb25-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="0eb25-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="0eb25-119">此範例使用下列語法：</span><span class="sxs-lookup"><span data-stu-id="0eb25-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="0eb25-120">`#:`：指出所要翻譯之字串內容的註解。</span><span class="sxs-lookup"><span data-stu-id="0eb25-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="0eb25-121">相同的字串可能會根據其使用位置而翻譯為不同內容。</span><span class="sxs-lookup"><span data-stu-id="0eb25-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="0eb25-122">`msgid`：未翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="0eb25-123">`msgstr`：已翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="0eb25-124">在支援複數表示的情況下，可以定義多個項目。</span><span class="sxs-lookup"><span data-stu-id="0eb25-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="0eb25-125">`msgid_plural`：未翻譯的複數字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="0eb25-126">`msgstr[0]`：案例 0 的已翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="0eb25-127">`msgstr[N]`：案例 N 的已翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="0eb25-128">您可以在[這裡](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)找到 PO 檔案規格。</span><span class="sxs-lookup"><span data-stu-id="0eb25-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="0eb25-129">在 ASP.NET Core 中設定 PO 檔案支援</span><span class="sxs-lookup"><span data-stu-id="0eb25-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="0eb25-130">這個範例是根據從 Visual Studio 2017 專案範本產生的 ASP.NET Core MVC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="0eb25-131">參考套件</span><span class="sxs-lookup"><span data-stu-id="0eb25-131">Referencing the package</span></span>

<span data-ttu-id="0eb25-132">將參考新增至 `OrchardCore.Localization.Core` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="0eb25-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="0eb25-133">*.csproj* 檔案現在包含與下列內容類似的一行 (版本號碼可能不同)：</span><span class="sxs-lookup"><span data-stu-id="0eb25-133">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="0eb25-134">註冊服務</span><span class="sxs-lookup"><span data-stu-id="0eb25-134">Registering the service</span></span>

<span data-ttu-id="0eb25-135">將所需的服務新增至 *Startup.cs* 的 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb25-135">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="0eb25-136">將必要的中介軟體新增至 *Startup.cs* 的 `Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb25-136">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="0eb25-137">將下列程式碼新增至您 Razor 選擇的觀點。</span><span class="sxs-lookup"><span data-stu-id="0eb25-137">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="0eb25-138">此範例中使用 *About.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="0eb25-138">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="0eb25-139">將會插入 `IViewLocalizer` 執行個體，用來翻譯文字 "Hello world!"。</span><span class="sxs-lookup"><span data-stu-id="0eb25-139">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="0eb25-140">建立 PO 檔案</span><span class="sxs-lookup"><span data-stu-id="0eb25-140">Creating a PO file</span></span>

<span data-ttu-id="0eb25-141">在您的應用程式根資料夾中，建立名為 po 的檔案 *\<culture code> 。*</span><span class="sxs-lookup"><span data-stu-id="0eb25-141">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="0eb25-142">在此範例中，檔案名稱是 *fr.po*，因為使用法文語言：</span><span class="sxs-lookup"><span data-stu-id="0eb25-142">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="0eb25-143">這個檔案會同時儲存要翻譯的字串和法文的翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-143">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="0eb25-144">如有必要，翻譯會還原為其父文化特性。</span><span class="sxs-lookup"><span data-stu-id="0eb25-144">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="0eb25-145">在此範例中，如果所要求的文化特性是 `fr-FR` 或 `fr-CA`，則會使用 *fr.po* 檔案。</span><span class="sxs-lookup"><span data-stu-id="0eb25-145">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="0eb25-146">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="0eb25-146">Testing the application</span></span>

<span data-ttu-id="0eb25-147">執行您的應用程式，並巡覽至 `/Home/About` URL。</span><span class="sxs-lookup"><span data-stu-id="0eb25-147">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="0eb25-148">文字 **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="0eb25-148">The text **Hello world!**</span></span> <span data-ttu-id="0eb25-149">就會出現。</span><span class="sxs-lookup"><span data-stu-id="0eb25-149">is displayed.</span></span>

<span data-ttu-id="0eb25-150">巡覽至 `/Home/About?culture=fr-FR`RL。</span><span class="sxs-lookup"><span data-stu-id="0eb25-150">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="0eb25-151">文字 **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="0eb25-151">The text **Bonjour le monde!**</span></span> <span data-ttu-id="0eb25-152">就會出現。</span><span class="sxs-lookup"><span data-stu-id="0eb25-152">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="0eb25-153">複數表示</span><span class="sxs-lookup"><span data-stu-id="0eb25-153">Pluralization</span></span>

<span data-ttu-id="0eb25-154">PO 檔案支援複數表示格式，如果相同的字串必須根據基數翻譯為不同的內容，這個檔案很有用。</span><span class="sxs-lookup"><span data-stu-id="0eb25-154">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="0eb25-155">由於每一種語言都會定義自訂規則來選取要根據基數使用的字串，因此這項工作變得複雜。</span><span class="sxs-lookup"><span data-stu-id="0eb25-155">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="0eb25-156">Orchard 當地語系化套件會提供 API，以自動叫用這些不同的複數形式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-156">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="0eb25-157">建立複數表示 PO 檔案</span><span class="sxs-lookup"><span data-stu-id="0eb25-157">Creating pluralization PO files</span></span>

<span data-ttu-id="0eb25-158">將下列內容新增至先前所述的 *fr.po* 檔案：</span><span class="sxs-lookup"><span data-stu-id="0eb25-158">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="0eb25-159">如需此範例中每個項目所代表意義的說明，請參閱[什麼是 PO 檔案？](#what-is-a-po-file)。</span><span class="sxs-lookup"><span data-stu-id="0eb25-159">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="0eb25-160">新增使用不同複數表示格式的語言</span><span class="sxs-lookup"><span data-stu-id="0eb25-160">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="0eb25-161">在上述範例中，已使用英文和法文字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-161">English and French strings were used in the previous example.</span></span> <span data-ttu-id="0eb25-162">英文和法文只有兩種複數表示格式，因此共用相同的格式規則，即基數一對應到第一個複數形式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-162">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="0eb25-163">任何其他基數都對應到第二個複數形式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-163">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="0eb25-164">並非所有語言都共用相同的規則。</span><span class="sxs-lookup"><span data-stu-id="0eb25-164">Not all languages share the same rules.</span></span> <span data-ttu-id="0eb25-165">以下是使用捷克文語言的說明，它有三種複數形式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-165">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="0eb25-166">如下所示建立 `cs.po` 檔案，並記下複數表示需要三種不同翻譯的方式：</span><span class="sxs-lookup"><span data-stu-id="0eb25-166">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="0eb25-167">若要接受捷克文的當地語系化，請將 `"cs"` 新增至 `ConfigureServices` 方法所支援的文化特性清單中：</span><span class="sxs-lookup"><span data-stu-id="0eb25-167">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="0eb25-168">編輯 *Views/Home/About.cshtml* 檔案來呈現數個基數的當地語系化複數字串：</span><span class="sxs-lookup"><span data-stu-id="0eb25-168">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="0eb25-169">**注意：** 在現實世界的案例中，變數將用來代表計數。</span><span class="sxs-lookup"><span data-stu-id="0eb25-169">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="0eb25-170">在這裡，我們會重複使用具有三個不同值的相同程式碼，以公開非常特殊的情況。</span><span class="sxs-lookup"><span data-stu-id="0eb25-170">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="0eb25-171">切換文化特性後，您會看到下列內容：</span><span class="sxs-lookup"><span data-stu-id="0eb25-171">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="0eb25-172">針對 `/Home/About`：</span><span class="sxs-lookup"><span data-stu-id="0eb25-172">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="0eb25-173">針對 `/Home/About?culture=fr`：</span><span class="sxs-lookup"><span data-stu-id="0eb25-173">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="0eb25-174">針對 `/Home/About?culture=cs`：</span><span class="sxs-lookup"><span data-stu-id="0eb25-174">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="0eb25-175">請注意，在捷克文文化特性中，這三種翻譯都不同。</span><span class="sxs-lookup"><span data-stu-id="0eb25-175">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="0eb25-176">法文和英文的文化特性則會對最後兩個翻譯字串共用相同的建構。</span><span class="sxs-lookup"><span data-stu-id="0eb25-176">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="0eb25-177">進階工作</span><span class="sxs-lookup"><span data-stu-id="0eb25-177">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="0eb25-178">內容化字串</span><span class="sxs-lookup"><span data-stu-id="0eb25-178">Contextualizing strings</span></span>

<span data-ttu-id="0eb25-179">應用程式通常包含要在數個位置中翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-179">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="0eb25-180">相同的字串在應用程式內的特定位置可能會有不同的轉譯 (Razor) 的視圖或類別檔案。</span><span class="sxs-lookup"><span data-stu-id="0eb25-180">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="0eb25-181">PO 檔案支援檔案內容的概念，可用來對所表示的字串進行分類。</span><span class="sxs-lookup"><span data-stu-id="0eb25-181">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="0eb25-182">使用檔案內容，字串可以根據檔案內容 (或缺乏檔案內容) 翻譯成不同的內容。</span><span class="sxs-lookup"><span data-stu-id="0eb25-182">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="0eb25-183">PO 當地語系化服務會使用翻譯字串時所使用的完整類別或檢視的名稱。</span><span class="sxs-lookup"><span data-stu-id="0eb25-183">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="0eb25-184">這是透過在 `msgctxt` 項目上設定值來完成的。</span><span class="sxs-lookup"><span data-stu-id="0eb25-184">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="0eb25-185">考慮對先前的 *fr.po* 範例進行微幅新增。</span><span class="sxs-lookup"><span data-stu-id="0eb25-185">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="0eb25-186">您 Razor 可以藉由設定保留專案的值，將位於 *Views/Home/About. cshtml* 的視圖定義為檔案內容 `msgctxt` ：</span><span class="sxs-lookup"><span data-stu-id="0eb25-186">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="0eb25-187">`msgctxt` 如此設定後，當巡覽至 `/Home/About?culture=fr-FR` 時，就會進行文字翻譯。</span><span class="sxs-lookup"><span data-stu-id="0eb25-187">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="0eb25-188">而巡覽至 `/Home/Contact?culture=fr-FR` 時，不會進行翻譯。</span><span class="sxs-lookup"><span data-stu-id="0eb25-188">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="0eb25-189">沒有特定項目與給定的檔案內容相符時，Orchard Core 的後援機制會在沒有內容的情況下尋找適當的 PO 檔案。</span><span class="sxs-lookup"><span data-stu-id="0eb25-189">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="0eb25-190">假設沒有針對 *Views/Home/Contact.cshtml* 所定義的特定檔案內容，請巡覽至 `/Home/Contact?culture=fr-FR` 以載入 PO 檔案，例如：</span><span class="sxs-lookup"><span data-stu-id="0eb25-190">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="0eb25-191">變更 PO 檔案的位置</span><span class="sxs-lookup"><span data-stu-id="0eb25-191">Changing the location of PO files</span></span>

<span data-ttu-id="0eb25-192">PO 檔案的預設位置可以在 `ConfigureServices` 中變更：</span><span class="sxs-lookup"><span data-stu-id="0eb25-192">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="0eb25-193">在此範例中，從 *Localization* 資料夾載入 PO 檔案。</span><span class="sxs-lookup"><span data-stu-id="0eb25-193">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="0eb25-194">實作自訂邏輯，以尋找當地語系化檔案</span><span class="sxs-lookup"><span data-stu-id="0eb25-194">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="0eb25-195">如果尋找 PO 檔案需要更複雜的邏輯，則可以實作 `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` 介面並將其註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="0eb25-195">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="0eb25-196">當 PO 檔案可以儲存在不同位置或檔案必須位在資料夾階層內時，這非常有用。</span><span class="sxs-lookup"><span data-stu-id="0eb25-196">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="0eb25-197">使用不同的預設複數化語言</span><span class="sxs-lookup"><span data-stu-id="0eb25-197">Using a different default pluralized language</span></span>

<span data-ttu-id="0eb25-198">此套件包含兩個複數形式特有的 `Plural` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="0eb25-198">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="0eb25-199">如果是需要更多複數形式的語言，請建立擴充方法。</span><span class="sxs-lookup"><span data-stu-id="0eb25-199">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="0eb25-200">利用擴充方法，您不需要提供預設語言的任何當地語系化檔案 &mdash; 原始字串已經可以直接在程式碼中使用。</span><span class="sxs-lookup"><span data-stu-id="0eb25-200">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="0eb25-201">您可以使用更通用的 `Plural(int count, string[] pluralForms, params object[] arguments)` 多載，其可接受翻譯的字串陣列。</span><span class="sxs-lookup"><span data-stu-id="0eb25-201">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0eb25-202">作者：[Sébastien Ros](https://github.com/sebastienros) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="0eb25-202">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="0eb25-203">本文將逐步說明在具有 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 架構的 ASP.NET Core 應用程式中使用可攜式物件 (PO) 檔案的步驟。</span><span class="sxs-lookup"><span data-stu-id="0eb25-203">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="0eb25-204">**注意：** Orchard Core 不是 Microsoft 產品。</span><span class="sxs-lookup"><span data-stu-id="0eb25-204">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="0eb25-205">因此，Microsoft 不提供這項功能的支援。</span><span class="sxs-lookup"><span data-stu-id="0eb25-205">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="0eb25-206">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="0eb25-206">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="0eb25-207">什麼是 PO 檔案？</span><span class="sxs-lookup"><span data-stu-id="0eb25-207">What is a PO file?</span></span>

<span data-ttu-id="0eb25-208">PO 檔案以文字檔的形式散發，其中包含給定語言的翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-208">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="0eb25-209">使用 PO 檔案而不是使用 *.resx* 檔案的一些優點包括：</span><span class="sxs-lookup"><span data-stu-id="0eb25-209">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="0eb25-210">PO 檔案支援複數表示；*.resx* 檔案不支援複數表示。</span><span class="sxs-lookup"><span data-stu-id="0eb25-210">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="0eb25-211">PO 檔案不會像 *.resx* 檔案一樣進行編譯。</span><span class="sxs-lookup"><span data-stu-id="0eb25-211">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="0eb25-212">因此，不需要特殊化工具與建置步驟。</span><span class="sxs-lookup"><span data-stu-id="0eb25-212">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="0eb25-213">PO 檔案適用於共同作業的線上編輯工具。</span><span class="sxs-lookup"><span data-stu-id="0eb25-213">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="0eb25-214">範例</span><span class="sxs-lookup"><span data-stu-id="0eb25-214">Example</span></span>

<span data-ttu-id="0eb25-215">以下是範例 PO 檔案，其中包含兩個字串的法文翻譯，包括其複數形式的字串：</span><span class="sxs-lookup"><span data-stu-id="0eb25-215">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="0eb25-216">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="0eb25-216">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="0eb25-217">此範例使用下列語法：</span><span class="sxs-lookup"><span data-stu-id="0eb25-217">This example uses the following syntax:</span></span>

- <span data-ttu-id="0eb25-218">`#:`：指出所要翻譯之字串內容的註解。</span><span class="sxs-lookup"><span data-stu-id="0eb25-218">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="0eb25-219">相同的字串可能會根據其使用位置而翻譯為不同內容。</span><span class="sxs-lookup"><span data-stu-id="0eb25-219">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="0eb25-220">`msgid`：未翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-220">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="0eb25-221">`msgstr`：已翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-221">`msgstr`: The translated string.</span></span>

<span data-ttu-id="0eb25-222">在支援複數表示的情況下，可以定義多個項目。</span><span class="sxs-lookup"><span data-stu-id="0eb25-222">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="0eb25-223">`msgid_plural`：未翻譯的複數字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-223">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="0eb25-224">`msgstr[0]`：案例 0 的已翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-224">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="0eb25-225">`msgstr[N]`：案例 N 的已翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-225">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="0eb25-226">您可以在[這裡](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)找到 PO 檔案規格。</span><span class="sxs-lookup"><span data-stu-id="0eb25-226">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="0eb25-227">在 ASP.NET Core 中設定 PO 檔案支援</span><span class="sxs-lookup"><span data-stu-id="0eb25-227">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="0eb25-228">這個範例是根據從 Visual Studio 2017 專案範本產生的 ASP.NET Core MVC 應用程式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-228">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="0eb25-229">參考套件</span><span class="sxs-lookup"><span data-stu-id="0eb25-229">Referencing the package</span></span>

<span data-ttu-id="0eb25-230">將參考新增至 `OrchardCore.Localization.Core` NuGet 套件。</span><span class="sxs-lookup"><span data-stu-id="0eb25-230">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="0eb25-231">*.csproj* 檔案現在包含與下列內容類似的一行 (版本號碼可能不同)：</span><span class="sxs-lookup"><span data-stu-id="0eb25-231">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="0eb25-232">註冊服務</span><span class="sxs-lookup"><span data-stu-id="0eb25-232">Registering the service</span></span>

<span data-ttu-id="0eb25-233">將所需的服務新增至 *Startup.cs* 的 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb25-233">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="0eb25-234">將必要的中介軟體新增至 *Startup.cs* 的 `Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="0eb25-234">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="0eb25-235">將下列程式碼新增至您 Razor 選擇的觀點。</span><span class="sxs-lookup"><span data-stu-id="0eb25-235">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="0eb25-236">此範例中使用 *About.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="0eb25-236">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="0eb25-237">將會插入 `IViewLocalizer` 執行個體，用來翻譯文字 "Hello world!"。</span><span class="sxs-lookup"><span data-stu-id="0eb25-237">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="0eb25-238">建立 PO 檔案</span><span class="sxs-lookup"><span data-stu-id="0eb25-238">Creating a PO file</span></span>

<span data-ttu-id="0eb25-239">在您的應用程式根資料夾中，建立名為 po 的檔案 *\<culture code> 。*</span><span class="sxs-lookup"><span data-stu-id="0eb25-239">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="0eb25-240">在此範例中，檔案名稱是 *fr.po*，因為使用法文語言：</span><span class="sxs-lookup"><span data-stu-id="0eb25-240">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="0eb25-241">這個檔案會同時儲存要翻譯的字串和法文的翻譯字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-241">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="0eb25-242">如有必要，翻譯會還原為其父文化特性。</span><span class="sxs-lookup"><span data-stu-id="0eb25-242">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="0eb25-243">在此範例中，如果所要求的文化特性是 `fr-FR` 或 `fr-CA`，則會使用 *fr.po* 檔案。</span><span class="sxs-lookup"><span data-stu-id="0eb25-243">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="0eb25-244">測試應用程式</span><span class="sxs-lookup"><span data-stu-id="0eb25-244">Testing the application</span></span>

<span data-ttu-id="0eb25-245">執行您的應用程式，並巡覽至 `/Home/About` URL。</span><span class="sxs-lookup"><span data-stu-id="0eb25-245">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="0eb25-246">文字 **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="0eb25-246">The text **Hello world!**</span></span> <span data-ttu-id="0eb25-247">就會出現。</span><span class="sxs-lookup"><span data-stu-id="0eb25-247">is displayed.</span></span>

<span data-ttu-id="0eb25-248">巡覽至 `/Home/About?culture=fr-FR`RL。</span><span class="sxs-lookup"><span data-stu-id="0eb25-248">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="0eb25-249">文字 **Bonjour le monde!**</span><span class="sxs-lookup"><span data-stu-id="0eb25-249">The text **Bonjour le monde!**</span></span> <span data-ttu-id="0eb25-250">就會出現。</span><span class="sxs-lookup"><span data-stu-id="0eb25-250">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="0eb25-251">複數表示</span><span class="sxs-lookup"><span data-stu-id="0eb25-251">Pluralization</span></span>

<span data-ttu-id="0eb25-252">PO 檔案支援複數表示格式，如果相同的字串必須根據基數翻譯為不同的內容，這個檔案很有用。</span><span class="sxs-lookup"><span data-stu-id="0eb25-252">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="0eb25-253">由於每一種語言都會定義自訂規則來選取要根據基數使用的字串，因此這項工作變得複雜。</span><span class="sxs-lookup"><span data-stu-id="0eb25-253">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="0eb25-254">Orchard 當地語系化套件會提供 API，以自動叫用這些不同的複數形式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-254">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="0eb25-255">建立複數表示 PO 檔案</span><span class="sxs-lookup"><span data-stu-id="0eb25-255">Creating pluralization PO files</span></span>

<span data-ttu-id="0eb25-256">將下列內容新增至先前所述的 *fr.po* 檔案：</span><span class="sxs-lookup"><span data-stu-id="0eb25-256">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="0eb25-257">如需此範例中每個項目所代表意義的說明，請參閱[什麼是 PO 檔案？](#what-is-a-po-file)。</span><span class="sxs-lookup"><span data-stu-id="0eb25-257">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="0eb25-258">新增使用不同複數表示格式的語言</span><span class="sxs-lookup"><span data-stu-id="0eb25-258">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="0eb25-259">在上述範例中，已使用英文和法文字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-259">English and French strings were used in the previous example.</span></span> <span data-ttu-id="0eb25-260">英文和法文只有兩種複數表示格式，因此共用相同的格式規則，即基數一對應到第一個複數形式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-260">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="0eb25-261">任何其他基數都對應到第二個複數形式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-261">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="0eb25-262">並非所有語言都共用相同的規則。</span><span class="sxs-lookup"><span data-stu-id="0eb25-262">Not all languages share the same rules.</span></span> <span data-ttu-id="0eb25-263">以下是使用捷克文語言的說明，它有三種複數形式。</span><span class="sxs-lookup"><span data-stu-id="0eb25-263">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="0eb25-264">如下所示建立 `cs.po` 檔案，並記下複數表示需要三種不同翻譯的方式：</span><span class="sxs-lookup"><span data-stu-id="0eb25-264">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="0eb25-265">若要接受捷克文的當地語系化，請將 `"cs"` 新增至 `ConfigureServices` 方法所支援的文化特性清單中：</span><span class="sxs-lookup"><span data-stu-id="0eb25-265">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="0eb25-266">編輯 *Views/Home/About.cshtml* 檔案來呈現數個基數的當地語系化複數字串：</span><span class="sxs-lookup"><span data-stu-id="0eb25-266">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="0eb25-267">**注意：** 在現實世界的案例中，變數將用來代表計數。</span><span class="sxs-lookup"><span data-stu-id="0eb25-267">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="0eb25-268">在這裡，我們會重複使用具有三個不同值的相同程式碼，以公開非常特殊的情況。</span><span class="sxs-lookup"><span data-stu-id="0eb25-268">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="0eb25-269">切換文化特性後，您會看到下列內容：</span><span class="sxs-lookup"><span data-stu-id="0eb25-269">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="0eb25-270">針對 `/Home/About`：</span><span class="sxs-lookup"><span data-stu-id="0eb25-270">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="0eb25-271">針對 `/Home/About?culture=fr`：</span><span class="sxs-lookup"><span data-stu-id="0eb25-271">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="0eb25-272">針對 `/Home/About?culture=cs`：</span><span class="sxs-lookup"><span data-stu-id="0eb25-272">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="0eb25-273">請注意，在捷克文文化特性中，這三種翻譯都不同。</span><span class="sxs-lookup"><span data-stu-id="0eb25-273">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="0eb25-274">法文和英文的文化特性則會對最後兩個翻譯字串共用相同的建構。</span><span class="sxs-lookup"><span data-stu-id="0eb25-274">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="0eb25-275">進階工作</span><span class="sxs-lookup"><span data-stu-id="0eb25-275">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="0eb25-276">內容化字串</span><span class="sxs-lookup"><span data-stu-id="0eb25-276">Contextualizing strings</span></span>

<span data-ttu-id="0eb25-277">應用程式通常包含要在數個位置中翻譯的字串。</span><span class="sxs-lookup"><span data-stu-id="0eb25-277">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="0eb25-278">相同的字串在應用程式內的特定位置可能會有不同的轉譯 (Razor) 的視圖或類別檔案。</span><span class="sxs-lookup"><span data-stu-id="0eb25-278">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="0eb25-279">PO 檔案支援檔案內容的概念，可用來對所表示的字串進行分類。</span><span class="sxs-lookup"><span data-stu-id="0eb25-279">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="0eb25-280">使用檔案內容，字串可以根據檔案內容 (或缺乏檔案內容) 翻譯成不同的內容。</span><span class="sxs-lookup"><span data-stu-id="0eb25-280">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="0eb25-281">PO 當地語系化服務會使用翻譯字串時所使用的完整類別或檢視的名稱。</span><span class="sxs-lookup"><span data-stu-id="0eb25-281">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="0eb25-282">這是透過在 `msgctxt` 項目上設定值來完成的。</span><span class="sxs-lookup"><span data-stu-id="0eb25-282">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="0eb25-283">考慮對先前的 *fr.po* 範例進行微幅新增。</span><span class="sxs-lookup"><span data-stu-id="0eb25-283">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="0eb25-284">您 Razor 可以藉由設定保留專案的值，將位於 *Views/Home/About. cshtml* 的視圖定義為檔案內容 `msgctxt` ：</span><span class="sxs-lookup"><span data-stu-id="0eb25-284">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="0eb25-285">`msgctxt` 如此設定後，當巡覽至 `/Home/About?culture=fr-FR` 時，就會進行文字翻譯。</span><span class="sxs-lookup"><span data-stu-id="0eb25-285">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="0eb25-286">而巡覽至 `/Home/Contact?culture=fr-FR` 時，不會進行翻譯。</span><span class="sxs-lookup"><span data-stu-id="0eb25-286">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="0eb25-287">沒有特定項目與給定的檔案內容相符時，Orchard Core 的後援機制會在沒有內容的情況下尋找適當的 PO 檔案。</span><span class="sxs-lookup"><span data-stu-id="0eb25-287">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="0eb25-288">假設沒有針對 *Views/Home/Contact.cshtml* 所定義的特定檔案內容，請巡覽至 `/Home/Contact?culture=fr-FR` 以載入 PO 檔案，例如：</span><span class="sxs-lookup"><span data-stu-id="0eb25-288">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="0eb25-289">變更 PO 檔案的位置</span><span class="sxs-lookup"><span data-stu-id="0eb25-289">Changing the location of PO files</span></span>

<span data-ttu-id="0eb25-290">PO 檔案的預設位置可以在 `ConfigureServices` 中變更：</span><span class="sxs-lookup"><span data-stu-id="0eb25-290">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="0eb25-291">在此範例中，從 *Localization* 資料夾載入 PO 檔案。</span><span class="sxs-lookup"><span data-stu-id="0eb25-291">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="0eb25-292">實作自訂邏輯，以尋找當地語系化檔案</span><span class="sxs-lookup"><span data-stu-id="0eb25-292">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="0eb25-293">如果尋找 PO 檔案需要更複雜的邏輯，則可以實作 `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` 介面並將其註冊為服務。</span><span class="sxs-lookup"><span data-stu-id="0eb25-293">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="0eb25-294">當 PO 檔案可以儲存在不同位置或檔案必須位在資料夾階層內時，這非常有用。</span><span class="sxs-lookup"><span data-stu-id="0eb25-294">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="0eb25-295">使用不同的預設複數化語言</span><span class="sxs-lookup"><span data-stu-id="0eb25-295">Using a different default pluralized language</span></span>

<span data-ttu-id="0eb25-296">此套件包含兩個複數形式特有的 `Plural` 擴充方法。</span><span class="sxs-lookup"><span data-stu-id="0eb25-296">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="0eb25-297">如果是需要更多複數形式的語言，請建立擴充方法。</span><span class="sxs-lookup"><span data-stu-id="0eb25-297">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="0eb25-298">利用擴充方法，您不需要提供預設語言的任何當地語系化檔案 &mdash; 原始字串已經可以直接在程式碼中使用。</span><span class="sxs-lookup"><span data-stu-id="0eb25-298">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="0eb25-299">您可以使用更通用的 `Plural(int count, string[] pluralForms, params object[] arguments)` 多載，其可接受翻譯的字串陣列。</span><span class="sxs-lookup"><span data-stu-id="0eb25-299">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end
