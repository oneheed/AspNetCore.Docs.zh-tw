---
title: 建立 Blazor 待辦事項清單應用程式
author: guardrex
description: 逐步建立 Blazor 應用程式。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
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
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870434"
---
# <a name="build-a-no-locblazor-todo-list-app"></a><span data-ttu-id="a804e-103">建立 Blazor 待辦事項清單應用程式</span><span class="sxs-lookup"><span data-stu-id="a804e-103">Build a Blazor todo list app</span></span>

<span data-ttu-id="a804e-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a804e-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a804e-105">本教學課程說明如何建立和修改 Blazor 應用程式。</span><span class="sxs-lookup"><span data-stu-id="a804e-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="a804e-106">您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="a804e-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a804e-107">建立待辦事項清單 Blazor 應用程式專案</span><span class="sxs-lookup"><span data-stu-id="a804e-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a804e-108">修改 Razor 元件</span><span class="sxs-lookup"><span data-stu-id="a804e-108">Modify Razor components</span></span>
> * <span data-ttu-id="a804e-109">在元件中使用事件處理和資料系結</span><span class="sxs-lookup"><span data-stu-id="a804e-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a804e-110">在應用程式中使用路由 Blazor</span><span class="sxs-lookup"><span data-stu-id="a804e-110">Use routing in a Blazor app</span></span>

<span data-ttu-id="a804e-111">在本教學課程結尾處，您將會有一個工作 todo 清單應用程式。</span><span class="sxs-lookup"><span data-stu-id="a804e-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a804e-112">必要條件</span><span class="sxs-lookup"><span data-stu-id="a804e-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a><span data-ttu-id="a804e-113">建立 todo 清單 Blazor 應用程式</span><span class="sxs-lookup"><span data-stu-id="a804e-113">Create a todo list Blazor app</span></span>

1. <span data-ttu-id="a804e-114">Blazor在命令列介面中建立名為的新應用程式 `TodoList` ：</span><span class="sxs-lookup"><span data-stu-id="a804e-114">Create a new Blazor app named `TodoList` in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   <span data-ttu-id="a804e-115">上述命令會建立名為的資料夾 `TodoList` 來保存應用程式。</span><span class="sxs-lookup"><span data-stu-id="a804e-115">The preceding command creates a folder named `TodoList` to hold the app.</span></span> <span data-ttu-id="a804e-116">`TodoList`資料夾是專案的 *根資料夾*。</span><span class="sxs-lookup"><span data-stu-id="a804e-116">The `TodoList` folder is the *root folder* of the project.</span></span> <span data-ttu-id="a804e-117">使用下列命令，將目錄變更為 `TodoList` 資料夾：</span><span class="sxs-lookup"><span data-stu-id="a804e-117">Change directories to the `TodoList` folder with the following command:</span></span>

   ```dotnetcli
   cd TodoList
   ```

1. <span data-ttu-id="a804e-118">`Todo` Razor 使用下列命令，在資料夾中將新元件新增至應用程式 `Pages` ：</span><span class="sxs-lookup"><span data-stu-id="a804e-118">Add a new `Todo` Razor component to the app in the `Pages` folder using the following command:</span></span>

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > <span data-ttu-id="a804e-119">Razor 元件檔案名需要大寫的第一個字母。</span><span class="sxs-lookup"><span data-stu-id="a804e-119">Razor component file names require a capitalized first letter.</span></span> <span data-ttu-id="a804e-120">開啟 `Pages` 資料夾，並確認 `Todo` 元件檔案名以大寫字母開頭 `T` 。</span><span class="sxs-lookup"><span data-stu-id="a804e-120">Open the `Pages` folder and confirm that the `Todo` component file name starts with a capital letter `T`.</span></span> <span data-ttu-id="a804e-121">檔案名應該是 `Todo.razor` 。</span><span class="sxs-lookup"><span data-stu-id="a804e-121">The file name should be `Todo.razor`.</span></span>

1. <span data-ttu-id="a804e-122">`Pages/Todo.razor`提供元件的初始標記：</span><span class="sxs-lookup"><span data-stu-id="a804e-122">In `Pages/Todo.razor` provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   <span data-ttu-id="a804e-123">儲存 `Pages/Todo.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="a804e-123">Save the `Pages/Todo.razor` file.</span></span>

1. <span data-ttu-id="a804e-124">將 `Todo` 元件新增至導覽列。</span><span class="sxs-lookup"><span data-stu-id="a804e-124">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="a804e-125">`NavMenu`元件 (`Shared/NavMenu.razor`) 會用於應用程式的版面配置中。</span><span class="sxs-lookup"><span data-stu-id="a804e-125">The `NavMenu` component (`Shared/NavMenu.razor`) is used in the app's layout.</span></span> <span data-ttu-id="a804e-126">版面配置是可讓您避免應用程式中內容重複的元件。</span><span class="sxs-lookup"><span data-stu-id="a804e-126">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="a804e-127">在檔案 `<NavLink>` `Todo` 中的現有清單專案下方新增下列清單專案標記，以新增元件的元素 `Shared/NavMenu.razor` ：</span><span class="sxs-lookup"><span data-stu-id="a804e-127">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the `Shared/NavMenu.razor` file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   <span data-ttu-id="a804e-128">儲存 `Shared/NavMenu.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="a804e-128">Save the `Shared/NavMenu.razor` file.</span></span>

