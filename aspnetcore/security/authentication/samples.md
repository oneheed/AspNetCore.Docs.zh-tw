---
title: ASP.NET Core 的驗證範例
author: rick-anderson
description: 提供 ASP.NET Core 存放庫中驗證範例的連結。
ms.author: riande
ms.date: 01/31/2019
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
uid: security/authentication/samples
ms.openlocfilehash: 290c956b2035e47e5b34dba15fbec665461dd94a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630740"
---
# <a name="authentication-samples-for-aspnet-core"></a>ASP.NET Core 的驗證範例

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[ASP.NET Core 存放庫](https://github.com/dotnet/AspNetCore)會在*AspNetCore/src/Security/samples*資料夾中包含下列驗證範例：

* [宣告轉換](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Cookie 認證](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [自訂原則提供者-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [動態驗證方案和選項](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [外部宣告](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [cookie根據要求選取和其他驗證配置](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [限制存取靜態檔案](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>執行範例

* 選取 [分支](https://github.com/dotnet/AspNetCore)。 例如， `release/3.1`
* 複製或下載 [ASP.NET Core 存放庫](https://github.com/dotnet/AspNetCore)。
* 確認您已安裝符合 ASP.NET Core 存放庫複製的 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。
* 流覽至 *AspNetCore/src/Security/* sample 中的範例，並使用執行範例 `dotnet run` 。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[ASP.NET Core 存放庫](https://github.com/dotnet/AspNetCore)會在*AspNetCore/src/Security/samples*資料夾中包含下列驗證範例：

* [宣告轉換](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* [Cookie 認證](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)
* [自訂原則提供者-IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [動態驗證方案和選項](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* [外部宣告](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)
* [cookie根據要求選取和其他驗證配置](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [限制存取靜態檔案](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>執行範例

* 選取 [分支](https://github.com/dotnet/AspNetCore)。 例如， `release/2.1`
* 複製或下載 [ASP.NET Core 存放庫](https://github.com/dotnet/AspNetCore)。
* 確認您已安裝符合 ASP.NET Core 存放庫複製的 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 版本。
* 流覽至 *AspNetCore/src/Security/* sample 中的範例，並使用執行範例 `dotnet run` 。

::: moniker-end
