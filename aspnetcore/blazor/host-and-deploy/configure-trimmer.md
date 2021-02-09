---
title: 設定 ASP.NET Core 的修剪器 Blazor
author: guardrex
description: 瞭解如何在建立應用程式時，控制 (IL) 連結器 (修剪器) 的中繼語言 Blazor 。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975207"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a>設定 ASP.NET Core 的修剪器 Blazor

Blazor WebAssembly 執行 [ (IL) 修剪的中繼語言 ](/dotnet/standard/managed-code#intermediate-language--execution) ，以縮減已發行輸出的大小。 依預設，會在發佈應用程式時進行修剪。

修剪可能會產生不利的影響。 在使用反映的應用程式中，修剪器通常無法判斷執行時間所需的反映類型。 若要修剪使用反映的應用程式，必須在應用程式的程式碼和應用程式所相依的封裝或架構中，針對反映的必要類型通知修剪器。 修剪器在執行時間也無法回應應用程式的動態行為。 若要確保已修剪的應用程式在部署後可以正常運作，請在開發期間經常測試已發佈的輸出。

若要設定修剪器，請參閱 .NET 基本概念檔中的 [修剪選項](/dotnet/core/deploying/trimming-options) 一文，其中包含下列主題的指引：

* 使用專案檔中的屬性來停用整個應用程式的修剪 `<PublishTrimmed>` 。
* 控制修剪器捨棄未使用 IL 的程度。
* 停止修剪器，使其無法修剪特定的元件。
* 用於修剪的「根」元件。
* 藉由在專案檔中將屬性設為，以呈現反映類型的警告 `<SuppressTrimAnalysisWarnings>` `false` 。
* 控制符號修剪和 degugger 支援。
* 設定修剪架構程式庫功能的修剪器功能。

## <a name="additional-resources"></a>其他資源

* [修剪獨立式部署及可執行檔](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
