---
title: 使用受授權保護的使用者資料建立 ASP.NET Core 應用程式
author: rick-anderson
description: 瞭解如何建立 ASP.NET Core web 應用程式，並以授權保護使用者資料。 包含 HTTPS、驗證、安全性 ASP.NET Core Identity 。
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: 662456af59c453df66ca48139a6de40d0e2cbf0d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102589188"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a>建立 ASP.NET Core web 應用程式，並以授權保護使用者資料

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 與 [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="= aspnetcore-2.0"

請參閱 [此 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

本教學課程說明如何建立 ASP.NET Core web 應用程式，並以授權保護使用者資料。 它會顯示已驗證 (註冊) 使用者建立的連絡人清單。 有三個安全性群組：

* **註冊的使用者** 可以查看所有核准的資料，並可編輯/刪除他們自己的資料。
* **管理員** 可以核准或拒絕連絡人資料。 只有核准的連絡人可以看到使用者。
* 系統 **管理員** 可以核准/拒絕和編輯/刪除任何資料。

本檔中的影像與最新的範本不完全相符。

在下圖中，使用者 Rick (`rick@example.com`) 已登入。 Rick 只能查看核准的連絡人，並 **編輯** / **刪除** / 為連絡人 **建立新** 的連結。 只有 Rick 所建立的最後一筆記錄會顯示 [ **編輯** ] 和 [ **刪除** ] 連結。 在管理員或系統管理員將狀態變更為 [已核准] 之前，其他使用者將不會看到最後一筆記錄。

![顯示 Rick 已登入的螢幕擷取畫面](secure-data/_static/rick.png)

在下圖中， `manager@contoso.com` 已登入並在管理員的角色中：

![顯示 manager@contoso.com 已登入的螢幕擷取畫面](secure-data/_static/manager1.png)

下圖顯示連絡人的經理詳細資料檢視：

![連絡人的經理觀點](secure-data/_static/manager.png)

[ **核准** ] 和 [ **拒絕** ] 按鈕只會顯示給管理員和系統管理員。

在下圖中， `admin@contoso.com` 已登入並以系統管理員的角色：

![顯示 admin@contoso.com 已登入的螢幕擷取畫面](secure-data/_static/admin.png)

系統管理員具有擁有權限。 她可以讀取/編輯/刪除任何連絡人，以及變更連絡人的狀態。

應用程式 [是由下列](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) `Contact` 模型建立：

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

此範例包含下列授權處理常式：

* `ContactIsOwnerAuthorizationHandler`：確保使用者只能編輯其資料。
* `ContactManagerAuthorizationHandler`：可讓管理員核准或拒絕連絡人。
* `ContactAdministratorsAuthorizationHandler`：可讓系統管理員核准或拒絕連絡人，以及編輯/刪除連絡人。

## <a name="prerequisites"></a>必要條件

本教學課程是 advanced。 您應該熟悉：

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [驗證](xref:security/authentication/identity)
* [帳戶確認和密碼復原](xref:security/authentication/accconfirm)
* [授權](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>入門和完成的應用程式

[下載](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples)的應用程式。 [測試](#test-the-completed-app) 完成的應用程式，讓您熟悉它的安全性功能。

### <a name="the-starter-app"></a>入門應用程式

[下載](xref:index#how-to-download-a-sample)[入門](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/)應用程式。

執行應用程式，並按一下 [ **ContactManager** ] 連結，然後確認您可以建立、編輯和刪除連絡人。 若要建立入門應用程式，請參閱 [建立入門應用程式](#create-the-starter-app)。

## <a name="secure-user-data"></a>保護使用者資料

下列各節具有建立安全使用者資料應用程式的所有主要步驟。 您可能會發現參考已完成的專案是有説明的。

### <a name="tie-the-contact-data-to-the-user"></a>將連絡人資料與使用者系結

使用 ASP.NET [Identity](xref:security/authentication/identity) 使用者識別碼，以確保使用者可以編輯其資料，而不是其他使用者資料。 將 `OwnerID` 和加入 `ContactStatus` 至 `Contact` 模型：

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` 這是 `AspNetUser` 資料庫中資料表的使用者識別碼 [Identity](xref:security/authentication/identity) 。 `Status`欄位會決定一般使用者是否可以看到連絡人。

建立新的遷移，並更新資料庫：

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>將角色服務新增至 Identity

附加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以新增角色服務：

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a>需要經過驗證的使用者

設定回溯驗證原則以要求使用者進行驗證：

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

上述反白顯示的程式碼會設定回溯 [驗證原則](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)。 除了 Razor 具有驗證屬性的頁面、控制器或動作方法之外，fallback 驗證原則需要驗證所有使用者。 例如，使用 Razor 或的頁面、控制器或動作方法，會 `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` 使用套用的驗證屬性，而不是回溯驗證原則。

<xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 加入 <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> 目前的實例，這會強制驗證目前的使用者。

回退驗證原則：

* 會套用至未明確指定驗證原則的所有要求。 針對端點路由所提供的要求，這會包含未指定授權屬性的任何端點。 針對其他中介軟體在授權中介軟體之後所提供的要求（例如 [靜態](xref:fundamentals/static-files)檔案），這會將原則套用至所有要求。

將回溯驗證原則設定為要求使用者進行驗證，可保護新新增 Razor 的頁面和控制器。 預設需要驗證比依賴新控制器和 Razor 頁面來包含屬性更安全 `[Authorize]` 。

<xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>類別也包含 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> 。 `DefaultPolicy`是 `[Authorize]` 未指定任何原則時，與屬性搭配使用的原則。 `[Authorize]` 不包含命名原則，與不同 `[Authorize(PolicyName="MyPolicy")]` 。

如需原則的詳細資訊，請參閱 <xref:security/authorization/policies> 。

MVC 控制器和 Razor 頁面需要驗證所有使用者的另一種方法是新增授權篩選準則：

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

上述程式碼會使用授權篩選準則，設定 fallback 原則會使用端點路由。 設定回復原則是要求所有使用者進行驗證的慣用方式。

將 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 新增至 `Index` 和 `Privacy` 頁面，讓匿名使用者在註冊之前可以取得網站的相關資訊：

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>設定測試帳戶

`SeedData`類別會建立兩個帳戶：系統管理員和管理員。 使用 [Secret Manager 工具](xref:security/app-secrets) 來設定這些帳戶的密碼。 將專案目錄中的密碼設定 (包含 *Program.cs*) 的目錄：

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

如果未指定強式密碼，則會在呼叫時擲回例外狀況 `SeedData.Initialize` 。

更新 `Main` 以使用測試密碼：

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>建立測試帳戶並更新連絡人

更新 `Initialize` 類別中的方法 `SeedData` ，以建立測試帳戶：

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

將系統管理員使用者識別碼和新增 `ContactStatus` 至連絡人。 讓其中一個連絡人成為「已提交」，另一個「已拒絕」。 將使用者識別碼和狀態新增至所有連絡人。 只會顯示一個連絡人：

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>建立擁有者、管理員和系統管理員授權處理常式

`ContactIsOwnerAuthorizationHandler`在 *授權* 資料夾中建立類別。 `ContactIsOwnerAuthorizationHandler`會確認對資源採取行動的使用者擁有資源。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`呼叫[內容。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果目前已驗證的使用者為連絡人擁有者，則會成功。 授權處理常式通常：

* `context.Succeed`符合需求時傳回。
* `Task.CompletedTask`不符合需求時傳回。 `Task.CompletedTask` 不是成功或失敗， &mdash; 允許其他授權處理常式執行。

如果您需要明確失敗，請傳回 [內容。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。

應用程式可讓連絡人擁有者編輯/刪除/建立自己的資料。 `ContactIsOwnerAuthorizationHandler` 不需要檢查需求參數中所傳遞的作業。

### <a name="create-a-manager-authorization-handler"></a>建立管理員授權處理常式

`ContactManagerAuthorizationHandler`在 *授權* 資料夾中建立類別。 `ContactManagerAuthorizationHandler`會驗證資源上的使用者是否為管理員。 只有管理員可以核准或拒絕 (新增或變更) 的內容變更。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>建立系統管理員授權處理常式

`ContactAdministratorsAuthorizationHandler`在 *授權* 資料夾中建立類別。 `ContactAdministratorsAuthorizationHandler`會驗證資源的使用者是否為系統管理員。 系統管理員可以進行所有作業。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>註冊授權處理常式

使用 Entity Framework Core 的服務必須使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)註冊相依性[插入](xref:fundamentals/dependency-injection)。 `ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 以 Entity Framework core 為基礎的 ASP.NET Core。 使用服務集合註冊處理常式，以便透過相依性插入來使用它們 `ContactsController` 。 [](xref:fundamentals/dependency-injection) 將下列程式碼加入至結尾 `ConfigureServices` ：

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 會新增為 singleton。 它們是 singleton 的，因為它們不會使用 EF，而且所需的所有資訊都在 `Context` 方法的參數中 `HandleRequirementAsync` 。

## <a name="support-authorization"></a>支援授權

在本節中，您會更新 Razor 頁面並新增作業需求類別。

### <a name="review-the-contact-operations-requirements-class"></a>複習 contact 作業需求課程

請參閱 `ContactOperations` 類別。 此類別包含應用程式支援的需求：

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>建立連絡人頁面的基類 Razor

建立包含 [連絡人] 頁面中所使用之服務的基類 Razor 。 基底類別會將初始化程式碼放在一個位置：

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

上述程式碼：

* 新增 `IAuthorizationService` 服務以存取授權處理常式。
* 加入 Identity `UserManager` 服務。
* 加入 `ApplicationDbContext`。

### <a name="update-the-createmodel"></a>更新 CreateModel

更新建立頁面模型的函式以使用 `DI_BasePageModel` 基類：

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

將 `CreateModel.OnPostAsync` 方法更新為：

* 將使用者識別碼加入至 `Contact` 模型。
* 呼叫授權處理常式來確認使用者有權建立連絡人。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>更新 IndexModel

更新 `OnGetAsync` 方法，如此一來，只有已核准的連絡人會顯示給一般使用者：

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>更新 EditModel

新增授權處理常式來確認使用者擁有該連絡人。 因為正在驗證資源授權，所以 `[Authorize]` 屬性不足。 評估屬性時，應用程式無法存取資源。 以資源為基礎的授權必須是必要的。 一旦應用程式可存取資源，就必須執行檢查，方法是將它載入頁面模型中，或在處理常式本身內載入。 您經常會藉由傳入資源金鑰來存取資源。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>更新 DeleteModel

更新 [刪除] 頁面模型，以使用授權處理常式來確認使用者擁有連絡人的 [刪除] 許可權。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>將授權服務插入至 views

目前，UI 會顯示使用者無法修改之連絡人的 [編輯] 和 [刪除] 連結。

將授權服務插入 *Pages/_ViewImports cshtml* 檔案，以便所有視圖都可使用：

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

上述標記會新增數個 `using` 語句。

更新 *Pages/Contacts/Index* 中的 [**編輯**] 和 [**刪除**] 連結，以便只針對具有適當許可權的使用者轉譯這些連結：

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 隱藏沒有變更資料許可權之使用者的連結，並不會保護應用程式的安全。 隱藏連結可只顯示有效的連結，讓應用程式更容易使用。 使用者可以攻擊產生的 Url，以叫用其未擁有之資料的編輯和刪除作業。 Razor頁面或控制器必須強制執行存取檢查以保護資料。

### <a name="update-details"></a>更新詳細資料

更新 [詳細資料] 視圖，讓管理員可以核准或拒絕連絡人：

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

更新詳細資料頁面模型：

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>新增或移除角色的使用者

如需相關資訊，請參閱 [此問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：

* 正在移除使用者的許可權。 例如，將聊天應用程式中的使用者靜音。
* 將許可權新增至使用者。

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>挑戰與禁止之間的差異

此應用程式會將預設原則設定為 [需要經過驗證的使用者](#rau)。 下列程式碼允許匿名使用者。 允許匿名使用者顯示挑戰與禁止之間的差異。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

在上述程式碼中：

* 當使用者 **未通過驗證** 時， `ChallengeResult` 就會傳回。 當 `ChallengeResult` 傳回時，會將使用者重新導向至登入頁面。
* 當使用者經過驗證但未獲授權時， `ForbidResult` 會傳回。 當 `ForbidResult` 傳回時，會將使用者重新導向至 [拒絕存取] 頁面。

## <a name="test-the-completed-app"></a>測試已完成的應用程式

如果您尚未設定植入使用者帳戶的密碼，請使用 [Secret Manager 工具](xref:security/app-secrets#secret-manager) 來設定密碼：

* 選擇強式密碼：使用八個或更多字元，以及至少一個大寫字元、數位和符號。 例如， `Passw0rd!` 符合強式密碼需求。
* 從專案的資料夾執行下列命令，其中 `<PW>` 是密碼：

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

如果應用程式有連絡人：

* 刪除資料表中的所有記錄 `Contact` 。
* 重新開機應用程式以植入資料庫。

測試完成的應用程式的簡單方法，就是啟動三個不同的瀏覽器 (或 incognito/InPrivate 會話) 。 在一個瀏覽器中，註冊新的使用者 (例如 `test@example.com`) 。 使用不同的使用者登入每個瀏覽器。 確認下列作業：

* 註冊的使用者可以查看所有核准的連絡人資料。
* 註冊的使用者可以編輯/刪除自己的資料。
* 管理員可以核准/拒絕連絡人資料。 此 `Details` 視圖會顯示 [ **核准** ] 和 [ **拒絕** ] 按鈕。
* 系統管理員可以核准/拒絕和編輯/刪除所有資料。

| User                | 由應用程式植入 | 選項                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | 編輯/刪除自己的資料。                |
| manager@contoso.com | Yes               | 核准/拒絕和編輯/刪除自己的資料。 |
| admin@contoso.com   | Yes               | 核准/拒絕和編輯/刪除所有資料。 |

在系統管理員的瀏覽器中建立連絡人。 從系統管理員連絡人複製 [刪除] 和 [編輯] 的 URL。 將這些連結貼到測試使用者的瀏覽器中，以確認測試使用者無法執行這些作業。

## <a name="create-the-starter-app"></a>建立入門應用程式

* 建立 Razor 名為 "ContactManager" 的頁面應用程式
  * 使用 **個別的使用者帳戶** 建立應用程式。
  * 將它命名為 "ContactManager"，讓命名空間符合範例中使用的命名空間。
  * `-uld` 指定 LocalDB 而不是 SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* 新增 *模型/連絡人 .cs*：

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Scaffold `Contact` 模型。
* 建立初始遷移並更新資料庫：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

如果您在命令中遇到錯誤 `dotnet aspnet-codegenerator razorpage` ，請參閱 [此 GitHub 問題](https://github.com/aspnet/Scaffolding/issues/984)。

* 更新 *Pages/Shared/_Layout cshtml* 檔案中的 **ContactManager** 錨點：

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* 建立、編輯和刪除連絡人以測試應用程式

### <a name="seed-the-database"></a>植入資料庫

將 [>seeddata.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 類別新增至 [ *資料* ] 資料夾：

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

呼叫 `SeedData.Initialize` 來源 `Main` ：

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

測試應用程式是否植入資料庫。 如果 contact DB 中有任何資料列，種子方法就不會執行。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

本教學課程說明如何建立 ASP.NET Core web 應用程式，並以授權保護使用者資料。 它會顯示已驗證 (註冊) 使用者建立的連絡人清單。 有三個安全性群組：

* **註冊的使用者** 可以查看所有核准的資料，並可編輯/刪除他們自己的資料。
* **管理員** 可以核准或拒絕連絡人資料。 只有核准的連絡人可以看到使用者。
* 系統 **管理員** 可以核准/拒絕和編輯/刪除任何資料。

在下圖中，使用者 Rick (`rick@example.com`) 已登入。 Rick 只能查看核准的連絡人，並 **編輯** / **刪除** / 為連絡人 **建立新** 的連結。 只有 Rick 所建立的最後一筆記錄會顯示 [ **編輯** ] 和 [ **刪除** ] 連結。 在管理員或系統管理員將狀態變更為 [已核准] 之前，其他使用者將不會看到最後一筆記錄。

![顯示 Rick 已登入的螢幕擷取畫面](secure-data/_static/rick.png)

在下圖中， `manager@contoso.com` 已登入並在管理員的角色中：

![顯示 manager@contoso.com 已登入的螢幕擷取畫面](secure-data/_static/manager1.png)

下圖顯示連絡人的經理詳細資料檢視：

![連絡人的經理觀點](secure-data/_static/manager.png)

[ **核准** ] 和 [ **拒絕** ] 按鈕只會顯示給管理員和系統管理員。

在下圖中， `admin@contoso.com` 已登入並以系統管理員的角色：

![顯示 admin@contoso.com 已登入的螢幕擷取畫面](secure-data/_static/admin.png)

系統管理員具有擁有權限。 她可以讀取/編輯/刪除任何連絡人，以及變更連絡人的狀態。

應用程式 [是由下列](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) `Contact` 模型建立：

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

此範例包含下列授權處理常式：

* `ContactIsOwnerAuthorizationHandler`：確保使用者只能編輯其資料。
* `ContactManagerAuthorizationHandler`：可讓管理員核准或拒絕連絡人。
* `ContactAdministratorsAuthorizationHandler`：可讓系統管理員核准或拒絕連絡人，以及編輯/刪除連絡人。

## <a name="prerequisites"></a>必要條件

本教學課程是 advanced。 您應該熟悉：

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [驗證](xref:security/authentication/identity)
* [帳戶確認和密碼復原](xref:security/authentication/accconfirm)
* [授權](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>入門和完成的應用程式

[下載](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples)的應用程式。 [測試](#test-the-completed-app) 完成的應用程式，讓您熟悉它的安全性功能。

### <a name="the-starter-app"></a>入門應用程式

[下載](xref:index#how-to-download-a-sample)[入門](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/)應用程式。

執行應用程式，並按一下 [ **ContactManager** ] 連結，然後確認您可以建立、編輯和刪除連絡人。

## <a name="secure-user-data"></a>保護使用者資料

下列各節具有建立安全使用者資料應用程式的所有主要步驟。 您可能會發現參考已完成的專案是有説明的。

### <a name="tie-the-contact-data-to-the-user"></a>將連絡人資料與使用者系結

使用 ASP.NET [Identity](xref:security/authentication/identity) 使用者識別碼，以確保使用者可以編輯其資料，而不是其他使用者資料。 將 `OwnerID` 和加入 `ContactStatus` 至 `Contact` 模型：

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` 這是 `AspNetUser` 資料庫中資料表的使用者識別碼 [Identity](xref:security/authentication/identity) 。 `Status`欄位會決定一般使用者是否可以看到連絡人。

建立新的遷移，並更新資料庫：

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>將角色服務新增至 Identity

附加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以新增角色服務：

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a>需要經過驗證的使用者

設定預設的驗證原則，要求使用者進行驗證：

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 您可以選擇不 Razor 使用屬性來進行頁面、控制器或動作方法層級的驗證 `[AllowAnonymous]` 。 將預設的驗證原則設定為要求使用者進行驗證，可保護新新增 Razor 的頁面和控制器。 預設需要驗證比依賴新控制器和 Razor 頁面來包含屬性更安全 `[Authorize]` 。

將 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 加入至 Index、About 和 Contact 頁面，讓匿名使用者在註冊之前，可以取得網站的相關資訊。

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>設定測試帳戶

`SeedData`類別會建立兩個帳戶：系統管理員和管理員。 使用 [Secret Manager 工具](xref:security/app-secrets) 來設定這些帳戶的密碼。 將專案目錄中的密碼設定 (包含 *Program.cs*) 的目錄：

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

如果未指定強式密碼，則會在呼叫時擲回例外狀況 `SeedData.Initialize` 。

更新 `Main` 以使用測試密碼：

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>建立測試帳戶並更新連絡人

更新 `Initialize` 類別中的方法 `SeedData` ，以建立測試帳戶：

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

將系統管理員使用者識別碼和新增 `ContactStatus` 至連絡人。 讓其中一個連絡人成為「已提交」，另一個「已拒絕」。 將使用者識別碼和狀態新增至所有連絡人。 只會顯示一個連絡人：

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>建立擁有者、管理員和系統管理員授權處理常式

建立 *授權* 資料夾，並 `ContactIsOwnerAuthorizationHandler` 在其中建立類別。 `ContactIsOwnerAuthorizationHandler`會確認對資源採取行動的使用者擁有資源。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`呼叫[內容。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果目前已驗證的使用者為連絡人擁有者，則會成功。 授權處理常式通常：

* `context.Succeed`符合需求時傳回。
* `Task.CompletedTask`不符合需求時傳回。 `Task.CompletedTask` 不是成功或失敗， &mdash; 允許其他授權處理常式執行。

如果您需要明確失敗，請傳回 [內容。失敗](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。

應用程式可讓連絡人擁有者編輯/刪除/建立自己的資料。 `ContactIsOwnerAuthorizationHandler` 不需要檢查需求參數中所傳遞的作業。

### <a name="create-a-manager-authorization-handler"></a>建立管理員授權處理常式

`ContactManagerAuthorizationHandler`在 *授權* 資料夾中建立類別。 `ContactManagerAuthorizationHandler`會驗證資源上的使用者是否為管理員。 只有管理員可以核准或拒絕 (新增或變更) 的內容變更。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>建立系統管理員授權處理常式

`ContactAdministratorsAuthorizationHandler`在 *授權* 資料夾中建立類別。 `ContactAdministratorsAuthorizationHandler`會驗證資源的使用者是否為系統管理員。 系統管理員可以進行所有作業。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>註冊授權處理常式

使用 Entity Framework Core 的服務必須使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)註冊相依性[插入](xref:fundamentals/dependency-injection)。 `ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 以 Entity Framework core 為基礎的 ASP.NET Core。 使用服務集合註冊處理常式，以便透過相依性插入來使用它們 `ContactsController` 。 [](xref:fundamentals/dependency-injection) 將下列程式碼加入至結尾 `ConfigureServices` ：

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 會新增為 singleton。 它們是 singleton 的，因為它們不會使用 EF，而且所需的所有資訊都在 `Context` 方法的參數中 `HandleRequirementAsync` 。

## <a name="support-authorization"></a>支援授權

在本節中，您會更新 Razor 頁面並新增作業需求類別。

### <a name="review-the-contact-operations-requirements-class"></a>複習 contact 作業需求課程

請參閱 `ContactOperations` 類別。 此類別包含應用程式支援的需求：

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>建立連絡人頁面的基類 Razor

建立包含 [連絡人] 頁面中所使用之服務的基類 Razor 。 基底類別會將初始化程式碼放在一個位置：

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

上述程式碼：

* 新增 `IAuthorizationService` 服務以存取授權處理常式。
* 加入 Identity `UserManager` 服務。
* 加入 `ApplicationDbContext`。

### <a name="update-the-createmodel"></a>更新 CreateModel

更新建立頁面模型的函式以使用 `DI_BasePageModel` 基類：

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

將 `CreateModel.OnPostAsync` 方法更新為：

* 將使用者識別碼加入至 `Contact` 模型。
* 呼叫授權處理常式來確認使用者有權建立連絡人。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>更新 IndexModel

更新 `OnGetAsync` 方法，如此一來，只有已核准的連絡人會顯示給一般使用者：

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>更新 EditModel

新增授權處理常式來確認使用者擁有該連絡人。 因為正在驗證資源授權，所以 `[Authorize]` 屬性不足。 評估屬性時，應用程式無法存取資源。 以資源為基礎的授權必須是必要的。 一旦應用程式可存取資源，就必須執行檢查，方法是將它載入頁面模型中，或在處理常式本身內載入。 您經常會藉由傳入資源金鑰來存取資源。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>更新 DeleteModel

更新 [刪除] 頁面模型，以使用授權處理常式來確認使用者擁有連絡人的 [刪除] 許可權。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>將授權服務插入至 views

目前，UI 會顯示使用者無法修改之連絡人的 [編輯] 和 [刪除] 連結。

將授權服務插入 *views/_ViewImports cshtml* 檔案，以便所有視圖都可使用：

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

上述標記會新增數個 `using` 語句。

更新 *Pages/Contacts/Index* 中的 [**編輯**] 和 [**刪除**] 連結，以便只針對具有適當許可權的使用者轉譯這些連結：

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 隱藏沒有變更資料許可權之使用者的連結，並不會保護應用程式的安全。 隱藏連結可只顯示有效的連結，讓應用程式更容易使用。 使用者可以攻擊產生的 Url，以叫用其未擁有之資料的編輯和刪除作業。 Razor頁面或控制器必須強制執行存取檢查以保護資料。

### <a name="update-details"></a>更新詳細資料

更新 [詳細資料] 視圖，讓管理員可以核准或拒絕連絡人：

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

更新詳細資料頁面模型：

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>新增或移除角色的使用者

如需相關資訊，請參閱 [此問題](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：

* 正在移除使用者的許可權。 例如，將聊天應用程式中的使用者靜音。
* 將許可權新增至使用者。

## <a name="test-the-completed-app"></a>測試已完成的應用程式

如果您尚未設定植入使用者帳戶的密碼，請使用 [Secret Manager 工具](xref:security/app-secrets#secret-manager) 來設定密碼：

* 選擇強式密碼：使用八個或更多字元，以及至少一個大寫字元、數位和符號。 例如， `Passw0rd!` 符合強式密碼需求。
* 從專案的資料夾執行下列命令，其中 `<PW>` 是密碼：

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* 卸載並更新資料庫

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* 重新開機應用程式以植入資料庫。

測試完成的應用程式的簡單方法，就是啟動三個不同的瀏覽器 (或 incognito/InPrivate 會話) 。 在一個瀏覽器中，註冊新的使用者 (例如 `test@example.com`) 。 使用不同的使用者登入每個瀏覽器。 確認下列作業：

* 註冊的使用者可以查看所有核准的連絡人資料。
* 註冊的使用者可以編輯/刪除自己的資料。
* 管理員可以核准/拒絕連絡人資料。 此 `Details` 視圖會顯示 [ **核准** ] 和 [ **拒絕** ] 按鈕。
* 系統管理員可以核准/拒絕和編輯/刪除所有資料。

| User                | 由應用程式植入 | 選項                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | No                | 編輯/刪除自己的資料。                |
| manager@contoso.com | Yes               | 核准/拒絕和編輯/刪除自己的資料。 |
| admin@contoso.com   | Yes               | 核准/拒絕和編輯/刪除所有資料。 |

在系統管理員的瀏覽器中建立連絡人。 從系統管理員連絡人複製 [刪除] 和 [編輯] 的 URL。 將這些連結貼到測試使用者的瀏覽器中，以確認測試使用者無法執行這些作業。

## <a name="create-the-starter-app"></a>建立入門應用程式

* 建立 Razor 名為 "ContactManager" 的頁面應用程式
  * 使用 **個別的使用者帳戶** 建立應用程式。
  * 將它命名為 "ContactManager"，讓命名空間符合範例中使用的命名空間。
  * `-uld` 指定 LocalDB 而不是 SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* 新增 *模型/連絡人 .cs*：

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Scaffold `Contact` 模型。
* 建立初始遷移並更新資料庫：

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* 更新 *Pages/_Layout cshtml* 檔案中的 **ContactManager** 錨點：

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* 建立、編輯和刪除連絡人以測試應用程式

### <a name="seed-the-database"></a>植入資料庫

將 [>seeddata.cs](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 類別加入至 [ *資料* ] 資料夾。

呼叫 `SeedData.Initialize` 來源 `Main` ：

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

測試應用程式是否植入資料庫。 如果 contact DB 中有任何資料列，種子方法就不會執行。

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>其他資源

* [在 Azure App Service 中建置 .NET Core 和 SQL Database Web 應用程式](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET 核心授權實驗室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。 本教學課程會詳細說明本教學課程中所引進的安全性功能。
* <xref:security/authorization/introduction>
* [自訂以原則為基礎的授權](xref:security/authorization/policies)
