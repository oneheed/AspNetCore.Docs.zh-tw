---
title: 在 ASP.NET Core 中使用 Grunt
author: rick-anderson
description: 在 ASP.NET Core 中使用 Grunt
ms.author: riande
ms.date: 12/05/2019
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
uid: client-side/using-grunt
ms.openlocfilehash: 374c23f440dcf301b3a1e1e9e6684dd050f218c6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054550"
---
# <a name="use-grunt-in-aspnet-core"></a>在 ASP.NET Core 中使用 Grunt

Grunt 是一種 JavaScript 工作執行器，可將腳本縮制、TypeScript 編譯、程式碼品質「不起毛」工具、CSS 前置處理器，以及任何需要執行以支援用戶端開發的重複性工作自動化。 Visual Studio 完全支援 Grunt。

此範例會使用空的 ASP.NET Core 專案作為其起點，以示範如何從頭開始自動執行用戶端組建程式。

完成的範例會清除目標部署目錄、結合 JavaScript 檔案、檢查程式碼品質、總是 JavaScript 檔案內容，並將其部署至 web 應用程式的根目錄。 我們將使用下列套件：

* **grunt**： grunt 工作執行器套件。

* **grunt-contrib-clean**：移除檔案或目錄的外掛程式。

* **grunt-contrib-jshint**：可檢查 JavaScript 程式碼品質的外掛程式。

* **grunt-contrib-concat**：將檔案加入單一檔案的外掛程式。

* **grunt-contrib-uglify**：縮短 JavaScript 以縮減大小的外掛程式。

* **grunt-contrib-watch：監看** 檔案活動的外掛程式。

## <a name="preparing-the-application"></a>準備應用程式

若要開始，請設定新的空白 web 應用程式，並新增 TypeScript 範例檔案。 TypeScript 檔案會使用預設 Visual Studio 設定自動編譯成 JavaScript，而且將會是使用 Grunt 處理的原始資料。

1. 在 Visual Studio 中，建立新的 `ASP.NET Web Application` 。

2. 在 [ **新增 ASP.NET 專案** ] 對話方塊中，選取 ASP.NET Core **空白** 範本，然後按一下 [確定] 按鈕。

3. 在 [方案總管中，檢查項目結構。 此 `\src` 資料夾包含空白 `wwwroot` 和 `Dependencies` 節點。

    ![空白的 web 方案](using-grunt/_static/grunt-solution-explorer.png)

4. 將名為的新資料夾新增 `TypeScript` 至您的專案目錄。

5. 新增任何檔案之前，請 Visual Studio 確定已核取 [在儲存時編譯] 選項。 流覽至 **工具**  >  **選項**  >  **文字編輯器**  >  **Typescript**  >  **專案**：

    ![設定自動編譯 TypeScript 檔案的選項](using-grunt/_static/typescript-options.png)

6. 以滑鼠右鍵按一下 `TypeScript` 目錄，然後從操作功能表中選取 [ **加入 > 新專案** ]。 選取 **JavaScript** 檔案專案並將檔案命名為 *Tastes* ， (記下 \*) 的 ts 延伸模組。 將下方的 TypeScript 程式程式碼複製到檔案 (當您儲存時，JavaScript 來源) 會出現新的 *Tastes.js* 檔案。

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. 將第二個檔案新增至 **TypeScript** 目錄，並為其命名 `Food.ts` 。 將下列程式碼複製到檔案中。

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a>設定 NPM

接下來，設定 NPM 以下載 grunt 和 grunt 工作。

1. 在方案總管中，以滑鼠右鍵按一下專案，然後從內容功能表中選取 [ **加入 > 新專案** ]。 選取 [ **NPM] 設定檔** 專案，保留預設名稱， *package.js*]，然後按一下 [ **新增** ] 按鈕。

