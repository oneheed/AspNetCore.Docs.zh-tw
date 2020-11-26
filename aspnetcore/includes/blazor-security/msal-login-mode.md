<span data-ttu-id="1ea1e-101">如果無法開啟快顯視窗，架構會預設為快顯登入模式，並切換回重新導向登入模式。</span><span class="sxs-lookup"><span data-stu-id="1ea1e-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="1ea1e-102">將的屬性設定為，將 MSAL 設定為使用重新導向登入模式 `LoginMode` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `redirect` ：</span><span class="sxs-lookup"><span data-stu-id="1ea1e-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="1ea1e-103">預設設定為 `popup` ，字串值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="1ea1e-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
