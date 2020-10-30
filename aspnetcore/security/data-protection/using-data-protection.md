---
title: ASP.NET Core 中的資料保護 Api 入門
author: rick-anderson
description: 瞭解如何使用 ASP.NET Core 資料保護 Api 來保護和取消保護應用程式中的資料。
ms.author: riande
ms.date: 11/12/2019
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
uid: security/data-protection/using-data-protection
ms.openlocfilehash: 1f0d42a7b12edb870481024372d75cdc9e57be21
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051651"
---
# <a name="get-started-with-the-data-protection-apis-in-aspnet-core"></a><span data-ttu-id="0b109-103">ASP.NET Core 中的資料保護 Api 入門</span><span class="sxs-lookup"><span data-stu-id="0b109-103">Get started with the Data Protection APIs in ASP.NET Core</span></span>

<a name="security-data-protection-getting-started"></a>

<span data-ttu-id="0b109-104">保護資料最簡單的方式是由下列步驟組成：</span><span class="sxs-lookup"><span data-stu-id="0b109-104">At its simplest, protecting data consists of the following steps:</span></span>

1. <span data-ttu-id="0b109-105">從資料保護提供者建立資料保護裝置。</span><span class="sxs-lookup"><span data-stu-id="0b109-105">Create a data protector from a data protection provider.</span></span>

2. <span data-ttu-id="0b109-106">`Protect`使用您想要保護的資料來呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="0b109-106">Call the `Protect` method with the data you want to protect.</span></span>

3. <span data-ttu-id="0b109-107">`Unprotect`使用您想要轉換回純文字的資料來呼叫方法。</span><span class="sxs-lookup"><span data-stu-id="0b109-107">Call the `Unprotect` method with the data you want to turn back into plain text.</span></span>

<span data-ttu-id="0b109-108">大部分的架構和應用程式模型（例如 ASP.NET Core 或 :::no-loc(SignalR)::: ）已設定資料保護系統，並將它新增至您透過相依性插入存取的服務容器。</span><span class="sxs-lookup"><span data-stu-id="0b109-108">Most frameworks and app models, such as ASP.NET Core or :::no-loc(SignalR):::, already configure the data protection system and add it to a service container you access via dependency injection.</span></span> <span data-ttu-id="0b109-109">下列範例示範如何設定服務容器以進行相依性插入，以及註冊資料保護堆疊、透過 DI 接收資料保護提供者、建立保護裝置，以及保護取消保護資料。</span><span class="sxs-lookup"><span data-stu-id="0b109-109">The following sample demonstrates configuring a service container for dependency injection and registering the data protection stack, receiving the data protection provider via DI, creating a protector and protecting then unprotecting data.</span></span>

[!code-csharp[](../../security/data-protection/using-data-protection/samples/protectunprotect.cs?highlight=26,34,35,36,37,38,39,40)]

<span data-ttu-id="0b109-110">當您建立保護裝置時，您必須提供一或多個 [目的字串](xref:security/data-protection/consumer-apis/purpose-strings)。</span><span class="sxs-lookup"><span data-stu-id="0b109-110">When you create a protector you must provide one or more [Purpose Strings](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="0b109-111">目的字串可提供取用者之間的隔離。</span><span class="sxs-lookup"><span data-stu-id="0b109-111">A purpose string provides isolation between consumers.</span></span> <span data-ttu-id="0b109-112">例如，使用「綠色」目的字串所建立的保護裝置，將無法解除保護保護裝置所提供的資料，目的為「紫色」。</span><span class="sxs-lookup"><span data-stu-id="0b109-112">For example, a protector created with a purpose string of "green" wouldn't be able to unprotect data provided by a protector with a purpose of "purple".</span></span>

>[!TIP]
> <span data-ttu-id="0b109-113">和的 `IDataProtectionProvider` 實例 `IDataProtector` 都是多個呼叫端的安全線程。</span><span class="sxs-lookup"><span data-stu-id="0b109-113">Instances of `IDataProtectionProvider` and `IDataProtector` are thread-safe for multiple callers.</span></span> <span data-ttu-id="0b109-114">它的目的是只要元件透過呼叫取得的參考 `IDataProtector` `CreateProtector` ，它就會將該參考用於多個對和的呼叫 `Protect` `Unprotect` 。</span><span class="sxs-lookup"><span data-stu-id="0b109-114">It's intended that once a component gets a reference to an `IDataProtector` via a call to `CreateProtector`, it will use that reference for multiple calls to `Protect` and `Unprotect`.</span></span>
>
><span data-ttu-id="0b109-115">`Unprotect`如果無法驗證或解密受保護的承載，的呼叫將會擲回 system.security.cryptography.cryptographicexception。</span><span class="sxs-lookup"><span data-stu-id="0b109-115">A call to `Unprotect` will throw CryptographicException if the protected payload cannot be verified or deciphered.</span></span> <span data-ttu-id="0b109-116">某些元件可能會想要忽略取消保護作業期間的錯誤;讀取驗證的元件 :::no-loc(cookie)::: 可能會處理此錯誤，並將要求視為完全沒有， :::no-loc(cookie)::: 而不是直接讓要求失敗。</span><span class="sxs-lookup"><span data-stu-id="0b109-116">Some components may wish to ignore errors during unprotect operations; a component which reads authentication :::no-loc(cookie):::s might handle this error and treat the request as if it had no :::no-loc(cookie)::: at all rather than fail the request outright.</span></span> <span data-ttu-id="0b109-117">需要此行為的元件應該明確地捕捉 System.security.cryptography.cryptographicexception，而不是抑制所有例外狀況。</span><span class="sxs-lookup"><span data-stu-id="0b109-117">Components which want this behavior should specifically catch CryptographicException instead of swallowing all exceptions.</span></span>
