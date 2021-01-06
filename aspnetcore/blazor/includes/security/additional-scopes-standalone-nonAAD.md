<span data-ttu-id="06ead-101">為和新增一 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 對 `openid` `offline_access` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> ：</span><span class="sxs-lookup"><span data-stu-id="06ead-101">Add a pair of <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `openid` and `offline_access` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("openid");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("offline_access");
});
```
