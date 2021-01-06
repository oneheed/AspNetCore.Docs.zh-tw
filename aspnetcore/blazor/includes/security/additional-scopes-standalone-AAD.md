<span data-ttu-id="99b81-101"><xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> `User.Read` 使用下列內容新增 for 許可權 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes> ：</span><span class="sxs-lookup"><span data-stu-id="99b81-101">Add a <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> for `User.Read` permission with <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions.DefaultAccessTokenScopes>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes
        .Add("https://graph.microsoft.com/User.Read");
});
```
