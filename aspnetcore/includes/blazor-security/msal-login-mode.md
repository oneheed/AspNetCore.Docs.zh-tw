如果無法開啟快顯視窗，架構會預設為快顯登入模式，並切換回重新導向登入模式。 將的屬性設定為，將 MSAL 設定為使用重新導向登入模式 `LoginMode` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `redirect` ：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

預設設定為 `popup` ，字串值不區分大小寫。
