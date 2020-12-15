Blazor 伺服器應用程式存留在伺服器記憶體中。 這表示在相同的進程中裝載多個應用程式。 針對每個應用程式會話，Blazor 會啟動具有自己的 DI 容器範圍的線路。 這表示範圍服務在每個 Blazor 會話中都是唯一的。

> [!WARNING]
> 除非特別小心，否則我們不建議在相同伺服器上使用單一服務共用狀態的應用程式，因為這可能會造成安全性弱點，例如線上路之間洩漏使用者狀態。

您可以在 Blazor apps 中使用具狀態的單一服務，如果它們是專為其設計的。 例如，您可以使用記憶體快取做為 singleton，因為它需要索引鍵來存取指定的專案，前提是使用者無法控制所使用的快取索引鍵。

**此外，基於安全性考慮，您不能 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> 在 Blazor apps 中使用。** Blazor apps 會在 ASP.NET Core 管線的內容之外執行。 <xref:Microsoft.AspNetCore.Http.HttpContext>不保證可在中使用 <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> ，也不保證會保留啟動 Blazor 應用程式的內容。

將要求狀態傳遞給 Blazor 應用程式的建議方式，是在應用程式的初始呈現中透過根元件的參數：

* 使用您想要傳遞給 Blazor 應用程式的所有資料來定義類別。
* 使用當時可用的，從 Razor 頁面填入該資料 <xref:Microsoft.AspNetCore.Http.HttpContext> 。
* 將資料以參數形式傳遞至 Blazor 應用程式 (應用程式) 的根元件。
* 在根元件中定義參數，以保存要傳遞至應用程式的資料。
* 使用應用程式中的使用者特定資料;或者，將該資料複製到內的範圍服務， <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 讓它可以跨應用程式使用。

如需詳細資訊和範例程式碼，請參閱 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>。
