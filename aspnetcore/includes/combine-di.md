<a name="csc"></a>

請考慮下列 `ConfigureServices` 方法，它會註冊服務並設定選項：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup2.cs?name=snippet)]

您可以將相關的註冊群組移至擴充方法，以註冊服務。 例如，設定服務會新增至下列類別：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/MyConfigServiceCollectionExtensions.cs)]

其餘的服務會註冊在類似的類別中。 下列 `ConfigureServices` 方法會使用新的擴充方法來註冊服務：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup4.cs?name=snippet)]

**_注意：_** 每個 `services.Add{GROUP_NAME}` 擴充方法會新增並可能設定服務。 例如， <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> 新增具有 views 所需的服務 MVC 控制器，並 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A> 新增 Razor Pages 所需的服務。 建議應用程式遵循此命名慣例。 在 <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> 命名空間中放置擴充方法，以封裝服務註冊群組。
