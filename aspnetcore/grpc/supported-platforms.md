---
title: .NET 支援平臺上的 gRPC
author: jamesnk
description: 瞭解在 .NET 上 gRPC 支援的平臺。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 3/11/2021
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
uid: grpc/supported-platforms
ms.openlocfilehash: c2bd808d16f11077e39aada829d79e8aedf2755b
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413414"
---
# <a name="grpc-on-net-supported-platforms"></a>.NET 支援平臺上的 gRPC

依 [James 牛頓](https://twitter.com/jamesnk)

本文討論使用 gRPC 搭配 .NET 的需求和支援的平臺。 這兩個主要 gRPC 工作負載有不同的需求：

* [在 ASP.NET Core 中裝載 gRPC 服務](#aspnet-core-grpc-server-requirements)
* [從 .NET 用戶端應用程式呼叫 gRPC](#net-grpc-client-requirements)

## <a name="wire-formats"></a>有線格式

gRPC 利用 HTTP/2 中提供的 advanced 功能。 在任何位置都不支援 HTTP/2，但使用 HTTP/1.1 的第二個網路格式可供 gRPC：

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC over HTTP/2 是 gRPC 的一般使用方式。
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web 會修改 gRPC 通訊協定，以與 HTTP/1.1 相容。 gRPC-Web 可以在更多位置使用。 gRPC Web 可供瀏覽器應用程式和網路使用，而不需要對 HTTP/2 的完整支援。 不再支援兩個 advanced gRPC 功能：用戶端串流和雙向串流。

.NET 上的 gRPC 支援兩種網路格式。 `application/grpc` 預設使用。 必須在用戶端和伺服器上設定 gRPC-Web，才能成功 gRPC Web 呼叫。 如需設定 gRPC Web 的詳細資訊，請參閱 <xref:grpc/browser> 。

## <a name="aspnet-core-grpc-server-requirements"></a>ASP.NET Core gRPC 伺服器需求

使用 ASP.NET Core 裝載 gRPC 服務需要 .NET Core 3.x 或更新版本。

> [!div class="checklist"]
>
> * .NET 5 或更新版本
> * .NET Core 3

ASP.NET Core gRPC 服務可以裝載于 .NET Core 支援的所有作業系統上。

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;

&dagger;[macOS 不支援使用 HTTPS 裝載 ASP.NET Core 應用程式](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。

### <a name="supported-aspnet-core-servers"></a>支援的 ASP.NET 核心伺服器

支援所有內建的 ASP.NET 核心伺服器。

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS 需要 .NET 5 和 Windows 10 組建20241或更新版本。

&Dagger;HTTP.sys 需要 .NET 5 和 Windows 10 組建19529或更新版本。

如需設定 ASP.NET 核心伺服器以執行 gRPC 的詳細資訊，請參閱 <xref:grpc/aspnetcore#server-options> 。

### <a name="azure-services"></a>Azure 服務

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service 不支援透過 HTTP/2 裝載 gRPC。 gRPC-Web 是相容的替代方案。

正在進行工作，以改善在 Azure App Service 中使用 HTTP/2 的 gRPC 支援。 如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/9020)。

## <a name="net-grpc-client-requirements"></a>.NET gRPC 用戶端需求

[Grpc .net. 用戶端](https://www.nuget.org/packages/Grpc.Net.Client/)套件支援在 .net Core 3 和 .net 5 或更新版本上透過 HTTP/2 的 Grpc 呼叫。

有限的支援可透過 .NET Framework 上的 HTTP/2 進行 gRPC。 其他 .NET 版本（例如 UWP、Xamarin 和 Unity）都不需要 HTTP/2 支援，而且必須改用 gRPC Web。

下表列出 .NET 的執行和其 gRPC 用戶端支援：

| .NET 實作                          | 透過 HTTP/2 的 gRPC   | gRPC-Web   |
|----------------------------------------------|--------------------|------------|
| .NET 5 或更新版本                              | ✔️                | ✔️         |
| .NET Core 3                                  | ✔️                | ✔️         |
| .NET Core 2.1                                | ❌                | ✔️         |
| .NET Framework 4.6.1                         | ⚠️&dagger;        | ✔️         |
| Blazor WebAssembly                           | ❌                | ✔️         |
| Mono 5.4                                     | ❌                | ✔️         |
| Xamarin.iOS 10.14                            | ❌                | ✔️         |
| Xamarin.Android 8.0                          | ❌                | ✔️         |
| 通用 Windows 平台 10.0.16299        | ❌                | ✔️         |
| Unity 2018。1                                 | ❌                | ✔️         |

&dagger;需要設定 .NET Framework <xref:System.Net.Http.WinHttpHandler> 和 Windows 10 組建19622或更新版本。

`Grpc.Net.Client`在 .Net Framework 或 GRPC Web 上使用需要額外的設定。 如需詳細資訊，請參閱<xref:grpc/netstandard>。

## <a name="additional-resources"></a>其他資源

* <xref:grpc/netstandard>
* [gRPC c # 核心-程式庫](https://grpc.io/docs/languages/csharp/quickstart/)
