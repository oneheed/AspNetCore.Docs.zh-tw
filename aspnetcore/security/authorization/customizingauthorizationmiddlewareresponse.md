---
title: 自訂 AuthorizationMiddleware 的行為
author: pranavkm
ms.author: prkrishn
description: 本文說明如何自訂 AuthorizationMiddleware 的結果處理。
monikerRange: '>= aspnetcore-5.0'
uid: security/authorization/authorizationmiddlewareresulthandler
ms.openlocfilehash: 55f4433c080d6ce6676ca1072dacacc137f15638
ms.sourcegitcommit: b3ec60f7682e43211c2b40c60eab3d4e45a48ab1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92156779"
---
# <a name="customize-the-behavior-of-authorizationmiddleware"></a><span data-ttu-id="e435c-103">自訂 AuthorizationMiddleware 的行為</span><span class="sxs-lookup"><span data-stu-id="e435c-103">Customize the behavior of AuthorizationMiddleware</span></span>

<span data-ttu-id="e435c-104">應用程式可以註冊 `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` 來自訂中介軟體處理授權結果的方式。</span><span class="sxs-lookup"><span data-stu-id="e435c-104">Applications can register a `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` to customize the way the middleware handles the authorization results.</span></span> <span data-ttu-id="e435c-105">應用程式可以使用自訂中介軟體來：</span><span class="sxs-lookup"><span data-stu-id="e435c-105">Applications can use the customized middleware to:</span></span>

* <span data-ttu-id="e435c-106">傳回自訂回應。</span><span class="sxs-lookup"><span data-stu-id="e435c-106">Return customized responses.</span></span>
* <span data-ttu-id="e435c-107">增強預設的挑戰或禁止回應。</span><span class="sxs-lookup"><span data-stu-id="e435c-107">Enhance the default challenge or forbid responses.</span></span>

<span data-ttu-id="e435c-108">下列程式碼顯示的授權處理常式範例會傳回特定授權失敗類型的自訂回應：</span><span class="sxs-lookup"><span data-stu-id="e435c-108">The following code shows an example of an authorization handler that returns a custom response for certain kinds of authorization failures:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

<span data-ttu-id="e435c-109">報名 `MyAuthorizationMiddlewareResultHandler` `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="e435c-109">Register `MyAuthorizationMiddlewareResultHandler` in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->