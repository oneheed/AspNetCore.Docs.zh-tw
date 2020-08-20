---
title: ASP.NET Core Blazor 支援的平臺
author: guardrex
description: 瞭解 ASP.NET Core 支援的平臺 Blazor 。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625943"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a>ASP.NET Core Blazor 支援的平臺

作者：[Luke Latham](https://github.com/guardrex)

## <a name="browser-requirements"></a>瀏覽器需求

### Blazor WebAssembly

| 瀏覽器                          | 版本               |
| -------------------------------- | :-------------------: |
| Microsoft Edge                   | 目前               |
| Mozilla Firefox                  | 目前               |
| Google Chrome （包括 Android） | 目前               |
| Safari，包括 iOS            | 目前               |
| Microsoft Internet Explorer      | 不支援&dagger; |

&dagger;Microsoft Internet Explorer 不支援 [WebAssembly](https://webassembly.org)。

### Blazor Server

| 瀏覽器                          | 版本    |
| -------------------------------- | :--------: |
| Microsoft Edge                   | 目前    |
| Mozilla Firefox                  | 目前    |
| Google Chrome （包括 Android） | 目前    |
| Safari，包括 iOS            | 目前    |
| Microsoft Internet Explorer      | rj-11&dagger; |

&dagger; (需要額外的 polyfill，例如，您可以透過組合) 加入承諾 [`Polyfill.io`](https://polyfill.io/v3/) 。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/hosting-models>
