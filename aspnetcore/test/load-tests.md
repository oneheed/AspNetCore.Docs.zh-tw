---
title: ASP.NET Core 負載/壓力測試
author: Jeremy-Meng
description: 瞭解 ASP.NET Core 應用程式的負載測試和壓力測試有幾個值得注意的工具和方法。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: test/loadtests
ms.openlocfilehash: 56f5a5caeea7581e26f8d8cec9662f439cd24b9e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060712"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core 負載/壓力測試

負載測試和壓力測試很重要，可確保 web 應用程式的效能和可調整性。 雖然它們通常會共用類似的測試，但它們的目標是不同的。

**負載測試** ：測試應用程式是否可在特定案例中處理指定的使用者負載，同時仍滿足回應目標。 應用程式會在正常情況下執行。

**壓力測試** ：在極端情況下執行時測試應用程式穩定性，通常很長一段時間。 這些測試會在應用程式上放置高度的使用者負載，可能會尖峰或逐漸增加負載，或限制應用程式的計算資源。

壓力測試會判斷壓力下的應用程式是否可以從失敗中復原，並正常地返回預期的行為。 在壓力下，應用程式不會在正常情況下執行。

Visual Studio 2019 宣佈計畫 [取代負載測試](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)。 對應的 Azure DevOps 雲端負載測試服務已關閉。

## <a name="third-party-tools"></a>協力廠商工具

下列清單包含具有各種功能集的協力廠商 web 效能工具：

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab) ](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [加特林](https://gatling.io/)
* [k6](https://k6.io)
* [蝗蟲](https://locust.io/)
* [West 風 WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)