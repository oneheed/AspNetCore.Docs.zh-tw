---
title: 設定 ASP.NET Core 的修剪器 Blazor
author: guardrex
description: 瞭解如何在建立應用程式時，控制 (IL) 連結器 (修剪器) 的中繼語言 Blazor 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 337b188d3c0aeac9c5c635ebca265b9a35c6904d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055798"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>設定 ASP.NET Core 的修剪器 Blazor

依 [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly 執行 [ (IL) 修剪的中繼語言 ](/dotnet/standard/managed-code#intermediate-language--execution) ，以縮減已發行輸出的大小。

調整應用程式的大小會優化，但可能會產生不利的影響。 使用反映或相關動態功能的應用程式可能會在修剪時中斷，因為修剪器不知道動態行為，而且無法判斷在執行時間的反映需要哪些類型。 若要修剪這類應用程式，必須在程式碼中和應用程式相依的封裝或架構中，通知修剪器所需的任何類型。

若要確保已修剪的應用程式在部署後可以正常運作，請務必在開發期間經常測試已發佈的輸出。

您可以 `PublishTrimmed` `false` 在應用程式的專案檔中，將 MSBuild 屬性設定為，以停用 .net 應用程式的修剪：

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```
您可以在 [修剪選項](/dotnet/core/deploying/trimming-options)中找到設定修剪器的其他選項。

## <a name="additional-resources"></a>其他資源

* [修剪獨立式部署及可執行檔](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
