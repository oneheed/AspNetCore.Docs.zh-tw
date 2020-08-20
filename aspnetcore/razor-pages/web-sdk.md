---
title: ASP.NET Core Web SDK
author: Rick-Anderson
description: 《 Web.config 簡介」。
ms.author: riande
ms.date: 01/25/2020
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
uid: razor-pages/web-sdk
ms.openlocfilehash: 163bc2679deda449f97cb4e50da1093e6b1edda4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634809"
---
# <a name="aspnet-core-web-sdk"></a>ASP.NET Core Web SDK

### <a name="overview"></a>概觀

`Microsoft.NET.Sdk.Web` 是用來建立 ASP.NET Core 應用程式的 [MSBuild 專案 SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) 。 您可以建立不含此 SDK 的 ASP.NET Core 應用程式，不過，Web SDK 是：

* 專為提供最先進的體驗量身打造。
* 大部分使用者的建議目標。

在專案中使用 Web. SDK：

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

使用 Web SDK 啟用的功能：

* 以 .NET Core 3.0 或更新版本為目標的專案會隱含參考：

  * [ASP.NET Core 的共用架構](xref:fundamentals/metapackage-app)。
  * 專為建立 ASP.NET Core 應用程式而設計的[分析器](/visualstudio/extensibility/getting-started-with-roslyn-analyzers)。
* Web SDK 會匯入可讓您使用發行設定檔，並使用 WebDeploy 發行的 MSBuild 目標。

### <a name="properties"></a>屬性

| 屬性 | 描述 |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | 停用共用架構的隱含參考 `Microsoft.AspNetCore.App` 。 |
| `DisableImplicitAspNetCoreAnalyzers` | 停用 ASP.NET Core 分析器的隱含參考。 |
| `DisableImplicitComponentsAnalyzers` | Razor建立 Blazor (伺服器) 應用程式時，停用元件分析器的隱含參考。 |
