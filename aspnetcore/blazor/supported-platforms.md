---
title: ASP.NET Core Blazor 支援的平臺
author: guardrex
description: 瞭解 ASP.NET Core 支援的平臺 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
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
ms.openlocfilehash: 67896bcdd5d99ff2c9cf22e3902262f9513eb4f6
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013524"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>ASP.NET Core Blazor 支援的平臺

作者：[Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>瀏覽器需求

### Blazor WebAssembly

| 瀏覽器                          | 版本               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 目前               |
| Mozilla Firefox                  | 目前               |
| Google Chrome，包括 Android | 目前               |
| Safari，包括 iOS            | 目前               |
| Microsoft Internet Explorer      | 不受支援&dagger; |

&dagger;Microsoft Internet Explorer 不支援[WebAssembly](https://webassembly.org)。

### Blazor Server

| 瀏覽器                          | 版本    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 目前    |
| Mozilla Firefox                  | 目前    |
| Google Chrome，包括 Android | 目前    |
| Safari，包括 iOS            | 目前    |
| Microsoft Internet Explorer      | 英寸&dagger; |

&dagger; (需要額外的 polyfills，例如，可透過配套) 新增承諾 [`Polyfill.io`](https://polyfill.io/v3/) 。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/hosting-models>
