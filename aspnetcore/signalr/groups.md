---
title: 管理中的使用者和群組 SignalR
author: bradygaster
description: ASP.NET Core SignalR 使用者和群組管理的總覽。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
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
uid: signalr/groups
ms.openlocfilehash: 8f9e91f34386bb5b4cf79bee429a39ae27c03c5e
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589214"
---
# <a name="manage-users-and-groups-in-signalr"></a>管理中的使用者和群組 SignalR

依 [Brennan Conroy](https://github.com/BrennanConroy)

SignalR 允許將訊息傳送至與特定使用者相關聯的所有連接，以及命名的連接群組。

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/groups/sample/) [ (如何下載) ](xref:index#how-to-download-a-sample)

## <a name="users-in-signalr"></a>中的使用者 SignalR

中的單一使用者 SignalR 可以有應用程式的多個連接。 例如，使用者可以連接到其桌面上以及電話。 每個裝置都有個別的 SignalR 連接，但全都與相同的使用者相關聯。 如果訊息傳送給使用者，與該使用者相關聯的所有連接都會收到訊息。 中樞的屬性可以存取連接的使用者識別碼 `Context.UserIdentifier` 。

根據預設，會 SignalR 使用 `ClaimTypes.NameIdentifier` `ClaimsPrincipal` 與連接相關聯的，做為使用者識別碼。 若要自訂此行為，請參閱 [使用宣告來自訂身分識別處理](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling)。

傳送訊息給特定使用者，方法是將使用者識別碼傳遞至 `User` 中樞方法中的函式，如下列範例所示：

> [!NOTE]
> 使用者識別碼會區分大小寫。

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-signalr"></a>群組于 SignalR

群組是與名稱相關聯的連接集合。 訊息可以傳送至群組中的所有連接。 群組是傳送至連線或多個連接的建議方式，因為群組是由應用程式所管理。 連接可以是多個群組的成員。 群組非常適合像是聊天應用程式之類的聊天應用程式，每個房間都可以用群組來表示。 連接會透過和方法新增至群組或從中 `AddToGroupAsync` 移除 `RemoveFromGroupAsync` 。

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

當連接重新連接時，不會保留群組成員資格。 重新建立時，連接必須重新加入群組。 無法計算群組的成員，因為如果應用程式調整為多部伺服器，就無法使用此資訊。

若要在使用群組時保護對資源的存取，請使用 ASP.NET Core 中的 [驗證和授權](xref:signalr/authn-and-authz) 功能。 如果只有在認證適用于該群組時才將使用者新增至群組，則傳送至該群組的訊息只會傳送給已授權的使用者。 但是，群組並不是安全性功能。 驗證宣告具有群組沒有的功能，例如過期和撤銷。 如果撤銷使用者存取群組的許可權，應用程式必須明確地從群組中移除該使用者。

> [!NOTE]
> 組名會區分大小寫。

## <a name="related-resources"></a>相關資源

* [開始使用](xref:tutorials/signalr)
* [中樞](xref:signalr/hubs)
* [發佈至 Azure](xref:signalr/publish-to-azure-web-app)
