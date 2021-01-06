<span data-ttu-id="a1e9d-101">如果您遇到了樣板錯誤，請確認目標 Framework 標記 (TFM) 符合專案檔中的 NuGet 套件版本。</span><span class="sxs-lookup"><span data-stu-id="a1e9d-101">If you get a scaffolding error, verify the Target Framework Moniker (TFM) matches the NuGet package version in the project file.</span></span> <span data-ttu-id="a1e9d-102">例如，下列專案檔會使用5.0 版的 .NET 和列出的 NuGet 套件：</span><span class="sxs-lookup"><span data-stu-id="a1e9d-102">For example, the following project file uses version 5.0 for .NET and the listed NuGet packages:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>net5.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.EntityFrameworkCore" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.AspNetCore.Identity.UI" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="5.0.0-*" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="5.0.0-*" />
  </ItemGroup>

</Project>
```
