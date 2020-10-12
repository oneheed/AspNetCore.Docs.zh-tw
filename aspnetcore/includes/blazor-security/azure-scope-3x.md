Azure 入口網站根據 AAD 租使用者是否有已 [驗證或未驗證的發行者網域](/azure/active-directory/develop/howto-configure-publisher-domain)，提供下列其中一個應用程式識別碼 URI 格式：

* `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}`
* `https://{TENANT}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}`

當用戶端應用程式中使用兩個先前的兩個應用程式識別碼 Uri 的第二個來形成範圍 URI，但授權不成功時，請嘗試提供沒有配置和主機的範圍 URI：

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
```

範例：

```csharp
options.ProviderOptions.DefaultAccessTokenScopes.Add(
    "41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```
