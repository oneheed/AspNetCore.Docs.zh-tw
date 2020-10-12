<span data-ttu-id="06ba9-101">使用已向 AAD 註冊的伺服器 API，而應用程式的 AAD 註冊在依賴未 [驗證發行者網域](/azure/active-directory/develop/howto-configure-publisher-domain)的租使用者中時，您的伺服器 api 應用程式的應用程式識別碼 URI 不是， `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` 而是採用格式 `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}` 。</span><span class="sxs-lookup"><span data-stu-id="06ba9-101">When working with a server API registered with AAD and the app's AAD registration is in an tenant that relies on an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), the App ID URI of your server API app isn't `api://{SERVER API APP CLIENT ID OR CUSTOM VALUE}` but instead is in the format `https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}`.</span></span> <span data-ttu-id="06ba9-102">如果是這種情況， `Program.Main` 應用程式 () 中的預設存取權杖範圍 `Program.cs` *`Client`* 會如下所示：</span><span class="sxs-lookup"><span data-stu-id="06ba9-102">If that's the case, the default access token scope in `Program.Main` (`Program.cs`) of the *`Client`* app appears similar to the following:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}/{DEFAULT SCOPE}");
```

<span data-ttu-id="06ba9-103">若要為相符的物件設定伺服器 API 應用程式，請 `Audience` 在 *`Server`* API 應用程式佈建檔中設定 (`appsettings.json`) ，以符合 Azure 入口網站提供的應用程式物件：</span><span class="sxs-lookup"><span data-stu-id="06ba9-103">To configure the server API app for a matching audience, set the `Audience` in the *`Server`* API app settings file (`appsettings.json`) to match the app's audience provided by the Azure portal:</span></span>

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

<span data-ttu-id="06ba9-104">在上述設定中，值的結尾不 `Audience` 包含預設**not**範圍 `/{DEFAULT SCOPE}` 。</span><span class="sxs-lookup"><span data-stu-id="06ba9-104">In the preceding configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`.</span></span>

<span data-ttu-id="06ba9-105">範例：</span><span class="sxs-lookup"><span data-stu-id="06ba9-105">Example:</span></span>

<span data-ttu-id="06ba9-106">`Program.Main``Program.cs`應用程式的 () *`Client`* ：</span><span class="sxs-lookup"><span data-stu-id="06ba9-106">`Program.Main` (`Program.cs`) of the *`Client`* app:</span></span>

```csharp
options.ProviderOptions.DefaultAccessTokenScopes
    .Add("https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
```

<span data-ttu-id="06ba9-107">將 *`Server`* API 應用程式佈建檔 (`appsettings.json`)  () 的相符物件設定 `Audience` ：</span><span class="sxs-lookup"><span data-stu-id="06ba9-107">Configure the *`Server`* API app settings file (`appsettings.json`) with a matching audience (`Audience`):</span></span>

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

<span data-ttu-id="06ba9-108">在上述範例中，值的結尾 `Audience` **不** 包含預設範圍 `/API.Access` 。</span><span class="sxs-lookup"><span data-stu-id="06ba9-108">In the preceding example, the end of the `Audience` value does **not** include the default scope `/API.Access`.</span></span>
