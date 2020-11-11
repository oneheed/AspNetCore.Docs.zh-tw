* `-F|--no-formatting`

  其目前狀態會隱藏 HTTP 回應格式的旗標。

* `-h|--header`

  設定 HTTP 要求標頭。 支援下列兩種值格式：

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  指定 HTTP 回應本文應寫入的檔案。 例如 `--response:body "C:\response.json"`。 如果檔案不存在，就會建立檔案。

* `--response:headers`

  指定 HTTP 回應標頭應寫入的檔案。 例如 `--response:headers "C:\response.txt"`。 如果檔案不存在，就會建立檔案。

* `-s|--streaming`

  其目前狀態會啟用 HTTP 回應串流的旗標。
