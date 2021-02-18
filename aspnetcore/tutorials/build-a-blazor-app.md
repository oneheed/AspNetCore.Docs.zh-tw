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
ms.openlocfilehash: d984023a1c46c5383d47a1634c54e61747b83d60
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101206"
---
# <a name="build-a-blazor-todo-list-app"></a>建立 Blazor 待辦事項清單應用程式

本教學課程說明如何建立和修改 Blazor 應用程式。 您會了解如何：

> [!div class="checklist"]
> * 建立待辦事項清單 Blazor 應用程式專案
> * 修改 Razor 元件
> * 在元件中使用事件處理和資料系結
> * 在應用程式中使用路由 Blazor

在本教學課程結尾處，您將會有一個工作 todo 清單應用程式。

## <a name="prerequisites"></a>必要條件

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-blazor-app"></a>建立 todo 清單 Blazor 應用程式

1. Blazor在命令列介面中建立名為的新應用程式 `TodoList` ：

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   上述命令會建立一個名為的資料夾， `TodoList` 其中包含用 `-o|--output` 來保存應用程式的選項。 `TodoList`資料夾是專案的 *根資料夾*。 使用下列命令，將目錄變更為 `TodoList` 資料夾：

   ```dotnetcli
   cd TodoList
   ```

1. 使用下列命令，將新的 `Todo` Razor 元件新增至應用程式：

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   `-n|--name`上述命令中的選項會指定新元件的名稱 Razor 。 您可以使用選項，在專案的資料夾中建立新的元件 `Pages` `-o|--output` 。

   > [!IMPORTANT]
   > Razor 元件檔案名需要大寫的第一個字母。 開啟 `Pages` 資料夾，並確認 `Todo` 元件檔案名以大寫字母開頭 `T` 。 檔案名應該是 `Todo.razor` 。

1. `Todo`在任何檔案編輯器中開啟元件，並將指示詞新增至檔案的頂端，並 `@page` Razor 使用的相對 URL `/todo` 。

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo0.razor?highlight=1)]

   ::: moniker-end

   儲存 `Pages/Todo.razor` 檔案。

1. 將 `Todo` 元件新增至導覽列。

   `NavMenu`元件會用於應用程式的版面配置中。 版面配置是可讓您避免應用程式中的內容重複的元件。 `NavLink`當應用程式載入元件 URL 時，此元件會在應用程式的 UI 中提供提示。

   在未排序的清單中 (`<ul>...</ul>` 元件的) `NavMenu` ， (`<li>...</li>` 元件的) 和元件新增下列清單專案 `NavLink` `Todo` 。

   在 `Shared/NavMenu.razor` 中：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/build-a-blazor-app/NavMenu.razor?highlight=5-9)]

   ::: moniker-end

   儲存 `Shared/NavMenu.razor` 檔案。

1. [`dotnet watch run`](xref:tutorials/dotnet-watch)從資料夾的命令 shell 中執行命令，以建立並執行應用程式 `TodoList` 。 應用程式執行之後，請流覽新的 [待辦事項] 頁面，方法是選取 **`Todo`** 應用程式巡覽列中的連結，此連結會將頁面載入至 `/todo` 。

   讓應用程式繼續執行命令 shell。 每次儲存檔案時，即會自動重建應用程式。 在編譯和重新開機時，瀏覽器會暫時失去其應用程式的連接。 重新建立連接時，會自動重載瀏覽器中的頁面。

1. 將檔案加入 `TodoItem.cs` 至專案的根目錄， (`TodoList` 資料夾) 來保存代表 todo 專案的類別。 針對 `TodoItem` 類別使用下列 C# 程式碼。

   `TodoItem.cs`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/build-a-blazor-app/TodoItem.cs)]

   ::: moniker-end

   > [!NOTE]
   > 如果使用 Visual Studio 建立檔案 `ToDoItem.cs` 和 `ToDoItem` 類別，請使用下列其中一種方法：
   >
   > * 移除 Visual Studio 為類別產生的命名空間。
   > * 使用上述程式碼區塊中的 [ **複製** ] 按鈕，並取代 Visual Studio 產生之檔案的整個內容。

