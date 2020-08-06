# <a name="aspnet-core-middleware-extensibility-sample"></a>ASP.NET Core 中介軟體擴充性範例

此範例說明如何使用[IMiddleware](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddleware)和[IMiddlewareFactory](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.http.imiddlewarefactory)搭配協力廠商相依性插入容器，[簡單的 Injector](https://simpleinjector.org)。 這個範例會示範 [Middleware activation with a third-party container in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/extensibility-third-party-container) (在 ASP.NET Core 中以協力廠商容器啟用中介軟體) 中所述的功能。
