---
title: 建立 Blazor 待辦事項清單應用程式
author: guardrex
description: 逐步建立 Blazor 應用程式。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2021
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
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: 260d921316d6fadecbd42db11048593b19a5ddee
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394430"
---
# <a name="build-a-blazor-todo-list-app"></a><span data-ttu-id="5ebb1-103">建立 Blazor 待辦事項清單應用程式</span><span class="sxs-lookup"><span data-stu-id="5ebb1-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="5ebb1-104">本教學課程說明如何建立和修改 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-104">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="5ebb1-105">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5ebb1-106">建立待辦事項清單 Blazor 應用程式專案</span><span class="sxs-lookup"><span data-stu-id="5ebb1-106">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="5ebb1-107">修改 Razor 元件</span><span class="sxs-lookup"><span data-stu-id="5ebb1-107">Modify Razor components</span></span>
> * <span data-ttu-id="5ebb1-108">在元件中使用事件處理和資料系結</span><span class="sxs-lookup"><span data-stu-id="5ebb1-108">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="5ebb1-109">在應用程式中使用路由 Blazor</span><span class="sxs-lookup"><span data-stu-id="5ebb1-109">Use routing in a Blazor app</span></span>

<span data-ttu-id="5ebb1-110">在本教學課程結尾處，您將會有一個工作 todo 清單應用程式。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-110">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5ebb1-111">必要條件</span><span class="sxs-lookup"><span data-stu-id="5ebb1-111">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a><span data-ttu-id="5ebb1-112">建立 todo 清單 Blazor 應用程式</span><span class="sxs-lookup"><span data-stu-id="5ebb1-112">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="5ebb1-113">Blazor在命令列介面中建立名為的新應用程式 `TodoList` ：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-113">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="5ebb1-114">上述命令會建立一個名為的資料夾， `TodoList` 其中包含用 `-o|--output` 來保存應用程式的選項。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-114">The preceding command creates a folder named `TodoList` with the `-o|--output` option to hold the app.</span></span> <span data-ttu-id="5ebb1-115">`TodoList`資料夾是專案的 *根資料夾*。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-115">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="5ebb1-116">使用下列命令，將目錄變更為 `TodoList` 資料夾：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-116">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="5ebb1-117">使用下列命令，將新的 `Todo` Razor 元件新增至應用程式：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-117">Add a new `Todo` Razor component to the app using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   <span data-ttu-id="5ebb1-118">`-n|--name`上述命令中的選項會指定新元件的名稱 Razor 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-118">The `-n|--name` option in the preceding command specifies the name of the new Razor component.</span></span> <span data-ttu-id="5ebb1-119">您可以使用選項，在專案的資料夾中建立新的元件 `Pages` `-o|--output` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-119">The new component is created in the project's `Pages` folder with the `-o|--output` option.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="5ebb1-120">Razor 元件檔案名需要大寫的第一個字母。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-120">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="5ebb1-121">開啟 `Pages` 資料夾，並確認 `Todo` 元件檔案名以大寫字母開頭 `T` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-121">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="5ebb1-122">檔案名應該是 `Todo.razor` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-122">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="5ebb1-123">`Todo`在任何檔案編輯器中開啟元件，並將指示詞新增至檔案的頂端，並 `@page` Razor 使用的相對 URL `/todo` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-123">Open the `Todo` component in any file editor and add an `@page` Razor directive to the top of the file with a relative URL of `/todo`.</span></span>

   <span data-ttu-id="5ebb1-124">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="5ebb1-124">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   <span data-ttu-id="5ebb1-125">儲存 `Pages/Todo.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-125">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="5ebb1-126">將 `Todo` 元件新增至導覽列。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-126">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="5ebb1-127">`NavMenu`元件會用於應用程式的版面配置中。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-127">The `NavMenu` component is used in the app's layout.</span></span> <span data-ttu-id="5ebb1-128">版面配置是可讓您避免應用程式中的內容重複的元件。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-128">Layouts are components that allow you to avoid duplication of content in an app.</span></span> <span data-ttu-id="5ebb1-129">`NavLink`當應用程式載入元件 URL 時，此元件會在應用程式的 UI 中提供提示。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-129">The `NavLink` component provides a cue in the app's UI when the component URL is loaded by the app.</span></span>

   <span data-ttu-id="5ebb1-130">在未排序的清單中 (`<ul>...</ul>` 元件的) `NavMenu` ， (`<li>...</li>` 元件的) 和元件新增下列清單專案 `NavLink` `Todo` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-130">In the unordered list (`<ul>...</ul>`) of the `NavMenu` component, add the following list item (`<li>...</li>`) and `NavLink` component for the `Todo` component.</span></span>

   <span data-ttu-id="5ebb1-131">在 `Shared/NavMenu.razor` 中：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-131">In `Shared/NavMenu.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   <span data-ttu-id="5ebb1-132">儲存 `Shared/NavMenu.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-132">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="5ebb1-133">[`dotnet watch run`](xref:tutorials/dotnet-watch)從資料夾的命令 shell 中執行命令，以建立並執行應用程式 `TodoList` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-133">Build and run the app by executing the [`dotnet watch run`](xref:tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="5ebb1-134">應用程式執行之後，請流覽新的 [待辦事項] 頁面，方法是選取 **`Todo`** 應用程式巡覽列中的連結，此連結會將頁面載入至 `/todo` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-134">After the app is running, visit the new Todo page by selecting the **`Todo`** link in the app's navigation bar, which loads the page at `/todo`.</span></span>

   <span data-ttu-id="5ebb1-135">讓應用程式繼續執行命令 shell。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-135">Leave the app running the command shell.</span></span> <span data-ttu-id="5ebb1-136">每次儲存檔案時，即會自動重建應用程式。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-136">Each time a file is saved, the app is automatically rebuilt.</span></span> <span data-ttu-id="5ebb1-137">在編譯和重新開機時，瀏覽器會暫時失去其應用程式的連接。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-137">The browser temporarily loses its connection to the app while compiling and restarting.</span></span> <span data-ttu-id="5ebb1-138">重新建立連接時，會自動重載瀏覽器中的頁面。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-138">The page in the browser is automatically reloaded when the connection is re-established.</span></span>

1. <span data-ttu-id="5ebb1-139">將檔案加入 `TodoItem.cs` 至專案的根目錄， (`TodoList` 資料夾) 來保存代表 todo 專案的類別。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-139">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="5ebb1-140">針對 `TodoItem` 類別使用下列 C# 程式碼。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-140">Use the following C# code for the `TodoItem` class.</span></span>

   <span data-ttu-id="5ebb1-141">`TodoItem.cs`:</span><span class="sxs-lookup"><span data-stu-id="5ebb1-141">`TodoItem.cs`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > <span data-ttu-id="5ebb1-142">如果使用 Visual Studio 建立檔案 `TodoItem.cs` 和 `TodoItem` 類別，請使用下列其中一種方法：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-142">If using Visual Studio to create the `TodoItem.cs` file and `TodoItem` class, use either of the following approaches:</span></span>
   >
   > * <span data-ttu-id="5ebb1-143">移除 Visual Studio 為類別產生的命名空間。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-143">Remove the namespace that Visual Studio generates for the class.</span></span>
   > * <span data-ttu-id="5ebb1-144">使用上述程式碼區塊中的 [ **複製** ] 按鈕，取代 Visual Studio 所產生之檔案的整個內容。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-144">Use the **Copy** button in the preceding code block and replace the entire contents of the file that Visual Studio generates.</span></span>

1. <span data-ttu-id="5ebb1-145">返回 `Todo` 元件，並執行下列工作：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-145">Return to the `Todo` component and perform the following tasks:</span></span>

   * <span data-ttu-id="5ebb1-146">在區塊中加入待辦事項的欄位 `@code` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-146">Add a field for the todo items in the `@code` block.</span></span> <span data-ttu-id="5ebb1-147">`Todo` 元件會使用此欄位來維護待辦事項清單的狀態。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-147">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="5ebb1-148">新增未排序的清單標記和 `foreach` 迴圈，將每個待辦事項轉譯為清單項目 (`<li>`)。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-148">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   <span data-ttu-id="5ebb1-149">`Pages/Todo.razor`:</span><span class="sxs-lookup"><span data-stu-id="5ebb1-149">`Pages/Todo.razor`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. <span data-ttu-id="5ebb1-150">應用程式需要 UI 元素，才能將待辦事項新增至清單。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-150">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="5ebb1-151">在未排序清單 (`<ul>...</ul>`) 下方新增文字輸出 (`<input>`) 與按鈕 (`<button>`)：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-151">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. <span data-ttu-id="5ebb1-152">儲存檔案 `TodoItem.cs` 和更新的檔案 `Pages/Todo.razor` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-152">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="5ebb1-153">在命令列介面中，應用程式會在儲存檔案時自動重建。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-153">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="5ebb1-154">瀏覽器會暫時失去其與應用程式的連線，然後在重新建立連接時重載頁面。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-154">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="5ebb1-155">當 **`Add todo`** 選取按鈕時，不會發生任何事，因為事件處理常式並未附加至按鈕。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-155">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="5ebb1-156">將 `AddTodo` 方法新增至 `Todo` 元件，並使用屬性註冊按鈕的方法 `@onclick` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-156">Add an `AddTodo` method to the `Todo` component and register the method for the button using the `@onclick` attribute.</span></span> <span data-ttu-id="5ebb1-157">當選取按鈕時，就會呼叫 `AddTodo` C# 方法：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-157">The `AddTodo` C# method is called when the button is selected:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. <span data-ttu-id="5ebb1-158">若要取得新待辦事項的標題，請 `newTodo` 在區塊頂端加入字串欄位 `@code` ：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-158">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   <span data-ttu-id="5ebb1-159">修改要與屬性系結的 text `<input>` 元素 `newTodo` `@bind` ：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-159">Modify the text `<input>` element to bind `newTodo` with the `@bind` attribute:</span></span>

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="5ebb1-160">更新 `AddTodo` 方法，將 `TodoItem` 與指定的標題新增至清單。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-160">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="5ebb1-161">請將 `newTodo` 設定為空字串，以清除文字輸入的值：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-161">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. <span data-ttu-id="5ebb1-162">儲存 `Pages/Todo.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-162">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="5ebb1-163">應用程式會在命令 shell 中自動重建。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-163">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="5ebb1-164">當瀏覽器重新連接至應用程式之後，瀏覽器中的頁面會重載。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-164">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="5ebb1-165">每個待辦事項的標題文字都可設定為可編輯，而核取方塊則可協助使用者記錄已完成的項目。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-165">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="5ebb1-166">請為每個待辦事項新增核取方塊輸入，然後將其值繫結至 `IsDone` 屬性。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-166">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="5ebb1-167">變更 `@todo.Title` 為系結 `<input>` 至 `todo.Title` 的元素 `@bind` ：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-167">Change `@todo.Title` to an `<input>` element bound to `todo.Title` with `@bind`:</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. <span data-ttu-id="5ebb1-168">更新 `<h3>` 標頭，以顯示未)  (完成的待辦事項數目計數 `IsDone` `false` 。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-168">Update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="5ebb1-169">完成的 `Todo` 元件 (`Pages/Todo.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-169">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. <span data-ttu-id="5ebb1-170">儲存 `Pages/Todo.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-170">Save the `Pages/Todo.razor` file.</span></span> <span data-ttu-id="5ebb1-171">應用程式會在命令 shell 中自動重建。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-171">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="5ebb1-172">當瀏覽器重新連接至應用程式之後，瀏覽器中的頁面會重載。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-172">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="5ebb1-173">加入專案、編輯專案，以及標記完成的待辦事項以測試元件。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-173">Add items, edit items, and mark todo items done to test the component.</span></span>

1. <span data-ttu-id="5ebb1-174">完成時，請在命令 shell 中關閉應用程式。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-174">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="5ebb1-175">許多命令 shell 都接受鍵盤命令<kbd>Ctrl</kbd> + <kbd>c</kbd>以停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="5ebb1-175">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5ebb1-176">後續步驟</span><span class="sxs-lookup"><span data-stu-id="5ebb1-176">Next steps</span></span>

<span data-ttu-id="5ebb1-177">在本教學課程中，您已了解如何：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-177">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5ebb1-178">建立待辦事項清單 Blazor 應用程式專案</span><span class="sxs-lookup"><span data-stu-id="5ebb1-178">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="5ebb1-179">修改 Razor 元件</span><span class="sxs-lookup"><span data-stu-id="5ebb1-179">Modify Razor components</span></span>
> * <span data-ttu-id="5ebb1-180">在元件中使用事件處理和資料系結</span><span class="sxs-lookup"><span data-stu-id="5ebb1-180">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="5ebb1-181">在應用程式中使用路由 Blazor</span><span class="sxs-lookup"><span data-stu-id="5ebb1-181">Use routing in a Blazor app</span></span>

<span data-ttu-id="5ebb1-182">瞭解 ASP.NET Core 的工具 Blazor ：</span><span class="sxs-lookup"><span data-stu-id="5ebb1-182">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
