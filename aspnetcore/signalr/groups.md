---
title: '管理中的使用者和群組 :::no-loc(SignalR):::'
author: bradygaster
description: 'ASP.NET Core :::no-loc(SignalR)::: 使用者和群組管理的總覽。'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 05/17/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/groups
ms.openlocfilehash: a86408eaae8d3df32faef79453d9db0cdbd64a78
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050949"
---
# <a name="manage-users-and-groups-in-no-locsignalr"></a><span data-ttu-id="f4706-103">管理中的使用者和群組 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="f4706-103">Manage users and groups in :::no-loc(SignalR):::</span></span>

<span data-ttu-id="f4706-104">依 [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="f4706-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="f4706-105">:::no-loc(SignalR)::: 允許將訊息傳送至與特定使用者相關聯的所有連接，以及命名的連接群組。</span><span class="sxs-lookup"><span data-stu-id="f4706-105">:::no-loc(SignalR)::: allows messages to be sent to all connections associated with a specific user, as well as to named groups of connections.</span></span>

<span data-ttu-id="f4706-106">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [ (如何下載) ](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="f4706-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/groups/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="users-in-no-locsignalr"></a><span data-ttu-id="f4706-107">中的使用者 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="f4706-107">Users in :::no-loc(SignalR):::</span></span>

<span data-ttu-id="f4706-108">中的單一使用者 :::no-loc(SignalR)::: 可以有應用程式的多個連接。</span><span class="sxs-lookup"><span data-stu-id="f4706-108">A single user in :::no-loc(SignalR)::: can have multiple connections to an app.</span></span> <span data-ttu-id="f4706-109">例如，使用者可以連接到其桌面上以及電話。</span><span class="sxs-lookup"><span data-stu-id="f4706-109">For example, a user could be connected on their desktop as well as their phone.</span></span> <span data-ttu-id="f4706-110">每個裝置都有個別的 :::no-loc(SignalR)::: 連接，但全都與相同的使用者相關聯。</span><span class="sxs-lookup"><span data-stu-id="f4706-110">Each device has a separate :::no-loc(SignalR)::: connection, but they're all associated with the same user.</span></span> <span data-ttu-id="f4706-111">如果訊息傳送給使用者，與該使用者相關聯的所有連接都會收到訊息。</span><span class="sxs-lookup"><span data-stu-id="f4706-111">If a message is sent to the user, all of the connections associated with that user receive the message.</span></span> <span data-ttu-id="f4706-112">中樞的屬性可以存取連接的使用者識別碼 `Context.UserIdentifier` 。</span><span class="sxs-lookup"><span data-stu-id="f4706-112">The user identifier for a connection can be accessed by the `Context.UserIdentifier` property in the hub.</span></span>

<span data-ttu-id="f4706-113">根據預設，會 :::no-loc(SignalR)::: 使用 `ClaimTypes.NameIdentifier` `ClaimsPrincipal` 與連接相關聯的，做為使用者識別碼。</span><span class="sxs-lookup"><span data-stu-id="f4706-113">By default, :::no-loc(SignalR)::: uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> <span data-ttu-id="f4706-114">若要自訂此行為，請參閱 [使用宣告來自訂身分識別處理](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling)。</span><span class="sxs-lookup"><span data-stu-id="f4706-114">To customize this behavior, see [Use claims to customize identity handling](xref:signalr/authn-and-authz#use-claims-to-customize-identity-handling).</span></span>

<span data-ttu-id="f4706-115">傳送訊息給特定使用者，方法是將使用者識別碼傳遞至 `User` 中樞方法中的函式，如下列範例所示：</span><span class="sxs-lookup"><span data-stu-id="f4706-115">Send a message to a specific user by passing the user identifier to the `User` function in a hub method, as shown in the following example:</span></span>

> [!NOTE]
> <span data-ttu-id="f4706-116">使用者識別碼會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="f4706-116">The user identifier is case-sensitive.</span></span>

[!code-csharp[Configure service](groups/sample/Hubs/ChatHub.cs?range=29-32)]

## <a name="groups-in-no-locsignalr"></a><span data-ttu-id="f4706-117">群組于 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="f4706-117">Groups in :::no-loc(SignalR):::</span></span>

<span data-ttu-id="f4706-118">群組是與名稱相關聯的連接集合。</span><span class="sxs-lookup"><span data-stu-id="f4706-118">A group is a collection of connections associated with a name.</span></span> <span data-ttu-id="f4706-119">訊息可以傳送至群組中的所有連接。</span><span class="sxs-lookup"><span data-stu-id="f4706-119">Messages can be sent to all connections in a group.</span></span> <span data-ttu-id="f4706-120">群組是傳送至連線或多個連接的建議方式，因為群組是由應用程式所管理。</span><span class="sxs-lookup"><span data-stu-id="f4706-120">Groups are the recommended way to send to a connection or multiple connections because the groups are managed by the application.</span></span> <span data-ttu-id="f4706-121">連接可以是多個群組的成員。</span><span class="sxs-lookup"><span data-stu-id="f4706-121">A connection can be a member of multiple groups.</span></span> <span data-ttu-id="f4706-122">群組非常適合像是聊天應用程式之類的聊天應用程式，每個房間都可以用群組來表示。</span><span class="sxs-lookup"><span data-stu-id="f4706-122">Groups are ideal for something like a chat application, where each room can be represented as a group.</span></span> <span data-ttu-id="f4706-123">連接會透過和方法新增至群組或從中 `AddToGroupAsync` 移除 `RemoveFromGroupAsync` 。</span><span class="sxs-lookup"><span data-stu-id="f4706-123">Connections are added to or removed from groups via the `AddToGroupAsync` and `RemoveFromGroupAsync` methods.</span></span>

[!code-csharp[Hub methods](groups/sample/Hubs/ChatHub.cs?range=15-27)]

<span data-ttu-id="f4706-124">當連接重新連接時，不會保留群組成員資格。</span><span class="sxs-lookup"><span data-stu-id="f4706-124">Group membership isn't preserved when a connection reconnects.</span></span> <span data-ttu-id="f4706-125">重新建立時，連接必須重新加入群組。</span><span class="sxs-lookup"><span data-stu-id="f4706-125">The connection needs to rejoin the group when it's re-established.</span></span> <span data-ttu-id="f4706-126">無法計算群組的成員，因為如果應用程式調整為多部伺服器，就無法使用此資訊。</span><span class="sxs-lookup"><span data-stu-id="f4706-126">It's not possible to count the members of a group, since this information is not available if the application is scaled to multiple servers.</span></span>

<span data-ttu-id="f4706-127">若要在使用群組時保護對資源的存取，請使用 ASP.NET Core 中的 [驗證和授權](xref:signalr/authn-and-authz) 功能。</span><span class="sxs-lookup"><span data-stu-id="f4706-127">To protect access to resources while using groups, use [authentication and authorization](xref:signalr/authn-and-authz) functionality in ASP.NET Core.</span></span> <span data-ttu-id="f4706-128">如果只有在認證適用于該群組時才將使用者新增至群組，則傳送至該群組的訊息只會傳送給已授權的使用者。</span><span class="sxs-lookup"><span data-stu-id="f4706-128">If a user is added to a group only when the credentials are valid for that group, messages sent to that group will only go to authorized users.</span></span> <span data-ttu-id="f4706-129">但是，群組並不是安全性功能。</span><span class="sxs-lookup"><span data-stu-id="f4706-129">However, groups are not a security feature.</span></span> <span data-ttu-id="f4706-130">驗證宣告具有群組沒有的功能，例如過期和撤銷。</span><span class="sxs-lookup"><span data-stu-id="f4706-130">Authentication claims have features that groups do not, such as expiry and revocation.</span></span> <span data-ttu-id="f4706-131">如果撤銷使用者存取群組的許可權，應用程式必須明確地從群組中移除該使用者。</span><span class="sxs-lookup"><span data-stu-id="f4706-131">If a user's permission to access the group is revoked, the app must remove the user from the group explicitly.</span></span>

> [!NOTE]
> <span data-ttu-id="f4706-132">組名會區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="f4706-132">Group names are case-sensitive.</span></span>

## <a name="related-resources"></a><span data-ttu-id="f4706-133">相關資源</span><span class="sxs-lookup"><span data-stu-id="f4706-133">Related resources</span></span>

* [<span data-ttu-id="f4706-134">開始使用</span><span class="sxs-lookup"><span data-stu-id="f4706-134">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="f4706-135">中樞</span><span class="sxs-lookup"><span data-stu-id="f4706-135">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f4706-136">發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="f4706-136">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
