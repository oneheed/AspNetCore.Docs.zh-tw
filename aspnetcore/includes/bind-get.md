> [!WARNING]
> <span data-ttu-id="f1b01-101">基於安全性考量，您必須選擇將 `GET` 要求資料繫結到頁面模型屬性。</span><span class="sxs-lookup"><span data-stu-id="f1b01-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="f1b01-102">請先驗證使用者輸入再將其對應至屬性。</span><span class="sxs-lookup"><span data-stu-id="f1b01-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="f1b01-103">當您 `GET` 針對依賴查詢字串或路由值的案例進行定址時，加入宣告系結會很有用。</span><span class="sxs-lookup"><span data-stu-id="f1b01-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="f1b01-104">若要系結要求的屬性 `GET` ，請將屬性 [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 的 `SupportsGet` 屬性設定為 `true` ：</span><span class="sxs-lookup"><span data-stu-id="f1b01-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="f1b01-105">如需詳細資訊，請參閱 [ASP.NET Core 社區站立會議： (YouTube) ](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s)的系結取得討論。</span><span class="sxs-lookup"><span data-stu-id="f1b01-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
