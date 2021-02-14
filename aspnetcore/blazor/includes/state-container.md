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
ms.openlocfilehash: 76dbf3cae1c264fa474101bc4398da28f45a1c10
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2021
ms.locfileid: "100254387"
---
<span data-ttu-id="3937e-101">嵌套元件通常會使用 *連結* 系結來系結資料，如中所述 <xref:blazor/components/data-binding> 。</span><span class="sxs-lookup"><span data-stu-id="3937e-101">Nested components typically bind data using *chained bind* as described in <xref:blazor/components/data-binding>.</span></span> <span data-ttu-id="3937e-102">嵌套和未嵌套的元件可以使用已註冊的記憶體內部狀態容器來共用資料的存取權。</span><span class="sxs-lookup"><span data-stu-id="3937e-102">Nested and un-nested components can share access to data using a registered in-memory state container.</span></span> <span data-ttu-id="3937e-103">自訂狀態容器類別可以使用可指派的 <xref:System.Action> ，在狀態變更應用程式的不同部分通知元件。</span><span class="sxs-lookup"><span data-stu-id="3937e-103">A custom state container class can use an assignable <xref:System.Action> to notify components in different parts of the app of state changes.</span></span> <span data-ttu-id="3937e-104">在下例中︰</span><span class="sxs-lookup"><span data-stu-id="3937e-104">In the following example:</span></span>

* <span data-ttu-id="3937e-105">一組元件使用狀態容器來追蹤屬性。</span><span class="sxs-lookup"><span data-stu-id="3937e-105">A pair of components uses a state container to track a property.</span></span>
* <span data-ttu-id="3937e-106">範例的元件是嵌套的，但這種方法不需要使用嵌套。</span><span class="sxs-lookup"><span data-stu-id="3937e-106">The components of the example are nested, but nesting isn't required for this approach to work.</span></span>

<span data-ttu-id="3937e-107">`StateContainer.cs`:</span><span class="sxs-lookup"><span data-stu-id="3937e-107">`StateContainer.cs`:</span></span>

```csharp
public class StateContainer
{
    public string Property { get; set; } = "Initial value from StateContainer";

    public event Action OnChange;

    public void SetProperty(string value)
    {
        Property = value;
        NotifyStateChanged();
    }

    private void NotifyStateChanged() => OnChange?.Invoke();
}
```

<span data-ttu-id="3937e-108">在 `Program.Main` (Blazor WebAssembly) ：</span><span class="sxs-lookup"><span data-stu-id="3937e-108">In `Program.Main` (Blazor WebAssembly):</span></span>

```csharp
builder.Services.AddSingleton<StateContainer>();
```

<span data-ttu-id="3937e-109">在 `Startup.ConfigureServices` (Blazor Server) ：</span><span class="sxs-lookup"><span data-stu-id="3937e-109">In `Startup.ConfigureServices` (Blazor Server):</span></span>

```csharp
services.AddSingleton<StateContainer>();
```

<span data-ttu-id="3937e-110">`Pages/Component1.razor`:</span><span class="sxs-lookup"><span data-stu-id="3937e-110">`Pages/Component1.razor`:</span></span>

```razor
@page "/Component1"
@inject StateContainer StateContainer
@implements IDisposable

<h1>Component 1</h1>

<p>Component 1 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 1</button>
</p>

<Component2 />

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 1: {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="3937e-111">`Shared/Component2.razor`:</span><span class="sxs-lookup"><span data-stu-id="3937e-111">`Shared/Component2.razor`:</span></span>

```razor
@inject StateContainer StateContainer
@implements IDisposable

<h2>Component 2</h2>

<p>Component 2 Property: <b>@StateContainer.Property</b></p>

<p>
    <button @onclick="ChangePropertyValue">Change Property from Component 2</button>
</p>

@code {
    protected override void OnInitialized()
    {
        StateContainer.OnChange += StateHasChanged;
    }

    private void ChangePropertyValue()
    {
        StateContainer.SetProperty($"New value set in Component 2: {DateTime.Now}");
    }

    public void Dispose()
    {
        StateContainer.OnChange -= StateHasChanged;
    }
}
```

<span data-ttu-id="3937e-112">上述元件會執行 <xref:System.IDisposable> ，而且 `OnChange` 委派會在方法中取消訂閱，而這些 `Dispose` 方法會在元件被處置時由架構呼叫。</span><span class="sxs-lookup"><span data-stu-id="3937e-112">The preceding components implement <xref:System.IDisposable>, and the `OnChange` delegates are unsubscribed in the `Dispose` methods, which are called by the framework when the components are disposed.</span></span> <span data-ttu-id="3937e-113">如需詳細資訊，請參閱<xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="3937e-113">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>
