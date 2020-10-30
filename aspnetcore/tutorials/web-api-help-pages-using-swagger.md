---
title: 使用 Swagger/OpenAPI ASP.NET Core web API 檔
author: RicoSuter
description: 本教學課程提供新增 Swagger，以產生 web API 應用程式之檔和說明頁面的逐步解說。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062450"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>使用 Swagger/OpenAPI ASP.NET Core web API 檔

作者：[Christoph Nienaber](https://twitter.com/zuckerthoben) 和 [Rico Suter](https://blog.rsuter.com/)

Swagger (OpenAPI) 是描述 REST Api 的語言無關規格。 它可讓電腦和人類瞭解 REST API 的功能，而不需要直接存取原始程式碼。 其主要目標是：

* 將連接低耦合服務所需的工作量降至最低。
* 減少正確記錄服務所需的時間量。

.NET 的兩個主要 OpenAPI 實作為 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 和 [NSwag](https://github.com/RicoSuter/NSwag)，請參閱：

* [使用 Swashbuckle 消費者入門](xref:tutorials/get-started-with-swashbuckle)
* [使用 NSwag 消費者入門](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenApi 與 Swagger 的比較

Swagger 專案已在2015中捐贈給 OpenAPI 方案，而且自稱為 OpenAPI。 這兩個名稱可交替使用。 但是，「OpenAPI」是指規格。 「Swagger」指的是 Smartbear ready 中使用 OpenAPI 規格的開放原始碼和商業產品系列。 後續的開放原始碼產品（例如 [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator)）也會落在 Swagger 系列名稱下，儘管 smartbear ready 並未發行。

簡言之：

* OpenAPI 是一種規格。
* Swagger 是使用 OpenAPI 規格的工具。 例如，OpenAPIGenerator 和 Swashbuckle.aspnetcore.swaggerui。

## <a name="openapi-specification-openapijson"></a>) 上的 OpenAPI 規格 ( # B0

OpenAPI 規格是描述 API 功能的檔。 檔是以控制器和模型內的 XML 和屬性注釋為基礎。 它是 OpenAPI 流程的核心部分，用來推動像是 Swashbuckle.aspnetcore.swaggerui 之類的工具。 預設會將其命名為 *openapi.js開啟* 。 以下是 OpenAPI 規格的範例，為了簡潔起見，已縮減：

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a>Swagger UI

[SWAGGER ui](https://swagger.io/swagger-ui/) 會提供 web UI，以提供服務的相關資訊，並使用所產生的 OpenAPI 規格。 Swashbuckle 和 NSwag 都包含內嵌的 Swagger UI 版本，因此它可以使用中介軟體的登錄呼叫裝載在 ASP.NET Core 應用程式中。 Web UI 顯示如下：

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

控制器中的每個公用動作方法都可以從 UI 進行測試。 選取方法名稱以展開區段。 新增任何必要的參數，然後選取 [立即 **試用]！** 。

![範例 Swagger GET 測試](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> 用於螢幕擷取畫面的 Swagger UI 版本為第 2 版。 如需第 3 版的範例，請參閱 [Petstore 範例](https://petstore.swagger.io/)。

## <a name="next-steps"></a>下一步

* [開始使用 Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [開始使用 NSwag](xref:tutorials/get-started-with-nswag)
