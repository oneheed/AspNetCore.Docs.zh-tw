---
title: 使用 ASP.NET Core 與 MongoDB 建立 Web API
author: prkhandelwal
description: 此教學課程示範如何使用 MongoDB NoSQL 資料庫建立 ASP.NET Core Web API。
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 08/17/2019
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
uid: tutorials/first-mongo-app
ms.openlocfilehash: 489a15f2011e44ffd9f9bfe7d5410b7d79a10632
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587602"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>使用 ASP.NET Core 與 MongoDB 建立 Web API

作者：[Pratik Khandelwal](https://twitter.com/K2Prk) 與 [Scott Addie](https://twitter.com/Scott_Addie)

::: moniker range=">= aspnetcore-3.0"

此教學課程會建立 Web API，此 API 會在 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 資料庫上執行建立、讀取、更新及刪除 (CRUD) 作業。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定 MongoDB
> * 建立 MongoDB 資料庫
> * 定義 MongoDB 集合與結構描述
> * 從 Web API 執行 MongoDB CRUD 作業
> * 自訂 JSON 序列化

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 3.0 或更新版本](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 與 **ASP.NET 和 網頁程式開發** 工作負載
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 3.0 或更新版本](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET Core SDK 3.0 或更新版本](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio for Mac 7.7 版或更新版本](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>設定 MongoDB

若使用 Windows，MongoDB 預設會安裝在 *C:\\Program Files\\MongoDB*。 將 *C： \\ Program Files \\ MongoDB \\ 伺服器 \\ \<version_number> \\ bin* 新增至 `Path` 環境變數。 此變更會啟用從您開發機器上的任意位置存取 MongoDB 的功能。

在下列步驟中使用 mongo 殼層來建立資料庫、建立集合及存放文件。 如需有關 mongo 殼層命令的詳細資訊，請參閱[使用 mongo 殼層](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。

1. 選擇您開發機器上的目錄來存放資料。 例如， Windows 上的 *C:\\BooksData*。 若該目錄不存在，請建立它。 mongo 殼層不會建立新目錄。
1. 開啟命令殼層。 執行下列命令以連線到預設連接埠 27017 上的 MongoDB。 請記得將 `<data_directory_path>` 取代為您在上一個步驟中選擇的目錄。

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. 開啟另一個命令殼層執行個體。 執行下列命令以連線到預設測試資料庫：

   ```console
   mongo
   ```

1. 在命令殼層中執行下列命令：

   ```console
   use BookstoreDb
   ```

   若它不存在，會建立名為 *BookstoreDb* 的資料庫。 若該資料庫存在，會開啟其連線進行交易。

1. 使用下列命令建立 `Books` 集合：

   ```console
   db.createCollection('Books')
   ```

   會顯示下列結果：

   ```console
   { "ok" : 1 }
   ```

1. 使用下列命令為 `Books` 集合定義結構描述並插入兩份文件：

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   會顯示下列結果：

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
   > 您執行此範例時的識別碼，將不同於本文中顯示的識別碼。

1. 使用下列命令檢視資料庫中的文件：

   ```console
   db.Books.find({}).pretty()
   ```

   會顯示下列結果：

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

   結構描述會為每份文件新增自動彙總的 `_id` 屬性 (類型 `ObjectId`)。

資料庫已就緒。 您可以開始建立 ASP.NET Core Web API。

## <a name="create-the-aspnet-core-web-api-project"></a>建立 ASP.NET Core Web API 專案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 移至 **[** > **新增** > **專案**]。
1. 選取 [ASP.NET Core Web 應用程式] 專案類型，然後選取 [下一步]。
1. 將專案命名為 *BooksApi*，然後選取 [建立]。
1. 選取 [.NET Core] 目標架構與 [ASP.NET Core 3.0]。 選取 [API] 專案範本，然後選取 [確定]。
1. 流覽 [NuGet 資源庫： mongodb](https://www.nuget.org/packages/MongoDB.Driver/) ，以判斷 .net Driver for mongodb 的最新穩定版本。 在 [套件管理員主控台] 視窗中，瀏覽到專案根目錄。 執行下列命令以安裝適用於 MongoDB 的 .NET 驅動程式：

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令殼層中執行下列命令：

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   會產生以 .NET Core 為目標的新 ASP.NET Core Web API 專案，並在 Visual Studio Code 中開啟。

1. 在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' >booksapi ' 中找不到建立和偵測所需的資產。加入它們？**。 選取 [是]  。
1. 流覽 [NuGet 資源庫： mongodb](https://www.nuget.org/packages/MongoDB.Driver/) ，以判斷 .net Driver for mongodb 的最新穩定版本。 開啟 [整合式終端機] 並瀏覽到專案根目錄。 執行下列命令以安裝適用於 MongoDB 的 .NET 驅動程式：

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在8.6 版之前的 Visual Studio for Mac 中，   >    >  從側邊欄選取 [從新的解決方案 **.net Core**  >  **應用程式**]。 在8.6 版或更新版本中，  >    >  從側邊欄選取 [檔案新的方案 **Web] 和 [主控台**  >  **應用程式**]。
1. 選取 [ **ASP.NET Core** > **API** c #] 專案範本，然後選取 **[下一步]**。
1. 從 [**目標 Framework** ] 下拉式清單中選取 [ **.Net Core 3.1** ]，然後選取 **[下一步]**。
1. 在 [專案名稱] 中輸入 *BooksApi*，然後選取 [建立]。
1. 在 [方案] 台中，以滑鼠右鍵按一下專案的 [相依性] 節點並選取 [新增封裝]。
1. 在搜尋方塊中輸入 *MongoDB.Driver*，然後依序選取 *MongoDB.Driver* 套件和 [新增套件]。
1. 選取 [授權接受] 對話方塊中的 [接受] 按鈕。

---

## <a name="add-an-entity-model"></a>新增實體模型

1. 新增 *Models* 目錄到專案根目錄。
1. 新增具有下列程式碼的 `Book` 類別到 *Models* 目錄：

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

   在上面的類別中，需要 `Id` 屬性：

   * 才能將通用語言執行平台 (CLR) 物件對應到 MongoDB 集合。
   * 已標注， [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 以將此屬性指定為檔的主鍵。
   * 的批註為， [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 以允許將參數傳遞為類型， `string` 而不是 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 結構。 Mongo 會處理從 `string` 轉換到 `ObjectId` 的作業。

   `BookName`屬性是以屬性（attribute）標注 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 。 `Name` 的屬性值代表 MongoDB 集合中的屬性名稱。

## <a name="add-a-configuration-model"></a>新增組態模型

1. 將下列資料庫設定值新增至 *appsettings.json* ：

   [!code-json[](first-mongo-app/samples/3.x/SampleApp/appsettings.json?highlight=2-6)]

1. 使用下列程式碼將 *BookstoreDatabaseSettings.cs* 檔案新增至 *Models* 目錄：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   上述 `BookstoreDatabaseSettings` 類別是用來儲存檔案的 *appsettings.json* `BookstoreDatabaseSettings` 屬性值。 JSON 和 C# 屬性名稱以相同方式命名，以簡化對應程序。

1. 將下列醒目提示的程式碼新增至 `Startup.ConfigureServices`：

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-8)]

   在上述程式碼中：

   * 檔案區段系結的設定實例 *appsettings.json* `BookstoreDatabaseSettings` ，會在相依性插入 (DI) 容器中註冊。 例如， `BookstoreDatabaseSettings` 物件的 `ConnectionString` 屬性會填入 `BookstoreDatabaseSettings:ConnectionString` 中的屬性 *appsettings.json* 。
   * `IBookstoreDatabaseSettings` 介面使用 singleton [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中註冊。 插入時，介面執行個體會解析成 `BookstoreDatabaseSettings` 物件。

1. 在 *Startup.cs* 的頂端新增下列程式碼，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 參考：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>新增 CRUD 作業服務

1. 新增 *Services* 目錄到專案根目錄。
1. 新增具有下列程式碼的 `BookService` 類別到 *Services* 目錄：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   在上述程式碼中，透過建構函式插入從 DI 擷取 `IBookstoreDatabaseSettings` 執行個體。 這項技術可讓您存取加入設定 *appsettings.json* [模型](#add-a-configuration-model) 一節中新增的設定值。

1. 將下列醒目提示的程式碼新增至 `Startup.ConfigureServices`：

   [!code-csharp[](first-mongo-app/samples_snapshot/3.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   在上述程式碼中，`BookService` 類別要向 DI 註冊才能在取用類別中支援建構函式插入。 因為 `BookService` 直接依存於 `MongoClient`，所以 Singleton 服務存留期最適合。 根據官方 [Mongo 用戶端重複使用指導方針](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)， `MongoClient` 應該在具有單一服務存留期的 DI 中註冊。

1. 在 *Startup.cs* 的頂端新增下列程式碼，以解析 `BookService` 參考：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService` 類別使用下列 `MongoDB.Driver` 成員來對資料庫執行 CRUD 作業：

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：讀取用於執行資料庫作業的伺服器實例。 此類別的建構函式是使用 MongoDB 連接字串提供：

  [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：代表用來執行作業的 Mongo 資料庫。 本教學課程在介面中使用一般的 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法來存取特定集合中的資料。 呼叫此方法之後，針對集合執行 CRUD 作業。 在 `GetCollection<TDocument>(collection)` 方法呼叫中：

  * `collection` 代表集合名稱。
  * `TDocument` 代表儲存在集合中的 CLR 物件類型。

`GetCollection<TDocument>(collection)` 傳回代表集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 物件。 在此教學課程中，會在集合上叫用下列方法：

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：刪除符合所提供搜尋條件的單一檔。
* [Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：傳回集合中符合所提供搜尋條件的所有檔。
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：將提供的物件插入至集合中的新檔。
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：以提供的物件取代符合所提供搜尋條件的單一檔。

## <a name="add-a-controller"></a>新增控制器

新增具有下列程式碼的 `BooksController` 類別到 *Controllers* 目錄：

[!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Controllers/BooksController.cs)]

上述 Web API 控制器會：

* 使用 `BookService` 類別來執行 CRUD 作業。
* 包含動作方法以支援 GET、POST、PUT 與 DELETE HTTP 要求。
* 在 `Create` 動作方法中呼叫 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以傳回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 回應。 對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是狀態碼 201。 `CreatedAtRoute` 也會將 `Location` 標頭新增至回應。 `Location` 標頭指定新建活頁簿的 URI。

## <a name="test-the-web-api"></a>測試 Web API

1. 建置並執行應用程式。

1. 巡覽至 `http://localhost:<port>/api/books`，測試控制器的無參數 `Get` 動作方法。 會顯示下列 JSON 回應：

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

1. 巡覽至 `http://localhost:<port>/api/books/{id here}`，測試控制器的多載 `Get` 動作方法。 會顯示下列 JSON 回應：

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>設定 JSON 序列化選項

[測試 Web API](#test-the-web-api) 區段中傳回的 JSON 回應，有兩個詳細資訊要變更：

* 屬性名稱的預設駝峰式命名法大小寫應予變更，使其符合CLR 物件屬性名稱的 Pascal 命名法大小寫。
* `bookName` 屬性應傳回為 `Name`。

為滿足上述需求，請進行下列變更：

1. 已從 ASP.NET 共用架構移除 JSON.NET。 將封裝參考加入至 [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) 。

1. 在 `Startup.ConfigureServices` 中，將下列醒目提示的程式碼鏈結至 `AddControllers` 方法呼叫：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   完成前述變更後，Web API 序列化 JSON 回應中屬性名稱即符合其對應的 CLR 物件類型屬性名稱。 例如，`Book` 類別的 `Author` 屬性會序列化為 `Author`。

1. 在 *模型/Book* 中，以 `BookName` 下列屬性標注屬性 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) ：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]` 的屬性值 `Name` 代表 Web API 序列化 JSON 回應中的屬性名稱。

1. 在 *Models/Book.cs* 的頂端新增下列程式碼，以解析 `[JsonProperty]` 屬性參考：

   [!code-csharp[](first-mongo-app/samples/3.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. 重複[測試 Web API](#test-the-web-api) 一節中定義的步驟。 請注意 JSON 屬性名稱中的差異。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

此教學課程會建立 Web API，此 API 會在 [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL 資料庫上執行建立、讀取、更新及刪除 (CRUD) 作業。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定 MongoDB
> * 建立 MongoDB 資料庫
> * 定義 MongoDB 集合與結構描述
> * 從 Web API 執行 MongoDB CRUD 作業
> * 自訂 JSON 序列化

[查看或下載範例程式碼](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-mongo-app/samples) ([如何下載](xref:index#how-to-download-a-sample)) 

## <a name="prerequisites"></a>必要條件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) 與 **ASP.NET 和 網頁程式開發** 工作負載
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* [.NET Core SDK 2.2](https://dotnet.microsoft.com/download/dotnet-core)
* [Visual Studio for Mac 7.7 版或更新版本](https://visualstudio.microsoft.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>設定 MongoDB

若使用 Windows，MongoDB 預設會安裝在 *C:\\Program Files\\MongoDB*。 將 *C： \\ Program Files \\ MongoDB \\ 伺服器 \\ \<version_number> \\ bin* 新增至 `Path` 環境變數。 此變更會啟用從您開發機器上的任意位置存取 MongoDB 的功能。

在下列步驟中使用 mongo 殼層來建立資料庫、建立集合及存放文件。 如需有關 mongo 殼層命令的詳細資訊，請參閱[使用 mongo 殼層](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell)。

1. 選擇您開發機器上的目錄來存放資料。 例如， Windows 上的 *C:\\BooksData*。 若該目錄不存在，請建立它。 mongo 殼層不會建立新目錄。
1. 開啟命令殼層。 執行下列命令以連線到預設連接埠 27017 上的 MongoDB。 請記得將 `<data_directory_path>` 取代為您在上一個步驟中選擇的目錄。

   ```console
   mongod --dbpath <data_directory_path>
   ```

1. 開啟另一個命令殼層執行個體。 執行下列命令以連線到預設測試資料庫：

   ```console
   mongo
   ```

1. 在命令殼層中執行下列命令：

   ```console
   use BookstoreDb
   ```

   若它不存在，會建立名為 *BookstoreDb* 的資料庫。 若該資料庫存在，會開啟其連線進行交易。

1. 使用下列命令建立 `Books` 集合：

   ```console
   db.createCollection('Books')
   ```

   會顯示下列結果：

   ```console
   { "ok" : 1 }
   ```

1. 使用下列命令為 `Books` 集合定義結構描述並插入兩份文件：

   ```console
   db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
   ```

   會顯示下列結果：

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
   > 您執行此範例時的識別碼，將不同於本文中顯示的識別碼。

1. 使用下列命令檢視資料庫中的文件：

   ```console
   db.Books.find({}).pretty()
   ```

   會顯示下列結果：

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

   結構描述會為每份文件新增自動彙總的 `_id` 屬性 (類型 `ObjectId`)。

資料庫已就緒。 您可以開始建立 ASP.NET Core Web API。

## <a name="create-the-aspnet-core-web-api-project"></a>建立 ASP.NET Core Web API 專案

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 移至 **[** > **新增** > **專案**]。
1. 選取 [ASP.NET Core Web 應用程式] 專案類型，然後選取 [下一步]。
1. 將專案命名為 *BooksApi*，然後選取 [建立]。
1. 選取 [.NET Core] 目標架構與 [ASP.NET Core 2.2]。 選取 [API] 專案範本，然後選取 [確定]。
1. 流覽 [NuGet 資源庫： mongodb](https://www.nuget.org/packages/MongoDB.Driver/) ，以判斷 .net Driver for mongodb 的最新穩定版本。 在 [套件管理員主控台] 視窗中，瀏覽到專案根目錄。 執行下列命令以安裝適用於 MongoDB 的 .NET 驅動程式：

   ```powershell
   Install-Package MongoDB.Driver -Version {VERSION}
   ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 在命令殼層中執行下列命令：

   ```dotnetcli
   dotnet new webapi -o BooksApi
   code BooksApi
   ```

   會產生以 .NET Core 為目標的新 ASP.NET Core Web API 專案，並在 Visual Studio Code 中開啟。

1. 在狀態列的 OmniSharp 火焰圖示變成綠色之後，會有一個對話方塊要求 **從 ' >booksapi ' 中找不到建立和偵測所需的資產。加入它們？**。 選取 [是]  。
1. 流覽 [NuGet 資源庫： mongodb](https://www.nuget.org/packages/MongoDB.Driver/) ，以判斷 .net Driver for mongodb 的最新穩定版本。 開啟 [整合式終端機] 並瀏覽到專案根目錄。 執行下列命令以安裝適用於 MongoDB 的 .NET 驅動程式：

   ```dotnetcli
   dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
   ```

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 在8.6 版之前的 Visual Studio for Mac 中，   >    >  從側邊欄選取 [從新的解決方案 **.net Core**  >  **應用程式**]。 在8.6 版或更新版本中，  >    >  從側邊欄選取 [檔案新的方案 **Web] 和 [主控台**  >  **應用程式**]。
1. 選取 [ASP.NET Core Web API] C# 專案範本，然後選取 [下一步]。
1. 從 [目標 Framework] 下拉式清單選取 [.NET Core 2.2]，然後選取 [下一步]。
1. 在 [專案名稱] 中輸入 *BooksApi*，然後選取 [建立]。
1. 在 [方案] 台中，以滑鼠右鍵按一下專案的 [相依性] 節點並選取 [新增封裝]。
1. 在搜尋方塊中輸入 *MongoDB.Driver*，然後依序選取 *MongoDB.Driver* 套件和 [新增套件]。
1. 選取 [授權接受] 對話方塊中的 [接受] 按鈕。

---

## <a name="add-an-entity-model"></a>新增實體模型

1. 新增 *Models* 目錄到專案根目錄。
1. 新增具有下列程式碼的 `Book` 類別到 *Models* 目錄：

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

   在上面的類別中，需要 `Id` 屬性：

   * 才能將通用語言執行平台 (CLR) 物件對應到 MongoDB 集合。
   * 已標注， [`[BsonId]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) 以將此屬性指定為檔的主鍵。
   * 的批註為， [`[BsonRepresentation(BsonType.ObjectId)]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) 以允許將參數傳遞為類型， `string` 而不是 [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) 結構。 Mongo 會處理從 `string` 轉換到 `ObjectId` 的作業。

   `BookName`屬性是以屬性（attribute）標注 [`[BsonElement]`](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) 。 `Name` 的屬性值代表 MongoDB 集合中的屬性名稱。

## <a name="add-a-configuration-model"></a>新增組態模型

1. 將下列資料庫設定值新增至 *appsettings.json* ：

   [!code-json[](first-mongo-app/samples/2.x/SampleApp/appsettings.json?highlight=2-6)]

1. 使用下列程式碼將 *BookstoreDatabaseSettings.cs* 檔案新增至 *Models* 目錄：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/BookstoreDatabaseSettings.cs)]

   上述 `BookstoreDatabaseSettings` 類別是用來儲存檔案的 *appsettings.json* `BookstoreDatabaseSettings` 屬性值。 JSON 和 C# 屬性名稱以相同方式命名，以簡化對應程序。

1. 將下列醒目提示的程式碼新增至 `Startup.ConfigureServices`：

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

   在上述程式碼中：

   * 檔案區段系結的設定實例 *appsettings.json* `BookstoreDatabaseSettings` ，會在相依性插入 (DI) 容器中註冊。 例如， `BookstoreDatabaseSettings` 物件的 `ConnectionString` 屬性會填入 `BookstoreDatabaseSettings:ConnectionString` 中的屬性 *appsettings.json* 。
   * `IBookstoreDatabaseSettings` 介面使用 singleton [服務存留期](xref:fundamentals/dependency-injection#service-lifetimes)在 DI 中註冊。 插入時，介面執行個體會解析成 `BookstoreDatabaseSettings` 物件。

1. 在 *Startup.cs* 的頂端新增下列程式碼，以解析 `BookstoreDatabaseSettings` 和 `IBookstoreDatabaseSettings` 參考：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a>新增 CRUD 作業服務

1. 新增 *Services* 目錄到專案根目錄。
1. 新增具有下列程式碼的 `BookService` 類別到 *Services* 目錄：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceClass)]

   在上述程式碼中，透過建構函式插入從 DI 擷取 `IBookstoreDatabaseSettings` 執行個體。 這項技術可讓您存取加入設定 *appsettings.json* [模型](#add-a-configuration-model) 一節中新增的設定值。

1. 將下列醒目提示的程式碼新增至 `Startup.ConfigureServices`：

   [!code-csharp[](first-mongo-app/samples_snapshot/2.x/SampleApp/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

   在上述程式碼中，`BookService` 類別要向 DI 註冊才能在取用類別中支援建構函式插入。 因為 `BookService` 直接依存於 `MongoClient`，所以 Singleton 服務存留期最適合。 根據官方 [Mongo 用戶端重複使用指導方針](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use)， `MongoClient` 應該在具有單一服務存留期的 DI 中註冊。

1. 在 *Startup.cs* 的頂端新增下列程式碼，以解析 `BookService` 參考：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_UsingBooksApiServices)]

`BookService` 類別使用下列 `MongoDB.Driver` 成員來對資料庫執行 CRUD 作業：

* [MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm)：讀取用於執行資料庫作業的伺服器實例。 此類別的建構函式是使用 MongoDB 連接字串提供：

  [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* [IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm)：代表用來執行作業的 Mongo 資料庫。 本教學課程在介面中使用一般的 [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) 方法來存取特定集合中的資料。 呼叫此方法之後，針對集合執行 CRUD 作業。 在 `GetCollection<TDocument>(collection)` 方法呼叫中：

  * `collection` 代表集合名稱。
  * `TDocument` 代表儲存在集合中的 CLR 物件類型。

`GetCollection<TDocument>(collection)` 傳回代表集合的 [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) 物件。 在此教學課程中，會在集合上叫用下列方法：

* [DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm)：刪除符合所提供搜尋條件的單一檔。
* [Find \<TDocument> ](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm)：傳回集合中符合所提供搜尋條件的所有檔。
* [InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm)：將提供的物件插入至集合中的新檔。
* [ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm)：以提供的物件取代符合所提供搜尋條件的單一檔。

## <a name="add-a-controller"></a>新增控制器

新增具有下列程式碼的 `BooksController` 類別到 *Controllers* 目錄：

[!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Controllers/BooksController.cs)]

上述 Web API 控制器會：

* 使用 `BookService` 類別來執行 CRUD 作業。
* 包含動作方法以支援 GET、POST、PUT 與 DELETE HTTP 要求。
* 在 `Create` 動作方法中呼叫 <xref:System.Web.Http.ApiController.CreatedAtRoute*>，以傳回 [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) 回應。 對於可在伺服器上建立新資源的 HTTP POST 方法，其標準回應是狀態碼 201。 `CreatedAtRoute` 也會將 `Location` 標頭新增至回應。 `Location` 標頭指定新建活頁簿的 URI。

## <a name="test-the-web-api"></a>測試 Web API

1. 建置並執行應用程式。

1. 巡覽至 `http://localhost:<port>/api/books`，測試控制器的無參數 `Get` 動作方法。 會顯示下列 JSON 回應：

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

1. 巡覽至 `http://localhost:<port>/api/books/{id here}`，測試控制器的多載 `Get` 動作方法。 會顯示下列 JSON 回應：

   ```json
   {
     "id":"{ID}",
     "bookName":"Clean Code",
     "price":43.15,
     "category":"Computers",
     "author":"Robert C. Martin"
   }
   ```

## <a name="configure-json-serialization-options"></a>設定 JSON 序列化選項

[測試 Web API](#test-the-web-api) 區段中傳回的 JSON 回應，有兩個詳細資訊要變更：

* 屬性名稱的預設駝峰式命名法大小寫應予變更，使其符合CLR 物件屬性名稱的 Pascal 命名法大小寫。
* `bookName` 屬性應傳回為 `Name`。

為滿足上述需求，請進行下列變更：

1. 在 `Startup.ConfigureServices` 中，將下列醒目提示的程式碼鏈結至 `AddMvc` 方法呼叫：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

   完成前述變更後，Web API 序列化 JSON 回應中屬性名稱即符合其對應的 CLR 物件類型屬性名稱。 例如，`Book` 類別的 `Author` 屬性會序列化為 `Author`。

1. 在 *模型/Book* 中，以 `BookName` 下列屬性標注屬性 [`[JsonProperty]`](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) ：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

   `[JsonProperty]` 的屬性值 `Name` 代表 Web API 序列化 JSON 回應中的屬性名稱。

1. 在 *Models/Book.cs* 的頂端新增下列程式碼，以解析 `[JsonProperty]` 屬性參考：

   [!code-csharp[](first-mongo-app/samples/2.x/SampleApp/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. 重複[測試 Web API](#test-the-web-api) 一節中定義的步驟。 請注意 JSON 屬性名稱中的差異。

::: moniker-end

## <a name="add-authentication-support-to-a-web-api"></a>將驗證支援新增至 web API

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="next-steps"></a>下一步

如需有關建置 ASP.NET Core Web API 的詳細資訊，請參閱下列資源：

* [本文的 YouTube 版本](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
* [Microsoft 瞭解：使用 ASP.NET Core 建立 web API](/learn/modules/build-web-api-aspnet-core/)
