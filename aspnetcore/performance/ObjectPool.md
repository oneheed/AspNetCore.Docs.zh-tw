---
title: 在 ASP.NET Core 中使用 ObjectPool 的物件重複使用
author: rick-anderson
description: 使用 ObjectPool 在 ASP.NET Core 應用程式中提高效能的秘訣。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
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
uid: performance/ObjectPool
ms.openlocfilehash: 6997dbfdd5c654e4a8b15a026fd3ec61d024f02d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88632365"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a>在 ASP.NET Core 中使用 ObjectPool 的物件重複使用

由 [Steve Gordon](https://twitter.com/stevejgordon)、 [Ryan Nowak](https://github.com/rynowak)和 [Günther Foidl](https://github.com/gfoidl)

<xref:Microsoft.Extensions.ObjectPool> 是 ASP.NET Core 基礎結構的一部分，可支援將一組物件保留在記憶體中以供重複使用，而不是允許物件進行垃圾收集。

如果正在管理的物件為，您可能會想要使用物件集區：

- 配置/初始化耗費資源。
- 代表某些有限的資源。
- 使用可預測且經常使用。

例如，ASP.NET Core framework 會在某些地方使用物件集區來重複使用 <xref:System.Text.StringBuilder> 實例。 `StringBuilder` 配置並管理它自己的緩衝區以保存字元資料。 ASP.NET Core 定期使用 `StringBuilder` 來執行功能，並重複使用它們可提供效能優勢。

物件共用不一定能改善效能：

- 除非物件的初始化成本很高，否則從集區取得物件通常會較慢。
- 在解除配置集區之前，不會解除配置集區所管理的物件。

在使用應用程式或程式庫的真實案例收集效能資料之後，才使用物件共用。

::: moniker range="< aspnetcore-3.0"
**警告： `ObjectPool` 不會執行 `IDisposable` 。我們不建議您將它與需要處置的類型搭配使用。** `ObjectPool` 在 ASP.NET Core 3.0 和更新版本中支援 `IDisposable` 。
::: moniker-end

**注意： ObjectPool 不會限制要配置的物件數目，而是會限制它將保留的物件數目。**

## <a name="concepts"></a>概念

<xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -基本物件集區抽象概念。 用來取得和傳回物件。

<xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -將此值用於自訂物件的建立方式，以及傳回給集區時的 *重設* 方式。 這可以傳遞至您直接建立的物件集區 .。。或

<xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> 作為建立物件集區的 factory。
<!-- REview, there is no ObjectPoolProvider<T> -->

ObjectPool 可以透過多種方式在應用程式中使用：

* 將集區具現化。
* 在相依性 [插入](xref:fundamentals/dependency-injection) (DI) 作為實例來註冊集區。
* `ObjectPoolProvider<>`在 DI 中註冊，並使用它作為 factory。

## <a name="how-to-use-objectpool"></a>如何使用 ObjectPool

呼叫 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> 以取得物件，並傳回 <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> 物件。  您不需要傳回每個物件。 如果您沒有傳回物件，則會進行垃圾收集。

::: moniker range=">= aspnetcore-3.0"
當 <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> 使用和 `T` 實行時 `IDisposable` ：

* 系統將會處置 ***未*** 傳回集區的專案。
* 當集區由 DI 處置時，集區中的所有專案都會被處置。

注意：處置集區之後：

* 呼叫會擲回 `Get` `ObjectDisposedException` 。
* `return` 處置指定的專案。

::: moniker-end

## <a name="objectpool-sample"></a>ObjectPool 範例

下列程式碼：

* 將相依性 `ObjectPoolProvider` [插入](xref:fundamentals/dependency-injection) (DI) 容器中。
* 將和設定 `ObjectPool<StringBuilder>` 為 DI 容器。
* 加入 `BirthdayMiddleware` 。

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

下列程式碼會執行 `BirthdayMiddleware`

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
