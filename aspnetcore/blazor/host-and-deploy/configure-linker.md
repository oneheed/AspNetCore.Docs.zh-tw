---
title: 設定 ASP.NET Core 的連結器 Blazor
author: guardrex
description: 瞭解如何在建立應用程式時，控制 (IL) 連結器的中繼語言 Blazor 。
monikerRange: '>= aspnetcore-3.1 < aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/host-and-deploy/configure-linker
ms.openlocfilehash: af3c059e7192d6b0d2b0a902b6e3a6121fdf6709
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102395028"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a>設定 ASP.NET Core 的連結器 Blazor

Blazor WebAssembly 在組建期間執行 [中繼語言 (IL) ](/dotnet/standard/managed-code#intermediate-language--execution) 連結，以從應用程式的輸出元件中修剪不必要的 IL。 在偵錯工具中建立時，連結器會停用。 應用程式必須在發行設定中建立，才能啟用連結器。 我們建議您在部署應用程式時，建立版本 Blazor WebAssembly 。 

連結應用程式可針對大小進行優化，但可能會產生不利的影響。 使用反映或相關動態功能的應用程式可能會在修剪時中斷，因為連結器不知道這個動態行為，而且無法判斷在執行時間的反映需要哪些類型。 若要修剪這類應用程式，您必須在程式碼中和應用程式相依的封裝或架構中，針對反映所需的任何類型通知連結器。

若要確保已修剪的應用程式在部署後可以正常運作，請務必在開發期間經常測試應用程式的發行組建。

您 Blazor 可以使用下列 MSBuild 功能來設定應用程式的連結：

* 使用 [MSBuild 屬性](#control-linking-with-an-msbuild-property)來全域設定連結。
* 使用 [設定檔](#control-linking-with-a-configuration-file)來控制每個元件的連結。

## <a name="control-linking-with-an-msbuild-property"></a>使用 MSBuild 屬性的控制項連結

在設定內建應用程式時，會啟用連結 `Release` 。 若要變更此專案，請 `BlazorWebAssemblyEnableLinking` 在專案檔中設定 MSBuild 屬性：

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>使用組態檔控制連結

透過提供 XML 設定檔，並將此檔案指定為專案檔中的 MSBuild 項目，即可根據組件控制連結：

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

`LinkerConfig.xml`:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

如需詳細資訊和範例，請參閱 [ (mono/連結器 GitHub 存放庫) 的資料格式 ](https://github.com/mono/linker/blob/main/docs/data-formats.md)。

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a>將 XML 連結器設定檔新增至程式庫

若要設定特定程式庫的連結器，請將 XML 連結器設定檔新增至程式庫做為內嵌資源。 內嵌資源的名稱必須與元件相同。

在下列範例中，會將檔案 `LinkerConfig.xml` 指定為內嵌資源，其名稱與程式庫的元件相同：

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a>設定適用于國際化的連結器

根據預設， Blazor 應用程式的連結器設定會 Blazor WebAssembly 去除國際化資訊（明確要求的地區設定除外）。 移除這些元件可將應用程式大小降到最低。

若要控制要保留哪些國際化元件，請 `<BlazorWebAssemblyI18NAssemblies>` 在專案檔中設定 MSBuild 屬性：

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| 區域值     | Mono 區域元件    |
| ---------------- | ----------------------- |
| `all`            | 包含的所有元件 |
| `cjk`            | `I18N.CJK.dll`          |
| `mideast`        | `I18N.MidEast.dll`      |
| `none` (預設) | 無                    |
| `other`          | `I18N.Other.dll`        |
| `rare`           | `I18N.Rare.dll`         |
| `west`           | `I18N.West.dll`         |

使用逗號來分隔多個值 (例如 `mideast,west`) 。

如需詳細資訊，請參閱 [國際化： Pnetlib 國際化架構程式庫 (mono/Mono GitHub 存放庫) ](https://github.com/mono/mono/tree/master/mcs/class/I18N)。

## <a name="additional-resources"></a>其他資源

* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-linking>
