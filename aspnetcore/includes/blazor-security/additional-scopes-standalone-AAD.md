<xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `User.Read` 使用下列內容新增 for 許可權 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> ：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
