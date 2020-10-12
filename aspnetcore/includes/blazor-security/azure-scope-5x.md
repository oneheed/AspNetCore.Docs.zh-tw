使用已向 AAD 註冊的伺服器 API，而應用程式的 AAD 註冊在依賴未 [驗證發行者網域](/azure/active-directory/develop/howto-configure-publisher-domain)的租使用者中時，您的伺服器 api 應用程式的應用程式識別碼 URI 不是， `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` 而是採用格式 `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` 。 如果是這種情況， `Program.Main` 應用程式 () 中的預設存取權杖範圍 `Program.cs` *`Client`* 會如下所示：

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

若要為相符的物件設定伺服器 API 應用程式，請 `Audience` 在 *`Server`* API 應用程式佈建檔中設定 (`appsettings.json`) ，以符合 Azure 入口網站提供的應用程式物件：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "ValidateAuthority": true,
    "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
  }
}
```

在上述設定中，值的結尾不 `Audience` 包含預設**not**範圍 `/{DEFAULT SCOPE}` 。

範例：

`Program.Main``Program.cs`應用程式的 () *`Client`* ：

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

將 *`Server`* API 應用程式佈建檔 (`appsettings.json`)  () 的相符物件設定 `Audience` ：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true,
    "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

在上述範例中，值的結尾 `Audience` **不** 包含預設範圍 `/API.Access` 。
