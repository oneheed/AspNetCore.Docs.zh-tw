## <a name="debug-diagnostics"></a>Debug 診斷

如需詳細的路由診斷輸出，請將設定 `Logging:LogLevel:Microsoft` 為 `Debug` 。 在開發環境中，將 *appsettings.Development.js* 中的記錄層級設定為：

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
