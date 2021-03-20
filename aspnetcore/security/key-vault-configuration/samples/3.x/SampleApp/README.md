# <a name="key-vault-configuration-provider-sample-app"></a>Key Vault 設定提供者範例應用程式

此範例說明如何使用 Azure Key Vault 設定提供者。

此範例會以 `#define` *程式 .cs* 檔案頂端的語句所決定的兩種模式之一來執行。 如需指示，請參閱 [範例程式碼中的預處理器](https://docs.microsoft.com/aspnet/core#preprocessor-directives-in-sample-code)指示詞：

* `Certificate`：示範如何使用 Azure Key Vault 用戶端識別碼和 x.509 憑證來存取儲存在 Azure Key Vault 中的秘密。 此版本的範例可從任何位置執行，部署到 Azure App Service 或任何能夠提供 ASP.NET Core 應用程式的主機。
* `Managed`：示範如何使用 Azure 的 [受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 來驗證應用程式，以在應用程式的程式碼或設定中使用 Azure AD 驗證來 Azure Key Vault，而不需要認證。 應用程式不需要 Azure AD 用戶端識別碼和密碼，即可使用 Azure Key Vault 進行驗證。 您必須將此範例部署到 Azure App Service，才能探索受控識別案例。

如需詳細資訊，請參閱 [Azure Key Vault 設定提供者](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration)。
