---
title: ASP.NET Core 資料保護的非 DI 感知案例
author: rick-anderson
description: 瞭解如何支援不能或不想使用相依性插入所提供之服務的資料保護案例。
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/configuration/non-di-scenarios
ms.openlocfilehash: 03257596cafd9ec99f90b44d8fcb878b6747ba39
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052821"
---
# <a name="non-di-aware-scenarios-for-data-protection-in-aspnet-core"></a>ASP.NET Core 資料保護的非 DI 感知案例

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 的資料保護系統通常會 [新增至服務容器](xref:security/data-protection/consumer-apis/overview) ，並透過相依性插入 (DI) 來供相依元件取用。 不過，在某些情況下，這並不可行或不需要，尤其是將系統匯入到現有的應用程式時。

為了支援這些案例， [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) 封裝提供具象類型 [DataProtectionProvider](/dotnet/api/Microsoft.AspNetCore.DataProtection.DataProtectionProvider)，可提供簡單的方式來使用資料保護，而不需要依賴 DI。 `DataProtectionProvider`類型會執行[IDataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionprovider)。 您 `DataProtectionProvider` 只需要提供 [DirectoryInfo](/dotnet/api/system.io.directoryinfo) 實例來指出應儲存提供者之密碼編譯金鑰的位置，如下列程式碼範例所示：

[!code-csharp[](non-di-scenarios/_static/nodisample1.cs)]

依預設， `DataProtectionProvider` 實體類型不會在將原始金鑰內容保存到檔案系統之前加密。 這是為了支援開發人員指向網路共用的案例，而資料保護系統無法自動推算適當的靜止金鑰加密機制。

此外， `DataProtectionProvider` 實體類型預設不會 [隔離應用程式](xref:security/data-protection/configuration/overview#per-application-isolation) 。 使用相同金鑰目錄的所有應用程式可以共用承載，只要其 [用途參數](xref:security/data-protection/consumer-apis/purpose-strings) 相符即可。

[DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider)函式會接受選擇性的設定回呼，可用來調整系統的行為。 下列範例示範如何使用 [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname)的明確呼叫來還原隔離。 此範例也會示範如何將系統設定為使用 Windows DPAPI 自動加密保存的金鑰。 如果目錄指向 UNC 共用，您可能會想要將共用憑證散發到所有相關的電腦上，並將系統設定為使用以憑證為基礎的加密與 [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)的呼叫。

[!code-csharp[](non-di-scenarios/_static/nodisample2.cs)]

> [!TIP]
> 實體類型的實例需要 `DataProtectionProvider` 建立很多資源。 如果應用程式維護多個此類型的實例，而且它們都是使用相同的金鑰儲存體目錄，應用程式效能可能會降低。 如果您使用 `DataProtectionProvider` 類型，建議您建立此類型一次，並盡可能重複使用它。 `DataProtectionProvider`從它建立的型別和所有[>idataprotector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector)實例，都是多個呼叫端的安全線程。
