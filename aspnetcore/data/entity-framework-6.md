---
title: ASP.NET Core 和 Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 和更新版本適用于 ASP.NET Core 3.1 和更新版本。
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: b25c6bf414c82ce4cdf617a435434ecd9b746542
ms.sourcegitcommit: 32203a42260e70d007c17a2b748b1bd5b9b662c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2021
ms.locfileid: "107298595"
---
# <a name="aspnet-core-and-entity-framework-6"></a>ASP.NET Core 和 Entity Framework 6
::: moniker range=">= aspnetcore-3.0"

依 [派翠克 Goode](https://github.com/attrib75)

## <a name="using-entity-framework-6-with-aspnet-core"></a>搭配使用 Entity Framework 6 與 ASP.NET Core

[Entity Framework Core](/ef/) 應該用於新的開發。 [下載範例](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/3.xsample)使用[ENTITY FRAMEWORK 6 (EF6) ](/ef/ef6)，可用來將現有的應用程式遷移至 ASP.NET Core。

## <a name="additional-resources"></a>其他資源

* [Entity Framework - Code-Based Configuration](/ef/ef6/fundamentals/configuring/code-based) (Entity Framework - 以程式碼為基礎的組態)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Paweł Grudzień](https://github.com/pgrudzien12)、[Damien Pontifex](https://github.com/DamienPontifex) 和 [Tom Dykstra](https://github.com/tdykstra)

本文示範如何在 ASP.NET Core 應用程式中使用 Entity Framework 6。    

## <a name="overview"></a>概觀 

若要使用 Entity Framework 6，您的專案必須針對 .NET Framework 進行編譯，因為 Entity Framework 6 不支援 .NET Core。 如果您需要跨平台功能，則必須升級至 [Entity Framework Core](/ef/)。  

在 ASP.NET Core 應用程式中使用 Entity Framework 6 的建議方式，是將 EF6 內容和模型類別放在以 .NET Framework 為目標的類別庫專案中。 從 ASP.NET Core 專案新增類別庫的參考。 請參閱[使用 EF6 和 ASP.NET Core 專案的 Visual Studio 方案](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/)範例。    

您無法將 EF6 內容置於 ASP.NET Core 專案中，因為 .NET Core 專案不支援 EF6 命令 (例如 *Enable-Migrations*) 需要的所有功能。    

不論您放置 EF6 內容的專案類型為何，只有 EF6 命令列工具才適用於 EF6 內容。 例如，`Scaffold-DbContext` 僅可用於 Entity Framework Core。 如果您需要對資料庫進行反向工程以 EF6 模型，請參閱 <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database> 。    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>在 ASP.NET Core 專案中參考完整 Framework 和 EF6 

您的 ASP.NET Core 專案必須以 .NET Framework 和參考 EF6 為目標。 例如，ASP.NET Core 專案的 *.csproj* 檔案看起來類似下列範例 (只顯示檔案的相關部分)。    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

建立新專案時，請使用 **ASP.NET Core Web 應用程式 (.NET Framework)** 範本。    

## <a name="handle-connection-strings"></a>處理連接字串    

您將在 EF6 類別庫專案中使用的 EF6 命令列工具需要預設建構函式，因此它們可以具現化內容。 但是，您可能想要指定連接字串以用於 ASP.NET Core 專案；在此情況下，內容建構函式必須要有可讓您以連接字串傳遞的參數。 以下為範例。   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

由於您的 EF6 內容沒有無參數的函式，因此您的 EF6 專案必須提供的實作為 <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0> 。 EF6 命令列工具將尋找並使用該實作，因此它們可以具現化內容。 以下為範例。   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

在此範例程式碼中，`IDbContextFactory` 實作將以硬式編碼的連接字串傳遞。 這是命令列工具將使用的連接字串。 您需要實作策略，以確保類別庫使用的連接字串與呼叫端應用程式所使用的連接字串相同。 例如，您可以在這兩個專案中從環境變數取得值。   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>設定 ASP.NET Core 專案中的相依性插入  

在 Core 專案的 *Startup.cs* 檔案中，在 `ConfigureServices` 內設定相依性插入 (DI) 的 EF6 內容。 EF 內容物件的範圍應該設為每個要求的存留期。   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

然後，您就可以使用 DI，在控制器中取得內容的執行個體。 此程式碼類似於您將針對 EF Core 內容撰寫的程式碼：    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a>範例應用程式   

如需可用的範例應用程式，請參閱本文所隨附的 [Visual Studio 方案範例 ](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/data/entity-framework-6/sample/)。    

此範例可以透過 Visual Studio 中的下列步驟從頭開始建立：    

* 建立方案。    

* [新增]**[新專案]** > **[Web]** > **[ASP.NET Core Web 應用程式]** >     
  * 在專案範本選取項目對話方塊中，選取下拉式清單中的 API 和.NET Framework 

* [新增]**[新增專案]** > **[Windows Desktop]** > **[類別庫 (.NET Framework)]** >   

* 在這兩個專案的 [套件管理員主控台] (PMC) 中，執行 `Install-Package Entityframework` 命令。    

* 在類別庫專案中，建立資料模型類別和內容類別，以及 `IDbContextFactory` 的實作。    

* 在類別庫專案的 PMC 中，執行 `Enable-Migrations` 和 `Add-Migration Initial` 命令。 如果您已設定 ASP.NET Core 專案作為啟始專案，請將 `-StartupProjectName EF6` 新增至這些命令。 

* 在 Core 專案中，將專案參考新增至類別庫專案中。    

* 在 Core 專案的 *Startup.cs* 中，登錄 DI 的內容。    

* 在 Core 專案的中， *appsettings.json* 加入連接字串。  

* 在 Core 專案中，新增控制器和檢視，以確認您可以讀取和寫入資料。 (請注意，ASP.NET Core MVC Scaffolding 不會使用參考自類別庫的 EF6 內容。)

::: moniker-end
