```console
npm run release
```

此命令會產生在執行應用程式時所要提供的用戶端資產。 這些資產位於 *wwwroot* 資料夾中。

Webpack 已完成下列工作：

* 清除 *wwwroot* 目錄的內容。
* 在稱為 *轉譯* 的進程中，將 TypeScript 轉換為 JavaScript。
* 破壞產生的 JavaScript，以在稱為 *縮制* 的進程中減少檔案大小。
* 將處理的 JavaScript、CSS 與 HTML 檔案從 *src* 複製到 *wwwroot* 目錄。
* 將下列元素插入到 *wwwroot/index.html* 檔案：
  * `<link>`參考 *wwwroot/main 的標記 \<hash\> 。css* 檔案。 此標記緊接在結尾 `</head>` 標記之前。
  * `<script>`參考縮減 *wwwroot/main 的標記 \<hash\> 。js* 檔案。 此標記緊接在結尾 `</body>` 標記之前。