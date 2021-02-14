---
title: ASP.NET Core Blazor 支援的平臺
author: guardrex
description: 瞭解 ASP.NET Core 支援的平臺 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280715"
---
# <a name="aspnet-core-blazor-supported-platforms"></a>ASP.NET Core Blazor 支援的平臺

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssemblyBlazor Server下表所示的瀏覽器支援和。

| 瀏覽器                          | 版本         |
| -------------------------------- | --------------- |
| Apple Safari，包括 iOS      | 當前&dagger; |
| Google Chrome （包括 Android） | 當前&dagger; |
| Microsoft Edge                   | 當前&dagger; |
| Mozilla Firefox                  | 當前&dagger; |  

&dagger;*Current* 指的是最新版的瀏覽器。  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| 瀏覽器                          | 版本               |
| -------------------------------- | --------------------- |
| Apple Safari，包括 iOS      | 當前&dagger;       |
| Google Chrome （包括 Android） | 當前&dagger;       |
| Microsoft Edge                   | 當前&dagger;       |
| Microsoft Internet Explorer      | 不支援&Dagger; |
| Mozilla Firefox                  | 當前&dagger;       |  

&dagger;*Current* 指的是最新版的瀏覽器。  
&Dagger;Microsoft Internet Explorer 不支援 [WebAssembly](https://webassembly.org)。

## Blazor Server

| 瀏覽器                          | 版本         |
| -------------------------------- | --------------- |
| Apple Safari，包括 iOS      | 當前&dagger; |
| Google Chrome （包括 Android） | 當前&dagger; |
| Microsoft Edge                   | 當前&dagger; |
| Microsoft Internet Explorer      | rj-11&Dagger;      |
| Mozilla Firefox                  | 當前&dagger; |

&dagger;*Current* 指的是最新版的瀏覽器。  
&Dagger;需要其他 polyfill。 例如，您可以透過組合來加入承諾 [`Polyfill.io`](https://polyfill.io/v3/) 。

::: moniker-end

## <a name="additional-resources"></a>其他資源

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
