---
title: 使用 ASP.NET Core 與 MongoDB 建立 Web API
author: prkhandelwal
description: 此教學課程示範如何使用 MongoDB NoSQL 資料庫建立 ASP.NET Core Web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mongo-app
ms.openlocfilehash: 350df417886fe1ea5fef89dc221c217d596768b3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060738"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="d2496-103">使用 ASP.NET Core 與 MongoDB 建立 Web API</span><span class="sxs-lookup"><span data-stu-id="d2496-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="d2496-104">作者：[Pratik Khandelwal](https://twitter.com/K2Prk) 與 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d2496-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d2496-105">此教學課程會建立 Web API，此 API 會在 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 資料庫上執行建立、讀取、更新及刪除 (CRUD) 作業。</span><span class="sxs-lookup"><span data-stu-id="d2496-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="d2496-106">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="d2496-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d2496-107">設定 MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-107">Configure MongoDB</span></span>
> * <span data-ttu-id="d2496-108">建立 MongoDB 資料庫</span><span class="sxs-lookup"><span data-stu-id="d2496-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="d2496-109">定義 MongoDB 集合與結構描述</span><span class="sxs-lookup"><span data-stu-id="d2496-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="d2496-110">從 Web API 執行 MongoDB CRUD 作業</span><span class="sxs-lookup"><span data-stu-id="d2496-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="d2496-111">自訂 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="d2496-111">Customize JSON serialization</span></span>

<span data-ttu-id="d2496-112">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d2496-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d2496-113">必要條件</span><span class="sxs-lookup"><span data-stu-id="d2496-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2496-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2496-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="d2496-115">.NET Core SDK 3.0 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d2496-115">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="d2496-116">**ASP.NET 和 網頁程式開發** 工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="d2496-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="d2496-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2496-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2496-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="d2496-119">.NET Core SDK 3.0 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d2496-119">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d2496-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2496-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="d2496-121">C# for Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2496-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="d2496-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2496-123">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d2496-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d2496-124">.NET Core SDK 3.0 或更新版本</span><span class="sxs-lookup"><span data-stu-id="d2496-124">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d2496-125">Visual Studio for Mac 7.7 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="d2496-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="d2496-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="d2496-127">設定 MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-127">Configure MongoDB</span></span>

<span data-ttu-id="d2496-128">若使用 Windows，MongoDB 預設會安裝在 *C:\\Program Files\\MongoDB* 。</span><span class="sxs-lookup"><span data-stu-id="d2496-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="d2496-129">將 *C： \\ Program Files \\ MongoDB \\ 伺服器 \\ \<version_number> \\ bin* 新增至 `Path` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="d2496-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="d2496-130">此變更會啟用從您開發機器上的任意位置存取 MongoDB 的功能。</span><span class="sxs-lookup"><span data-stu-id="d2496-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="d2496-131">在下列步驟中使用 mongo 殼層來建立資料庫、建立集合及存放文件。</span><span class="sxs-lookup"><span data-stu-id="d2496-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="d2496-132">如需有關 mongo 殼層命令的詳細資訊，請參閱[使用 mongo 殼層](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="d2496-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="d2496-133">選擇您開發機器上的目錄來存放資料。</span><span class="sxs-lookup"><span data-stu-id="d2496-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="d2496-134">例如， Windows 上的 *C:\\BooksData* 。</span><span class="sxs-lookup"><span data-stu-id="d2496-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="d2496-135">若該目錄不存在，請建立它。</span><span class="sxs-lookup"><span data-stu-id="d2496-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="d2496-136">mongo 殼層不會建立新目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="d2496-137">開啟命令殼層。</span><span class="sxs-lookup"><span data-stu-id="d2496-137">Open a command shell.</span></span> <span data-ttu-id="d2496-138">執行下列命令以連線到預設連接埠 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="d2496-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="d2496-139">請記得將 `<data_directory_path>` 取代為您在上一個步驟中選擇的目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="d2496-140">開啟另一個命令殼層執行個體。</span><span class="sxs-lookup"><span data-stu-id="d2496-140">Open another command shell instance.</span></span> <span data-ttu-id="d2496-141">執行下列命令以連線到預設測試資料庫：</span><span class="sxs-lookup"><span data-stu-id="d2496-141">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="d2496-142">在命令殼層中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="d2496-142">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="d2496-143">若它不存在，會建立名為 *BookstoreDb* 的資料庫。</span><span class="sxs-lookup"><span data-stu-id="d2496-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="d2496-144">若該資料庫存在，會開啟其連線進行交易。</span><span class="sxs-lookup"><span data-stu-id="d2496-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="d2496-145">使用下列命令建立 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="d2496-145">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="d2496-146">會顯示下列結果：</span><span class="sxs-lookup"><span data-stu-id="d2496-146">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="d2496-147">使用下列命令為 `Books` 集合定義結構描述並插入兩份文件：</span><span class="sxs-lookup"><span data-stu-id="d2496-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="d2496-148">會顯示下列結果：</span><span class="sxs-lookup"><span data-stu-id="d2496-148">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="d2496-149">您執行此範例時的識別碼，將不同於本文中顯示的識別碼。</span><span class="sxs-lookup"><span data-stu-id="d2496-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="d2496-150">使用下列命令檢視資料庫中的文件：</span><span class="sxs-lookup"><span data-stu-id="d2496-150">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="d2496-151">會顯示下列結果：</span><span class="sxs-lookup"><span data-stu-id="d2496-151">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="d2496-152">結構描述會為每份文件新增自動彙總的 `_id` 屬性 (類型 `ObjectId`)。</span><span class="sxs-lookup"><span data-stu-id="d2496-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="d2496-153">資料庫已就緒。</span><span class="sxs-lookup"><span data-stu-id="d2496-153">The database is ready.</span></span> <span data-ttu-id="d2496-154">您可以開始建立 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="d2496-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="d2496-155">建立 ASP.NET Core Web API 專案</span><span class="sxs-lookup"><span data-stu-id="d2496-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2496-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2496-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d2496-157">移至 **[** > **新增** > **專案** ]。</span><span class="sxs-lookup"><span data-stu-id="d2496-157">Go to **File** > **New** > **Project** .</span></span>
1. <span data-ttu-id="d2496-158">選取 [ASP.NET Core Web 應用程式]  專案類型，然後選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-158">Select the **ASP.NET Core Web Application** project type, and select **Next** .</span></span>
1. <span data-ttu-id="d2496-159">將專案命名為  。</span><span class="sxs-lookup"><span data-stu-id="d2496-159">Name the project *BooksApi* , and select **Create** .</span></span>
1. <span data-ttu-id="d2496-160">選取 [.NET Core]  目標架構與 [ASP.NET Core 3.0]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-160">Select the **.NET Core** target framework and **ASP.NET Core 3.0** .</span></span> <span data-ttu-id="d2496-161">選取 [API]  專案範本，然後選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-161">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="d2496-162">流覽 [NuGet 資源庫： mongodb](https://www.nuget.org/packages/MongoDB.Driver/) ，以判斷 .net Driver for mongodb 的最新穩定版本。</span><span class="sxs-lookup"><span data-stu-id="d2496-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d2496-163">在 [套件管理員主控台]  視窗中，瀏覽到專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="d2496-164">執行下列命令以安裝適用於 MongoDB 的 .NET 驅動程式：</span><span class="sxs-lookup"><span data-stu-id="d2496-164">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2496-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2496-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d2496-166">在命令殼層中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="d2496-166">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="d2496-167">會產生以 .NET Core 為目標的新 ASP.NET Core Web API 專案，並在 Visual Studio Code 中開啟。</span><span class="sxs-lookup"><span data-stu-id="d2496-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="d2496-168">在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' >booksapi ' 中找不到建立和偵測所需的資產。加入它們？** 。</span><span class="sxs-lookup"><span data-stu-id="d2496-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?** .</span></span> <span data-ttu-id="d2496-169">選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="d2496-169">Select **Yes** .</span></span>
1. <span data-ttu-id="d2496-170">流覽 [NuGet 資源庫： mongodb](https://www.nuget.org/packages/MongoDB.Driver/) ，以判斷 .net Driver for mongodb 的最新穩定版本。</span><span class="sxs-lookup"><span data-stu-id="d2496-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d2496-171">開啟 [整合式終端機]  並瀏覽到專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="d2496-172">執行下列命令以安裝適用於 MongoDB 的 .NET 驅動程式：</span><span class="sxs-lookup"><span data-stu-id="d2496-172">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2496-173">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d2496-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d2496-174">在8.6 版之前的 Visual Studio for Mac 中， **File**  >  **New Solution**  >  從側邊欄選取 [將新的解決方案 **.net Core**  >  **應用程式** 新增]。</span><span class="sxs-lookup"><span data-stu-id="d2496-174">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="d2496-175">在8.6 版或更新版本中 **File** ，  >  **New Solution**  >  從側邊欄選取 [檔案新的方案 **Web] 和 [主控台**  >  **應用程式** ]。</span><span class="sxs-lookup"><span data-stu-id="d2496-175">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="d2496-176">選取 [ **ASP.NET Core** > **API** c #] 專案範本，然後選取 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="d2496-176">Select the **ASP.NET Core** > **API** C# project template, and select **Next** .</span></span>
1. <span data-ttu-id="d2496-177">從 [ **目標 Framework** ] 下拉式清單中選取 [ **.Net Core 3.1** ]，然後選取 **[下一步]** 。</span><span class="sxs-lookup"><span data-stu-id="d2496-177">Select **.NET Core 3.1** from the **Target Framework** drop-down list, and select **Next** .</span></span>
1. <span data-ttu-id="d2496-178">在 [專案名稱]  中輸入  。</span><span class="sxs-lookup"><span data-stu-id="d2496-178">Enter *BooksApi* for the **Project Name** , and select **Create** .</span></span>
1. <span data-ttu-id="d2496-179">在 [方案]  台中，以滑鼠右鍵按一下專案的 [相依性]  節點並選取 [新增封裝]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-179">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages** .</span></span>
1. <span data-ttu-id="d2496-180">在搜尋方塊中輸入  。</span><span class="sxs-lookup"><span data-stu-id="d2496-180">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package** .</span></span>
1. <span data-ttu-id="d2496-181">選取 [授權接受]  對話方塊中的 [接受]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="d2496-181">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="d2496-182">新增實體模型</span><span class="sxs-lookup"><span data-stu-id="d2496-182">Add an entity model</span></span>

1. <span data-ttu-id="d2496-183">新增 *Models* 目錄到專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-183">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="d2496-184">新增具有下列程式碼的 `Book` 類別到 *Models* 目錄：</span><span class="sxs-lookup"><span data-stu-id="d2496-184">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="d2496-185">在上面的類別中，需要 `Id` 屬性：</span><span class="sxs-lookup"><span data-stu-id="d2496-185">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="d2496-186">才能將通用語言執行平台 (CLR) 物件對應到 MongoDB 集合。</span><span class="sxs-lookup"><span data-stu-id="d2496-186">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="d2496-187">已標注， [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 以將此屬性指定為檔的主鍵。</span><span class="sxs-lookup"><span data-stu-id="d2496-187">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="d2496-188">的批註為， [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 以允許將參數傳遞為類型， `string` 而不是 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 結構。</span><span class="sxs-lookup"><span data-stu-id="d2496-188">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="d2496-189">Mongo 會處理從 `string` 轉換到 `ObjectId` 的作業。</span><span class="sxs-lookup"><span data-stu-id="d2496-189">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="d2496-190">`BookName`屬性是以屬性（attribute）標注 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 。</span><span class="sxs-lookup"><span data-stu-id="d2496-190">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="d2496-191">`Name` 的屬性值代表 MongoDB 集合中的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="d2496-191">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="d2496-192">新增組態模型</span><span class="sxs-lookup"><span data-stu-id="d2496-192">Add a configuration model</span></span>

1. <span data-ttu-id="d2496-193">將下列資料庫設定值新增至 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d2496-193">Add the following database configuration values to *appsettings.json* :</span></span>

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="d2496-194">使用下列程式碼將 *BookstoreDatabaseSettings.cs* 檔案新增至 *Models* 目錄：</span><span class="sxs-lookup"><span data-stu-id="d2496-194">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="d2496-195">上述 `BookstoreDatabaseSettings` 類別是用來儲存檔案的 *appsettings.json* `BookstoreDatabaseSettings` 屬性值。</span><span class="sxs-lookup"><span data-stu-id="d2496-195">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="d2496-196">JSON 和 C# 屬性名稱以相同方式命名，以簡化對應程序。</span><span class="sxs-lookup"><span data-stu-id="d2496-196">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="d2496-197">將下列醒目提示的程式碼新增至 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="d2496-197">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   <span data-ttu-id="d2496-198">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="d2496-198">In the preceding code:</span></span>

   * <span data-ttu-id="d2496-199">檔案區段系結的設定實例 *appsettings.json* `BookstoreDatabaseSettings` ，會在相依性插入 (DI) 容器中註冊。</span><span class="sxs-lookup"><span data-stu-id="d2496-199">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="d2496-200">例如， `BookstoreDatabaseSettings` 物件的 `ConnectionString` 屬性會填入 `BookstoreDatabaseSettings:ConnectionString` 中的屬性 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d2496-200">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json* .</span></span>
   * <span data-ttu-id="d2496-201">`IBookstoreDatabaseSettings` 介面使用 singleton [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中註冊。</span><span class="sxs-lookup"><span data-stu-id="d2496-201">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d2496-202">插入時，介面執行個體會解析成 `BookstoreDatabaseSettings` 物件。</span><span class="sxs-lookup"><span data-stu-id="d2496-202">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="d2496-203">在 *Startup.cs* 的頂端新增下列程式碼，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 參考：</span><span class="sxs-lookup"><span data-stu-id="d2496-203">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="d2496-204">新增 CRUD 作業服務</span><span class="sxs-lookup"><span data-stu-id="d2496-204">Add a CRUD operations service</span></span>

1. <span data-ttu-id="d2496-205">新增 *Services* 目錄到專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-205">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="d2496-206">新增具有下列程式碼的 `BookService` 類別到 *Services* 目錄：</span><span class="sxs-lookup"><span data-stu-id="d2496-206">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="d2496-207">在上述程式碼中，透過建構函式插入從 DI 擷取 `IBookstoreDatabaseSettings` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d2496-207">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="d2496-208">這項技術可讓您存取加入設定 *appsettings.json* [模型](#add-a-configuration-model) 一節中新增的設定值。</span><span class="sxs-lookup"><span data-stu-id="d2496-208">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="d2496-209">將下列醒目提示的程式碼新增至 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="d2496-209">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="d2496-210">在上述程式碼中，`BookService` 類別要向 DI 註冊才能在取用類別中支援建構函式插入。</span><span class="sxs-lookup"><span data-stu-id="d2496-210">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="d2496-211">因為 `BookService` 直接依存於 `MongoClient`，所以 Singleton 服務存留期最適合。</span><span class="sxs-lookup"><span data-stu-id="d2496-211">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="d2496-212">根據官方 [Mongo 用戶端重複使用指導方針](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)， `MongoClient` 應該在具有單一服務存留期的 DI 中註冊。</span><span class="sxs-lookup"><span data-stu-id="d2496-212">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="d2496-213">在 *Startup.cs* 的頂端新增下列程式碼，以解析 `BookService` 參考：</span><span class="sxs-lookup"><span data-stu-id="d2496-213">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="d2496-214">`BookService` 類別使用下列 `MongoDB.Driver` 成員來對資料庫執行 CRUD 作業：</span><span class="sxs-lookup"><span data-stu-id="d2496-214">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="d2496-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：讀取用於執行資料庫作業的伺服器實例。</span><span class="sxs-lookup"><span data-stu-id="d2496-215">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="d2496-216">此類別的建構函式是使用 MongoDB 連接字串提供：</span><span class="sxs-lookup"><span data-stu-id="d2496-216">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="d2496-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：代表用來執行作業的 Mongo 資料庫。</span><span class="sxs-lookup"><span data-stu-id="d2496-217">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="d2496-218">本教學課程在介面中使用一般的 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法來存取特定集合中的資料。</span><span class="sxs-lookup"><span data-stu-id="d2496-218">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="d2496-219">呼叫此方法之後，針對集合執行 CRUD 作業。</span><span class="sxs-lookup"><span data-stu-id="d2496-219">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="d2496-220">在 `GetCollection<TDocument>(collection)` 方法呼叫中：</span><span class="sxs-lookup"><span data-stu-id="d2496-220">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="d2496-221">`collection` 代表集合名稱。</span><span class="sxs-lookup"><span data-stu-id="d2496-221">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="d2496-222">`TDocument` 代表儲存在集合中的 CLR 物件類型。</span><span class="sxs-lookup"><span data-stu-id="d2496-222">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="d2496-223">`GetCollection<TDocument>(collection)` 傳回代表集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 物件。</span><span class="sxs-lookup"><span data-stu-id="d2496-223">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="d2496-224">在此教學課程中，會在集合上叫用下列方法：</span><span class="sxs-lookup"><span data-stu-id="d2496-224">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="d2496-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：刪除符合所提供搜尋條件的單一檔。</span><span class="sxs-lookup"><span data-stu-id="d2496-225">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="d2496-226">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：傳回集合中符合所提供搜尋條件的所有檔。</span><span class="sxs-lookup"><span data-stu-id="d2496-226">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="d2496-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：將提供的物件插入至集合中的新檔。</span><span class="sxs-lookup"><span data-stu-id="d2496-227">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="d2496-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：以提供的物件取代符合所提供搜尋條件的單一檔。</span><span class="sxs-lookup"><span data-stu-id="d2496-228">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="d2496-229">新增控制器</span><span class="sxs-lookup"><span data-stu-id="d2496-229">Add a controller</span></span>

<span data-ttu-id="d2496-230">新增具有下列程式碼的 `BooksController` 類別到 *Controllers* 目錄：</span><span class="sxs-lookup"><span data-stu-id="d2496-230">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="d2496-231">上述 Web API 控制器會：</span><span class="sxs-lookup"><span data-stu-id="d2496-231">The preceding web API controller:</span></span>

* <span data-ttu-id="d2496-232">使用 `BookService` 類別來執行 CRUD 作業。</span><span class="sxs-lookup"><span data-stu-id="d2496-232">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="d2496-233">包含動作方法以支援 GET、POST、PUT 與 DELETE HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="d2496-233">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="d2496-234">在 `Create` 動作方法中呼叫 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以傳回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 回應。</span><span class="sxs-lookup"><span data-stu-id="d2496-234">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="d2496-235">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是狀態碼 201。</span><span class="sxs-lookup"><span data-stu-id="d2496-235">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="d2496-236">`CreatedAtRoute` 也會將 `Location` 標頭新增至回應。</span><span class="sxs-lookup"><span data-stu-id="d2496-236">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="d2496-237">`Location` 標頭指定新建活頁簿的 URI。</span><span class="sxs-lookup"><span data-stu-id="d2496-237">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="d2496-238">測試 Web API</span><span class="sxs-lookup"><span data-stu-id="d2496-238">Test the web API</span></span>

1. <span data-ttu-id="d2496-239">建置並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d2496-239">Build and run the app.</span></span>

1. <span data-ttu-id="d2496-240">巡覽至 `http://localhost:<port>/api/books`，測試控制器的無參數 `Get` 動作方法。</span><span class="sxs-lookup"><span data-stu-id="d2496-240">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="d2496-241">會顯示下列 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="d2496-241">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="d2496-242">巡覽至 `http://localhost:<port>/api/books/{id here}`，測試控制器的多載 `Get` 動作方法。</span><span class="sxs-lookup"><span data-stu-id="d2496-242">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="d2496-243">會顯示下列 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="d2496-243">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="d2496-244">設定 JSON 序列化選項</span><span class="sxs-lookup"><span data-stu-id="d2496-244">Configure JSON serialization options</span></span>

<span data-ttu-id="d2496-245">[測試 Web API](#test-the-web-api) 區段中傳回的 JSON 回應，有兩個詳細資訊要變更：</span><span class="sxs-lookup"><span data-stu-id="d2496-245">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="d2496-246">屬性名稱的預設駝峰式命名法大小寫應予變更，使其符合CLR 物件屬性名稱的 Pascal 命名法大小寫。</span><span class="sxs-lookup"><span data-stu-id="d2496-246">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="d2496-247">`bookName` 屬性應傳回為 `Name`。</span><span class="sxs-lookup"><span data-stu-id="d2496-247">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="d2496-248">為滿足上述需求，請進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="d2496-248">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="d2496-249">已從 ASP.NET 共用架構移除 JSON.NET。</span><span class="sxs-lookup"><span data-stu-id="d2496-249">JSON.NET has been removed from ASP.NET shared framework.</span></span> <span data-ttu-id="d2496-250">將封裝參考加入至 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) 。</span><span class="sxs-lookup"><span data-stu-id="d2496-250">Add a package reference to [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson).</span></span>

1. <span data-ttu-id="d2496-251">在 `Startup.ConfigureServices` 中，將下列醒目提示的程式碼鏈結至 `AddControllers` 方法呼叫：</span><span class="sxs-lookup"><span data-stu-id="d2496-251">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddControllers` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="d2496-252">完成前述變更後，Web API 序列化 JSON 回應中屬性名稱即符合其對應的 CLR 物件類型屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="d2496-252">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="d2496-253">例如，`Book` 類別的 `Author` 屬性會序列化為 `Author`。</span><span class="sxs-lookup"><span data-stu-id="d2496-253">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="d2496-254">在 *模型/Book* 中，以 `BookName` 下列屬性標注屬性 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) ：</span><span class="sxs-lookup"><span data-stu-id="d2496-254">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="d2496-255">`[JsonProperty]` 的屬性值 `Name` 代表 Web API 序列化 JSON 回應中的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="d2496-255">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="d2496-256">在 *Models/Book.cs* 的頂端新增下列程式碼，以解析 `[JsonProperty]` 屬性參考：</span><span class="sxs-lookup"><span data-stu-id="d2496-256">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="d2496-257">重複[測試 Web API](#test-the-web-api) 一節中定義的步驟。</span><span class="sxs-lookup"><span data-stu-id="d2496-257">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="d2496-258">請注意 JSON 屬性名稱中的差異。</span><span class="sxs-lookup"><span data-stu-id="d2496-258">Notice the difference in JSON property names.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d2496-259">此教學課程會建立 Web API，此 API 會在 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 資料庫上執行建立、讀取、更新及刪除 (CRUD) 作業。</span><span class="sxs-lookup"><span data-stu-id="d2496-259">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="d2496-260">在本教學課程中，您會了解如何：</span><span class="sxs-lookup"><span data-stu-id="d2496-260">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d2496-261">設定 MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-261">Configure MongoDB</span></span>
> * <span data-ttu-id="d2496-262">建立 MongoDB 資料庫</span><span class="sxs-lookup"><span data-stu-id="d2496-262">Create a MongoDB database</span></span>
> * <span data-ttu-id="d2496-263">定義 MongoDB 集合與結構描述</span><span class="sxs-lookup"><span data-stu-id="d2496-263">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="d2496-264">從 Web API 執行 MongoDB CRUD 作業</span><span class="sxs-lookup"><span data-stu-id="d2496-264">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="d2496-265">自訂 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="d2496-265">Customize JSON serialization</span></span>

<span data-ttu-id="d2496-266">[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([如何下載](xref:index#how-to-download-a-sample)) </span><span class="sxs-lookup"><span data-stu-id="d2496-266">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d2496-267">必要條件</span><span class="sxs-lookup"><span data-stu-id="d2496-267">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2496-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2496-268">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="d2496-269">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="d2496-269">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="d2496-270">**ASP.NET 和 網頁程式開發** 工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="d2496-270">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="d2496-271">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-271">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2496-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2496-272">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="d2496-273">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="d2496-273">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d2496-274">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2496-274">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="d2496-275">C# for Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2496-275">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [<span data-ttu-id="d2496-276">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-276">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2496-277">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d2496-277">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="d2496-278">.NET Core SDK 2.2</span><span class="sxs-lookup"><span data-stu-id="d2496-278">.NET Core SDK 2.2</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="d2496-279">Visual Studio for Mac 7.7 版或更新版本</span><span class="sxs-lookup"><span data-stu-id="d2496-279">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="d2496-280">MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-280">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="d2496-281">設定 MongoDB</span><span class="sxs-lookup"><span data-stu-id="d2496-281">Configure MongoDB</span></span>

<span data-ttu-id="d2496-282">若使用 Windows，MongoDB 預設會安裝在 *C:\\Program Files\\MongoDB* 。</span><span class="sxs-lookup"><span data-stu-id="d2496-282">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="d2496-283">將 *C： \\ Program Files \\ MongoDB \\ 伺服器 \\ \<version_number> \\ bin* 新增至 `Path` 環境變數。</span><span class="sxs-lookup"><span data-stu-id="d2496-283">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="d2496-284">此變更會啟用從您開發機器上的任意位置存取 MongoDB 的功能。</span><span class="sxs-lookup"><span data-stu-id="d2496-284">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="d2496-285">在下列步驟中使用 mongo 殼層來建立資料庫、建立集合及存放文件。</span><span class="sxs-lookup"><span data-stu-id="d2496-285">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="d2496-286">如需有關 mongo 殼層命令的詳細資訊，請參閱[使用 mongo 殼層](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。</span><span class="sxs-lookup"><span data-stu-id="d2496-286">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="d2496-287">選擇您開發機器上的目錄來存放資料。</span><span class="sxs-lookup"><span data-stu-id="d2496-287">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="d2496-288">例如， Windows 上的 *C:\\BooksData* 。</span><span class="sxs-lookup"><span data-stu-id="d2496-288">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="d2496-289">若該目錄不存在，請建立它。</span><span class="sxs-lookup"><span data-stu-id="d2496-289">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="d2496-290">mongo 殼層不會建立新目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-290">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="d2496-291">開啟命令殼層。</span><span class="sxs-lookup"><span data-stu-id="d2496-291">Open a command shell.</span></span> <span data-ttu-id="d2496-292">執行下列命令以連線到預設連接埠 27017 上的 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="d2496-292">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="d2496-293">請記得將 `<data_directory_path>` 取代為您在上一個步驟中選擇的目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-293">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. <span data-ttu-id="d2496-294">開啟另一個命令殼層執行個體。</span><span class="sxs-lookup"><span data-stu-id="d2496-294">Open another command shell instance.</span></span> <span data-ttu-id="d2496-295">執行下列命令以連線到預設測試資料庫：</span><span class="sxs-lookup"><span data-stu-id="d2496-295">Connect to the default test database by running the following command:</span></span>

   ```console
   mongo
   ```

1. <span data-ttu-id="d2496-296">在命令殼層中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="d2496-296">Run the following in a command shell:</span></span>

   ```console
   use BookstoreDb
   ```

   <span data-ttu-id="d2496-297">若它不存在，會建立名為 *BookstoreDb* 的資料庫。</span><span class="sxs-lookup"><span data-stu-id="d2496-297">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="d2496-298">若該資料庫存在，會開啟其連線進行交易。</span><span class="sxs-lookup"><span data-stu-id="d2496-298">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="d2496-299">使用下列命令建立 `Books` 集合：</span><span class="sxs-lookup"><span data-stu-id="d2496-299">Create a `Books` collection using following command:</span></span>

   ```console
   db.createCollection('Books')
   ```

   <span data-ttu-id="d2496-300">會顯示下列結果：</span><span class="sxs-lookup"><span data-stu-id="d2496-300">The following result is displayed:</span></span>

   ```console
   { "ok" : 1 }
   ```

1. <span data-ttu-id="d2496-301">使用下列命令為 `Books` 集合定義結構描述並插入兩份文件：</span><span class="sxs-lookup"><span data-stu-id="d2496-301">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   <span data-ttu-id="d2496-302">會顯示下列結果：</span><span class="sxs-lookup"><span data-stu-id="d2496-302">The following result is displayed:</span></span>

   ```console
   {
     "acknowledged" : true,
     "insertedIds" : [
       ObjectId("5bfd996f7b8e48dc15ff215d"),
       ObjectId("5bfd996f7b8e48dc15ff215e")
     ]
   }
   ```
  
   > [!NOTE]
   > <span data-ttu-id="d2496-303">您執行此範例時的識別碼，將不同於本文中顯示的識別碼。</span><span class="sxs-lookup"><span data-stu-id="d2496-303">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="d2496-304">使用下列命令檢視資料庫中的文件：</span><span class="sxs-lookup"><span data-stu-id="d2496-304">View the documents in the database using the following command:</span></span>

   ```console
   db.Books.find({}).pretty()
   ```

   <span data-ttu-id="d2496-305">會顯示下列結果：</span><span class="sxs-lookup"><span data-stu-id="d2496-305">The following result is displayed:</span></span>

   ```console
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
     "Name" : "Design Patterns",
     "Price" : 54.93,
     "Category" : "Computers",
     "Author" : "Ralph Johnson"
   }
   {
     "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
     "Name" : "Clean Code",
     "Price" : 43.15,
     "Category" : "Computers",
     "Author" : "Robert C. Martin"
   }
   ```

   <span data-ttu-id="d2496-306">結構描述會為每份文件新增自動彙總的 `_id` 屬性 (類型 `ObjectId`)。</span><span class="sxs-lookup"><span data-stu-id="d2496-306">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="d2496-307">資料庫已就緒。</span><span class="sxs-lookup"><span data-stu-id="d2496-307">The database is ready.</span></span> <span data-ttu-id="d2496-308">您可以開始建立 ASP.NET Core Web API。</span><span class="sxs-lookup"><span data-stu-id="d2496-308">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="d2496-309">建立 ASP.NET Core Web API 專案</span><span class="sxs-lookup"><span data-stu-id="d2496-309">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d2496-310">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d2496-310">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d2496-311">移至 **[** > **新增** > **專案** ]。</span><span class="sxs-lookup"><span data-stu-id="d2496-311">Go to **File** > **New** > **Project** .</span></span>
1. <span data-ttu-id="d2496-312">選取 [ASP.NET Core Web 應用程式]  專案類型，然後選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-312">Select the **ASP.NET Core Web Application** project type, and select **Next** .</span></span>
1. <span data-ttu-id="d2496-313">將專案命名為  。</span><span class="sxs-lookup"><span data-stu-id="d2496-313">Name the project *BooksApi* , and select **Create** .</span></span>
1. <span data-ttu-id="d2496-314">選取 [.NET Core]  目標架構與 [ASP.NET Core 2.2]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-314">Select the **.NET Core** target framework and **ASP.NET Core 2.2** .</span></span> <span data-ttu-id="d2496-315">選取 [API]  專案範本，然後選取 [確定]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-315">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="d2496-316">流覽 [NuGet 資源庫： mongodb](https://www.nuget.org/packages/MongoDB.Driver/) ，以判斷 .net Driver for mongodb 的最新穩定版本。</span><span class="sxs-lookup"><span data-stu-id="d2496-316">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d2496-317">在 [套件管理員主控台]  視窗中，瀏覽到專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-317">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="d2496-318">執行下列命令以安裝適用於 MongoDB 的 .NET 驅動程式：</span><span class="sxs-lookup"><span data-stu-id="d2496-318">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[<span data-ttu-id="d2496-319">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d2496-319">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d2496-320">在命令殼層中執行下列命令：</span><span class="sxs-lookup"><span data-stu-id="d2496-320">Run the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   <span data-ttu-id="d2496-321">會產生以 .NET Core 為目標的新 ASP.NET Core Web API 專案，並在 Visual Studio Code 中開啟。</span><span class="sxs-lookup"><span data-stu-id="d2496-321">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="d2496-322">在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' >booksapi ' 中找不到建立和偵測所需的資產。加入它們？** 。</span><span class="sxs-lookup"><span data-stu-id="d2496-322">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?** .</span></span> <span data-ttu-id="d2496-323">選取 [是]。</span><span class="sxs-lookup"><span data-stu-id="d2496-323">Select **Yes** .</span></span>
1. <span data-ttu-id="d2496-324">流覽 [NuGet 資源庫： mongodb](https://www.nuget.org/packages/MongoDB.Driver/) ，以判斷 .net Driver for mongodb 的最新穩定版本。</span><span class="sxs-lookup"><span data-stu-id="d2496-324">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="d2496-325">開啟 [整合式終端機]  並瀏覽到專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-325">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="d2496-326">執行下列命令以安裝適用於 MongoDB 的 .NET 驅動程式：</span><span class="sxs-lookup"><span data-stu-id="d2496-326">Run the following command to install the .NET driver for MongoDB:</span></span>

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d2496-327">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d2496-327">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d2496-328">在8.6 版之前的 Visual Studio for Mac 中， **File**  >  **New Solution**  >  從側邊欄選取 [將新的解決方案 **.net Core**  >  **應用程式** 新增]。</span><span class="sxs-lookup"><span data-stu-id="d2496-328">In Visual Studio for Mac earlier than version 8.6, select **File** > **New Solution** > **.NET Core** > **App** from the sidebar.</span></span> <span data-ttu-id="d2496-329">在8.6 版或更新版本中 **File** ，  >  **New Solution**  >  從側邊欄選取 [檔案新的方案 **Web] 和 [主控台**  >  **應用程式** ]。</span><span class="sxs-lookup"><span data-stu-id="d2496-329">In version 8.6 or later, select **File** > **New Solution** > **Web and Console** > **App** from the sidebar.</span></span>
1. <span data-ttu-id="d2496-330">選取 [ASP.NET Core Web API]  C# 專案範本，然後選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-330">Select the **ASP.NET Core Web API** C# project template, and select **Next** .</span></span>
1. <span data-ttu-id="d2496-331">從 [目標 Framework]  下拉式清單選取 [.NET Core 2.2]  ，然後選取 [下一步]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-331">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next** .</span></span>
1. <span data-ttu-id="d2496-332">在 [專案名稱]  中輸入  。</span><span class="sxs-lookup"><span data-stu-id="d2496-332">Enter *BooksApi* for the **Project Name** , and select **Create** .</span></span>
1. <span data-ttu-id="d2496-333">在 [方案]  台中，以滑鼠右鍵按一下專案的 [相依性]  節點並選取 [新增封裝]  。</span><span class="sxs-lookup"><span data-stu-id="d2496-333">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages** .</span></span>
1. <span data-ttu-id="d2496-334">在搜尋方塊中輸入  。</span><span class="sxs-lookup"><span data-stu-id="d2496-334">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package** .</span></span>
1. <span data-ttu-id="d2496-335">選取 [授權接受]  對話方塊中的 [接受]  按鈕。</span><span class="sxs-lookup"><span data-stu-id="d2496-335">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="d2496-336">新增實體模型</span><span class="sxs-lookup"><span data-stu-id="d2496-336">Add an entity model</span></span>

1. <span data-ttu-id="d2496-337">新增 *Models* 目錄到專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-337">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="d2496-338">新增具有下列程式碼的 `Book` 類別到 *Models* 目錄：</span><span class="sxs-lookup"><span data-stu-id="d2496-338">Add a `Book` class to the *Models* directory with the following code:</span></span>

   ```csharp
   using MongoDB.Bson;
   using MongoDB.Bson.Serialization.Attributes;

   namespace BooksApi.Models
   {
       public class Book
       {
           [BsonId]
           [BsonRepresentation(BsonType.ObjectId)]
           public string Id { get; set; }

           [BsonElement("Name")]
           public string BookName { get; set; }

           public decimal Price { get; set; }

           public string Category { get; set; }

           public string Author { get; set; }
       }
   }
   ```

   <span data-ttu-id="d2496-339">在上面的類別中，需要 `Id` 屬性：</span><span class="sxs-lookup"><span data-stu-id="d2496-339">In the preceding class, the `Id` property:</span></span>

   * <span data-ttu-id="d2496-340">才能將通用語言執行平台 (CLR) 物件對應到 MongoDB 集合。</span><span class="sxs-lookup"><span data-stu-id="d2496-340">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
   * <span data-ttu-id="d2496-341">已標注， [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 以將此屬性指定為檔的主鍵。</span><span class="sxs-lookup"><span data-stu-id="d2496-341">Is annotated with [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
   * <span data-ttu-id="d2496-342">的批註為， [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 以允許將參數傳遞為類型， `string` 而不是 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 結構。</span><span class="sxs-lookup"><span data-stu-id="d2496-342">Is annotated with [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="d2496-343">Mongo 會處理從 `string` 轉換到 `ObjectId` 的作業。</span><span class="sxs-lookup"><span data-stu-id="d2496-343">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

   <span data-ttu-id="d2496-344">`BookName`屬性是以屬性（attribute）標注 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 。</span><span class="sxs-lookup"><span data-stu-id="d2496-344">The `BookName` property is annotated with the [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="d2496-345">`Name` 的屬性值代表 MongoDB 集合中的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="d2496-345">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="d2496-346">新增組態模型</span><span class="sxs-lookup"><span data-stu-id="d2496-346">Add a configuration model</span></span>

1. <span data-ttu-id="d2496-347">將下列資料庫設定值新增至 *appsettings.json* ：</span><span class="sxs-lookup"><span data-stu-id="d2496-347">Add the following database configuration values to *appsettings.json* :</span></span>

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="d2496-348">使用下列程式碼將 *BookstoreDatabaseSettings.cs* 檔案新增至 *Models* 目錄：</span><span class="sxs-lookup"><span data-stu-id="d2496-348">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   <span data-ttu-id="d2496-349">上述 `BookstoreDatabaseSettings` 類別是用來儲存檔案的 *appsettings.json* `BookstoreDatabaseSettings` 屬性值。</span><span class="sxs-lookup"><span data-stu-id="d2496-349">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="d2496-350">JSON 和 C# 屬性名稱以相同方式命名，以簡化對應程序。</span><span class="sxs-lookup"><span data-stu-id="d2496-350">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="d2496-351">將下列醒目提示的程式碼新增至 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="d2496-351">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   <span data-ttu-id="d2496-352">在上述程式碼中：</span><span class="sxs-lookup"><span data-stu-id="d2496-352">In the preceding code:</span></span>

   * <span data-ttu-id="d2496-353">檔案區段系結的設定實例 *appsettings.json* `BookstoreDatabaseSettings` ，會在相依性插入 (DI) 容器中註冊。</span><span class="sxs-lookup"><span data-stu-id="d2496-353">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="d2496-354">例如， `BookstoreDatabaseSettings` 物件的 `ConnectionString` 屬性會填入 `BookstoreDatabaseSettings:ConnectionString` 中的屬性 *appsettings.json* 。</span><span class="sxs-lookup"><span data-stu-id="d2496-354">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json* .</span></span>
   * <span data-ttu-id="d2496-355">`IBookstoreDatabaseSettings` 介面使用 singleton [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中註冊。</span><span class="sxs-lookup"><span data-stu-id="d2496-355">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="d2496-356">插入時，介面執行個體會解析成 `BookstoreDatabaseSettings` 物件。</span><span class="sxs-lookup"><span data-stu-id="d2496-356">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="d2496-357">在 *Startup.cs* 的頂端新增下列程式碼，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 參考：</span><span class="sxs-lookup"><span data-stu-id="d2496-357">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="d2496-358">新增 CRUD 作業服務</span><span class="sxs-lookup"><span data-stu-id="d2496-358">Add a CRUD operations service</span></span>

1. <span data-ttu-id="d2496-359">新增 *Services* 目錄到專案根目錄。</span><span class="sxs-lookup"><span data-stu-id="d2496-359">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="d2496-360">新增具有下列程式碼的 `BookService` 類別到 *Services* 目錄：</span><span class="sxs-lookup"><span data-stu-id="d2496-360">Add a `BookService` class to the *Services* directory with the following code:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   <span data-ttu-id="d2496-361">在上述程式碼中，透過建構函式插入從 DI 擷取 `IBookstoreDatabaseSettings` 執行個體。</span><span class="sxs-lookup"><span data-stu-id="d2496-361">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="d2496-362">這項技術可讓您存取加入設定 *appsettings.json* [模型](#add-a-configuration-model) 一節中新增的設定值。</span><span class="sxs-lookup"><span data-stu-id="d2496-362">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="d2496-363">將下列醒目提示的程式碼新增至 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="d2496-363">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   <span data-ttu-id="d2496-364">在上述程式碼中，`BookService` 類別要向 DI 註冊才能在取用類別中支援建構函式插入。</span><span class="sxs-lookup"><span data-stu-id="d2496-364">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="d2496-365">因為 `BookService` 直接依存於 `MongoClient`，所以 Singleton 服務存留期最適合。</span><span class="sxs-lookup"><span data-stu-id="d2496-365">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="d2496-366">根據官方 [Mongo 用戶端重複使用指導方針](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)， `MongoClient` 應該在具有單一服務存留期的 DI 中註冊。</span><span class="sxs-lookup"><span data-stu-id="d2496-366">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="d2496-367">在 *Startup.cs* 的頂端新增下列程式碼，以解析 `BookService` 參考：</span><span class="sxs-lookup"><span data-stu-id="d2496-367">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="d2496-368">`BookService` 類別使用下列 `MongoDB.Driver` 成員來對資料庫執行 CRUD 作業：</span><span class="sxs-lookup"><span data-stu-id="d2496-368">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="d2496-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：讀取用於執行資料庫作業的伺服器實例。</span><span class="sxs-lookup"><span data-stu-id="d2496-369">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Reads the server instance for performing database operations.</span></span> <span data-ttu-id="d2496-370">此類別的建構函式是使用 MongoDB 連接字串提供：</span><span class="sxs-lookup"><span data-stu-id="d2496-370">The constructor of this class is provided the MongoDB connection string:</span></span>

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="d2496-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：代表用來執行作業的 Mongo 資料庫。</span><span class="sxs-lookup"><span data-stu-id="d2496-371">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="d2496-372">本教學課程在介面中使用一般的 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法來存取特定集合中的資料。</span><span class="sxs-lookup"><span data-stu-id="d2496-372">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="d2496-373">呼叫此方法之後，針對集合執行 CRUD 作業。</span><span class="sxs-lookup"><span data-stu-id="d2496-373">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="d2496-374">在 `GetCollection<TDocument>(collection)` 方法呼叫中：</span><span class="sxs-lookup"><span data-stu-id="d2496-374">In the `GetCollection<TDocument>(collection)` method call:</span></span>

  * <span data-ttu-id="d2496-375">`collection` 代表集合名稱。</span><span class="sxs-lookup"><span data-stu-id="d2496-375">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="d2496-376">`TDocument` 代表儲存在集合中的 CLR 物件類型。</span><span class="sxs-lookup"><span data-stu-id="d2496-376">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="d2496-377">`GetCollection<TDocument>(collection)` 傳回代表集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 物件。</span><span class="sxs-lookup"><span data-stu-id="d2496-377">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="d2496-378">在此教學課程中，會在集合上叫用下列方法：</span><span class="sxs-lookup"><span data-stu-id="d2496-378">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="d2496-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：刪除符合所提供搜尋條件的單一檔。</span><span class="sxs-lookup"><span data-stu-id="d2496-379">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="d2496-380">[Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：傳回集合中符合所提供搜尋條件的所有檔。</span><span class="sxs-lookup"><span data-stu-id="d2496-380">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="d2496-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：將提供的物件插入至集合中的新檔。</span><span class="sxs-lookup"><span data-stu-id="d2496-381">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="d2496-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：以提供的物件取代符合所提供搜尋條件的單一檔。</span><span class="sxs-lookup"><span data-stu-id="d2496-382">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="d2496-383">新增控制器</span><span class="sxs-lookup"><span data-stu-id="d2496-383">Add a controller</span></span>

<span data-ttu-id="d2496-384">新增具有下列程式碼的 `BooksController` 類別到 *Controllers* 目錄：</span><span class="sxs-lookup"><span data-stu-id="d2496-384">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

<span data-ttu-id="d2496-385">上述 Web API 控制器會：</span><span class="sxs-lookup"><span data-stu-id="d2496-385">The preceding web API controller:</span></span>

* <span data-ttu-id="d2496-386">使用 `BookService` 類別來執行 CRUD 作業。</span><span class="sxs-lookup"><span data-stu-id="d2496-386">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="d2496-387">包含動作方法以支援 GET、POST、PUT 與 DELETE HTTP 要求。</span><span class="sxs-lookup"><span data-stu-id="d2496-387">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="d2496-388">在 `Create` 動作方法中呼叫 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以傳回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 回應。</span><span class="sxs-lookup"><span data-stu-id="d2496-388">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="d2496-389">對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是狀態碼 201。</span><span class="sxs-lookup"><span data-stu-id="d2496-389">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="d2496-390">`CreatedAtRoute` 也會將 `Location` 標頭新增至回應。</span><span class="sxs-lookup"><span data-stu-id="d2496-390">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="d2496-391">`Location` 標頭指定新建活頁簿的 URI。</span><span class="sxs-lookup"><span data-stu-id="d2496-391">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="d2496-392">測試 Web API</span><span class="sxs-lookup"><span data-stu-id="d2496-392">Test the web API</span></span>

1. <span data-ttu-id="d2496-393">建置並執行應用程式。</span><span class="sxs-lookup"><span data-stu-id="d2496-393">Build and run the app.</span></span>

1. <span data-ttu-id="d2496-394">巡覽至 `http://localhost:<port>/api/books`，測試控制器的無參數 `Get` 動作方法。</span><span class="sxs-lookup"><span data-stu-id="d2496-394">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="d2496-395">會顯示下列 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="d2496-395">The following JSON response is displayed:</span></span>

   ```json
   [
     {
       "id":"5bfd996f7b8e48dc15ff215d",
       "bookName":"Design Patterns",
       "price":54.93,
       "category":"Computers",
       "author":"Ralph Johnson"
     },
     {
       "id":"5bfd996f7b8e48dc15ff215e",
       "bookName":"Clean Code",
       "price":43.15,
       "category":"Computers",
       "author":"Robert C. Martin"
     }
   ]
   ```

1. <span data-ttu-id="d2496-396">巡覽至 `http://localhost:<port>/api/books/{id here}`，測試控制器的多載 `Get` 動作方法。</span><span class="sxs-lookup"><span data-stu-id="d2496-396">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="d2496-397">會顯示下列 JSON 回應：</span><span class="sxs-lookup"><span data-stu-id="d2496-397">The following JSON response is displayed:</span></span>

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="d2496-398">設定 JSON 序列化選項</span><span class="sxs-lookup"><span data-stu-id="d2496-398">Configure JSON serialization options</span></span>

<span data-ttu-id="d2496-399">[測試 Web API](#test-the-web-api) 區段中傳回的 JSON 回應，有兩個詳細資訊要變更：</span><span class="sxs-lookup"><span data-stu-id="d2496-399">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="d2496-400">屬性名稱的預設駝峰式命名法大小寫應予變更，使其符合CLR 物件屬性名稱的 Pascal 命名法大小寫。</span><span class="sxs-lookup"><span data-stu-id="d2496-400">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="d2496-401">`bookName` 屬性應傳回為 `Name`。</span><span class="sxs-lookup"><span data-stu-id="d2496-401">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="d2496-402">為滿足上述需求，請進行下列變更：</span><span class="sxs-lookup"><span data-stu-id="d2496-402">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="d2496-403">在 `Startup.ConfigureServices` 中，將下列醒目提示的程式碼鏈結至 `AddMvc` 方法呼叫：</span><span class="sxs-lookup"><span data-stu-id="d2496-403">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   <span data-ttu-id="d2496-404">完成前述變更後，Web API 序列化 JSON 回應中屬性名稱即符合其對應的 CLR 物件類型屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="d2496-404">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="d2496-405">例如，`Book` 類別的 `Author` 屬性會序列化為 `Author`。</span><span class="sxs-lookup"><span data-stu-id="d2496-405">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="d2496-406">在 *模型/Book* 中，以 `BookName` 下列屬性標注屬性 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) ：</span><span class="sxs-lookup"><span data-stu-id="d2496-406">In *Models/Book.cs* , annotate the `BookName` property with the following [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   <span data-ttu-id="d2496-407">`[JsonProperty]` 的屬性值 `Name` 代表 Web API 序列化 JSON 回應中的屬性名稱。</span><span class="sxs-lookup"><span data-stu-id="d2496-407">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="d2496-408">在 *Models/Book.cs* 的頂端新增下列程式碼，以解析 `[JsonProperty]` 屬性參考：</span><span class="sxs-lookup"><span data-stu-id="d2496-408">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="d2496-409">重複[測試 Web API](#test-the-web-api) 一節中定義的步驟。</span><span class="sxs-lookup"><span data-stu-id="d2496-409">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="d2496-410">請注意 JSON 屬性名稱中的差異。</span><span class="sxs-lookup"><span data-stu-id="d2496-410">Notice the difference in JSON property names.</span></span>

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a><span data-ttu-id="d2496-411">將驗證支援新增至 web API</span><span class="sxs-lookup"><span data-stu-id="d2496-411">Add authentication support to a web API</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a><span data-ttu-id="d2496-412">後續步驟</span><span class="sxs-lookup"><span data-stu-id="d2496-412">Next steps</span></span>

<span data-ttu-id="d2496-413">如需有關建置 ASP.NET Core Web API 的詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="d2496-413">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="d2496-414">本文的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="d2496-414">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
