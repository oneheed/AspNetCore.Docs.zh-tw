---
title: Versioning gRPC 服務
author: jamesnk
description: 瞭解如何將 gRPC 服務的版本。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 079cca8a7d47897827c314b82136e9eb10b7a516
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633951"
---
# <a name="versioning-grpc-services"></a><span data-ttu-id="02018-103">Versioning gRPC 服務</span><span class="sxs-lookup"><span data-stu-id="02018-103">Versioning gRPC services</span></span>

<span data-ttu-id="02018-104">依 [James 牛頓](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="02018-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="02018-105">新增至應用程式的新功能可能需要提供給用戶端的 gRPC 服務變更，有時會以非預期和中斷的方式進行變更。</span><span class="sxs-lookup"><span data-stu-id="02018-105">New features added to an app can require gRPC services provided to clients to change, sometimes in unexpected and breaking ways.</span></span> <span data-ttu-id="02018-106">當 gRPC services 變更時：</span><span class="sxs-lookup"><span data-stu-id="02018-106">When gRPC services change:</span></span>

* <span data-ttu-id="02018-107">您應在變更影響用戶端的方式方面提供考慮。</span><span class="sxs-lookup"><span data-stu-id="02018-107">Consideration should be given on how changes impact clients.</span></span>
* <span data-ttu-id="02018-108">您應執行支援變更的版本控制策略。</span><span class="sxs-lookup"><span data-stu-id="02018-108">A versioning strategy to support changes should be implemented.</span></span>

## <a name="backwards-compatibility"></a><span data-ttu-id="02018-109">回溯相容性</span><span class="sxs-lookup"><span data-stu-id="02018-109">Backwards compatibility</span></span>

<span data-ttu-id="02018-110">GRPC 通訊協定是設計來支援隨時間變化的服務。</span><span class="sxs-lookup"><span data-stu-id="02018-110">The gRPC protocol is designed to support services that change over time.</span></span> <span data-ttu-id="02018-111">一般而言，gRPC 服務和方法的新增專案不會中斷。</span><span class="sxs-lookup"><span data-stu-id="02018-111">Generally, additions to gRPC services and methods are non-breaking.</span></span> <span data-ttu-id="02018-112">非中斷性變更可讓現有的用戶端繼續運作，而不需要變更。</span><span class="sxs-lookup"><span data-stu-id="02018-112">Non-breaking changes allow existing clients to continue working without changes.</span></span> <span data-ttu-id="02018-113">變更或刪除 gRPC 服務是重大變更。</span><span class="sxs-lookup"><span data-stu-id="02018-113">Changing or deleting gRPC services are breaking changes.</span></span> <span data-ttu-id="02018-114">當 gRPC 服務有突破性的變更時，必須更新並重新部署使用該服務的用戶端。</span><span class="sxs-lookup"><span data-stu-id="02018-114">When gRPC services have breaking changes, clients using that service have to be updated and redeployed.</span></span>

<span data-ttu-id="02018-115">對服務進行非中斷性變更有許多優點：</span><span class="sxs-lookup"><span data-stu-id="02018-115">Making non-breaking changes to a service has a number of benefits:</span></span>

* <span data-ttu-id="02018-116">現有的用戶端會繼續執行。</span><span class="sxs-lookup"><span data-stu-id="02018-116">Existing clients continue to run.</span></span>
* <span data-ttu-id="02018-117">避免與通知用戶端中斷的變更及更新它們相關的工作。</span><span class="sxs-lookup"><span data-stu-id="02018-117">Avoids work involved with notifying clients of breaking changes, and updating them.</span></span>
* <span data-ttu-id="02018-118">只有一個服務版本需要記錄和維護。</span><span class="sxs-lookup"><span data-stu-id="02018-118">Only one version of the service needs to be documented and maintained.</span></span>

### <a name="non-breaking-changes"></a><span data-ttu-id="02018-119">非中斷性變更</span><span class="sxs-lookup"><span data-stu-id="02018-119">Non-breaking changes</span></span>

<span data-ttu-id="02018-120">這些變更在 gRPC 通訊協定層級和 .NET 二進位層級不會中斷。</span><span class="sxs-lookup"><span data-stu-id="02018-120">These changes are non-breaking at a gRPC protocol level and .NET binary level.</span></span>

* <span data-ttu-id="02018-121">**新增服務**</span><span class="sxs-lookup"><span data-stu-id="02018-121">**Adding a new service**</span></span>
* <span data-ttu-id="02018-122">**將新方法加入至服務**</span><span class="sxs-lookup"><span data-stu-id="02018-122">**Adding a new method to a service**</span></span>
* <span data-ttu-id="02018-123">**將欄位加入至要求訊息** -新增至要求訊息的欄位會在未設定時，以伺服器上的 [預設值](https://developers.google.com/protocol-buffers/docs/proto3#default) 還原序列化。</span><span class="sxs-lookup"><span data-stu-id="02018-123">**Adding a field to a request message** - Fields added to a request message are deserialized with the [default value](https://developers.google.com/protocol-buffers/docs/proto3#default) on the server when not set.</span></span> <span data-ttu-id="02018-124">若要成為非重大變更，當舊的用戶端未設定新欄位時，服務必須成功。</span><span class="sxs-lookup"><span data-stu-id="02018-124">To be a non-breaking change, the service must succeed when the new field isn't set by older clients.</span></span>
* <span data-ttu-id="02018-125">**將欄位加入至回應** 消息-新增至回應訊息的欄位會還原序列化至用戶端上訊息的 [未知欄位](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) 集合。</span><span class="sxs-lookup"><span data-stu-id="02018-125">**Adding a field to a response message** - Fields added to a response message are deserialized into the message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) collection on the client.</span></span>
* <span data-ttu-id="02018-126">**將值新增至列舉列舉** 會序列化為數值。</span><span class="sxs-lookup"><span data-stu-id="02018-126">**Adding a value to an enum** - Enums are serialized as a numeric value.</span></span> <span data-ttu-id="02018-127">新的列舉值會在用戶端上還原序列化為沒有列舉名稱的列舉值。</span><span class="sxs-lookup"><span data-stu-id="02018-127">New enum values are deserialized on the client to the enum value without an enum name.</span></span> <span data-ttu-id="02018-128">若要成為非重大變更，舊版用戶端必須在接收新的列舉值時正確地執行。</span><span class="sxs-lookup"><span data-stu-id="02018-128">To be a non-breaking change, older clients must run correctly when receiving the new enum value.</span></span>

### <a name="binary-breaking-changes"></a><span data-ttu-id="02018-129">二進位中斷性變更</span><span class="sxs-lookup"><span data-stu-id="02018-129">Binary breaking changes</span></span>

<span data-ttu-id="02018-130">下列變更在 gRPC 通訊協定層級不會中斷，但如果用戶端升級至最新的 *proto* 合約或用戶端 .net 元件，則需要更新用戶端。</span><span class="sxs-lookup"><span data-stu-id="02018-130">The following changes are non-breaking at a gRPC protocol level, but the client needs to be updated if it upgrades to the latest *.proto* contract or client .NET assembly.</span></span> <span data-ttu-id="02018-131">如果您打算將 gRPC 程式庫發佈至 NuGet，二進位相容性很重要。</span><span class="sxs-lookup"><span data-stu-id="02018-131">Binary compatibility is important if you plan to publish a gRPC library to NuGet.</span></span>

* <span data-ttu-id="02018-132">從已移除的欄位**移除欄位**值會還原序列化為訊息的[未知欄位](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)。</span><span class="sxs-lookup"><span data-stu-id="02018-132">**Removing a field** - Values from a removed field are deserialized to a message's [unknown fields](https://developers.google.com/protocol-buffers/docs/proto3#unknowns).</span></span> <span data-ttu-id="02018-133">這不是 gRPC 的通訊協定重大變更，但如果用戶端升級至最新的合約，則需要更新用戶端。</span><span class="sxs-lookup"><span data-stu-id="02018-133">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="02018-134">很重要的是，在未來不會意外重複使用移除的欄位編號。</span><span class="sxs-lookup"><span data-stu-id="02018-134">It's important that a removed field number isn't accidentally reused in the future.</span></span> <span data-ttu-id="02018-135">為確保不會發生這種情況，請使用 Protobuf 的 [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) 關鍵字來指定訊息上已刪除的欄位編號和名稱。</span><span class="sxs-lookup"><span data-stu-id="02018-135">To ensure this doesn't happen, specify deleted field numbers and names on the message using Protobuf's [reserved](https://developers.google.com/protocol-buffers/docs/proto3#reserved) keyword.</span></span>
* <span data-ttu-id="02018-136">重新**命名訊息**訊息名稱通常不會在網路上傳送，因此這不是 gRPC 的通訊協定中斷變更。</span><span class="sxs-lookup"><span data-stu-id="02018-136">**Renaming a message** - Message names aren't typically sent on the network, so this isn't a gRPC protocol breaking change.</span></span> <span data-ttu-id="02018-137">如果用戶端升級至最新的合約，就必須更新用戶端。</span><span class="sxs-lookup"><span data-stu-id="02018-137">The client will need to be updated if it upgrades to the latest contract.</span></span> <span data-ttu-id="02018-138">當訊息名稱用來識別訊息類型時，網路 **上傳送訊息名稱的** 其中一種情況是使用 [任何](https://developers.google.com/protocol-buffers/docs/proto3#any) 欄位。</span><span class="sxs-lookup"><span data-stu-id="02018-138">One situation where message names **are** sent on the network is with [Any](https://developers.google.com/protocol-buffers/docs/proto3#any) fields, when the message name is used to identify the message type.</span></span>
* <span data-ttu-id="02018-139">變更**csharp_namespace**變更 `csharp_namespace` 將會變更所產生 .net 類型的命名空間。</span><span class="sxs-lookup"><span data-stu-id="02018-139">**Changing csharp_namespace** - Changing `csharp_namespace` will change the namespace of generated .NET types.</span></span> <span data-ttu-id="02018-140">這不是 gRPC 的通訊協定重大變更，但如果用戶端升級至最新的合約，則需要更新用戶端。</span><span class="sxs-lookup"><span data-stu-id="02018-140">This isn't a gRPC protocol breaking change, but the client needs to be updated if it upgrades to the latest contract.</span></span>

### <a name="protocol-breaking-changes"></a><span data-ttu-id="02018-141">通訊協定的重大變更</span><span class="sxs-lookup"><span data-stu-id="02018-141">Protocol breaking changes</span></span>

<span data-ttu-id="02018-142">下列專案是通訊協定和二進位中斷性變更：</span><span class="sxs-lookup"><span data-stu-id="02018-142">The following items are protocol and binary breaking changes:</span></span>

* <span data-ttu-id="02018-143">重新**命名欄位**-使用 Protobuf 內容時，功能變數名稱只會在產生的程式碼中使用。</span><span class="sxs-lookup"><span data-stu-id="02018-143">**Renaming a field** - With Protobuf content, the field names are only used in generated code.</span></span> <span data-ttu-id="02018-144">欄位號用來識別網路上的欄位。</span><span class="sxs-lookup"><span data-stu-id="02018-144">The field number is used to identify fields on the network.</span></span> <span data-ttu-id="02018-145">重新命名欄位不是 Protobuf 的通訊協定重大變更。</span><span class="sxs-lookup"><span data-stu-id="02018-145">Renaming a field isn't a protocol breaking change for Protobuf.</span></span> <span data-ttu-id="02018-146">但是，如果伺服器使用 JSON 內容，則重新命名欄位是一項重大變更。</span><span class="sxs-lookup"><span data-stu-id="02018-146">However, if a server is using JSON content then renaming a field is a breaking change.</span></span>
* <span data-ttu-id="02018-147">**變更欄位資料類型** -將欄位的資料類型變更為 [不相容的類型](https://developers.google.com/protocol-buffers/docs/proto3#updating) 時，將會在還原序列化訊息時發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="02018-147">**Changing a field data type** - Changing a field's data type to an [incompatible type](https://developers.google.com/protocol-buffers/docs/proto3#updating) will cause errors when deserializing the message.</span></span> <span data-ttu-id="02018-148">即使新的資料類型相容，如果用戶端升級至最新的合約，則可能需要更新用戶端，以支援新的類型。</span><span class="sxs-lookup"><span data-stu-id="02018-148">Even if the new data type is compatible, it's likely the client needs to be updated to support the new type if it upgrades to the latest contract.</span></span>
* <span data-ttu-id="02018-149">**變更欄位編號** -使用 Protobuf 承載時，會使用欄位編號來識別網路上的欄位。</span><span class="sxs-lookup"><span data-stu-id="02018-149">**Changing a field number** - With Protobuf payloads, the field number is used to identify fields on the network.</span></span>
* <span data-ttu-id="02018-150">重新**命名封裝、服務或方法**gRPC 時，會使用封裝名稱、服務名稱和方法名稱來建立 URL。</span><span class="sxs-lookup"><span data-stu-id="02018-150">**Renaming a package, service or method** - gRPC uses the package name, service name, and method name to build the URL.</span></span> <span data-ttu-id="02018-151">用戶端會從伺服器取得未 *實現* 的狀態。</span><span class="sxs-lookup"><span data-stu-id="02018-151">The client gets an *UNIMPLEMENTED* status from the server.</span></span>
* <span data-ttu-id="02018-152">**移除服務或方法** -當呼叫已移除的方法時，用戶端會從伺服器 *取得未完成的狀態* 。</span><span class="sxs-lookup"><span data-stu-id="02018-152">**Removing a service or method** - The client gets an *UNIMPLEMENTED* status from the server when calling the removed method.</span></span>

### <a name="behavior-breaking-changes"></a><span data-ttu-id="02018-153">行為重大變更</span><span class="sxs-lookup"><span data-stu-id="02018-153">Behavior breaking changes</span></span>

<span data-ttu-id="02018-154">進行非中斷的變更時，您也必須考慮較舊的用戶端是否可以繼續使用新的服務行為。</span><span class="sxs-lookup"><span data-stu-id="02018-154">When making non-breaking changes, you must also consider whether older clients can continue working with the new service behavior.</span></span> <span data-ttu-id="02018-155">例如，將新欄位加入至要求訊息：</span><span class="sxs-lookup"><span data-stu-id="02018-155">For example, adding a new field to a request message:</span></span>

* <span data-ttu-id="02018-156">不是通訊協定的重大變更。</span><span class="sxs-lookup"><span data-stu-id="02018-156">Isn't a protocol breaking change.</span></span>
* <span data-ttu-id="02018-157">如果未設定新欄位，則傳回伺服器上的錯誤狀態，使其成為舊用戶端的重大變更。</span><span class="sxs-lookup"><span data-stu-id="02018-157">Returning an error status on the server if the new field isn't set makes it a breaking change for old clients.</span></span>

<span data-ttu-id="02018-158">行為相容性取決於您的應用程式專屬程式碼。</span><span class="sxs-lookup"><span data-stu-id="02018-158">Behavior compatibility is determined by your app-specific code.</span></span>

## <a name="version-number-services"></a><span data-ttu-id="02018-159">版本號碼服務</span><span class="sxs-lookup"><span data-stu-id="02018-159">Version number services</span></span>

<span data-ttu-id="02018-160">服務應該致力於維持與舊版用戶端的回溯相容性。</span><span class="sxs-lookup"><span data-stu-id="02018-160">Services should strive to remain backwards compatible with old clients.</span></span> <span data-ttu-id="02018-161">最後，您的應用程式變更可能需要中斷變更。</span><span class="sxs-lookup"><span data-stu-id="02018-161">Eventually changes to your app may require breaking changes.</span></span> <span data-ttu-id="02018-162">中斷舊用戶端，並強制這些用戶端與您的服務一起更新，並不是很好的使用者體驗。</span><span class="sxs-lookup"><span data-stu-id="02018-162">Breaking old clients and forcing them to be updated along with your service isn't a good user experience.</span></span> <span data-ttu-id="02018-163">在進行重大變更時維持回溯相容性的方法是發佈多個版本的服務。</span><span class="sxs-lookup"><span data-stu-id="02018-163">A way to maintain backwards compatibility while making breaking changes is to publish multiple versions of a service.</span></span>

<span data-ttu-id="02018-164">gRPC 支援選擇性的 [封裝](https://developers.google.com/protocol-buffers/docs/proto3#packages) 規範，其功能與 .net 命名空間很類似。</span><span class="sxs-lookup"><span data-stu-id="02018-164">gRPC supports an optional [package](https://developers.google.com/protocol-buffers/docs/proto3#packages) specifier, which functions much like a .NET namespace.</span></span> <span data-ttu-id="02018-165">事實上， `package` 如果 `option csharp_namespace` 未在 *proto* 檔案中設定，則會使用做為所產生 .net 類型的 .net 命名空間。</span><span class="sxs-lookup"><span data-stu-id="02018-165">In fact, the `package` will be used as the .NET namespace for generated .NET types if `option csharp_namespace` is not set in the *.proto* file.</span></span> <span data-ttu-id="02018-166">封裝可以用來指定您的服務和其訊息的版本號碼：</span><span class="sxs-lookup"><span data-stu-id="02018-166">The package can be used to specify a version number for your service and its messages:</span></span>

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

<span data-ttu-id="02018-167">封裝名稱會與服務名稱結合以識別服務位址。</span><span class="sxs-lookup"><span data-stu-id="02018-167">The package name is combined with the service name to identify a service address.</span></span> <span data-ttu-id="02018-168">服務位址可讓多個版本的服務並行託管：</span><span class="sxs-lookup"><span data-stu-id="02018-168">A service address allows multiple versions of a service to be hosted side-by-side:</span></span>

* `greet.v1.Greeter`
* `greet.v2.Greeter`

<span data-ttu-id="02018-169">已建立版本之服務的實 *Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="02018-169">Implementations of the versioned service are registered in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

<span data-ttu-id="02018-170">在套件名稱中包含版本號碼，讓您有機會發行服務的 *v2* 版本與中斷的變更，同時繼續支援呼叫 *v1* 版本的舊版用戶端。</span><span class="sxs-lookup"><span data-stu-id="02018-170">Including a version number in the package name gives you the opportunity to publish a *v2* version of your service with breaking changes, while continuing to support older clients who call the *v1* version.</span></span> <span data-ttu-id="02018-171">用戶端更新為使用 *v2* 服務之後，您可以選擇移除舊版本。</span><span class="sxs-lookup"><span data-stu-id="02018-171">Once clients have updated to use the *v2* service, you can choose to remove the old version.</span></span> <span data-ttu-id="02018-172">規劃發行多個版本的服務時：</span><span class="sxs-lookup"><span data-stu-id="02018-172">When planning to publish multiple versions of a service:</span></span>

* <span data-ttu-id="02018-173">如果合理，請避免中斷的變更。</span><span class="sxs-lookup"><span data-stu-id="02018-173">Avoid breaking changes if reasonable.</span></span>
* <span data-ttu-id="02018-174">除非進行重大變更，否則請勿更新版本號碼。</span><span class="sxs-lookup"><span data-stu-id="02018-174">Don't update the version number unless making breaking changes.</span></span>
* <span data-ttu-id="02018-175">當您進行重大變更時，請更新版本號碼。</span><span class="sxs-lookup"><span data-stu-id="02018-175">Do update the version number when you make breaking changes.</span></span>

<span data-ttu-id="02018-176">發行多個版本的服務會複製它。</span><span class="sxs-lookup"><span data-stu-id="02018-176">Publishing multiple versions of a service duplicates it.</span></span> <span data-ttu-id="02018-177">若要減少重複的情況，請考慮將商務邏輯從服務的執行移至可由舊的和新的執行重複使用的集中位置：</span><span class="sxs-lookup"><span data-stu-id="02018-177">To reduce duplication, consider moving business logic from the service implementations to a centralized location that can be reused by the old and new implementations:</span></span>

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

<span data-ttu-id="02018-178">使用不同封裝名稱產生的服務和訊息是 **不同的 .net 類型**。</span><span class="sxs-lookup"><span data-stu-id="02018-178">Services and messages generated with different package names are **different .NET types**.</span></span> <span data-ttu-id="02018-179">將商務邏輯移至中央位置需要將訊息對應至一般類型。</span><span class="sxs-lookup"><span data-stu-id="02018-179">Moving business logic to a centralized location requires mapping messages to common types.</span></span>
