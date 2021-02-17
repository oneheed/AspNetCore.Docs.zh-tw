---
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
ms.openlocfilehash: f0d34557b5442f41ebd1ef9cb3a61122b8516f7d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552266"
---
如果您遇到了樣板錯誤，請確認目標 Framework 標記 (TFM) 符合專案檔中的 NuGet 套件版本。 例如，下列專案檔會使用5.0 版的 .NET 和列出的 NuGet 套件：

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
