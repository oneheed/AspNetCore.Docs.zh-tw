<span data-ttu-id="28075-101">架構預設為快顯登入模式，如果無法開啟快顯，則會回復為重新導向登入模式。</span><span class="sxs-lookup"><span data-stu-id="28075-101">The framework defaults to pop-up login mode and falls back to redirect login mode if a pop-up can't be opened.</span></span> <span data-ttu-id="28075-102">藉由將的屬性設為，將 MSAL 設定為使用重新導向登入模式 `LoginMode` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `redirect` ：</span><span class="sxs-lookup"><span data-stu-id="28075-102">Configure MSAL to use redirect login mode by setting the `LoginMode` property of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> to `redirect`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

<span data-ttu-id="28075-103">預設設定為 `popup` ，且字串值不區分大小寫。</span><span class="sxs-lookup"><span data-stu-id="28075-103">The default setting is `popup`, and the string value isn't case sensitive.</span></span>
