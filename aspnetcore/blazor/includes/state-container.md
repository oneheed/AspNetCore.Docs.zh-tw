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
ms.openlocfilehash: 62d000082180163737ae95bf8fc829b9f026b8a8
ms.sourcegitcommit: 7354c2029164702d075fd3786d96a92c6d49bc6e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178585"
---
嵌套元件通常會使用 *連結* 系結來系結資料，如中所述 <xref:blazor/components/data-binding> 。 嵌套和未嵌套的元件可以使用已註冊的記憶體內部狀態容器來共用資料的存取權。 自訂狀態容器類別可以使用可指派的 <xref:System.Action> ，在狀態變更應用程式的不同部分通知元件。 在下例中︰

* 一組元件使用狀態容器來追蹤屬性。
* 下列範例中的一個元件會嵌套在另一個元件中，但這種方法不需要進行嵌套。

`StateContainer.cs`:

```csharp
using System;

public class StateContainer
{
    private string savedString;

    public string Property
    {
        get => savedString;
        set
        {
            savedString = value;
            NotifyStateChanged();
        }
    }

    public event Action OnChange;

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

在 `Program.Main` (Blazor WebAssembly) ：

```csharp
builder.Services.AddSingleton<StateContainer>();
```

在 `Startup.ConfigureServices` (Blazor Server) ：

```csharp
services.AddScoped<StateContainer>();
```

`Shared/Nested.razor`:

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Nested component</h2>

<p>Nested component Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">
        Change the Property from the Nested component
    </button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.Property = 
            $"New value set in the Nested component: {DateTime.Now}";
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

`Pages/StateContainer.razor`:

```razor
@page "/state-container"
@inject StateContainer StateContainer
@implements IDisposable

<h1>State Container component</h1>

<p>State Container component Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">
        Change the Property from the State Container component
    </button>
</p>

<Nested />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.Property = 
            $"New value set in the State Container component: {DateTime.Now}";
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

上述元件會執行 <xref:System.IDisposable> ，而且 `OnChange` 委派會在方法中取消訂閱，而這些 `Dispose` 方法會在元件被處置時由架構呼叫。 如需詳細資訊，請參閱<xref:blazor/components/lifecycle#component-disposal-with-idisposable>。
