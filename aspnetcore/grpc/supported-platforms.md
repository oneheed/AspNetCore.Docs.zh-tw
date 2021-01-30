---
title: 適用于 .NET 的 gRPC 支援平臺
author: jamesnk
description: 瞭解在 .NET 上 gRPC 支援的平臺。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/22/2021
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
ms.openlocfilehash: 92ca38875c6618c8630a66af16548d32bc469a62
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057710"
---
# <a name="grpc-for-net-supported-platforms"></a>適用于 .NET 的 gRPC 支援平臺

依 [James 牛頓](https://twitter.com/jamesnk)

本文討論使用 gRPC 搭配 .NET 的需求和支援的平臺。

gRPC 的設計目的是要針對一些更先進的功能使用 HTTP/2。 在所有可能防止使用 gRPC 的地方，都不支援 HTTP/2。 因為這種情況下，第二種連線格式與 HTTP/1.1 相容，可在用戶端與伺服器之間傳送 gRPC 呼叫：

* [`application/grpc`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) -gRPC over HTTP/2 是 gRPC 的一般使用方式。
* [`application/grpc-web`](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) -gRPC-Web 會修改 gRPC 通訊協定，以與 HTTP/1.1 相容。 gRPC Web 可以在更多位置使用，尤其是瀏覽器應用程式可以呼叫它。 不再支援兩個 advanced gRPC 功能：用戶端串流和雙向串流。

適用于 .NET 的 gRPC 支援兩種網路格式。 如需設定 gRPC Web 的詳細資訊，請參閱 <xref:grpc/browser> 。

## <a name="device-requirements"></a>裝置需求

gRPC for .NET 支援 .NET Core 支援的任何裝置。

> [!div class="checklist"]
>
> * Windows
> * Linux
> * macOS&dagger;
> * 瀏覽器&Dagger;

&dagger;MacOS 上託管的應用程式不支援 HTTPS。 ASP.NET Core 呼叫遠端服務時，macOS 上的 gRPC 用戶端仍然可以使用 HTTPS。

&Dagger;Blazor WebAssembly 應用程式可以使用 gRPC （Web）來呼叫 gRPC 服務。

## <a name="aspnet-core-server-requirements"></a>ASP.NET Core 伺服器需求

gRPC 服務可以裝載在所有內建的 ASP.NET Core 伺服器上。

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&dagger;

&dagger;IIS 和 HTTP.sys 需要 .NET 5 和 Windows 10 組建20241或更新版本。

如需詳細資訊，請參閱<xref:grpc/aspnetcore>。

## <a name="net-version-requirements"></a>.NET 版本需求

適用于 .NET 的 gRPC 支援 .NET Core 3 和 .NET 5 或更新版本。

> [!div class="checklist"]
>
> * .NET 5 或更新版本
> * .NET Core 3

gRPC for .NET 不支援在 .NET Framework 和 Xamarin 上執行。 [GRPC c # core-程式庫](https://grpc.io/docs/languages/csharp/quickstart/) 是支援 .NET Framework 和 Xamarin 的協力廠商程式庫。 Microsoft 不支援 gRPC C-核心。

## <a name="azure-services"></a>Azure 服務

> [!div class="checklist"]
>
> * [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/)
> * [Azure App Service](https://azure.microsoft.com/services/app-service/)&dagger;

&dagger;Azure App Service 不支援透過 HTTP/2 裝載 gRPC。 gRPC-Web 是相容的替代方案。

工作正在進行中，以 Azure App Service 中的 HTTP/2 改善 gRPC 支援。 如需詳細資訊，請參閱 [此 GitHub 問題](https://github.com/dotnet/AspNetCore/issues/9020)。

## <a name="additional-resources"></a>其他資源

* [gRPC c # 核心-程式庫](https://grpc.io/docs/languages/csharp/quickstart/)
