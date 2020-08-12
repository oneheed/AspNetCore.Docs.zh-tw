<a name="csc"></a>

## <a name="combining-service-collection"></a>合併服務集合

請考慮下列 `ConfigureServices` 包含數個服務集合的：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

可以將相關的註冊群組移至擴充方法，以註冊服務。 例如，設定服務會新增至下列類別：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfgServiceCollectionExtensions.cs)]

其餘服務會在類似的類別中註冊。 下列 `ConfigureServices` 使用新的擴充方法來註冊服務：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

***注意：*** 每個 `services.Add{SERVICE_NAME}` 擴充方法都會新增並可能設定服務。 例如，會 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> 加入具有 views 所需的服務 MVC 控制器。 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>新增 Razor Pages 所需的服務。 我們建議應用程式遵循此命名慣例。 在 [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) 命名空間中放置擴充方法，以封裝服務註冊群組。