1. 返回 `Todo` 元件，並執行下列工作：

   * 在區塊中加入待辦事項的欄位 `@code` 。 `Todo` 元件會使用此欄位來維護待辦事項清單的狀態。
   * 新增未排序的清單標記和 `foreach` 迴圈，將每個待辦事項轉譯為清單項目 (`<li>`)。

   `Pages/Todo.razor`:

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo2.razor?highlight=5-10,13)]

   ::: moniker-end

1. 應用程式需要 UI 元素，才能將待辦事項新增至清單。 在未排序清單 (`<ul>...</ul>`) 下方新增文字輸出 (`<input>`) 與按鈕 (`<button>`)：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo3.razor?highlight=12-13)]

   ::: moniker-end

1. 儲存檔案 `TodoItem.cs` 和更新的檔案 `Pages/Todo.razor` 。 在命令列介面中，應用程式會在儲存檔案時自動重建。 瀏覽器會暫時失去其與應用程式的連線，然後在重新建立連接時重載頁面。

1. 當 **`Add todo`** 選取按鈕時，不會發生任何事，因為事件處理常式並未附加至按鈕。

1. 將 `AddTodo` 方法新增至 `Todo` 元件，並使用屬性註冊按鈕的方法 `@onclick` 。 當選取按鈕時，就會呼叫 `AddTodo` C# 方法：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo4.razor?highlight=2,7-10)]

   ::: moniker-end

1. 若要取得新待辦事項的標題，請 `newTodo` 在區塊頂端加入字串欄位 `@code` ：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo5.razor?highlight=3)]

   ::: moniker-end

   修改要與屬性系結的 text `<input>` 元素 `newTodo` `@bind` ：

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. 更新 `AddTodo` 方法，將 `TodoItem` 與指定的標題新增至清單。 請將 `newTodo` 設定為空字串，以清除文字輸入的值：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo6.razor?highlight=19-26)]

   ::: moniker-end

1. 儲存 `Pages/Todo.razor` 檔案。 應用程式會在命令 shell 中自動重建。 當瀏覽器重新連接至應用程式之後，瀏覽器中的頁面會重載。

1. 每個待辦事項的標題文字都可設定為可編輯，而核取方塊則可協助使用者記錄已完成的項目。 請為每個待辦事項新增核取方塊輸入，然後將其值繫結至 `IsDone` 屬性。 變更 `@todo.Title` 為系結 `<input>` 至 `todo.Title` 的元素 `@bind` ：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo7.razor?name=snippet&highlight=4-7)]

   ::: moniker-end

1. 更新 `<h3>` 標頭，以顯示未)  (完成的待辦事項數目計數 `IsDone` `false` 。

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. 完成的 `Todo` 元件 (`Pages/Todo.razor`) ：

   ::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

   ::: moniker range="< aspnetcore-5.0"

   [!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/build-a-blazor-app/Todo1.razor)]

   ::: moniker-end

1. 儲存 `Pages/Todo.razor` 檔案。 應用程式會在命令 shell 中自動重建。 當瀏覽器重新連接至應用程式之後，瀏覽器中的頁面會重載。

1. 加入專案、編輯專案，以及標記完成的待辦事項以測試元件。

1. 完成時，請在命令 shell 中關閉應用程式。 許多命令 shell 都接受鍵盤命令<kbd>Ctrl</kbd> + <kbd>c</kbd>以停止應用程式。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立待辦事項清單 Blazor 應用程式專案
> * 修改 Razor 元件
> * 在元件中使用事件處理和資料系結
> * 在應用程式中使用路由 Blazor

瞭解 ASP.NET Core 的工具 Blazor ：

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
