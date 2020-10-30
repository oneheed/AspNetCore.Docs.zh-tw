---
title: '使用 ASP.NET Core 中的中樞篩選 :::no-loc(SignalR):::'
author: brecon
description: '瞭解如何使用 ASP.NET Core 中的中樞篩選 :::no-loc(SignalR)::: 。'
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/hub-filters
ms.openlocfilehash: 5a4cb5122080b72875ac11cf2e682162d017d7b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052717"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="f7509-103">使用 ASP.NET Core 中的中樞篩選 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="f7509-103">Use hub filters in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="f7509-104">中樞篩選器：</span><span class="sxs-lookup"><span data-stu-id="f7509-104">Hub filters:</span></span>

* <span data-ttu-id="f7509-105">適用于 ASP.NET Core 5.0 或更新版本。</span><span class="sxs-lookup"><span data-stu-id="f7509-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="f7509-106">允許邏輯在用戶端叫用中樞方法之前和之後執行。</span><span class="sxs-lookup"><span data-stu-id="f7509-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="f7509-107">本文提供撰寫和使用中樞篩選器的指引。</span><span class="sxs-lookup"><span data-stu-id="f7509-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="f7509-108">設定中樞篩選</span><span class="sxs-lookup"><span data-stu-id="f7509-108">Configure hub filters</span></span>

<span data-ttu-id="f7509-109">中樞篩選器可以全域套用或依中樞類型套用。</span><span class="sxs-lookup"><span data-stu-id="f7509-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="f7509-110">篩選準則的加入順序是篩選準則的執行順序。</span><span class="sxs-lookup"><span data-stu-id="f7509-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="f7509-111">全域中樞篩選器會在本機中樞篩選之前執行。</span><span class="sxs-lookup"><span data-stu-id="f7509-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="f7509-112">您可以使用下列其中一種方式來新增中樞篩選：</span><span class="sxs-lookup"><span data-stu-id="f7509-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="f7509-113">依具體類型新增篩選：</span><span class="sxs-lookup"><span data-stu-id="f7509-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="f7509-114">這會從相依性插入 (DI) 或類型啟用來解決。</span><span class="sxs-lookup"><span data-stu-id="f7509-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="f7509-115">依執行時間類型新增篩選準則：</span><span class="sxs-lookup"><span data-stu-id="f7509-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="f7509-116">這會從 DI 或類型啟用來解析。</span><span class="sxs-lookup"><span data-stu-id="f7509-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="f7509-117">依實例新增篩選：</span><span class="sxs-lookup"><span data-stu-id="f7509-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="f7509-118">此實例將用來作為單一實例。</span><span class="sxs-lookup"><span data-stu-id="f7509-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="f7509-119">所有中樞方法調用都會使用相同的實例。</span><span class="sxs-lookup"><span data-stu-id="f7509-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="f7509-120">中樞篩選器會根據中樞調用來建立和處置。</span><span class="sxs-lookup"><span data-stu-id="f7509-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="f7509-121">如果您想要將全域狀態儲存在篩選器中，或沒有狀態，請將中樞篩選類型新增至 DI 作為單一狀態，以獲得更好的效能。</span><span class="sxs-lookup"><span data-stu-id="f7509-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="f7509-122">或者，如果您可以的話，也可以將篩選準則加入至實例。</span><span class="sxs-lookup"><span data-stu-id="f7509-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="f7509-123">建立中樞篩選器</span><span class="sxs-lookup"><span data-stu-id="f7509-123">Create hub filters</span></span>

<span data-ttu-id="f7509-124">宣告繼承自的類別 `IHubFilter` ，並新增方法，以建立篩選 `InvokeMethodAsync` 。</span><span class="sxs-lookup"><span data-stu-id="f7509-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="f7509-125">此外，也 `OnConnectedAsync` `OnDisconnectedAsync` 可以選擇性地執行，以 `OnConnectedAsync` 分別包裝和 `OnDisconnectedAsync` 中樞方法。</span><span class="sxs-lookup"><span data-stu-id="f7509-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="f7509-126">篩選準則與中介軟體非常類似。</span><span class="sxs-lookup"><span data-stu-id="f7509-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="f7509-127">方法會叫用 `next` 下一個篩選準則。</span><span class="sxs-lookup"><span data-stu-id="f7509-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="f7509-128">最後一個篩選準則將會叫用中樞方法。</span><span class="sxs-lookup"><span data-stu-id="f7509-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="f7509-129">篩選也可以在從傳回 `next` 結果之前呼叫中樞方法之後，儲存等候和執行邏輯的結果 `next` 。</span><span class="sxs-lookup"><span data-stu-id="f7509-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="f7509-130">若要略過篩選中的中樞方法叫用，則會擲回類型的例外狀況， `HubException` 而不是呼叫 `next` 。</span><span class="sxs-lookup"><span data-stu-id="f7509-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="f7509-131">用戶端會在預期結果時收到錯誤。</span><span class="sxs-lookup"><span data-stu-id="f7509-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="f7509-132">使用中樞篩選</span><span class="sxs-lookup"><span data-stu-id="f7509-132">Use hub filters</span></span>

