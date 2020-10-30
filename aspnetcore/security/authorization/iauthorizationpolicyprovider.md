---
title: ASP.NET Core 中的自訂授權原則提供者
author: mjrousos
description: 瞭解如何在 ASP.NET Core 應用程式中使用自訂 IAuthorizationPolicyProvider，以動態方式產生授權原則。
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
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
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 0f6f628cee0623c21a2a93aa11470005f8c78c58
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053133"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>在 ASP.NET Core 中使用 IAuthorizationPolicyProvider 的自訂授權原則提供者 

由 [Mike Rousos](https://github.com/mjrousos)

通常在使用 [原則型授權](xref:security/authorization/policies)時，會藉由在授權服務設定中呼叫來註冊原則 `AuthorizationOptions.AddPolicy` 。 在某些情況下，可能無法 (或希望) 以這種方式註冊所有授權原則。 在這些情況下，您可以[使用自 `IAuthorizationPolicyProvider` 定義](#ci)來控制如何提供授權原則。

自訂 [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) 可能有用的案例範例包括：

* 使用外部服務來提供原則評估。
* 使用適用于不同房間號碼或年齡的大量原則 (（例如) ），因此使用呼叫新增每個個別授權原則並不合理。 `AuthorizationOptions.AddPolicy`
* 根據外部資料源中的資訊在執行時間建立原則 (例如資料庫) ，或透過其他機制動態判斷授權需求。

從[AspNetCore GitHub 存放庫](https://github.com/dotnet/AspNetCore)[查看或下載範例程式碼](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)。 下載 dotnet/AspNetCore 存放庫壓縮檔案。 將檔案解壓縮。 流覽至 *src/Security/samples/CustomPolicyProvider* 專案資料夾。

## <a name="customize-policy-retrieval"></a>自訂原則抓取

ASP.NET Core apps 會使用介面的實作為 `IAuthorizationPolicyProvider` 來取得授權原則。 預設會註冊並使用 [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) 。 `DefaultAuthorizationPolicyProvider` 傳回 `AuthorizationOptions` 呼叫中所提供的原則 `IServiceCollection.AddAuthorization` 。

`IAuthorizationPolicyProvider`在應用程式的相依性[插入](xref:fundamentals/dependency-injection)容器中註冊不同的執行，以自訂此行為。 

`IAuthorizationPolicyProvider`介面包含三個 api：

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) 會傳回指定名稱的授權原則。
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) 會傳回預設授權原則 (用於 `[Authorize]` 未指定原則) 原則的屬性。 
* 當未指定) 的原則時， [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync)會傳回授權中介軟體所使用的原則 (的回溯授權原則。 

藉由執行這些 Api，您可以自訂如何提供授權原則。

## <a name="parameterized-authorize-attribute-example"></a>參數化授權屬性範例

其中一個實用的案例 `IAuthorizationPolicyProvider` ，就是啟用其需求相依于參數的自訂 `[Authorize]` 屬性。 例如，在以 [原則為基礎的授權](xref:security/authorization/policies) 檔中，以 age ( "AtLeast21" ) 原則做為範例使用。 如果應用程式中的不同控制器動作應可供 *不同* 年齡的使用者使用，則有許多不同的年齡型原則可能會很有用。 `AuthorizationOptions`您可以使用自訂動態產生原則，而不是註冊應用程式所需的所有不同以 age 為基礎的原則 `IAuthorizationPolicyProvider` 。 為了讓您更輕鬆地使用這些原則，您可以使用自訂授權屬性（例如）來標注動作 `[MinimumAgeAuthorize(20)]` 。

## <a name="custom-authorization-attributes"></a>自訂授權屬性

授權原則是以其名稱來識別。 `MinimumAgeAuthorizeAttribute`先前所述的自訂必須將引數對應至可用於取出對應授權原則的字串。 您可以藉由衍生自 `AuthorizeAttribute` 並讓 `Age` 屬性包裝屬性來完成這項作業 `AuthorizeAttribute.Policy` 。

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

這個屬性類型具有以 `Policy` 硬式編碼的前置詞為基礎的字串 (`"MinimumAge"`) ，以及透過函式傳入的整數。

您可以使用與其他屬性相同的方式將它套用至動作， `Authorize` 但它會將整數作為參數。

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>自訂 IAuthorizationPolicyProvider

自訂 `MinimumAgeAuthorizeAttribute` 可讓您輕鬆地要求授權原則，以達到所需的最短時間。 下一個要解決的問題，就是確定授權原則適用于所有不同的年齡。 這是很有用的地方 `IAuthorizationPolicyProvider` 。

使用時 `MinimumAgeAuthorizationAttribute` ，授權原則名稱將會遵循此模式 `"MinimumAge" + Age` ，因此自訂 `IAuthorizationPolicyProvider` 應透過下列方式產生授權原則：

* 從原則名稱剖析年齡。
* 使用 `AuthorizationPolicyBuilder` 建立新的 `AuthorizationPolicy`
* 在下列範例中，系統會假設使用者是透過進行驗證 cookie 。 `AuthorizationPolicyBuilder`應該使用至少一個授權配置名稱來建立，或一律成功。 否則，不會提供如何為使用者提供挑戰的相關資訊，也不會擲回例外狀況。
* 根據的年齡將需求加入至原則 `AuthorizationPolicyBuilder.AddRequirements` 。 在其他情況下，您可以改用 `RequireClaim` 、 `RequireRole` 或 `RequireUserName` 。

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>多個授權原則提供者

使用自訂執行時 `IAuthorizationPolicyProvider` ，請記住，ASP.NET Core 只會使用一個實例 `IAuthorizationPolicyProvider` 。 如果自訂提供者無法針對將使用的所有原則名稱提供授權原則，則應該延遲至備份提供者。 

例如，假設應用程式同時需要自訂的年齡原則和更傳統的角色型原則抓取。 這類應用程式可以使用自訂授權原則提供者：

* 嘗試剖析原則名稱。 
* `DefaultAuthorizationPolicyProvider`如果原則名稱不包含存留期，則呼叫不同的原則提供者 (例如) 。

您 `IAuthorizationPolicyProvider` 可以藉由在其函式中建立備份原則提供者，將上面顯示的範例執行更新為使用， `DefaultAuthorizationPolicyProvider` (要在原則名稱不符合其預期的 ' MinimumAge ' + age) 模式時使用。

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

然後， `GetPolicyAsync` 可以更新方法以使用， `BackupPolicyProvider` 而不是傳回 null：

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>預設原則

除了提供命名的授權原則之外，還需要執行自訂， `IAuthorizationPolicyProvider` `GetDefaultPolicyAsync` 以針對 `[Authorize]` 未指定原則名稱的屬性提供授權原則。

在許多情況下，此授權屬性只需要經過驗證的使用者，因此您可以呼叫來建立必要的原則 `RequireAuthenticatedUser` ：

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

如同自訂的所有層面 `IAuthorizationPolicyProvider` ，您可以視需要自訂。 在某些情況下，可能會想要從回復中取出預設原則 `IAuthorizationPolicyProvider` 。

## <a name="fallback-policy"></a>Fallback 原則

自訂 `IAuthorizationPolicyProvider` 可以選擇性地執行 `GetFallbackPolicyAsync` ，以提供在 [合併原則](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) 和未指定任何原則時使用的原則。 如果 `GetFallbackPolicyAsync` 傳回非 null 原則，則在沒有針對要求指定任何原則時，授權中介軟體會使用傳回的原則。

如果不需要任何回送原則，提供者可以傳回 `null` 或延遲至 fallback 提供者：

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>使用自訂 IAuthorizationPolicyProvider

若要從使用自訂原則 `IAuthorizationPolicyProvider` ，您 **必須** *：

_ 向相依性插入註冊適當的 `AuthorizationHandler` 類型， (以原則為基礎的 [授權](xref:security/authorization/policies#authorization-handlers)) 中所述，如同所有以原則為基礎的授權案例。
* `IAuthorizationPolicyProvider`在中，于應用程式的相依性插入服務集合中註冊自訂類型 `Startup.ConfigureServices` ，以取代預設的原則提供者。

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

`IAuthorizationPolicyProvider` [Dotnet/aspnetcore GitHub 存放庫](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)中提供完整的自訂範例。
