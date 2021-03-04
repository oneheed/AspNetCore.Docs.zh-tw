---
title: 建立 .NET 應用程式的 Protobuf 訊息
author: jamesnk
description: 瞭解如何建立 .NET 應用程式的 Protobuf 訊息。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/12/2021
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
uid: grpc/protobuf
ms.openlocfilehash: 65a84c34e182b7a330d14e1f7ace17790ee4ed47
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2021
ms.locfileid: "102110166"
---
# <a name="create-protobuf-messages-for-net-apps"></a><span data-ttu-id="40490-103">建立 .NET 應用程式的 Protobuf 訊息</span><span class="sxs-lookup"><span data-stu-id="40490-103">Create Protobuf messages for .NET apps</span></span>

<span data-ttu-id="40490-104">依 [James 牛頓](https://twitter.com/jamesnk) 和 [Mark Rendle](https://twitter.com/markrendle)</span><span class="sxs-lookup"><span data-stu-id="40490-104">By [James Newton-King](https://twitter.com/jamesnk) and [Mark Rendle](https://twitter.com/markrendle)</span></span>

<span data-ttu-id="40490-105">gRPC 會使用 [Protobuf](https://developers.google.com/protocol-buffers) 作為其介面定義語言 (IDL) 。</span><span class="sxs-lookup"><span data-stu-id="40490-105">gRPC uses [Protobuf](https://developers.google.com/protocol-buffers) as its Interface Definition Language (IDL).</span></span> <span data-ttu-id="40490-106">Protobuf IDL 是一種語言中性格式，用於指定 gRPC 服務所傳送和接收的訊息。</span><span class="sxs-lookup"><span data-stu-id="40490-106">Protobuf IDL is a language neutral format for specifying the messages sent and received by gRPC services.</span></span> <span data-ttu-id="40490-107">Protobuf 訊息是在檔案中定義 `.proto` 。</span><span class="sxs-lookup"><span data-stu-id="40490-107">Protobuf messages are defined in `.proto` files.</span></span> <span data-ttu-id="40490-108">本檔說明 Protobuf 概念如何對應至 .NET。</span><span class="sxs-lookup"><span data-stu-id="40490-108">This document explains how Protobuf concepts map to .NET.</span></span>

## <a name="protobuf-messages"></a><span data-ttu-id="40490-109">Protobuf 訊息</span><span class="sxs-lookup"><span data-stu-id="40490-109">Protobuf messages</span></span>

<span data-ttu-id="40490-110">訊息是 Protobuf 中的主要資料傳輸物件。</span><span class="sxs-lookup"><span data-stu-id="40490-110">Messages are the main data transfer object in Protobuf.</span></span> <span data-ttu-id="40490-111">它們在概念上類似于 .NET 類別。</span><span class="sxs-lookup"><span data-stu-id="40490-111">They are conceptually similar to .NET classes.</span></span>

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

<span data-ttu-id="40490-112">上述訊息定義將三個欄位指定為名稱/值配對。</span><span class="sxs-lookup"><span data-stu-id="40490-112">The preceding message definition specifies three fields as name-value pairs.</span></span> <span data-ttu-id="40490-113">如同 .NET 類型上的屬性，每個欄位都有名稱和類型。</span><span class="sxs-lookup"><span data-stu-id="40490-113">Like properties on .NET types, each field has a name and a type.</span></span> <span data-ttu-id="40490-114">欄位類型可以是 Protobuf 純量實 [數值型別](#scalar-value-types)，例如 `int32` 或其他訊息。</span><span class="sxs-lookup"><span data-stu-id="40490-114">The field type can be a [Protobuf scalar value type](#scalar-value-types), e.g. `int32`, or another message.</span></span>

<span data-ttu-id="40490-115">[Protobuf 樣式指南](https://developers.google.com/protocol-buffers/docs/style)建議使用 `underscore_separated_names` 來做為功能變數名稱。</span><span class="sxs-lookup"><span data-stu-id="40490-115">The [Protobuf style guide](https://developers.google.com/protocol-buffers/docs/style) recommends using `underscore_separated_names` for field names.</span></span> <span data-ttu-id="40490-116">針對 .NET 應用程式所建立的新 Protobuf 訊息應遵循 Protobuf 樣式指導方針。</span><span class="sxs-lookup"><span data-stu-id="40490-116">New Protobuf messages created for .NET apps should follow the Protobuf style guidelines.</span></span> <span data-ttu-id="40490-117">.NET 工具會自動產生使用 .NET 命名標準的 .NET 類型。</span><span class="sxs-lookup"><span data-stu-id="40490-117">.NET tooling automatically generates .NET types that use .NET naming standards.</span></span> <span data-ttu-id="40490-118">例如， `first_name` Protobuf 欄位會產生 `FirstName` .net 屬性。</span><span class="sxs-lookup"><span data-stu-id="40490-118">For example, a `first_name` Protobuf field generates a `FirstName` .NET property.</span></span>

<span data-ttu-id="40490-119">除了名稱之外，訊息定義中的每個欄位都有唯一的數位。</span><span class="sxs-lookup"><span data-stu-id="40490-119">In addition to a name, each field in the message definition has a unique number.</span></span> <span data-ttu-id="40490-120">當訊息序列化為 Protobuf 時，會使用欄位號碼來識別欄位。</span><span class="sxs-lookup"><span data-stu-id="40490-120">Field numbers are used to identify fields when the message is serialized to Protobuf.</span></span> <span data-ttu-id="40490-121">將小數位序列化比序列化整個功能變數名稱更快。</span><span class="sxs-lookup"><span data-stu-id="40490-121">Serializing a small number is faster than serializing the entire field name.</span></span> <span data-ttu-id="40490-122">因為欄位編號可識別欄位，所以變更時請務必小心。</span><span class="sxs-lookup"><span data-stu-id="40490-122">Because field numbers identify a field it is important to take care when changing them.</span></span> <span data-ttu-id="40490-123">如需變更 Protobuf 訊息的詳細資訊，請參閱 <xref:grpc/versioning> 。</span><span class="sxs-lookup"><span data-stu-id="40490-123">For more information about changing Protobuf messages see <xref:grpc/versioning>.</span></span>

<span data-ttu-id="40490-124">建立應用程式時，Protobuf 工具會從檔案產生 .NET 類型 `.proto` 。</span><span class="sxs-lookup"><span data-stu-id="40490-124">When an app is built the Protobuf tooling generates .NET types from `.proto` files.</span></span> <span data-ttu-id="40490-125">此 `Person` 訊息會產生 .net 類別：</span><span class="sxs-lookup"><span data-stu-id="40490-125">The `Person` message generates a .NET class:</span></span>

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

<span data-ttu-id="40490-126">如需 Protobuf 訊息的詳細資訊，請參閱 [Protobuf 語言指南](https://developers.google.com/protocol-buffers/docs/proto3#simple)。</span><span class="sxs-lookup"><span data-stu-id="40490-126">For more information about Protobuf messages see the [Protobuf language guide](https://developers.google.com/protocol-buffers/docs/proto3#simple).</span></span>

## <a name="scalar-value-types"></a><span data-ttu-id="40490-127">純量數值型別</span><span class="sxs-lookup"><span data-stu-id="40490-127">Scalar Value Types</span></span>

<span data-ttu-id="40490-128">Protobuf 支援一系列原生純量實數值型別。</span><span class="sxs-lookup"><span data-stu-id="40490-128">Protobuf supports a range of native scalar value types.</span></span> <span data-ttu-id="40490-129">下表列出它們都具有對等的 c # 型別：</span><span class="sxs-lookup"><span data-stu-id="40490-129">The following table lists them all with their equivalent C# type:</span></span>

| <span data-ttu-id="40490-130">Protobuf 類型</span><span class="sxs-lookup"><span data-stu-id="40490-130">Protobuf type</span></span> | <span data-ttu-id="40490-131">C# 類型</span><span class="sxs-lookup"><span data-stu-id="40490-131">C# type</span></span>      |
| ------------- | ------------ |
| `double`      | `double`     |
| `float`       | `float`      |
| `int32`       | `int`        |
| `int64`       | `long`       |
| `uint32`      | `uint`       |
| `uint64`      | `ulong`      |
| `sint32`      | `int`        |
| `sint64`      | `long`       |
| `fixed32`     | `uint`       |
| `fixed64`     | `ulong`      |
| `sfixed32`    | `int`        |
| `sfixed64`    | `long`       |
| `bool`        | `bool`       |
| `string`      | `string`     |
| `bytes`       | `ByteString` |

<span data-ttu-id="40490-132">純量值一律會有預設值，而且不能設定為 `null` 。</span><span class="sxs-lookup"><span data-stu-id="40490-132">Scalar values always have a default value and can't be set to `null`.</span></span> <span data-ttu-id="40490-133">此條件約束包含 `string` 和 `ByteString` c # 類別。</span><span class="sxs-lookup"><span data-stu-id="40490-133">This constraint includes `string` and `ByteString` which are C# classes.</span></span> <span data-ttu-id="40490-134">`string` 預設值為空字串值， `ByteString` 預設為空的位元組值。</span><span class="sxs-lookup"><span data-stu-id="40490-134">`string` defaults to an empty string value and `ByteString` defaults to an empty bytes value.</span></span> <span data-ttu-id="40490-135">嘗試將其設定為擲回 `null` 錯誤。</span><span class="sxs-lookup"><span data-stu-id="40490-135">Attempting to set them to `null` throws an error.</span></span>

<span data-ttu-id="40490-136">可為 null 的包裝函式[類型](#nullable-types)可用來支援 null 值。</span><span class="sxs-lookup"><span data-stu-id="40490-136">[Nullable wrapper types](#nullable-types) can be used to support null values.</span></span>

### <a name="dates-and-times"></a><span data-ttu-id="40490-137">日期和時間</span><span class="sxs-lookup"><span data-stu-id="40490-137">Dates and times</span></span>

<span data-ttu-id="40490-138">原生純量類型不提供日期和時間值，相當於。NET 的 <xref:System.DateTimeOffset> 、 <xref:System.DateTime> 和 <xref:System.TimeSpan> 。</span><span class="sxs-lookup"><span data-stu-id="40490-138">The native scalar types don't provide for date and time values, equivalent to .NET's <xref:System.DateTimeOffset>, <xref:System.DateTime>, and <xref:System.TimeSpan>.</span></span> <span data-ttu-id="40490-139">您可以使用一些 Protobuf 的 *已知類型* 延伸來指定這些類型。</span><span class="sxs-lookup"><span data-stu-id="40490-139">These types can be specified by using some of Protobuf's *Well-Known Types* extensions.</span></span> <span data-ttu-id="40490-140">這些擴充功能可在支援的平臺上，為複雜的欄位類型提供程式碼產生與執行時間支援。</span><span class="sxs-lookup"><span data-stu-id="40490-140">These extensions provide code generation and runtime support for complex field types across the supported platforms.</span></span>

<span data-ttu-id="40490-141">下表顯示日期和時間類型：</span><span class="sxs-lookup"><span data-stu-id="40490-141">The following table shows the date and time types:</span></span>

| <span data-ttu-id="40490-142">.NET 類型</span><span class="sxs-lookup"><span data-stu-id="40490-142">.NET type</span></span>        | <span data-ttu-id="40490-143">Protobuf Well-Known 類型</span><span class="sxs-lookup"><span data-stu-id="40490-143">Protobuf Well-Known Type</span></span>    |
| ---------------- | --------------------------- |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime`       | `google.protobuf.Timestamp` |
| `TimeSpan`       | `google.protobuf.Duration`  |

```protobuf  
syntax = "proto3"

import "google/protobuf/duration.proto";  
import "google/protobuf/timestamp.proto";

message Meeting {
    string subject = 1;
    google.protobuf.Timestamp start = 2;
    google.protobuf.Duration duration = 3;
}  
```

<span data-ttu-id="40490-144">C # 類別中產生的屬性不是 .NET 日期和時間類型。</span><span class="sxs-lookup"><span data-stu-id="40490-144">The generated properties in the C# class aren't the .NET date and time types.</span></span> <span data-ttu-id="40490-145">屬性使用 `Timestamp` `Duration` 命名空間中的和類別 `Google.Protobuf.WellKnownTypes` 。</span><span class="sxs-lookup"><span data-stu-id="40490-145">The properties use the `Timestamp` and `Duration` classes in the `Google.Protobuf.WellKnownTypes` namespace.</span></span> <span data-ttu-id="40490-146">這些類別提供在、和之間轉換的 `DateTimeOffset` 方法 `DateTime` `TimeSpan` 。</span><span class="sxs-lookup"><span data-stu-id="40490-146">These classes provide methods for converting to and from `DateTimeOffset`, `DateTime`, and `TimeSpan`.</span></span>

```csharp
// Create Timestamp and Duration from .NET DateTimeOffset and TimeSpan.
var meeting = new Meeting
{
    Time = Timestamp.FromDateTimeOffset(meetingTime), // also FromDateTime()
    Duration = Duration.FromTimeSpan(meetingLength)
};

// Convert Timestamp and Duration to .NET DateTimeOffset and TimeSpan.
var time = meeting.Time.ToDateTimeOffset();
var duration = meeting.Duration?.ToTimeSpan();
```

> [!NOTE]
> <span data-ttu-id="40490-147">此 `Timestamp` 類型適用于 UTC 時間。</span><span class="sxs-lookup"><span data-stu-id="40490-147">The `Timestamp` type works with UTC times.</span></span> <span data-ttu-id="40490-148">`DateTimeOffset` 值的位移一律為零，而且 `DateTime.Kind` 屬性一律為 `DateTimeKind.Utc` 。</span><span class="sxs-lookup"><span data-stu-id="40490-148">`DateTimeOffset` values always have an offset of zero, and the `DateTime.Kind` property is always `DateTimeKind.Utc`.</span></span>

### <a name="nullable-types"></a><span data-ttu-id="40490-149">可為 Null 的型別</span><span class="sxs-lookup"><span data-stu-id="40490-149">Nullable types</span></span>

<span data-ttu-id="40490-150">C # 的 Protobuf 程式碼產生會使用原生類型，例如 `int` for `int32` 。</span><span class="sxs-lookup"><span data-stu-id="40490-150">The Protobuf code generation for C# uses the native types, such as `int` for `int32`.</span></span> <span data-ttu-id="40490-151">因此，一律會包含這些值，而且不能是 `null` 。</span><span class="sxs-lookup"><span data-stu-id="40490-151">So the values are always included and can't be `null`.</span></span>

<span data-ttu-id="40490-152">對於需要明確的值（ `null` 例如 `int?` 在 c # 程式碼中使用），Protobuf 的 Well-Known 類型包含編譯成可為 Null 的 c # 類型的包裝函式。</span><span class="sxs-lookup"><span data-stu-id="40490-152">For values that require explicit `null`, such as using `int?` in C# code, Protobuf's Well-Known Types include wrappers that are compiled to nullable C# types.</span></span> <span data-ttu-id="40490-153">若要使用這些檔案，請將其匯入 `wrappers.proto` 您 `.proto` 的檔案，如下列程式碼所示：</span><span class="sxs-lookup"><span data-stu-id="40490-153">To use them, import `wrappers.proto` into your `.proto` file, like the following code:</span></span>

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

<span data-ttu-id="40490-154">`wrappers.proto` 類型不會在產生的屬性中公開。</span><span class="sxs-lookup"><span data-stu-id="40490-154">`wrappers.proto` types aren't exposed in generated properties.</span></span> <span data-ttu-id="40490-155">Protobuf 會自動將它們對應至 c # 訊息中適當的 .NET 可為 null 類型。</span><span class="sxs-lookup"><span data-stu-id="40490-155">Protobuf automatically maps them to appropriate .NET nullable types in C# messages.</span></span> <span data-ttu-id="40490-156">例如，欄位會 `google.protobuf.Int32Value` 產生 `int?` 屬性。</span><span class="sxs-lookup"><span data-stu-id="40490-156">For example, a `google.protobuf.Int32Value` field generates an `int?` property.</span></span> <span data-ttu-id="40490-157">參考型別屬性（例如 `string` 和 `ByteString` ）不會變更，但 `null` 可以指派給它們，而不會發生錯誤。</span><span class="sxs-lookup"><span data-stu-id="40490-157">Reference type properties like `string` and `ByteString` are unchanged except `null` can be assigned to them without error.</span></span>

<span data-ttu-id="40490-158">下表顯示包裝函式類型的完整清單及其對等的 c # 類型：</span><span class="sxs-lookup"><span data-stu-id="40490-158">The following table shows the complete list of wrapper types with their equivalent C# type:</span></span>

| <span data-ttu-id="40490-159">C# 類型</span><span class="sxs-lookup"><span data-stu-id="40490-159">C# type</span></span>      | <span data-ttu-id="40490-160">Well-Known 型別包裝函式</span><span class="sxs-lookup"><span data-stu-id="40490-160">Well-Known Type wrapper</span></span>       |
| ------------ | ----------------------------- |
| `bool?`      | `google.protobuf.BoolValue`   |
| `double?`    | `google.protobuf.DoubleValue` |
| `float?`     | `google.protobuf.FloatValue`  |
| `int?`       | `google.protobuf.Int32Value`  |
| `long?`      | `google.protobuf.Int64Value`  |
| `uint?`      | `google.protobuf.UInt32Value` |
| `ulong?`     | `google.protobuf.UInt64Value` |
| `string`     | `google.protobuf.StringValue` |
| `ByteString` | `google.protobuf.BytesValue`  |

### <a name="bytes"></a><span data-ttu-id="40490-161">位元組</span><span class="sxs-lookup"><span data-stu-id="40490-161">Bytes</span></span>

<span data-ttu-id="40490-162">Protobuf 具有純量數值型別的支援二進位承載 `bytes` 。</span><span class="sxs-lookup"><span data-stu-id="40490-162">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="40490-163">C # 中產生的屬性會使用 `ByteString` 做為屬性類型。</span><span class="sxs-lookup"><span data-stu-id="40490-163">A generated property in C# uses `ByteString` as the property type.</span></span>

<span data-ttu-id="40490-164">使用 `ByteString.CopyFrom(byte[] data)` 從位元組陣列建立新的實例：</span><span class="sxs-lookup"><span data-stu-id="40490-164">Use `ByteString.CopyFrom(byte[] data)` to create a new instance from a byte array:</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = ByteString.CopyFrom(data);
```

<span data-ttu-id="40490-165">`ByteString` 您可以使用或直接存取資料 `ByteString.Span` `ByteString.Memory` 。</span><span class="sxs-lookup"><span data-stu-id="40490-165">`ByteString` data is accessed directly using `ByteString.Span` or `ByteString.Memory`.</span></span> <span data-ttu-id="40490-166">或呼叫將 `ByteString.ToByteArray()` 實例轉換回位元組陣列：</span><span class="sxs-lookup"><span data-stu-id="40490-166">Or call `ByteString.ToByteArray()` to convert an instance back into a byte array:</span></span>

```csharp
var payload = await client.GetPayload(new PayloadRequest());

await File.WriteAllBytesAsync(path, payload.Data.ToByteArray());
```

### <a name="decimals"></a><span data-ttu-id="40490-167">小數位數</span><span class="sxs-lookup"><span data-stu-id="40490-167">Decimals</span></span>

<span data-ttu-id="40490-168">Protobuf 本身並不支援 .NET `decimal` 型別， `double` 而是和 `float` 。</span><span class="sxs-lookup"><span data-stu-id="40490-168">Protobuf doesn't natively support the .NET `decimal` type, just `double` and `float`.</span></span> <span data-ttu-id="40490-169">Protobuf 專案中有一項持續的討論，當中有可能會將標準的 decimal 型別新增至 Well-Known 類型，並提供支援的語言和架構平臺支援。</span><span class="sxs-lookup"><span data-stu-id="40490-169">There's an ongoing discussion in the Protobuf project about the possibility of adding a standard decimal type to the Well-Known Types, with platform support for languages and frameworks that support it.</span></span> <span data-ttu-id="40490-170">尚未執行任何動作。</span><span class="sxs-lookup"><span data-stu-id="40490-170">Nothing has been implemented yet.</span></span>

<span data-ttu-id="40490-171">您可以建立訊息定義來代表可在 `decimal` .net 用戶端和伺服器之間進行安全序列化的型別。</span><span class="sxs-lookup"><span data-stu-id="40490-171">It's possible to create a message definition to represent the `decimal` type that works for safe serialization between .NET clients and servers.</span></span> <span data-ttu-id="40490-172">但是，其他平臺上的開發人員必須瞭解所使用的格式，並對其執行自己的處理。</span><span class="sxs-lookup"><span data-stu-id="40490-172">But developers on other platforms would have to understand the format being used and implement their own handling for it.</span></span>

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a><span data-ttu-id="40490-173">建立 Protobuf 的自訂十進位類型</span><span class="sxs-lookup"><span data-stu-id="40490-173">Creating a custom decimal type for Protobuf</span></span>

```protobuf
package CustomTypes;

// Example: 12345.6789 -> { units = 12345, nanos = 678900000 }
message DecimalValue {

    // Whole units part of the amount
    int64 units = 1;

    // Nano units of the amount (10^-9)
    // Must be same sign as units
    sfixed32 nanos = 2;
}
```

<span data-ttu-id="40490-174">`nanos`欄位表示從到的 `0.999_999_999` 值 `-0.999_999_999` 。</span><span class="sxs-lookup"><span data-stu-id="40490-174">The `nanos` field represents values from `0.999_999_999` to `-0.999_999_999`.</span></span> <span data-ttu-id="40490-175">例如，此 `decimal` 值會 `1.5m` 表示為 `{ units = 1, nanos = 500_000_000 }` 。</span><span class="sxs-lookup"><span data-stu-id="40490-175">For example, the `decimal` value `1.5m` would be represented as `{ units = 1, nanos = 500_000_000 }`.</span></span> <span data-ttu-id="40490-176">這就是為什麼 `nanos` 此範例中的欄位使用 `sfixed32` 型別，其編碼方式比 `int32` 較大的值更有效率。</span><span class="sxs-lookup"><span data-stu-id="40490-176">This is why the `nanos` field in this example uses the `sfixed32` type, which encodes more efficiently than `int32` for larger values.</span></span> <span data-ttu-id="40490-177">如果 `units` 欄位是負數，則 `nanos` 欄位也應為負數。</span><span class="sxs-lookup"><span data-stu-id="40490-177">If the `units` field is negative, the `nanos` field should also be negative.</span></span>

> [!NOTE]
> <span data-ttu-id="40490-178">有多個其他演算法可將 `decimal` 值編碼為位元組字串，但這則訊息比任何這些演算法更容易瞭解。</span><span class="sxs-lookup"><span data-stu-id="40490-178">There are multiple other algorithms for encoding `decimal` values as byte strings, but this message is easier to understand than any of them.</span></span> <span data-ttu-id="40490-179">在不同平臺上，這些值不會受到位元組由大到小或位元組由大到小的影響。</span><span class="sxs-lookup"><span data-stu-id="40490-179">The values are not affected by big-endian or little-endian on different platforms.</span></span>

<span data-ttu-id="40490-180">此類型與 BCL 類型之間的轉換 `decimal` 可能會在 c # 中執行，如下所示：</span><span class="sxs-lookup"><span data-stu-id="40490-180">Conversion between this type and the BCL `decimal` type might be implemented in C# like this:</span></span>

```csharp
namespace CustomTypes
{
    public partial class DecimalValue
    {
        private const decimal NanoFactor = 1_000_000_000;
        public DecimalValue(long units, int nanos)
        {
            Units = units;
            Nanos = nanos;
        }

        public long Units { get; }
        public int Nanos { get; }

        public static implicit operator decimal(CustomTypes.DecimalValue grpcDecimal)
        {
            return grpcDecimal.Units + grpcDecimal.Nanos / NanoFactor;
        }

        public static implicit operator CustomTypes.DecimalValue(decimal value)
        {
            var units = decimal.ToInt64(value);
            var nanos = decimal.ToInt32((value - units) * NanoFactor);
            return new CustomTypes.DecimalValue(units, nanos);
        }
    }
}
```

## <a name="collections"></a><span data-ttu-id="40490-181">集合</span><span class="sxs-lookup"><span data-stu-id="40490-181">Collections</span></span>

### <a name="lists"></a><span data-ttu-id="40490-182">清單</span><span class="sxs-lookup"><span data-stu-id="40490-182">Lists</span></span>

<span data-ttu-id="40490-183">Protobuf 中的清單是藉由 `repeated` 在欄位上使用 prefix 關鍵字來指定。</span><span class="sxs-lookup"><span data-stu-id="40490-183">Lists in Protobuf are specified by using the `repeated` prefix keyword on a field.</span></span> <span data-ttu-id="40490-184">下列範例顯示如何建立清單：</span><span class="sxs-lookup"><span data-stu-id="40490-184">The following example shows how to create a list:</span></span>

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

<span data-ttu-id="40490-185">在產生的程式碼中， `repeated` 欄位會以 `Google.Protobuf.Collections.RepeatedField<T>` 泛型型別表示。</span><span class="sxs-lookup"><span data-stu-id="40490-185">In the generated code, `repeated` fields are represented by the `Google.Protobuf.Collections.RepeatedField<T>` generic type.</span></span>

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

<span data-ttu-id="40490-186">`RepeatedField<T>` 會實作 <xref:System.Collections.Generic.IList%601>。</span><span class="sxs-lookup"><span data-stu-id="40490-186">`RepeatedField<T>` implements <xref:System.Collections.Generic.IList%601>.</span></span> <span data-ttu-id="40490-187">因此，您可以使用 LINQ 查詢，或將它轉換成陣列或清單。</span><span class="sxs-lookup"><span data-stu-id="40490-187">So you can use LINQ queries or convert it to an array or a list.</span></span> <span data-ttu-id="40490-188">`RepeatedField<T>` 屬性沒有公用 setter。</span><span class="sxs-lookup"><span data-stu-id="40490-188">`RepeatedField<T>` properties don't have a public setter.</span></span> <span data-ttu-id="40490-189">專案應新增至現有的集合。</span><span class="sxs-lookup"><span data-stu-id="40490-189">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a><span data-ttu-id="40490-190">字典</span><span class="sxs-lookup"><span data-stu-id="40490-190">Dictionaries</span></span>

<span data-ttu-id="40490-191">.NET <xref:System.Collections.Generic.IDictionary%602> 類型在 Protobuf 中會使用來表示 `map<key_type, value_type>` 。</span><span class="sxs-lookup"><span data-stu-id="40490-191">The .NET <xref:System.Collections.Generic.IDictionary%602> type is represented in Protobuf using `map<key_type, value_type>`.</span></span>

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

<span data-ttu-id="40490-192">在產生的 .NET 程式碼中， `map` 欄位會以 `Google.Protobuf.Collections.MapField<TKey, TValue>` 泛型型別表示。</span><span class="sxs-lookup"><span data-stu-id="40490-192">In generated .NET code, `map` fields are represented by the `Google.Protobuf.Collections.MapField<TKey, TValue>` generic type.</span></span> <span data-ttu-id="40490-193">`MapField<TKey, TValue>` 會實作 <xref:System.Collections.Generic.IDictionary%602>。</span><span class="sxs-lookup"><span data-stu-id="40490-193">`MapField<TKey, TValue>` implements <xref:System.Collections.Generic.IDictionary%602>.</span></span> <span data-ttu-id="40490-194">如同 `repeated` 屬性， `map` 屬性沒有公用 setter。</span><span class="sxs-lookup"><span data-stu-id="40490-194">Like `repeated` properties, `map` properties don't have a public setter.</span></span> <span data-ttu-id="40490-195">專案應新增至現有的集合。</span><span class="sxs-lookup"><span data-stu-id="40490-195">Items should be added to the existing collection.</span></span>

```csharp
var person = new Person();

// Add one item.
person.Attributes["created_by"] = "James";

// Add all items from another collection.
var attributes = new Dictionary<string, string>
{
    ["last_modified"] = DateTime.UtcNow.ToString()
};
person.Attributes.Add(attributes);
```

## <a name="unstructured-and-conditional-messages"></a><span data-ttu-id="40490-196">非結構化和條件式訊息</span><span class="sxs-lookup"><span data-stu-id="40490-196">Unstructured and conditional messages</span></span>

<span data-ttu-id="40490-197">Protobuf 是合約優先的訊息格式。</span><span class="sxs-lookup"><span data-stu-id="40490-197">Protobuf is a contract-first messaging format.</span></span> <span data-ttu-id="40490-198">建立應用程式時，必須在檔案中指定應用程式的訊息，包括其欄位和類型 `.proto` 。</span><span class="sxs-lookup"><span data-stu-id="40490-198">An app's messages, including its fields and types, must be specified in `.proto` files when the app is built.</span></span> <span data-ttu-id="40490-199">Protobuf 的合約優先設計很適合用來強制訊息內容，但可以限制不需要嚴格合約的案例：</span><span class="sxs-lookup"><span data-stu-id="40490-199">Protobuf's contract-first design is great at enforcing message content but can limit scenarios where a strict contract isn't required:</span></span>

* <span data-ttu-id="40490-200">具有未知承載的訊息。</span><span class="sxs-lookup"><span data-stu-id="40490-200">Messages with unknown payloads.</span></span> <span data-ttu-id="40490-201">例如，具有可包含任何訊息之欄位的訊息。</span><span class="sxs-lookup"><span data-stu-id="40490-201">For example, a message with a field that could contain any message.</span></span>
* <span data-ttu-id="40490-202">條件式訊息。</span><span class="sxs-lookup"><span data-stu-id="40490-202">Conditional messages.</span></span> <span data-ttu-id="40490-203">例如，從 gRPC 服務傳回的訊息可能是成功結果或錯誤結果。</span><span class="sxs-lookup"><span data-stu-id="40490-203">For example, a message returned from a gRPC service might be a success result or an error result.</span></span>
* <span data-ttu-id="40490-204">動態值。</span><span class="sxs-lookup"><span data-stu-id="40490-204">Dynamic values.</span></span> <span data-ttu-id="40490-205">例如，包含包含非結構化值之欄位的訊息，類似于 JSON。</span><span class="sxs-lookup"><span data-stu-id="40490-205">For example, a message with a field that contains an unstructured collection of values, similar to JSON.</span></span>

<span data-ttu-id="40490-206">Protobuf 提供語言功能和類型來支援這些案例。</span><span class="sxs-lookup"><span data-stu-id="40490-206">Protobuf offers language features and types to support these scenarios.</span></span>

### <a name="any"></a><span data-ttu-id="40490-207">任意</span><span class="sxs-lookup"><span data-stu-id="40490-207">Any</span></span>

<span data-ttu-id="40490-208">`Any`型別可讓您使用訊息做為內嵌類型，而不需要其 `.proto` 定義。</span><span class="sxs-lookup"><span data-stu-id="40490-208">The `Any` type lets you use messages as embedded types without having their `.proto` definition.</span></span> <span data-ttu-id="40490-209">若要使用 `Any` 類型，請匯入 `any.proto` 。</span><span class="sxs-lookup"><span data-stu-id="40490-209">To use the `Any` type, import `any.proto`.</span></span>

```protobuf
import "google/protobuf/any.proto";

message Status {
    string message = 1;
    google.protobuf.Any detail = 2;
}
```

```csharp
// Create a status with a Person message set to detail.
var status = new ErrorStatus();
status.Detail = Any.Pack(new Person { FirstName = "James" });

// Read Person message from detail.
if (status.Detail.Is(Person.Descriptor))
{
    var person = status.Detail.Unpack<Person>();
    // ...
}
```

### <a name="oneof"></a><span data-ttu-id="40490-210">Oneof</span><span class="sxs-lookup"><span data-stu-id="40490-210">Oneof</span></span>

<span data-ttu-id="40490-211">`oneof` 欄位是語言功能。</span><span class="sxs-lookup"><span data-stu-id="40490-211">`oneof` fields are a language feature.</span></span> <span data-ttu-id="40490-212">編譯器 `oneof` 會在產生訊息類別時處理關鍵字。</span><span class="sxs-lookup"><span data-stu-id="40490-212">The compiler handles the `oneof` keyword when it generates the message class.</span></span> <span data-ttu-id="40490-213">使用指定可能會傳回 `oneof` 或的回應訊息， `Person` 可能如下 `Error` 所示：</span><span class="sxs-lookup"><span data-stu-id="40490-213">Using `oneof` to specify a response message that could either return a `Person` or `Error` might look like this:</span></span>

```protobuf
message Person {
    // ...
}

message Error {
    // ...
}

message ResponseMessage {
  oneof result {
    Error error = 1;
    Person person = 2;
  }
}
```

<span data-ttu-id="40490-214">集合內的欄位在 `oneof` 整體訊息宣告中必須有唯一的欄位編號。</span><span class="sxs-lookup"><span data-stu-id="40490-214">Fields within the `oneof` set must have unique field numbers in the overall message declaration.</span></span>

<span data-ttu-id="40490-215">使用時 `oneof` ，產生的 c # 程式碼會包含列舉，以指定已設定的欄位。</span><span class="sxs-lookup"><span data-stu-id="40490-215">When using `oneof`, the generated C# code includes an enum that specifies which of the fields has been set.</span></span> <span data-ttu-id="40490-216">您可以測試列舉，以找出已設定的欄位。</span><span class="sxs-lookup"><span data-stu-id="40490-216">You can test the enum to find which field is set.</span></span> <span data-ttu-id="40490-217">未設定的欄位會傳回 `null` 或預設值，而不是擲回例外狀況。</span><span class="sxs-lookup"><span data-stu-id="40490-217">Fields that aren't set return `null` or the default value, rather than throwing an exception.</span></span>

```csharp
var response = await client.GetPersonAsync(new RequestMessage());

switch (response.ResultCase)
{
    case ResponseMessage.ResultOneofCase.Person:
        HandlePerson(response.Person);
        break;
    case ResponseMessage.ResultOneofCase.Error:
        HandleError(response.Error);
        break;
    default:
        throw new ArgumentException("Unexpected result.");
}
```

### <a name="value"></a><span data-ttu-id="40490-218">值</span><span class="sxs-lookup"><span data-stu-id="40490-218">Value</span></span>

<span data-ttu-id="40490-219">`Value`型別代表動態類型的值。</span><span class="sxs-lookup"><span data-stu-id="40490-219">The `Value` type represents a dynamically typed value.</span></span> <span data-ttu-id="40490-220">它可以是 `null` 、數位、字串、布林值、值的字典 (`Struct`) ，或 () 的值清單 `ValueList` 。</span><span class="sxs-lookup"><span data-stu-id="40490-220">It can be either `null`, a number, a string, a boolean, a dictionary of values (`Struct`), or a list of values (`ValueList`).</span></span> <span data-ttu-id="40490-221">`Value` 是使用先前所討論之功能的 Protobuf Well-Known 類型 `oneof` 。</span><span class="sxs-lookup"><span data-stu-id="40490-221">`Value` is a Protobuf Well-Known Type that uses the previously discussed `oneof` feature.</span></span> <span data-ttu-id="40490-222">若要使用 `Value` 類型，請匯入 `struct.proto` 。</span><span class="sxs-lookup"><span data-stu-id="40490-222">To use the `Value` type, import `struct.proto`.</span></span>

```protobuf
import "google/protobuf/struct.proto";

message Status {
    // ...
    google.protobuf.Value data = 3;
}
```

```csharp
// Create dynamic values.
var status = new Status();
status.Data = Value.FromStruct(new Struct
{
    Fields =
    {
        ["enabled"] = Value.ForBoolean(true),
        ["metadata"] = Value.ForList(
            Value.FromString("value1"),
            Value.FromString("value2"))
    }
});

// Read dynamic values.
switch (status.Data.KindCase)
{
    case Value.KindOneofCase.StructValue:
        foreach (var field in status.Data.StructValue.Fields)
        {
            // Read struct fields...
        }
        break;
    // ...
}
```

<span data-ttu-id="40490-223">`Value`直接使用可以是詳細資訊。</span><span class="sxs-lookup"><span data-stu-id="40490-223">Using `Value` directly can be verbose.</span></span> <span data-ttu-id="40490-224">另一種使用方式 `Value` 是使用 Protobuf 的內建支援，將訊息對應至 JSON。</span><span class="sxs-lookup"><span data-stu-id="40490-224">An alternative way to use `Value` is with Protobuf's built-in support for mapping messages to JSON.</span></span> <span data-ttu-id="40490-225">Protobuf 的 `JsonFormatter` 和 `JsonWriter` 類型可以搭配任何 Protobuf 訊息使用。</span><span class="sxs-lookup"><span data-stu-id="40490-225">Protobuf's `JsonFormatter` and `JsonWriter` types can be used with any Protobuf message.</span></span> <span data-ttu-id="40490-226">`Value` 尤其適合在 JSON 之間進行轉換。</span><span class="sxs-lookup"><span data-stu-id="40490-226">`Value` is particularly well suited to being converted to and from JSON.</span></span>

<span data-ttu-id="40490-227">這是先前程式碼的 JSON 對等專案：</span><span class="sxs-lookup"><span data-stu-id="40490-227">This is the JSON equivalent of the previous code:</span></span>

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Data);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a><span data-ttu-id="40490-228">其他資源</span><span class="sxs-lookup"><span data-stu-id="40490-228">Additional resources</span></span>

* [<span data-ttu-id="40490-229">Protobuf 語言指南</span><span class="sxs-lookup"><span data-stu-id="40490-229">Protobuf language guide</span></span>](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
