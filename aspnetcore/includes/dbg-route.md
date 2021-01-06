## <a name="debug-diagnostics"></a><span data-ttu-id="5af0b-101">Debug 診斷</span><span class="sxs-lookup"><span data-stu-id="5af0b-101">Debug diagnostics</span></span>

<span data-ttu-id="5af0b-102">如需詳細的路由診斷輸出，請將設定 `Logging:LogLevel:Microsoft` 為 `Debug` 。</span><span class="sxs-lookup"><span data-stu-id="5af0b-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="5af0b-103">在開發環境中，將 *appsettings.Development.js* 中的記錄層級設定為：</span><span class="sxs-lookup"><span data-stu-id="5af0b-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

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