2. 在 *package.json* 檔案的 `devDependencies` 物件括弧內，輸入 "grunt"。 `grunt`從 Intellisense 清單中選取，然後按下 enter 鍵。 Visual Studio 會將 grunt 套件名稱加上引號，並新增冒號。 在冒號右邊，從 Intellisense 清單頂端選取封裝的最新穩定版本， (按下 [ `Ctrl-Space` intellisense 未) 出現]。

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM 使用 [語義版本](https://semver.org/) 設定來組織相依性。 語義版本設定（也稱為 SemVer）會識別具有 \<major> 編號配置的封裝 \<minor> ... \<patch>Intellisense 只會顯示一些常見的選項，以簡化語義版本控制。 Intellisense 清單中的最上層專案 (上述範例中的0.4.5，) 被視為套件的最新穩定版本。 插入號 (^) 符號符合最新的主要版本，而波狀符號 (~) 符合最新的次要版本。 如需 SemVer 所提供完整講求表現度的指南，請參閱 [NPM semver 版本](https://www.npmjs.com/package/semver) 剖析器參考。

3. 新增更多相依性以載入 grunt-contrib- \* 適用于 *clean*、 *jshint*、 *concat*、 *uglify* 和 *watch* 的套件，如下列範例所示。 版本不需要符合範例。

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. 儲存 package.json 檔案。

每個專案的封裝 `devDependencies` 將會下載，以及每個封裝所需的任何檔案。 您可以啟用 **方案總管** 中的 [**顯示所有** 檔案] 按鈕，在 *node_modules* 目錄中找到封裝檔案。

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> 如有需要，您可以在 **方案總管** 中，以滑鼠右鍵按一下 `Dependencies\NPM` 並選取 [ **還原套件** ] 功能表選項，以手動還原相依性。

![還原套件](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>設定 Grunt

Grunt 會使用名為 *Gruntfile.js* 的資訊清單進行設定，此指令程式會定義、載入和註冊可以手動執行或設定為根據 Visual Studio 中事件自動執行的工作。

1. 以滑鼠右鍵按一下專案，然後選取 [**加入**  >  **新專案**]。 選取 **JavaScript** 檔案專案範本，將名稱變更為 *Gruntfile.js*，然後按一下 [ **加入** ] 按鈕。

1. 將下列程式碼新增至 *Gruntfile.js*。 此函式會 `initConfig` 設定每個封裝的選項，而模組的其餘部分則會載入和註冊工作。

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. 在函式中 `initConfig` ，新增工作的選項， `clean` 如下列範例 *Gruntfile.js* 所示。 此工作會 `clean` 接受目錄字串的陣列。 此工作會從 *wwwroot/lib* 移除檔案，並移除整個 */temp* 目錄。

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. 在函式下方 `initConfig` ，加入的呼叫 `grunt.loadNpmTasks` 。 這會讓工作從 Visual Studio 中執行。

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. 儲存 *Gruntfile.js*。 檔案看起來應該像下面的螢幕擷取畫面。

    ![初始 gruntfile.js](using-grunt/_static/gruntfile-js-initial.png)

1. 以滑鼠右鍵按一下 *Gruntfile.js* ，然後從內容功能表中選取 [工作執行 **器瀏覽器** ]。 [工作執行 **器瀏覽器** ] 視窗隨即開啟。

    ![工作執行器功能表](using-grunt/_static/task-runner-explorer-menu.png)

1. 確認 `clean` 在 [工作執行 **器瀏覽器**] 中的 [工作] 底下顯示。

    ![工作執行器瀏覽器工作清單](using-grunt/_static/task-runner-explorer-tasks.png)

1. 以滑鼠右鍵按一下清除工作，然後從內容功能表中選取 [ **執行** ]。 命令視窗會顯示工作的進度。

    ![工作執行器瀏覽器執行清除工作](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > 尚未清除任何檔案或目錄。 如果您想要的話，可以在方案總管中手動建立它們，然後以測試的形式執行清除工作。

1. 在函 `initConfig` 式中，加入 `concat` 使用下列程式碼的專案。

    `src`屬性陣列會依應結合的順序，列出要合併的檔案。 `dest`屬性會將路徑指派給所產生的合併檔案。

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > `all`上述程式碼中的屬性是目標的名稱。 目標用於某些 Grunt 工作，以允許多個組建環境。 您可以使用 IntelliSense 來查看內建目標，或指派自己的目標。

1. `jshint`使用下列程式碼來新增工作。

    Jshint `code-quality` 公用程式會針對在 *temp* 目錄中找到的每個 JavaScript 檔案執行。

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > 當 JavaScript 使用括弧語法來指派屬性，而不是以點標記法（例如，而不是）時，W069 是由 jshint 所產生的錯誤。 `Tastes["Sweet"]` `Tastes.Sweet` 選項會關閉警告，以允許其餘的進程繼續執行。

1. `uglify`使用下列程式碼來新增工作。

    此工作會縮短在 temp 目錄中找到的 *combined.js* 檔案，並在 *\<file name\>.min.js* 標準命名慣例之後，在 wwwroot/lib 中建立結果檔。

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. 在對該載入的呼叫下 `grunt.loadNpmTasks` `grunt-contrib-clean` ，使用下列程式碼，包含對 jshint、concat 和 uglify 的相同呼叫。

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. 儲存 *Gruntfile.js*。 檔案看起來應該會像下面的範例。

    ![完整的 grunt 檔案範例](using-grunt/_static/gruntfile-js-complete.png)

1. 請注意，[工作執行 **器** ] 的 [工作] 清單包括 `clean` 、 `concat` 和工作 `jshint` `uglify` 。 依序執行每項工作，並觀察 **方案總管** 的結果。 每項工作都應該執行而不會發生錯誤。

    ![工作執行器瀏覽器執行每項工作](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Concat 工作會建立新的 *combined.js* 檔，並將它放入 temp 目錄中。 此工作 `jshint` 只會執行，而不會產生輸出。 此工作 `uglify` 會建立新的 *combined.min.js* 檔，並將它放入 *wwwroot/lib* 中。 完成時，解決方案看起來應該像下面的螢幕擷取畫面：

    ![所有工作之後的方案 explorer](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > 如需每個封裝之選項的詳細資訊，請 [https://www.npmjs.com/](https://www.npmjs.com/) 在主頁面上的 [搜尋] 方塊中，流覽並查閱封裝名稱。 例如，您可以查詢 grunt-contrib-clean 封裝，以取得說明其所有參數的檔連結。

### <a name="all-together-now"></a>摘要

使用 Grunt `registerTask()` 方法，以特定循序執行一系列的工作。 例如，若要執行上述 order clean > concat-> jshint-> uglify 中的範例步驟，請將下列程式碼新增至模組。 程式碼應該加入至與 loadNpmTasks ( # A1 呼叫相同的層級，而不是 initConfig 外部。

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

新的工作會顯示在 [工作執行器] 的 [別名工作] 下。 您可以用滑鼠右鍵按一下並執行它，就像是其他工作一樣。 此工作 `all` 將依 `clean` 序執行、 `concat` `jshint` 和 `uglify` 。

![別名 grunt 工作](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>監看變更

工作會 `watch` 持續留意檔案和目錄。 如果偵測到變更，監看會自動觸發工作。 將下列程式碼新增至 initConfig，以監看 \* TypeScript 目錄中的 .js 檔案是否有變更。 如果 JavaScript 檔案已變更， `watch` 將會執行工作 `all` 。

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

加入的呼叫 `loadNpmTasks()` ，以顯示工作執行 `watch` 器瀏覽器中的工作。

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

以滑鼠右鍵按一下 [工作執行器瀏覽器] 中的監看式工作，然後從操作功能表選取 [執行]。 顯示正在執行之監看式工作的命令視窗將會顯示「正在等候 ...」消息。 開啟其中一個 TypeScript 檔案，新增空格，然後儲存檔案。 這會觸發 watch 工作，並觸發其他工作以依序執行。 下列螢幕擷取畫面顯示範例執行。

![正在執行工作輸出](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>系結至 Visual Studio 事件

除非您想要在每次於 Visual Studio 中工作時手動啟動工作，否則請 **在組建、****清除** 和 **專案開啟** 事件 **之後**，將工作系結至。

系結， `watch` 以便在每次 Visual Studio 開啟時執行。 在工作執行器瀏覽器中，以滑鼠右鍵按一下 [監看式] 工作，然後從內容功能表 **選取 [** 系  >  **結專案開啟**]。

![將工作系結至開啟的專案](using-grunt/_static/bindings-project-open.png)

卸載並重載專案。 當專案再次載入時，[監看式] 工作會自動開始執行。

## <a name="summary"></a>摘要

Grunt 是功能強大的工作執行器，可用來將大部分的用戶端組建工作自動化。 Grunt 利用 NPM 來提供其套件，並將功能工具與 Visual Studio 整合。 Visual Studio 的工作執行器 Explorer 會偵測設定檔的變更，並提供便利的介面來執行工作、查看執行中的工作，以及將工作系結至 Visual Studio 事件。