1. <span data-ttu-id="a804e-129">[`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch)從資料夾的命令 shell 中執行命令，以建立並執行應用程式 `TodoList` 。</span><span class="sxs-lookup"><span data-stu-id="a804e-129">Build and run the app by executing the [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) command in the command shell from the `TodoList` folder.</span></span> <span data-ttu-id="a804e-130">請流覽新的 [待辦事項] 頁面， `https://localhost:5001/todo` 以確認元件的提要欄位導覽連結可 `Todo` 運作。</span><span class="sxs-lookup"><span data-stu-id="a804e-130">Visit the new Todo page at `https://localhost:5001/todo` to confirm that the sidebar navigation link to the `Todo` component works.</span></span>

1. <span data-ttu-id="a804e-131">將檔案加入 `TodoItem.cs` 至專案的根目錄， (`TodoList` 資料夾) 來保存代表 todo 專案的類別。</span><span class="sxs-lookup"><span data-stu-id="a804e-131">Add a `TodoItem.cs` file to the root of the project (the `TodoList` folder) to hold a class that represents a todo item.</span></span> <span data-ttu-id="a804e-132">請使用下列 `TodoItem` 類別的 C# 程式碼：</span><span class="sxs-lookup"><span data-stu-id="a804e-132">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. <span data-ttu-id="a804e-133">回到 `Todo` 元件 (`Pages/Todo.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="a804e-133">Return to the `Todo` component (`Pages/Todo.razor`):</span></span>

   * <span data-ttu-id="a804e-134">在 `@code` 區塊中新增待辦事項的欄位。</span><span class="sxs-lookup"><span data-stu-id="a804e-134">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="a804e-135">`Todo` 元件會使用此欄位來維護待辦事項清單的狀態。</span><span class="sxs-lookup"><span data-stu-id="a804e-135">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="a804e-136">新增未排序的清單標記和 `foreach` 迴圈，將每個待辦事項轉譯為清單項目 (`<li>`)。</span><span class="sxs-lookup"><span data-stu-id="a804e-136">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="a804e-137">應用程式需要 UI 元素，才能將待辦事項新增至清單。</span><span class="sxs-lookup"><span data-stu-id="a804e-137">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="a804e-138">在未排序清單 (`<ul>...</ul>`) 下方新增文字輸出 (`<input>`) 與按鈕 (`<button>`)：</span><span class="sxs-lookup"><span data-stu-id="a804e-138">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. <span data-ttu-id="a804e-139">儲存檔案 `TodoItem.cs` 和更新的檔案 `Pages/Todo.razor` 。</span><span class="sxs-lookup"><span data-stu-id="a804e-139">Save the `TodoItem.cs` file and the updated `Pages/Todo.razor` file.</span></span> <span data-ttu-id="a804e-140">在命令列介面中，應用程式會在儲存檔案時自動重建。</span><span class="sxs-lookup"><span data-stu-id="a804e-140">In the command shell, the app is automatically rebuilt when the files are saved.</span></span> <span data-ttu-id="a804e-141">瀏覽器會暫時失去其與應用程式的連線，然後在重新建立連接時重載頁面。</span><span class="sxs-lookup"><span data-stu-id="a804e-141">The browser temporarily loses its connection to the app and then reloads the page when the connection is re-established.</span></span>

1. <span data-ttu-id="a804e-142">當 **`Add todo`** 選取按鈕時，不會發生任何事，因為事件處理常式並未附加至按鈕。</span><span class="sxs-lookup"><span data-stu-id="a804e-142">When the **`Add todo`** button is selected, nothing happens because an event handler isn't attached to the button.</span></span>

1. <span data-ttu-id="a804e-143">將 `AddTodo` 方法新增至 `Todo` 元件並註冊，以便使用 `@onclick` 屬性來進行按鈕選取。</span><span class="sxs-lookup"><span data-stu-id="a804e-143">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="a804e-144">當選取按鈕時，就會呼叫 `AddTodo` C# 方法：</span><span class="sxs-lookup"><span data-stu-id="a804e-144">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. <span data-ttu-id="a804e-145">若要取得新待辦事項的標題，請在 `@code` 區塊頂端新增 `newTodo` 字串欄位，然後使用 `<input>` 元素中的 `bind` 屬性將它繫結至文字輸入的值：</span><span class="sxs-lookup"><span data-stu-id="a804e-145">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="a804e-146">更新 `AddTodo` 方法，將 `TodoItem` 與指定的標題新增至清單。</span><span class="sxs-lookup"><span data-stu-id="a804e-146">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="a804e-147">請將 `newTodo` 設定為空字串，以清除文字輸入的值：</span><span class="sxs-lookup"><span data-stu-id="a804e-147">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. <span data-ttu-id="a804e-148">儲存 `Pages/ToDo.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="a804e-148">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="a804e-149">應用程式會在命令 shell 中自動重建。</span><span class="sxs-lookup"><span data-stu-id="a804e-149">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a804e-150">當瀏覽器重新連接至應用程式之後，瀏覽器中的頁面會重載。</span><span class="sxs-lookup"><span data-stu-id="a804e-150">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a804e-151">每個待辦事項的標題文字都可設定為可編輯，而核取方塊則可協助使用者記錄已完成的項目。</span><span class="sxs-lookup"><span data-stu-id="a804e-151">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="a804e-152">請為每個待辦事項新增核取方塊輸入，然後將其值繫結至 `IsDone` 屬性。</span><span class="sxs-lookup"><span data-stu-id="a804e-152">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="a804e-153">將 `@todo.Title` 變更為繫結至 `@todo.Title` 的 `<input>` 元素：</span><span class="sxs-lookup"><span data-stu-id="a804e-153">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. <span data-ttu-id="a804e-154">若要確認是否已繫結這些值，請更新 `<h3>` 標頭，以顯示未完成之待辦事項 (`IsDone` 為 `false`) 的數目計數。</span><span class="sxs-lookup"><span data-stu-id="a804e-154">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="a804e-155">完成的 `Todo` 元件 (`Pages/Todo.razor`) ：</span><span class="sxs-lookup"><span data-stu-id="a804e-155">The completed `Todo` component (`Pages/Todo.razor`):</span></span>

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. <span data-ttu-id="a804e-156">儲存 `Pages/ToDo.razor` 檔案。</span><span class="sxs-lookup"><span data-stu-id="a804e-156">Save the `Pages/ToDo.razor` file.</span></span> <span data-ttu-id="a804e-157">應用程式會在命令 shell 中自動重建。</span><span class="sxs-lookup"><span data-stu-id="a804e-157">The app is automatically rebuilt in the command shell.</span></span> <span data-ttu-id="a804e-158">當瀏覽器重新連接至應用程式之後，瀏覽器中的頁面會重載。</span><span class="sxs-lookup"><span data-stu-id="a804e-158">The page reloads in the browser after the browser reconnects to the app.</span></span>

1. <span data-ttu-id="a804e-159">請新增待辦事項，以測試新程式碼。</span><span class="sxs-lookup"><span data-stu-id="a804e-159">Add todo items to test the new code.</span></span>

1. <span data-ttu-id="a804e-160">完成時，請在命令 shell 中關閉應用程式。</span><span class="sxs-lookup"><span data-stu-id="a804e-160">When finished, shut down the app in the command shell.</span></span> <span data-ttu-id="a804e-161">許多命令 shell 都接受鍵盤命令<kbd>Ctrl</kbd> + <kbd>c</kbd>以停止應用程式。</span><span class="sxs-lookup"><span data-stu-id="a804e-161">Many command shells accept the keyboard command <kbd>Ctrl</kbd>+<kbd>c</kbd> to stop an app.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a804e-162">後續步驟</span><span class="sxs-lookup"><span data-stu-id="a804e-162">Next steps</span></span>

<span data-ttu-id="a804e-163">在本教學課程中，您已了解如何：</span><span class="sxs-lookup"><span data-stu-id="a804e-163">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a804e-164">建立待辦事項清單 Blazor 應用程式專案</span><span class="sxs-lookup"><span data-stu-id="a804e-164">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="a804e-165">修改 Razor 元件</span><span class="sxs-lookup"><span data-stu-id="a804e-165">Modify Razor components</span></span>
> * <span data-ttu-id="a804e-166">在元件中使用事件處理和資料系結</span><span class="sxs-lookup"><span data-stu-id="a804e-166">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="a804e-167">在應用程式中使用路由 Blazor</span><span class="sxs-lookup"><span data-stu-id="a804e-167">Use routing in a Blazor app</span></span>

<span data-ttu-id="a804e-168">瞭解 ASP.NET Core 的工具 Blazor ：</span><span class="sxs-lookup"><span data-stu-id="a804e-168">Learn about tooling for ASP.NET Core Blazor:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
