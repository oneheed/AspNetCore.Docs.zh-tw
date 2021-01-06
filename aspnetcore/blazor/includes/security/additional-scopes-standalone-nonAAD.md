為和新增一 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 對 `openid` `offline_access` <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> ：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("openid");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("offline_access");
});
```
