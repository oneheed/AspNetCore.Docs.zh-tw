架構預設為快顯登入模式，如果無法開啟快顯，則會回復為重新導向登入模式。 藉由將的屬性設為，將 MSAL 設定為使用重新導向登入模式 `LoginMode` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `redirect` ：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

預設設定為 `popup` ，且字串值不區分大小寫。
