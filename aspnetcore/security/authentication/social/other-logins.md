---
title: 外部 OAuth 驗證提供者
author: rick-anderson
description: 探索可搭配 ASP.NET Core apps 使用的外部 OAuth 驗證提供者。
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
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
uid: security/authentication/otherlogins
ms.openlocfilehash: f410f9ade34c3ffb19dc9f6e5409f44d0a6e5d32
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634237"
---
# <a name="external-oauth-authentication-providers"></a><span data-ttu-id="efc31-103">外部 OAuth 驗證提供者</span><span class="sxs-lookup"><span data-stu-id="efc31-103">External OAuth authentication providers</span></span>

<span data-ttu-id="efc31-104">由 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Pranav 請參閱 rastogi](https://github.com/rustd)和 [Valeriy Novytskyy](https://github.com/01binary)</span><span class="sxs-lookup"><span data-stu-id="efc31-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Pranav Rastogi](https://github.com/rustd), and [Valeriy Novytskyy](https://github.com/01binary)</span></span>

<span data-ttu-id="efc31-105">下列清單包含可搭配 ASP.NET Core apps 使用的常見外部 OAuth 驗證提供者。</span><span class="sxs-lookup"><span data-stu-id="efc31-105">The following list includes common external OAuth authentication providers that work with ASP.NET Core apps.</span></span> <span data-ttu-id="efc31-106">協力廠商 NuGet 套件（例如由 [aspnet contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth)維護的套件）可用來補充 ASP.NET Core 小組所實行的驗證提供者。</span><span class="sxs-lookup"><span data-stu-id="efc31-106">Third-party NuGet packages, such as the ones maintained by [aspnet-contrib](https://www.nuget.org/packages?q=owners%3Aaspnet-contrib+title%3AOAuth), can be used to complement the authentication providers implemented by the ASP.NET Core team.</span></span>

* <span data-ttu-id="efc31-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([指示](https://developer.linkedin.com/docs/oauth2)) </span><span class="sxs-lookup"><span data-stu-id="efc31-107">[LinkedIn](https://www.linkedin.com/developer/apps) ([Instructions](https://developer.linkedin.com/docs/oauth2))</span></span>

* <span data-ttu-id="efc31-108">[Instagram](https://www.instagram.com/developer/register/) ([指示](https://www.instagram.com/developer/authentication/)) </span><span class="sxs-lookup"><span data-stu-id="efc31-108">[Instagram](https://www.instagram.com/developer/register/) ([Instructions](https://www.instagram.com/developer/authentication/))</span></span>

* <span data-ttu-id="efc31-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([指示](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example)) </span><span class="sxs-lookup"><span data-stu-id="efc31-109">[Reddit](https://www.reddit.com/login?dest=https%3A%2F%2Fwww.reddit.com%2Fprefs%2Fapps) ([Instructions](https://github.com/reddit/reddit/wiki/OAuth2-Quick-Start-Example))</span></span>

* <span data-ttu-id="efc31-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([指示](https://developer.github.com/v3/oauth/)) </span><span class="sxs-lookup"><span data-stu-id="efc31-110">[Github](https://github.com/login?return_to=https%3A%2F%2Fgithub.com%2Fsettings%2Fapplications%2Fnew) ([Instructions](https://developer.github.com/v3/oauth/))</span></span>

* <span data-ttu-id="efc31-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([指示](https://developer.yahoo.com/bbauth/user.html)) </span><span class="sxs-lookup"><span data-stu-id="efc31-111">[Yahoo](https://login.yahoo.com/config/login?src=devnet&.done=http%3A%2F%2Fdeveloper.yahoo.com%2Fapps%2Fcreate%2F) ([Instructions](https://developer.yahoo.com/bbauth/user.html))</span></span>

* <span data-ttu-id="efc31-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([指示](https://www.tumblr.com/docs/api/v2#auth)) </span><span class="sxs-lookup"><span data-stu-id="efc31-112">[Tumblr](https://www.tumblr.com/oauth/apps) ([Instructions](https://www.tumblr.com/docs/api/v2#auth))</span></span>

* <span data-ttu-id="efc31-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([指示](https://developers.pinterest.com/docs/api/overview/?)) </span><span class="sxs-lookup"><span data-stu-id="efc31-113">[Pinterest](https://www.pinterest.com/login/?next=http%3A%2F%2Fdevsite%2Fapps%2F) ([Instructions](https://developers.pinterest.com/docs/api/overview/?))</span></span>

* <span data-ttu-id="efc31-114">[Pocket](https://getpocket.com/developer/apps/new) ([指示](https://getpocket.com/developer/docs/authentication)) </span><span class="sxs-lookup"><span data-stu-id="efc31-114">[Pocket](https://getpocket.com/developer/apps/new) ([Instructions](https://getpocket.com/developer/docs/authentication))</span></span>

* <span data-ttu-id="efc31-115">[Flickr](https://www.flickr.com/services/apps/create) ([指示](https://www.flickr.com/services/api/auth.oauth.html)) </span><span class="sxs-lookup"><span data-stu-id="efc31-115">[Flickr](https://www.flickr.com/services/apps/create) ([Instructions](https://www.flickr.com/services/api/auth.oauth.html))</span></span>

* <span data-ttu-id="efc31-116">[Dribble](https://dribbble.com/signup) ([指示](https://developer.dribbble.com/v1/oauth/)) </span><span class="sxs-lookup"><span data-stu-id="efc31-116">[Dribble](https://dribbble.com/signup) ([Instructions](https://developer.dribbble.com/v1/oauth/))</span></span>

* <span data-ttu-id="efc31-117">[Vimeo](https://vimeo.com/join) ([指示](https://developer.vimeo.com/api/authentication)) </span><span class="sxs-lookup"><span data-stu-id="efc31-117">[Vimeo](https://vimeo.com/join) ([Instructions](https://developer.vimeo.com/api/authentication))</span></span>

* <span data-ttu-id="efc31-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([指示](https://developers.soundcloud.com/blog/we-love-oauth-2)) </span><span class="sxs-lookup"><span data-stu-id="efc31-118">[SoundCloud](https://soundcloud.com/you/apps/new) ([Instructions](https://developers.soundcloud.com/blog/we-love-oauth-2))</span></span>

* <span data-ttu-id="efc31-119">[VK](https://vk.com/apps?act=manage) ([指示](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites)) </span><span class="sxs-lookup"><span data-stu-id="efc31-119">[VK](https://vk.com/apps?act=manage) ([Instructions](https://vk.com/pages?oid=-17680044&p=Authorizing_Sites))</span></span>

[!INCLUDE[Multiple authentication providers](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]
