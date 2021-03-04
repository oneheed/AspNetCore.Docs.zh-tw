---
title: 適用于 ASP.NET Core 的驗證範例
author: rick-anderson
description: 提供 ASP.NET Core 存放庫中驗證範例的連結。
ms.author: riande
ms.date: 02/21/2021
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
uid: security/authentication/samples
ms.openlocfilehash: e7fb2ac32f57cf4ecd3c5db294bd0df8e186b6c6
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110114"
---
# <a name="authentication-samples-for-aspnet-core"></a>適用于 ASP.NET Core 的驗證範例

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[ASP.NET Core 存放庫](https://github.com/dotnet/aspnetcore)包含下列 (分支) 的驗證範例 `main` ：

* [宣告轉換](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/ClaimsTransformation)
* [Cookie 認證](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Cookies)
* [自訂授權失敗回應](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomAuthorizationFailureResponse)
* [自訂原則提供者-IAuthorizationPolicyProvider](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/CustomPolicyProvider)
* [動態驗證方案和選項](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/DynamicSchemes)
* [外部宣告](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/Identity.ExternalClaims)
* [cookie根據要求選取和其他驗證配置](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/PathSchemeSelection)
* [限制存取靜態檔案](https://github.com/dotnet/aspnetcore/tree/main/src/Security/samples/StaticFilesAuth)

## <a name="obtain-and-run-the-samples"></a>取得並執行範例

本文提供的範例連結提供即將發行的 ASP.NET Core 範例。 若要取得目前版本或先前版本的範例，請執行下列步驟：

* 選取 [ASP.NET Core 存放庫](https://github.com/dotnet/aspnetcore)的發行分支] (https://github.com/dotnet/aspnetcore) 。 例如， `release/5.0` 分支包含 ASP.NET Core 5.0 版本的範例。
* 複製或下載 ASP.NET Core 存放庫。
* 在您的本機系統上，確認已安裝符合 ASP.NET Core 存放庫複製的 [.Net CORE SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。
* 流覽至資料夾中的範例 `aspnetcore/src/Security/samples` ，並使用[ `dotnet run` 命令](/dotnet/core/tools/dotnet-run)執行範例。
