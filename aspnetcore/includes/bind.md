讀取相關設定值的慣用方式是使用 [選項模式](xref:fundamentals/configuration/options)。 例如，若要讀取下列設定值：

```json
  "Position": {
    "Title": "Editor",
    "Name": "Joe Smith"
  }
```

建立下列 `PositionOptions` 類別：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Options/PositionOptions.cs?name=snippet)]

Options 類別：

* 必須是具有公用無參數的函式的非抽象。
* 型別的所有公用讀寫屬性都會系結。
* 欄位為 ***not** _ 系結。 在上述程式碼中，未系結 `Position` 。 使用屬性，因此在將類別系結 `Position` `"Position"` 至設定提供者時，不需要在應用程式中硬式編碼字串。

下列程式碼：

_ 呼叫 [ConfigurationBinder](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) ，將類別系結至 `PositionOptions` `Position` 區段。
* 顯示 `Position` 設置資料。

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test22.cshtml.cs?name=snippet)]

在上述程式碼中，預設會讀取應用程式啟動後的 JSON 設定檔變更。

[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) 系結並傳回指定的型別。 `ConfigurationBinder.Get<T>` 可能比使用更方便 `ConfigurationBinder.Bind` 。 下列程式碼顯示如何搭配 `ConfigurationBinder.Get<T>` `PositionOptions` 類別使用：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test21.cshtml.cs?name=snippet)]

在上述程式碼中，預設會讀取應用程式啟動後的 JSON 設定檔變更。

使用 ***選項模式** _ 的替代方法是系結 `Position` 區段，並將它加入至相依性 [插入服務容器](xref:fundamentals/dependency-injection)。 在下列程式碼中， `PositionOptions` 會新增至服務容器， <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure_> 並系結至設定：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Startup.cs?name=snippet)]

使用上述程式碼，下列程式碼會讀取位置選項：

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Pages/Test2.cshtml.cs?name=snippet)]

在上述程式碼中， ***不*** 會讀取應用程式啟動後的 JSON 設定檔變更。 若要讀取應用程式啟動後的變更，請使用 [IOptionsSnapshot](xref:fundamentals/configuration/options#ios)。
