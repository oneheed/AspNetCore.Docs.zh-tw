<span data-ttu-id="bfa5f-101">Azure 入口網站根據 AAD 租使用者是否有已 [驗證或未驗證的發行者網域](/azure/active-directory/develop/howto-configure-publisher-domain)，提供下列其中一個應用程式識別碼 URI 格式：</span><span class="sxs-lookup"><span data-stu-id="bfa5f-101">The Azure portal provides one of the following App ID URI formats based on whether or not the AAD tenant has a [verified or unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain):</span></span>

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

<span data-ttu-id="bfa5f-102">當用戶端應用程式中使用兩個先前的兩個應用程式識別碼 Uri 的第二個來形成範圍 URI，但授權不成功時，請嘗試提供沒有配置和主機的範圍 URI：</span><span class="sxs-lookup"><span data-stu-id="bfa5f-102">When the latter of the two preceding App ID URIs is used in the client app to form the scope URI and authorization isn't successful, try supplying the scope URI without the scheme and host:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

<span data-ttu-id="bfa5f-103">範例：</span><span class="sxs-lookup"><span data-stu-id="bfa5f-103">Example:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
