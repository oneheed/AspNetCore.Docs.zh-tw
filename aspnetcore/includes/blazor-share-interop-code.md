## <a name="share-interop-code-in-a-class-library"></a>在類別庫中共用 interop 程式碼

JS interop 程式碼可以包含在類別庫中，以讓您共用 NuGet 套件中的程式碼。

類別庫會處理在建立的元件中內嵌 JavaScript 資源。 JavaScript 檔案會放在資料夾中 `wwwroot` 。 在建立程式庫時，工具會負責內嵌資源。

在應用程式的專案檔中，會以參考任何 NuGet 套件的相同方式參考所建立的 NuGet 套件。 還原封裝之後，應用程式程式碼就可以像 c # 一樣呼叫 JavaScript。

如需詳細資訊，請參閱<xref:blazor/components/class-libraries>。
