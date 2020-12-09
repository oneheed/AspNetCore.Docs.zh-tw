> [!WARNING]
> 基於安全性考量，您必須選擇將 `GET` 要求資料繫結到頁面模型屬性。 請先驗證使用者輸入再將其對應至屬性。 當您 `GET` 針對依賴查詢字串或路由值的案例進行定址時，加入宣告系結會很有用。
>
> 若要系結要求的屬性 `GET` ，請將屬性 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 的 `SupportsGet` 屬性設定為 `true` ：
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> 如需詳細資訊，請參閱 [ASP.NET Core 社區站立會議： (YouTube) ](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s)的系結取得討論。
