---
title: ASP.NET Core Blazor 支援的平臺
author: guardrex
description: 瞭解 ASP.NET Core 支援的平臺 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 1ffe98636ed200adbf00e89c2c3499eb69792d3f
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754537"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>ASP.NET Core Blazor 支援的平臺

作者：[Luke Latham](https://github.com/guardrex)

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
