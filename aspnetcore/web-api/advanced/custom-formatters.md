---
title: ASP.NET Core Web API 中的自訂格式器
author: rick-anderson
description: 了解如何建立和使用 ASP.NET Core 中的 Web API 自訂格式器。
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 9f87d02dd3abe6dca8db495e482ccf9c440a2469
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88627542"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a>ASP.NET Core Web API 中的自訂格式器

[Kirk Larkin](https://twitter.com/serpent5)和[Tom Dykstra](https://github.com/tdykstra)。

ASP.NET Core MVC 使用輸入和輸出格式器，支援 Web Api 中的資料交換。 [模型](xref:mvc/models/model-binding)系結會使用輸入格式器。 輸出格式器會用來 [格式化回應](xref:web-api/advanced/formatting)。

架構會針對 JSON 和 XML 提供內建的輸入和輸出格式器。 它會為純文字提供內建的輸出格式器，但不提供純文字的輸入格式器。

本文說明如何藉由建立自訂的格式器來新增對其他格式的支援。 如需自訂純文字輸入格式器的範例，請參閱 GitHub 上的 [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) 。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="when-to-use-custom-formatters"></a>自訂格式器的使用時機

您可以使用自訂格式器來新增內建格式器未處理之內容類型的支援。

## <a name="overview-of-how-to-use-a-custom-formatter"></a>如何使用自訂格式器的概觀

若要建立自訂格式器：

* 若要序列化傳送至用戶端的資料，請建立輸出格式器類別。
* 若要還原序列化從用戶端接收的資料，請建立輸入格式器類別。
* 將格式器類別的實例加入 `InputFormatters` 至 `OutputFormatters` 中的和集合 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 。

## <a name="how-to-create-a-custom-formatter-class"></a>如何建立自訂格式器類別

若要建立格式器：

* 請從適當的基底類別衍生類別。 範例應用程式衍生自 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> 和 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> 。
* 在建構函式中指定有效的媒體類型和編碼方式。
* 覆寫 <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> 和 <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> 方法。
* 覆寫 <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> 和 `WriteResponseBodyAsync` 方法。

下列程式碼顯示 `VcardOutputFormatter` [範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)中的類別：

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a>從適當的基底類別衍生

若為文字媒體類型 (例如，vCard) ，則衍生自 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> 或 <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> 基類。

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

如果是二進位類型，則衍生自 <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> 或 <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> 基類。

### <a name="specify-valid-media-types-and-encodings"></a>指定有效的媒體類型和編碼方式

在建構函式中，您可以新增 `SupportedMediaTypes` 和 `SupportedEncodings` 集合，以指定有效的媒體類型和編碼方式。

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

格式器類別 **不** 能針對其相依性使用函式插入。 例如， `ILogger<VcardOutputFormatter>` 無法將參數加入至函式的參數。 若要存取服務，請使用傳遞至方法的內容物件。 本文中的程式碼範例和 [範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) 示範如何進行這項作業。

### <a name="override-canreadtype-and-canwritetype"></a>覆寫 CanReadType 和 CanWriteType

藉由覆寫或方法，指定要還原序列化或序列化的型別 `CanReadType` `CanWriteType` 。 例如，從類型建立 vCard 文字 `Contact` ，反之亦然。

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a>CanWriteResult 方法

在某些情況下， `CanWriteResult` 必須覆寫，而不是 `CanWriteType` 。 如果符合下列所有條件，請使用 `CanWriteResult`：

* 動作方法會傳回模型類別。
* 在執行階段期間，可能會傳回衍生的類別。
* 動作所傳回的衍生類別在執行時間必須是已知的。

例如，假設動作方法：

* Signature 會傳回 `Person` 型別。
* 可以傳回 `Student` `Instructor` 衍生自的或類型 `Person` 。 

若要讓格式器只處理 `Student` 物件，請 <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> 在提供給方法的內容物件中，檢查的型別 `CanWriteResult` 。 當動作方法傳回時 `IActionResult` ：

* 不需要使用 `CanWriteResult` 。
* `CanWriteType`方法會接收執行時間型別。

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a>覆寫 ReadRequestBodyAsync 和 WriteResponseBodyAsync

還原序列化或序列化是在 `ReadRequestBodyAsync` 或中執行 `WriteResponseBodyAsync` 。 下列範例顯示如何從相依性插入容器取得服務。 無法從函式參數取得服務。

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a>如何設定 MVC 以使用自訂格式器

若要使用自訂格式器，請將格式器類別的執行個體新增至 `InputFormatters` 或 `OutputFormatters` 集合。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

系統會依據您插入格式器的順序進行評估。 第一個會優先使用。

## <a name="the-complete-vcardinputformatter-class"></a>完整 `VcardInputFormatter` 類別

下列程式碼顯示 `VcardInputFormatter` [範例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)中的類別：

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a>測試應用程式

[執行本文的範例應用程式](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples)，它會執行基本的 vCard 輸入和輸出格式器。 應用程式會讀取並寫入 Vcard，如下所示：

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

若要查看 vCard 輸出，請執行應用程式，並將具有 Accept 標頭的 Get 要求傳送 `text/vcard` 至 `https://localhost:5001/api/contacts` 。

若要在記憶體中的連絡人集合中加入 vCard：

* `Post` `/api/contacts` 使用 Postman 之類的工具將要求傳送至。
* 將 `Content-Type` 標頭設定為 `text/vcard`。
* 設定 `vCard` 主體中的文字，格式如上述範例所示。

## <a name="additional-resources"></a>其他資源

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
