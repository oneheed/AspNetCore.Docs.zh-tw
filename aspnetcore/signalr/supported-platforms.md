---
title: ASP.NET Core SignalR 支援的平臺
author: bradygaster
description: 瞭解 ASP.NET Core 支援的平臺 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: ee6e263fb5bef7bfb84587c3b0f04175eb8073cd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051014"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>ASP.NET Core SignalR 支援的平臺

## <a name="server-system-requirements"></a>伺服器系統需求

SignalR 針對 ASP.NET Core 支援 ASP.NET Core 支援的任何伺服器平臺。

## <a name="javascript-client"></a>JavaScript 用戶端

[JavaScript 用戶端](xref:signalr/javascript-client)會在 NodeJS 8 和更新版本以及下列瀏覽器上執行：

| 瀏覽器                          | 版本         |
| -------------------------------- | --------------- |
| Apple Safari，包括 iOS      | 目前&dagger; |
| Google Chrome （包括 Android） | 目前&dagger; |
| Microsoft Edge                   | 目前&dagger; |
| Mozilla Firefox                  | 目前&dagger; |

&dagger;*Current* 指的是最新版的瀏覽器。

## <a name="net-client"></a>.NET 用戶端

[.Net 用戶端](xref:signalr/dotnet-client)會在 ASP.NET Core 所支援的任何平臺上執行。 例如， [xamarin 開發人員可 SignalR ](https://github.com/aspnet/Announcements/issues/305)使用 xamarin. android 8.4.0.1 和更新版本，以及使用11.14.0.4 和更新版本的 ios 應用程式來建立 android 應用程式。

如果伺服器執行 IIS，則 Websocket 傳輸需要 Windows Server 2012 或更新版本上的 IIS 8.0 或更新版本。 所有平臺都支援其他傳輸。

## <a name="java-client"></a>Java 用戶端

[JAVA 用戶端](xref:signalr/java-client)支援 java 8 和更新版本。

## <a name="unsupported-clients"></a>不支援的用戶端

下列用戶端可供使用，但是實驗性或非官方的。 它們目前不受支援，而且可能永遠不會存在。

* [C + + 用戶端](https://github.com/aspnet/SignalR-Client-Cpp)

* [Swift 用戶端](https://github.com/moozzyk/SignalR-Client-Swift)
