---
title: 使用 Swagger/OpenAPI ASP.NET Core web API 檔
author: RicoSuter
description: 本教學課程提供新增 Swagger，以產生 web API 應用程式之檔和說明頁面的逐步解說。
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062450"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="2245f-103">使用 Swagger/OpenAPI ASP.NET Core web API 檔</span><span class="sxs-lookup"><span data-stu-id="2245f-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="2245f-104">作者：[Christoph Nienaber](https://twitter.com/zuckerthoben) 和 [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="2245f-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="2245f-105">Swagger (OpenAPI) 是描述 REST Api 的語言無關規格。</span><span class="sxs-lookup"><span data-stu-id="2245f-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="2245f-106">它可讓電腦和人類瞭解 REST API 的功能，而不需要直接存取原始程式碼。</span><span class="sxs-lookup"><span data-stu-id="2245f-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="2245f-107">其主要目標是：</span><span class="sxs-lookup"><span data-stu-id="2245f-107">Its main goals are to:</span></span>

* <span data-ttu-id="2245f-108">將連接低耦合服務所需的工作量降至最低。</span><span class="sxs-lookup"><span data-stu-id="2245f-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="2245f-109">減少正確記錄服務所需的時間量。</span><span class="sxs-lookup"><span data-stu-id="2245f-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="2245f-110">.NET 的兩個主要 OpenAPI 實作為 [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) 和 [NSwag](https://github.com/RicoSuter/NSwag)，請參閱：</span><span class="sxs-lookup"><span data-stu-id="2245f-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="2245f-111">使用 Swashbuckle 消費者入門</span><span class="sxs-lookup"><span data-stu-id="2245f-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="2245f-112">使用 NSwag 消費者入門</span><span class="sxs-lookup"><span data-stu-id="2245f-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="2245f-113">OpenApi 與 Swagger 的比較</span><span class="sxs-lookup"><span data-stu-id="2245f-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="2245f-114">Swagger 專案已在2015中捐贈給 OpenAPI 方案，而且自稱為 OpenAPI。</span><span class="sxs-lookup"><span data-stu-id="2245f-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="2245f-115">這兩個名稱可交替使用。</span><span class="sxs-lookup"><span data-stu-id="2245f-115">Both names are used interchangeably.</span></span> <span data-ttu-id="2245f-116">但是，「OpenAPI」是指規格。</span><span class="sxs-lookup"><span data-stu-id="2245f-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="2245f-117">「Swagger」指的是 Smartbear ready 中使用 OpenAPI 規格的開放原始碼和商業產品系列。</span><span class="sxs-lookup"><span data-stu-id="2245f-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="2245f-118">後續的開放原始碼產品（例如 [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator)）也會落在 Swagger 系列名稱下，儘管 smartbear ready 並未發行。</span><span class="sxs-lookup"><span data-stu-id="2245f-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="2245f-119">簡言之：</span><span class="sxs-lookup"><span data-stu-id="2245f-119">In short:</span></span>

* <span data-ttu-id="2245f-120">OpenAPI 是一種規格。</span><span class="sxs-lookup"><span data-stu-id="2245f-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="2245f-121">Swagger 是使用 OpenAPI 規格的工具。</span><span class="sxs-lookup"><span data-stu-id="2245f-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="2245f-122">例如，OpenAPIGenerator 和 Swashbuckle.aspnetcore.swaggerui。</span><span class="sxs-lookup"><span data-stu-id="2245f-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="2245f-123">) 上的 OpenAPI 規格 ( # B0</span><span class="sxs-lookup"><span data-stu-id="2245f-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="2245f-124">OpenAPI 規格是描述 API 功能的檔。</span><span class="sxs-lookup"><span data-stu-id="2245f-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="2245f-125">檔是以控制器和模型內的 XML 和屬性注釋為基礎。</span><span class="sxs-lookup"><span data-stu-id="2245f-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="2245f-126">它是 OpenAPI 流程的核心部分，用來推動像是 Swashbuckle.aspnetcore.swaggerui 之類的工具。</span><span class="sxs-lookup"><span data-stu-id="2245f-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="2245f-127">預設會將其命名為 *openapi.js開啟* 。</span><span class="sxs-lookup"><span data-stu-id="2245f-127">By default, it's named *openapi.json* .</span></span> <span data-ttu-id="2245f-128">以下是 OpenAPI 規格的範例，為了簡潔起見，已縮減：</span><span class="sxs-lookup"><span data-stu-id="2245f-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="2245f-129">Swagger UI</span><span class="sxs-lookup"><span data-stu-id="2245f-129">Swagger UI</span></span>

<span data-ttu-id="2245f-130">[SWAGGER ui](https://swagger.io/swagger-ui/) 會提供 web UI，以提供服務的相關資訊，並使用所產生的 OpenAPI 規格。</span><span class="sxs-lookup"><span data-stu-id="2245f-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="2245f-131">Swashbuckle 和 NSwag 都包含內嵌的 Swagger UI 版本，因此它可以使用中介軟體的登錄呼叫裝載在 ASP.NET Core 應用程式中。</span><span class="sxs-lookup"><span data-stu-id="2245f-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="2245f-132">Web UI 顯示如下：</span><span class="sxs-lookup"><span data-stu-id="2245f-132">The web UI looks like this:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="2245f-134">控制器中的每個公用動作方法都可以從 UI 進行測試。</span><span class="sxs-lookup"><span data-stu-id="2245f-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="2245f-135">選取方法名稱以展開區段。</span><span class="sxs-lookup"><span data-stu-id="2245f-135">Select a method name to expand the section.</span></span> <span data-ttu-id="2245f-136">新增任何必要的參數，然後選取 [立即 **試用]！** 。</span><span class="sxs-lookup"><span data-stu-id="2245f-136">Add any necessary parameters, and select **Try it out!** .</span></span>

![範例 Swagger GET 測試](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="2245f-138">用於螢幕擷取畫面的 Swagger UI 版本為第 2 版。</span><span class="sxs-lookup"><span data-stu-id="2245f-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="2245f-139">如需第 3 版的範例，請參閱 [Petstore 範例](https://petstore.swagger.io/)。</span><span class="sxs-lookup"><span data-stu-id="2245f-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="2245f-140">下一步</span><span class="sxs-lookup"><span data-stu-id="2245f-140">Next steps</span></span>

* [<span data-ttu-id="2245f-141">開始使用 Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="2245f-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="2245f-142">開始使用 NSwag</span><span class="sxs-lookup"><span data-stu-id="2245f-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
