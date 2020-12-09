<span data-ttu-id="aff0d-101">執行下列 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="aff0d-101">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

<span data-ttu-id="aff0d-102">上述命令會新增：</span><span class="sxs-lookup"><span data-stu-id="aff0d-102">The preceding commands add:</span></span>

* <span data-ttu-id="aff0d-103">[Aspnet codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator)的樣板工具。</span><span class="sxs-lookup"><span data-stu-id="aff0d-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="aff0d-104">.NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="aff0d-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="aff0d-105">EF Core SQLite 提供者，該提供者會將 EF Core 套件作為相依性安裝。</span><span class="sxs-lookup"><span data-stu-id="aff0d-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="aff0d-106">Scaffolding `Microsoft.VisualStudio.Web.CodeGeneration.Design` 和 `Microsoft.EntityFrameworkCore.SqlServer` 需要的套件。</span><span class="sxs-lookup"><span data-stu-id="aff0d-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="aff0d-107">如需多個環境設定的指引，以允許應用程式依環境設定其資料庫內容，請參閱 <xref:fundamentals/environments#environment-based-startup-class-and-methods> 。</span><span class="sxs-lookup"><span data-stu-id="aff0d-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]