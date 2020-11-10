<span data-ttu-id="89f12-101">執行下列 .NET CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="89f12-101">Run the following .NET CLI commands:</span></span>

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

<span data-ttu-id="89f12-102">上述命令會新增：</span><span class="sxs-lookup"><span data-stu-id="89f12-102">The preceding commands add:</span></span>

* <span data-ttu-id="89f12-103">[Aspnet codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)的樣板工具。</span><span class="sxs-lookup"><span data-stu-id="89f12-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="89f12-104">適用于 .NET CLI 的 EF Core 工具。</span><span class="sxs-lookup"><span data-stu-id="89f12-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="89f12-105">EF Core SQLite 提供者，該提供者會將 EF Core 套件作為相依性安裝。</span><span class="sxs-lookup"><span data-stu-id="89f12-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="89f12-106">Scaffolding `Microsoft.VisualStudio.Web.CodeGeneration.Design` 和 `Microsoft.EntityFrameworkCore.SqlServer` 需要的套件。</span><span class="sxs-lookup"><span data-stu-id="89f12-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="89f12-107">如需多個環境設定的指引，以允許應用程式依環境設定其資料庫內容，請參閱 <xref:fundamentals/environments#environment-based-startup-class-and-methods> 。</span><span class="sxs-lookup"><span data-stu-id="89f12-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
