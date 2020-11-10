執行下列 .NET CLI 命令：

```dotnetcli
dotnet tool install --global dotnet-ef --version 5.0.100-rc.2.20479.15
dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.100-rc.2.20479.15
dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
dotnet add package Microsoft.Extensions.Logging.Debug -v 5.0.0-*
```

上述命令會新增：

* [Aspnet codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)的樣板工具。
* 適用于 .NET CLI 的 EF Core 工具。
* EF Core SQLite 提供者，該提供者會將 EF Core 套件作為相依性安裝。
* Scaffolding `Microsoft.VisualStudio.Web.CodeGeneration.Design` 和 `Microsoft.EntityFrameworkCore.SqlServer` 需要的套件。

如需多個環境設定的指引，以允許應用程式依環境設定其資料庫內容，請參閱 <xref:fundamentals/environments#environment-based-startup-class-and-methods> 。

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
