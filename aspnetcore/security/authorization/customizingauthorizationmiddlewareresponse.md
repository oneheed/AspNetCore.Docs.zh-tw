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
# <a name="customize-the-behavior-of-authorizationmiddleware"></a>自訂 AuthorizationMiddleware 的行為

應用程式可以註冊 `Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler` 來自訂中介軟體處理授權結果的方式。 應用程式可以使用自訂中介軟體來：

* 傳回自訂回應。
* 增強預設的挑戰或禁止回應。

下列程式碼顯示的授權處理常式範例會傳回特定授權失敗類型的自訂回應：

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/MyAuthorizationMiddlewareResultHandler.cs)]

報名 `MyAuthorizationMiddlewareResultHandler` `Startup.ConfigureServices` ：

[!code-csharp[](customizingauthorizationmiddlewareresponse/sample/AuthorizationMiddlewareResultHandlerSample/Startup.cs?name=snippet)]

<!-- <xref:Microsoft.AspNetCore.Authorization.IAuthorizationMiddlewareResultHandler /> -->