---
title: React-with-Redux 專案範本搭配 ASP.NET Core 使用
author: SteveSandersonMS
description: 了解如何開始使用 React with Redux 和 create-react-app 適用的 ASP.NET Core 單頁應用程式 (SPA) 專案範本。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/13/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/react-with-redux
ms.openlocfilehash: 96ee4a06360d6cca8d48c300ecebab9014da9c56
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013134"
---
# <a name="use-the-react-with-redux-project-template-with-aspnet-core"></a><span data-ttu-id="5e041-103">React-with-Redux 專案範本搭配 ASP.NET Core 使用</span><span class="sxs-lookup"><span data-stu-id="5e041-103">Use the React-with-Redux project template with ASP.NET Core</span></span>

<span data-ttu-id="5e041-104">更新的 React-with-Redux 專案範本很適合用來設計 ASP.NET Core 應用程式，而且可利用 React、Redux 和 [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) 慣例來實作一個功能多樣的用戶端使用者介面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="5e041-104">The updated React-with-Redux project template provides a convenient starting point for ASP.NET Core apps using React, Redux, and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="5e041-105">除了專案建立命令，所有與 React-with-Redux 範本有關的資訊，都與 React 範本相同。</span><span class="sxs-lookup"><span data-stu-id="5e041-105">With the exception of the project creation command, all information about the React-with-Redux template is the same as the React template.</span></span> <span data-ttu-id="5e041-106">若要建立這種專案類型，請執行 `dotnet new reactredux` 而不是 `dotnet new react`。</span><span class="sxs-lookup"><span data-stu-id="5e041-106">To create this project type, run `dotnet new reactredux` instead of `dotnet new react`.</span></span> <span data-ttu-id="5e041-107">對於這兩個以 React 為基礎的範本，如需深入了解其通用功能，請參閱 [React 範本文件](xref:spa/react)。</span><span class="sxs-lookup"><span data-stu-id="5e041-107">For more information about the functionality common to both React-based templates, see [React template documentation](xref:spa/react).</span></span>

<span data-ttu-id="5e041-108">如需在 IIS 中設定 Redux 子應用程式的相關資訊，請參閱[ReactRedux 範本2.1：無法在 iis 上使用 SPA (aspnet/樣板化 &num; 555) ](https://github.com/aspnet/Templating/issues/555)。</span><span class="sxs-lookup"><span data-stu-id="5e041-108">For information on configuring a React-with-Redux sub-application in IIS, see [ReactRedux Template 2.1: Unable to use SPA on IIS (aspnet/Templating &num;555)](https://github.com/aspnet/Templating/issues/555).</span></span>
