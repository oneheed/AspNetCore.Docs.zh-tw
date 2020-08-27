---
title: 建立 .NET 應用程式的 Protobuf 訊息
author: jamesnk
description: 瞭解如何建立 .NET 應用程式的 Protobuf 訊息。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
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
ms.openlocfilehash: b8149b79c1e7b204e52cc8595d1193b623bb0008
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945668"
---
# <a name="create-protobuf-messages-for-net-apps"></a>建立 .NET 應用程式的 Protobuf 訊息

依 [James 牛頓](https://twitter.com/jamesnk) 和 [Mark Rendle](https://twitter.com/markrendle)

gRPC 會使用 [Protobuf](https://developers.google.com/protocol-buffers) 作為其介面定義語言 (IDL) 。 Protobuf IDL 是一種語言中性格式，用於指定 gRPC 服務所傳送和接收的訊息。 Protobuf 訊息是在 *proto* 檔案中定義。 本檔說明 Protobuf 概念如何對應至 .NET。

## <a name="protobuf-messages"></a>Protobuf 訊息

訊息是 Protobuf 中的主要資料傳輸物件。 它們在概念上類似于 .NET 類別。

```protobuf
syntax = "proto3";

option csharp_namespace = "Contoso.Messages";

message Person {
    int32 id = 1;
    string first_name = 2;
    string last_name = 3;
}  
```

上述訊息定義將三個欄位指定為名稱/值配對。 如同 .NET 類型上的屬性，每個欄位都有名稱和類型。 欄位類型可以是 Protobuf 純量實 [數值型別](#scalar-value-types)，例如 `int32` 或其他訊息。

除了名稱之外，訊息定義中的每個欄位都有唯一的數位。 當訊息序列化為 Protobuf 時，會使用欄位號碼來識別欄位。 將小數位序列化比序列化整個功能變數名稱更快。 因為欄位編號可識別欄位，所以變更時請務必小心。 如需變更 Protobuf 訊息的詳細資訊，請參閱 <xref:grpc/versioning> 。

建立應用程式時，Protobuf 工具會從 *proto* 檔案產生 .net 類型。 此 `Person` 訊息會產生 .net 類別：

```csharp
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

如需 Protobuf 訊息的詳細資訊，請參閱 [Protobuf 語言指南](https://developers.google.com/protocol-buffers/docs/proto3#simple)。

## <a name="scalar-value-types"></a>純量數值型別

Protobuf 支援一系列原生純量實數值型別。 下表列出它們都具有對等的 c # 型別：

| Protobuf 類型 | C# 類型      |
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

### <a name="dates-and-times"></a>日期和時間

原生純量類型不提供日期和時間值，相當於。NET 的 <xref:System.DateTimeOffset> 、 <xref:System.DateTime> 和 <xref:System.TimeSpan> 。 您可以使用一些 Protobuf 的「已知類型」延伸模組來指定這些類型。 這些擴充功能可在支援的平臺上，為複雜的欄位類型提供程式碼產生與執行時間支援。

下表顯示日期和時間類型：

| .NET 類型 | Protobuf 知名型別 |
| ------- | ------------------------ |
| `DateTimeOffset` | `google.protobuf.Timestamp` |
| `DateTime` | `google.protobuf.Timestamp` |
| `TimeSpan` | `google.protobuf.Duration` |

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

C # 類別中產生的屬性不是 .NET 日期和時間類型。 屬性使用 `Timestamp` `Duration` 命名空間中的和類別 `Google.Protobuf.WellKnownTypes` 。 這些類別提供在、和之間轉換的 `DateTimeOffset` 方法 `DateTime` `TimeSpan` 。

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
> 此 `Timestamp` 類型適用于 UTC 時間。 `DateTimeOffset` 值的位移一律為零，而且 `DateTime.Kind` 屬性一律為 `DateTimeKind.Utc` 。

### <a name="nullable-types"></a>可為 Null 的型別

C # 的 Protobuf 程式碼產生會使用原生類型，例如 `int` for `int32` 。 因此，一律會包含這些值，而且不能是 `null` 。

對於需要明確的值（ `null` 例如 `int?` 在 c # 程式碼中使用），Protobuf 的「已知型別」會包含編譯成可為 Null 的 c # 型別的包裝函式。 若要使用這些檔案，請將其匯入 `wrappers.proto` 您 `.proto` 的檔案，如下列程式碼所示：

```protobuf  
syntax = "proto3"

import "google/protobuf/wrappers.proto"

message Person {
    // ...
    google.protobuf.Int32Value age = 5;
}
```

Protobuf 使用 .NET 可為 null 的型別，例如， `int?` 針對產生的訊息屬性。

下表顯示包裝函式類型的完整清單及其對等的 c # 類型：

| C# 類型   | 知名的型別包裝函式       |
| --------- | ----------------------------- |
| `bool?`   | `google.protobuf.BoolValue`   |
| `double?` | `google.protobuf.DoubleValue` |
| `float?`  | `google.protobuf.FloatValue`  |
| `int?`    | `google.protobuf.Int32Value`  |
| `long?`   | `google.protobuf.Int64Value`  |
| `uint?`   | `google.protobuf.UInt32Value` |
| `ulong?`  | `google.protobuf.UInt64Value` |

### <a name="decimals"></a>小數位數

Protobuf 本身並不支援 .NET `decimal` 型別， `double` 而是和 `float` 。 Protobuf 專案中有一項持續的討論，當中有可能會將標準的 decimal 型別新增至已知型別，並提供支援的語言和架構平臺支援。 尚未執行任何動作。

您可以建立訊息定義來代表可在 `decimal` .net 用戶端和伺服器之間進行安全序列化的型別。 但是，其他平臺上的開發人員必須瞭解所使用的格式，並對其執行自己的處理。

#### <a name="creating-a-custom-decimal-type-for-protobuf"></a>建立 Protobuf 的自訂十進位類型

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

`nanos`欄位表示從到的 `0.999_999_999` 值 `-0.999_999_999` 。 例如，此 `decimal` 值會 `1.5m` 表示為 `{ units = 1, nanos = 500_000_000 }` 。 這就是為什麼 `nanos` 此範例中的欄位使用 `sfixed32` 型別，其編碼方式比 `int32` 較大的值更有效率。 如果 `units` 欄位是負數，則 `nanos` 欄位也應為負數。

> [!NOTE]
> 有多個其他演算法可將 `decimal` 值編碼為位元組字串，但這則訊息比任何這些演算法更容易瞭解。 在不同平臺上，這些值不會受到位元組由大到小或位元組由大到小的影響。

此類型與 BCL 類型之間的轉換 `decimal` 可能會在 c # 中執行，如下所示：

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

## <a name="collections"></a>集合

### <a name="lists"></a>清單

Protobuf 中的清單是藉由 `repeated` 在欄位上使用 prefix 關鍵字來指定。 下列範例顯示如何建立清單：

```protobuf
message Person {
    // ...
    repeated string roles = 8;
}
```

在產生的程式碼中， `repeated` 欄位會以 `Google.Protobuf.Collections.RepeatedField<T>` 泛型型別表示。

```csharp
public class Person
{
    // ...
    public RepeatedField<string> Roles { get; }
}
```

`RepeatedField<T>` 會實作 <xref:System.Collections.Generic.IList%601>。 因此，您可以使用 LINQ 查詢，或將它轉換成陣列或清單。 `RepeatedField<T>` 屬性沒有公用 setter。 專案應新增至現有的集合。

```csharp
var person = new Person();

// Add one item.
person.Roles.Add("user");

// Add all items from another collection.
var roles = new [] { "admin", "manager" };
person.Roles.Add(roles);
```

### <a name="dictionaries"></a>字典

.NET <xref:System.Collections.Generic.IDictionary%602> 類型在 Protobuf 中會使用來表示 `map<key_type, value_type>` 。

```protobuf
message Person {
    // ...
    map<string, string> attributes = 9;
}
```

在產生的 .NET 程式碼中， `map` 欄位會以 `Google.Protobuf.Collections.MapField<TKey, TValue>` 泛型型別表示。 `MapField<TKey, TValue>` 會實作 <xref:System.Collections.Generic.IDictionary%602>。 如同 `repeated` 屬性， `map` 屬性沒有公用 setter。 專案應新增至現有的集合。

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

## <a name="unstructured-and-conditional-messages"></a>非結構化和條件式訊息

Protobuf 是合約優先的訊息格式，而且必須在建立應用程式時，于 *proto* 檔案中指定應用程式訊息。 針對先進的案例，Protobuf 提供語言功能和知名的型別，以支援條件式和未知的訊息。

### <a name="any"></a>任意

`Any`型別可讓您使用訊息做為內嵌類型，而不需要其*proto*定義。 若要使用 `Any` 類型，請匯入 `any.proto` 。

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

### <a name="oneof"></a>Oneof

`oneof` 欄位是語言功能。 編譯器 `oneof` 會在產生訊息類別時處理關鍵字。 使用指定可能會傳回 `oneof` 或的回應訊息， `Person` 可能如下 `Error` 所示：

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

集合內的欄位在 `oneof` 整體訊息宣告中必須有唯一的欄位編號。

使用時 `oneof` ，產生的 c # 程式碼會包含列舉，以指定已設定的欄位。 您可以測試列舉，以找出已設定的欄位。 未設定的欄位會傳回 `null` 或預設值，而不是擲回例外狀況。

```csharp
var response = client.GetPersonAsync(new RequestMessage());

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

### <a name="value"></a>值

`Value`型別代表動態類型的值。 它可以是 `null` 、數位、字串、布林值、值的字典 (`Struct`) ，或 () 的值清單 `ValueList` 。 `Value` 是使用先前所討論之功能的知名型別 `oneof` 。 若要使用 `Value` 類型，請匯入 `struct.proto` 。

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

`Value`直接使用可以是詳細資訊。 另一種使用方式 `Value` 是使用 Protobuf 的內建支援，將訊息對應至 JSON。 Protobuf 的 `JsonFormatter` 和 `JsonWriter` 類型可以搭配任何 Protobuf 訊息使用。 `Value` 尤其適合在 JSON 之間進行轉換。

這是先前程式碼的 JSON 對等專案：

```csharp
// Create dynamic values from JSON.
var status = new Status();
status.Data = Value.Parser.ParseJson(@"{
    ""enabled"": true,
    ""metadata"": [ ""value1"", ""value2"" ]
}");

// Convert dynamic values to JSON.
// JSON can be read with a library like System.Text.Json or Newtonsoft.Json
var json = JsonFormatter.Default.Format(status.Metadata);
var document = JsonDocument.Parse(json);
```

## <a name="additional-resources"></a>其他資源

* [Protobuf 語言指南](https://developers.google.com/protocol-buffers/docs/proto3#simple)
* <xref:grpc/versioning>
