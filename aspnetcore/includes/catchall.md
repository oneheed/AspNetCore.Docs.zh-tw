::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> **Catch-all** 參數可能會因為路由中的 [錯誤](https://github.com/dotnet/aspnetcore/issues/18677)而不正確地符合路由。 受此 bug 影響的應用程式具有下列特性：
>
> * Catch-all 路由，例如 `{**slug}"`
> * Catch-all 路由無法符合它應該符合的要求。
> * 移除其他路由會讓全部攔截路由開始運作。
>
> 如需遇到此錯誤的範例案例，請參閱 GitHub bug [18677](https://github.com/dotnet/aspnetcore/issues/18677) 和 [16579](https://github.com/dotnet/aspnetcore/issues/16579) 。
>
> [.Net Core 3.1.301 SDK 和更新版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)包含此錯誤的加入宣告修正。 下列程式碼會設定可修正此錯誤的內部參數：
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end