<span data-ttu-id="f7509-133">撰寫篩選邏輯時，請嘗試使用中樞方法上的屬性，而不是檢查中樞方法名稱，使其成為泛型。</span><span class="sxs-lookup"><span data-stu-id="f7509-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="f7509-134">假設有一個篩選準則會檢查禁用片語的中樞方法引數，並取代它找到的任何片語 `***` 。</span><span class="sxs-lookup"><span data-stu-id="f7509-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="f7509-135">在此範例中，假設已 `LanguageFilterAttribute` 定義類別。</span><span class="sxs-lookup"><span data-stu-id="f7509-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="f7509-136">類別具有名為的屬性 `FilterArgument` ，可以在使用屬性時設定。</span><span class="sxs-lookup"><span data-stu-id="f7509-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="f7509-137">將屬性放在具有要清除之字串引數的中樞方法上：</span><span class="sxs-lookup"><span data-stu-id="f7509-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="f7509-138">定義中樞篩選以檢查屬性，並以下列內容取代中樞方法引數中的禁用片語 `**_` ：</span><span class="sxs-lookup"><span data-stu-id="f7509-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `**_`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "_**");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="f7509-139">在方法中註冊中樞篩選準則 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="f7509-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f7509-140">為了避免重新初始化每個叫用的禁用片語清單，中樞篩選會註冊為 singleton：</span><span class="sxs-lookup"><span data-stu-id="f7509-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(SignalR):::(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="f7509-141">HubInvocationCoNtext 物件</span><span class="sxs-lookup"><span data-stu-id="f7509-141">The HubInvocationContext object</span></span>

<span data-ttu-id="f7509-142">`HubInvocationContext`包含目前中樞方法調用的資訊。</span><span class="sxs-lookup"><span data-stu-id="f7509-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="f7509-143">屬性</span><span class="sxs-lookup"><span data-stu-id="f7509-143">Property</span></span> | <span data-ttu-id="f7509-144">描述</span><span class="sxs-lookup"><span data-stu-id="f7509-144">Description</span></span> | <span data-ttu-id="f7509-145">類型</span><span class="sxs-lookup"><span data-stu-id="f7509-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="f7509-146">`HubCallerContext`包含連接的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="f7509-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="f7509-147">用於此中樞方法調用的中樞實例。</span><span class="sxs-lookup"><span data-stu-id="f7509-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="f7509-148">所叫用之中樞方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="f7509-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="f7509-149">要傳遞至中樞方法的引數清單。</span><span class="sxs-lookup"><span data-stu-id="f7509-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="f7509-150">此中樞方法叫用的範圍服務提供者。</span><span class="sxs-lookup"><span data-stu-id="f7509-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="f7509-151">中樞方法資訊。</span><span class="sxs-lookup"><span data-stu-id="f7509-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="f7509-152">HubLifetimeCoNtext 物件</span><span class="sxs-lookup"><span data-stu-id="f7509-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="f7509-153">`HubLifetimeContext`包含 `OnConnectedAsync` 和中樞方法的資訊 `OnDisconnectedAsync` 。</span><span class="sxs-lookup"><span data-stu-id="f7509-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="f7509-154">屬性</span><span class="sxs-lookup"><span data-stu-id="f7509-154">Property</span></span> | <span data-ttu-id="f7509-155">描述</span><span class="sxs-lookup"><span data-stu-id="f7509-155">Description</span></span> | <span data-ttu-id="f7509-156">類型</span><span class="sxs-lookup"><span data-stu-id="f7509-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="f7509-157">`HubCallerContext`包含連接的相關資訊。</span><span class="sxs-lookup"><span data-stu-id="f7509-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="f7509-158">用於此中樞方法調用的中樞實例。</span><span class="sxs-lookup"><span data-stu-id="f7509-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="f7509-159">此中樞方法叫用的範圍服務提供者。</span><span class="sxs-lookup"><span data-stu-id="f7509-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="f7509-160">授權和篩選</span><span class="sxs-lookup"><span data-stu-id="f7509-160">Authorization and filters</span></span>

<span data-ttu-id="f7509-161">在中樞篩選之前執行[的中樞方法上的屬性授權](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization)。</span><span class="sxs-lookup"><span data-stu-id="f7509-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
