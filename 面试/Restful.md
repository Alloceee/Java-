**70.Restful有几种请求**

参考文章，[http://www.infoq.com/cn/articles/designing-restful-http-apps-roth，该篇写的比较全。](https://www.infoq.cn/article/designing-restful-http-apps-roth/)

# RESTful HTTP 的实践

设计RESTful HTTP应用程序

- Gregor Roth



- 马国耀

阅读数：517482009 年 11 月 3 日 00:57



**本文对 RESTful HTTP 的基础原理做了一个概览，探讨了开发者在设计 RESTful HTTP 应用时所面临的典型问题，展示了如何在实践中应用 REST 架构风格，描述了常用的 URI 命名方法，讨论了如何使用统一接口进行资源交互，何时使用 PUT 或 POST 以及如何支持非 CURD 操作等。**

REST 是一种风格，而不是标准。因为既没有 REST RFC，也没有 REST 协议规范或者类似的规定。REST 架构是 Roy Fielding（他也是 HTTP 和 URI 规范的主要作者之一）在一篇论文中描述的。像 REST 这样的架构风格通常会定义一组高层决定让应用程序去实现。所有实现了某种特定架构风格的应用程序，都使用相同的模式，也用相同的方式使用别的架构元素，如缓存，分布式策略等。Roy Fielding 把 REST 定义成一种架构风格，其目标是“使延迟和网络交互最小化，同时使组件实现的独立性和扩展性最大化”

虽然 REST 受 Web 技术的影响很深，但是理论上 REST 架构风格并非绑定在 HTTP 上。然而，HTTP 是唯一与 REST 相关的实例。基于该原因，本文描述了通过 HTTP 实现的 REST，通常它也被称为 RESTful HTTP。

REST 并没有创造新的技术，组件或服务，隐藏在 RESTful HTTP 背后的理念是使用 Web 的现有特征和能力。RESTful HTTP 定义了如何更好地使用现有 Web 标准中的一些准则和约束。

## 资源

资源是 REST 中最关键的抽象概念，它们是能够被远程访问的应用程序对象。一个资源就是一个标识单位，任何可以被访问或被远程操纵的东西都可能是一个资源。资源可以是静态的，也就是该资源的状态永远不会改变。相反，某些资源的状态可能随着时间推移呈现很大的可变性。这两种类型的资源都是有效的。

图 1 中所显示的这些类都能被很容易地映射成资源。面向对象设计者不容易理解把实体类（如`Hotel`或者`Room`）映射成资源，同样他们也不太理解从控制类（如 coordination，事务和某个类的控制类）到资源的映射。

[![img](https://static001.infoq.cn/resource/image/82/5a/829b473b12fba628c6ba6bc697ac715a.gif)](http://www.infoq.com/resource/articles/designing-restful-http-apps-roth/en/resources/image1.gif)

**图 1：分析模型的例子**

分析模型是识别资源的一个非常好的“切入点”。然而，并非只能进行 1 对 1 映射，比如， `<Hotel>.listOccupancy()`操作也可以被建模成资源。此外，也有可能某些资源只表示实体的某一部分。资源设计的主要驱动力是网络因素而不是对象模型。

任何重要的资源都应该能够通过一个唯一的标识被访问。RESTful HTTP 使用 URI 来识别资源。URI 提供了 Web 通用的识别机制，它包含了客户端直接与被引用的资源进行交互时需要的所有信息。

### 如何命名资源标识？

虽然 RESTful HTTP 并没有明确指出如何构造一个 URI 路径，但实际上经常被使用的是一些特定的命名模式。URI 命名模式有助于应用程序调试和跟踪，通常一个 URI 包含资源类型及其后面用于定位特定资源的标识。这样的 URI 不包括指定业务操作的动词（verb），而只用于定位资源。图中 a1 给出了`Hotel`资源的一个示例，同一个`Hotel`资源也可以通过 URI（a2）访问。同一资源可以被多个 URI 引用。

复制代码

```
(a1) http://localhost/hotel/656bcee2-28d2-404b-891b

(a2) http://127.0.0.1/hotel/656bcee2-28d2-404b-891b

(b) http://localhost/hotel/656bcee2-28d2-404b-891b/Room/4

(c) http://localhost/hotel/656bcee2-28d2-404b-891b/Reservation/15

(d) http://localhost/hotel/656bcee2-28d2-404b-891b/Room/4/Reservation/15

(e) http://localhost/hotel/656bcee2-28d2-404b-891b/Room/4/Reservation/15v7

(f) http://localhost/hotel/656bcee2-28d2-404b-891bv12
```

**图 2：资源寻址的例子**

URI 也可以被资源用来在资源表示（representation）之间建立关联。例如，Hotel 表示通过 URI 去引用已分配的 Room 资源，而不是使用普通的 RoomID。使用普通的 ID 会强制调用者通过对资源的访问去构造 URI，而调用者如果没有主机名和基础 URI 路径等上下文信息是无法访问到该资源的。

超链接常被客户端用于资源导航。RESTful API 是超文本驱动的，这表示客户端通过获得一个 Hotel 表示，就能够导航到已分配的 Room 表示和 Reservation 表示。

在实践中，图 1 所示的这些类经常被映射成某种业务对象，这意味着在业务对象的整个生命周期中 URI 将保持不变。如果要创建一个新资源，则要为之分配一个新的 URI。而一旦这个新资源被删除，相应的 URI 则跟着失效。如图 2 中的（a），（b），（c）和（d）就是这种标识的例子。另一方面，URI 也可以用来引用资源快照，比如（e）和（f）就是对这类快照的引用，其 URI 中包含了一个版本标识。

URI 还可以定位子资源，如示例中的（b），（c），（d）和（e）。通常，被聚集的对象会被映射成子资源，如 Room 是被 Hotel 聚集的。被聚集的对象通常没有自己的生命周期，如果它的父对象被删除，所有的被聚集对象也跟着被删除。

然而，如果一个子资源可以从一个父资源移动到另一个父对象， 那么在它的 URI 中就不应该包含其父资源的标识。比如图 1 中的 Reservation 资源，它就可以被分配给另一个 Room 资源。如果一个 Reservation 资源的 URI 包含了其父资源 Room 的标识，如（d）所示，则当 Room 实例标识改变时，如果该 Reservation 资源又被另一个资源引用的话，这就会出问题。为了避免无效的 URI，Reservation 应该通过（c）这样的方式进行寻址。

通常，资源的 URI 是由服务器控制的。客户端访问资源时并不需要理解资源的 URI 命名空间结构。比如，使用（c）和（d）两个 URI 结构对客户端而言具有效果相同。

## 统一资源接口

为了简化整体系统架构，REST 架构风格包含了统一接口的概念。统一接口包含一组受限的良定义的操作，由它们进行资源的访问和操作。不论什么资源，都使用相同的接口。客户端与 Hotel，Room 或 CreditScore 等资源交互时使用的接口是一样的。统一接口独立于资源的 URI，并且也不需要类似 IDL 的文件去描述可用的操作。

RESTful HTTP 的接口非常流行且广为使用。它包含标准的 HTTP 方法如 GET，PUT 和 POST（浏览器使用它发出请求并提取页面）。不幸的是，很多开发者认为实现 RESTful 应用就是用一种直接使用 HTTP 的方式，这种理解是错误的。举个例子，HTTP 方法的实现必须要遵循 HTTP 规范的，而通过 GET 方法创建或修改对象是不遵守 HTTP 规范的。

### 应用统一接口

关于何时以及如何使用不同的 HTTP 动词（verb），在 Fielding 的论文中没有任何表格、列表或其他方式的描述。对于大部分方法，如 GET 或 DELETE，通过阅读 HTTP 规范就能清楚其含义，而对于 POST 和部分更新，就不那么容易了。在实践中，对资源进行部分更新有好几种方法，下文将有详细介绍。

表 1 列出了大部分重要的方法 GET，DELETE，PUT 和 POST 的典型用法：

| **重要方法** | **典型用法**                                                 | **典型状态码**                                               | **安全？** | **幂等？** |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------- | ---------- |
| **GET**      | - 获取表示- 变更时获取表示（缓存）                           | 200（OK） - 表示已在响应中发出204（无内容） - 资源有空表示301（Moved Permanently） - 资源的 URI 已被更新303（See Other） - 其他（如，负载均衡）304（not modified）- 资源未更改（缓存）400 （bad request）- 指代坏请求（如，参数错误）404 （not found）- 资源不存在406 （not acceptable）- 服务端不支持所需表示500 （internal server error）- 通用错误响应503 （Service Unavailable）- 服务端当前无法处理请求 | 是         | 是         |
| **DELETE**   | - 删除资源                                                   | 200 （OK）- 资源已被删除301 （Moved Permanently）- 资源的 URI 已更改  303 （See Other）- 其他，如负载均衡400 （bad request）- 指代坏请求 t  404 （not found）- 资源不存在  409 （conflict）- 通用冲突500 （internal server error）- 通用错误响应  503 （Service Unavailable）- 服务端当前无法处理请求 | 否         | 是         |
| **PUT**      | - 用客户端管理的实例号创建一个资源- 通过替换的方式更新资源- 如果未被修改，则更新资源（乐观锁） | 200 （OK）- 如果已存在资源被更改  201 （created）- 如果新资源被创建301（Moved Permanently）- 资源的 URI 已更改303 （See Other）- 其他（如，负载均衡）400 （bad request）- 指代坏请求404 （not found）- 资源不存在406 （not acceptable）- 服务端不支持所需表示 /p>409 （conflict）- 通用冲突412 （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）415 （unsupported media type）- 接受到的表示不受支持500 （internal server error）- 通用错误响应503 （Service Unavailable）- 服务当前无法处理请求 | 否         | 是         |
| **POST**     | - 使用服务端管理的（自动产生）的实例号创建资源- 创建子资源- 部分更新资源- 如果没有被修改，则不过更新资源（乐观锁） | 200（OK）- 如果现有资源已被更改  201（created）- 如果新资源被创建  202（accepted）- 已接受处理请求但尚未完成（异步处理）301（Moved Permanently）- 资源的 URI 被更新  303（See Other）- 其他（如，负载均衡）400（bad request）- 指代坏请求  404 （not found）- 资源不存在  406 （not acceptable）- 服务端不支持所需表示  409 （conflict）- 通用冲突  412 （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）  415 （unsupported media type）- 接受到的表示不受支持500 （internal server error）- 通用错误响应  503 （Service Unavailable）- 服务当前无法处理请求 | 否         | 否         |

**表 1：统一接口示例**

## 表示

对资源的操纵永远是通过其表示实现的。资源可能永远不会在网络中传输，相反，传输的是资源的表示。资源的表示包括数据和描述数据的元数据，例如，HTTP 头“Content-Type” 就是这样一个元数据属性。

图 3 展示了如何使用 Java 获取表示。该例程使用了 Java HTTP 库 xLightweb 中的 HttpClient 类，这个库由作者本人维护。

```
HttpClient httpClient = new HttpClient();

IHttpRequest request = new GetRequest(centralHotelURI);
IHttpResponse response = httpClient.call(request); 
```

**图 3：获取表示的 Java 例程**

通过调用 HTTP 客户端的 call 方法，一个访问 Hotel 资源表示的 HTTP 请求就被发送出去。返回的表示如图 4 所示，它也包含了用于指示实体主体的多媒体类型的 Content-Type 头。

```
REQUEST:
GET /hotel/656bcee2-28d2-404b-891b HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6


RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 277
Content-Type: application/x-www-form-urlencoded


classification=Comfort&name=Central&RoomURI=http%3A%2F%2Flocalhost%2Fhotel%2F
656bcee2-28d2-404b-891b%2FRoom%2F2&RoomURI=http%3A%2F%2Flocalhost%2Fhotel%2F6
56bcee2-28d2-404b-891b%2FRoom%2F1
```

**图 4：RESTful HTTP 交互**

### 如何支持特定表示？

为了避免传输很大的数据集，有时应该接收表示属性一个子集。在实现时，用于指定部分属性的一种方式就是支持对指定属性的寻址，如图 5 所示。

```
REQUEST:
GET /hotel/656bcee2-28d2-404b-891b/classification HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Accept: application/x-www-form-urlencoded


RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 26
Content-Type: application/x-www-form-urlencoded; charset=utf-8


classification=Comfort
```

**图 5：属性过滤**

图 5 中所示的 GET 调用只请求了一个属性（classification），如果要请求多个属性，所请求的属性要用逗号隔开，如图 6 所示。

```
REQUEST:
GET /hotel/656bcee2-28d2-404b-891b/classification,name HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Accept: application/x-www-form-urlencoded


RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 43
Content-Type: application/x-www-form-urlencoded; charset=utf-8


classification=Comfort&name=Central
```

**图 6：多属性过滤**

确定所需属性的另一种方法是使用查询参数，通过它列出所请求的属性，如图 7 所示。查询参数将用于定义查询条件以及更复杂的过滤或查询准则。

```
REQUEST:
GET /hotel/656bcee2-28d2-404b-891b?reqAttr=classification&reqAttr=name HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Accept: application/x-www-form-urlencoded


RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 43
Content-Type: application/x-www-form-urlencoded; charset=utf-8


classification=Comfort&name=Central
```

**图 7：查询字符串**

在上述例子中，服务器总是返回以`application/x-www-form-urlencoded`编码的媒体类型的表示。该媒体类型将实体编码成键值对列表。键值方法理解起来很容易，但缺点是，它不适用与更加复杂的数据结构。此外，这种媒体类型不支持标量数据类型的绑定，如 Integer，Boolean，Date 等。基于这个原因，通常使用 XML，JSON 或 Atom 来表征资源（JSON 也没有定义 Data 类型的绑定）

```
HttpClient httpClient = new HttpClient();


IHttpRequest request = new GetRequest(centralHotelURI);
request.setHeader("Accept", "application/json");


IHttpResponse response = httpClient.call(request);


String jsonString = response.getBlockingBody().readString();
JSONObject jsonObject = (JSONObject) JSONSerializer.toJSON(jsonString);
HotelHotel= (Hotel) JSONObject.toBean(jsonObject, Hotel.class);
```

**图 8：请求 JSON 表示**

通过设置“Accept”请求头，客户端就可以请求指定的表示编码。图 8 展示了如何对 application/json 类型的表示的请求。JSONlib 将把图 9 中显示的返回响应消息映射成 Hotel bean。

```
REQUEST:
GET /hotel/656bcee2-28d2-404b-891b HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Accept: application/json


RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 263
Content-Type: application/json; charset=utf-8


{"classification":"Comfort",
"name":"Central",
"RoomURI":["http://localhost/hotel/656bcee2-28d2-404b-891b/Room/1",
       "http://localhost/hotel/656bcee2-28d2-404b-891b/Room/2"]}
```

**图 9：JSON 表示**

#### 如何报告错误？

当服务器不支持所请求的表示时怎么办？图 10 展示了一个请求 XML 表示资源的 HTTP 交互，若服务器不支持这种表示，它将返回一个 HTTP 406 响应，表示拒绝处理该请求。

```
REQUEST:
GET /hotel/656bcee2-28d2-404b-891b HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Accept: text/xml


RESPONSE:
HTTP/1.1 406 No match for accept header
Server: xLightweb/2.6
Content-Length: 1468
Content-Type: text/html; charset=iso-8859-1


<html>
   <head>
      <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1"/>
      <title>Error 406 No match for accept header</title>
   </head>
   <body>
       <h2>HTTP ERROR: 406</h2><pre>No match for accept header</pre>


         ...
   </body>
</html>
```

**图 10：不支持的表示**

RESTful HTTP 服务端程序必须根据 HTTP 规范返回状态码。状态码的第一个数字标识返回类型，1xx 表示临时响应，2xx 表示成功响应 ，3xx 代表转发，4xx 表示客户端错误，5xx 代表服务端错误。使用错误的响应码，或者总返回 200 响应，并在消息主体中包含特定应用程序的响应，这两种做法都是不好的实践。

客户代理和中介也要分析返回码。例如，xLightweb HttpClient 默认会把持久的 HTTP 连接保存在连接池中，当一个 HTTP 交互完成时，持久化 HTTP 连接就应返回到内部连接池已备重用。而只有完好的连接才能被放回连接池，比如，若返回码是 5xx，那该连接就不会重回连接池了。

有时某些特定的客户端要求更简洁的返回码。一种方法是增加一个 HTTP 头“X-Header”，用它来详细描述 HTTP 状态码。

```
REQUEST:
POST /Guest/ HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Content-Length: 94
Content-Type: application/x-www-form-urlencoded


zip=30314&lastName=Gump&street=42+Plantation+Street&firstName=Forest&country=US&
city=Baytown&state=LA



RESPONSE:
HTTP/1.1 400 Bad Request
Server: xLightweb/2.6
Content-Length: 55
Content-Type: text/plain; charset=utf-8
X-Enhanced-Status: BAD_ADDR_ZIP


AddressException: bad zip code 99566
```

**图 11：附加状态码**

通常只有在进行编程问题诊断时才需要详细的错误码。尽管比起详细的错误码，HTTP 状态码的描述性总是要差很多，但是在大多数情况下，它们对于客户端正确处理问题已经足够了。另一种方法是在响应主体中包含详细的错误码。

## PUT 还是 POST？

较之流行的 RPC 方式，HTTP 方法不仅仅在方法名上有所不同，而且 HTTP 方法中的某些属性（如幂等性，安全性等）也扮演着重要的角色。不同的 HTTP 方法的幂等性和安全性属性也是不同的。

```
HttpClient httpClient = new HttpClient();


String[] params = new String[] { "firstName=Forest",
			    "lastName=Gump",
			    "street=42 Plantation Street",
			    "zip=30314",
			    "city=Baytown",
			    "state=LA",
			    "country=US"};
IHttpRequest request = new PutRequest(gumpURI, params);
IHttpResponse response = httpClient.call(request);
```

**图 12：使用 PUT 方法**

如图 12 和 13 所示，使用 PUT 操作来创建一个新的 Guest 资源。PUT 方法将封装好的资源存放在 Request-URI 之下。该 URI 是由客户端决定的，当 Request-URI 指向某现存资源时，该资源将被新资源替换。基于该原因，PUT 方法一般用于创建新资源或更新现有资源。然而，通过使用 PUT，资源的整个状态都会被改变，若一个请求只需要修改 zip 域，它不得不包含该资源的其他域，如 firstName，city 等。

```
REQUEST:
PUT Hotel/guest/bc45-9aa3-3f22d HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Content-Length: 94
Content-Type: application/x-www-form-urlencoded


zip=30314&lastName=Gump&street=42+Plantation+Street&firstName=Forest&country=US&
city=Baytown&state=LA



RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 36
Content-Type: text/plain; charset=utf-8
Location: http://localhost/guest/bc45-9aa3-3f22d


The guest resource has been updated. 
```

**图 13：HTTP PUT 交互**

PUT 方法是幂等的，幂等性意味着对于一个成功执行的请求，不管其执行多少次，其结果都是一致的。也就是说，只要你愿意，你可以用 PUT 方法对 Hotel 资源进行任意次更新，其结果都一样。如果两个 PUT 方法同时发生，那么只有其中之一会赢得最后的胜利并决定资源的最终状态。删除操作也是幂等的，如果一个 PUT 方法和 DELETE 方法同时发生，那么资源或者被更新，或者被删除，而不可能停留在某个中间状态。

如果你不确定是 PUT 还是 DELETE 被成功执行，并且没有得到状态码 409 (Conflict) 或者 417 (Expectation Failed) 的话，那么就重新执行一遍。而不需要附加的可靠性协议来避免重复请求，因为通常重复的请求不会有任何影响。

上述描述对于 POST 方法就不适用了，因为 POST 方法不是幂等的，若要两次执行同一个 POST 请求那就要注意了。POST 方法所缺失的幂等性就解释了为什么当你每次重新发送 POST 请求时浏览器总是弹出警告。POST 方法用于创建资源，而不需要由客户端指定实例 id，图 14 展示了通过 POST 方法创建一个 Hotel 资源的 HTTP 交互过程。通常，客户端使用只包含基路径和资源类型名的 URI 来发送 POST 请求。

```
REQUEST:
POST /HotelHTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Content-Length: 35
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Accept: text/plain


classification=Comfort&name=Central


RESPONSE:
HTTP/1.1 201 Created
Server: xLightweb/2.6
Content-Length: 40
Content-Type: text/plain; charset=utf-8
Location: http://localhost/hotel/656bcee2-28d2-404b-891b


the Hotelresource has been created
```

**图 14：HTTP POST 交互（创建）**

POST 方法也经常用于更新资源的部分内容，比如，如果我们要通过发送仅包含 classification 属性的 PUT 请求去更新 Hotel 资源的话，这就是违反 HTTP 的，但是用 POST 方法则没有问题。POST 方法既不是幂等的，也不是安全的。图 15 展示了一个执行部分更新的 POST 方法。

```
REQUEST:
POST /hotel/0ae526f0-9c3d HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Content-Length: 19
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Accept: text/plain

classification=First+Class



RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 52
Content-Type: text/plain; charset=utf-8


the Hotelresource has been updated (classification)
```

**图 15： HTTP POST 交互 （更新）**

还可以使用 PATCH 方法来进行部分更新，PATCH 方法是对资源进行部分更新的一个特殊方法。一个 PATCH 请求包含一个补丁文档，它将应用于由 Request-URI 所指定的资源。然而 PATCH 的 RFC 规范还在草稿中。

## 使用 HTTP 缓存

为提高扩展性并降低服务端负载， RESTful 的 HTTP 应用程序可以利用 WEB 基础设施的缓存机制。HTTP 已经意识到缓存是 WEB 基础设施必不可少的一部分，比如，HTTP 协议定义了专门的消息头来支持缓存。如果服务端设置了这个头，客户端（如 HTTP 客户端或 Web 缓存代理）就能够有效地支持缓存策略。

```
HttpClient httpClient = new HttpClient();
httpClient.setCacheMaxSizeKB(500000);


IHttpRequest request = new GetRequest(centralHotelURI + "/classification");
request.setHeader("Accept", "text/plain");


IHttpResponse response = httpClient.call(request);
String classification = response.getBlockingBody.readString();


// ... sometime later re-execute the request
response = httpClient.call(request);
classification = response.getBlockingBody.readString();
```

**图 16：客户端缓存交互**

图 16 显示了一个重复的 GET 调用。通过设置最大缓存大小的值 >0 激活了 HttpClient 的缓存功能。如果响应消息中包含了刷新头，比如 Expires 或 Cache-Control: max-age，该响应就会被 HttpClient 缓存。这些头指明了关联的表示可以保鲜的时间为多久。如果在一段时间内发出了相同的请求，那么 HttpClient 就会使用缓存为这些请求提供服务，而不需要重复进行网络调用。在网络上总共只有一次 HTTP 交互，如图 17 所示。诸如 WEB 代理之类的缓存中介也实现了相同的功能，而且该缓存还可以在不同客户端之间共享。

```
REQUEST:
GET /hotel/656bcee2-28d2-404b-891b/classification HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Accept: text/plain


RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Cache-Control: public, max-age=60
Content-Length: 26
Content-Type: text/plain; charset=utf-8


comfort
```

**图 17：包含过期头的 HTTP 响应**

过期模型在静态资源上很好用，可是，对于动态资源（资源状态经常改变且无法预测）则不尽相同。HTTP 通过验证头，如 Last-Modified 以及 ETag 来支持动态资源的缓存。与过期模型相比，验证模型没有节省网络调用。但是，当执行带条件的 GET 方法时它会对昂贵的操作节约网络传输，图 18（2.request）显示了带条件的 GET 操作，它带有一个额外的 Last-Modified 头，这个头包含了缓存对象最后修改日期。如果该资源未被更改，服务端将会返回一个 304 (Not Modified) 响应。

```
1. REQUEST:
GET /hotel/656bcee2-28d2-404b-891b/Reservation/1 HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Accept: application/x-www-form-urlencoded


1. RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 252
Content-Type: application/x-www-form-urlencoded
Last-Modified: Mon, 01 Jun 2009 08:56:18 GMT


from=2009-06-01T09%3A49%3A09.718&to=2009-06-05T09%3A49%3A09.718&guestURI=
http%3A%2F%2Flocalhost%2Fguest%2Fbc45-9aa3-3f22d&RoomURI=http%3A%2F%2F
localhost%2Fhotel%2F656bcee2-28d2-404b-891b%2FRoom%2F1


2. REQUEST:
GET /hotel/0ae526f0-9c3d/Reservation/1 HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Accept: application/x-www-form-urlencoded
If-Modified-Since: Mon, 01 Jun 2009 08:56:18 GMT


2. RESPONSE:
HTTP/1.1 304 Not Modified
Server: xLightweb/2.6
Last-Modified: Mon, 01 Jun 2009 08:56:18 GMT
```

**图 18：基于验证的缓存**

## 不要在服务端存储应用状态

RESTful HTTP 的交互必须是无状态的，这表明每一次请求要包含处理该请求所需的一切信息。客户端负责维护应用状态。RESTful 服务端不需要在请求间保留应用状态，服务端负责维护资源状态而不是应用状态。服务端和中介能够理解独立的请求和响应。Web 缓存代理拥有一切正确处理请求所需的信息并管理它的缓存。

这种无状态的方法是实现高扩展和高可用应用的基本原则。通常无状态使得每一个客户请求可以由不同的服务器来响应，当流量增加时，新的服务器可以加进来，而如果某个服务器失败，它也可以从集群中移除。若要了解关于负载均衡以及故障恢复方面的更详细信息，请参考这篇文章[服务器负载均衡架构 ](http://www.javaworld.com/javaworld/jw-10-2008/jw-10-load-balancing-1.html)。

## 对 non-CRED 操作的支持

开发者经常想了解如何将 non-CRUD（Create-Read-Update-Delete）操作映射到资源。显然，Create、Read、Update 和 Delete 等操作能够很容易地映射到资源的方法。然而， RESTful HTTP 还不仅限于面向 CRUD 的应用。

[![img](https://static001.infoq.cn/resource/image/2c/c4/2c6af0e2a459f8e77013a8ce9a8b6dc4.gif)](http://www.infoq.com/resource/articles/designing-restful-http-apps-roth/en/resources/image2.gif)

**图 19: RESTful HTTP 资源**

就如图 19 所示的 creditScoreCheck 而言，它提供了一个 non-CRUD 操作 creditScore(...)，该操作接受一个 address，计算出 score 并返回。这样的操作可以通过 CreditScoreResource 实现，该资源代表着计算的返回。图 20 展示了一个 GET 方法，它传入 address，然后提取 CreditScoreResource 表示，查询参数被用来指定 CreditScoreResource。GET 方法是安全的，并且可缓存，所提它很适用于 CreditScore Check 的 creditScore(...) 方法的非功能性行为。计算的结果可以缓存一段时间，如图 20 所示，响应包含了一个缓存头，它通知客户端和中介执行响应缓存。

```
REQUEST:
GET /CreditScore/?zip=30314&lastName=Gump&street=42+Plantation+Street&
	      firstName=Forest&country=US&city=Baytown&state=LA HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Accept: application/x-www-form-urlencoded


RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 31
Content-Type: application/x-www-form-urlencoded
Cache-Control: public, no-transform, max-age=300


scorecard=Excellent&points=92
```

**图 20：Non-CRUD HTTP GET 交互**

上述例子还显示了 GET 方法的局限性。尽管 HTTP 规范并没有指定 URL 的最大长度，但是实际上客户端，中介以及服务端对 URL 的长度都有限制。基于此，通过 GET 的查询参数发送一个很大的实体可能会因为中介和服务器对 URL 长度的限制而失败。

另一解决方法是使用 POST 方法，如果作了设置，它也是可缓存的。如图 21 所示，第一个 POST 请求的结果是创建了一个虚拟资源 CreditScoreResource。输入的 address 数据用 text/card 这个 mime 类型进行编码，在服务端计算得到 score 之后，它发回一个 201（created）响应，该响应包含着所创建的 CreditScoreResource 资源的 URI。 示例中还展示了如果进行了设定，POST 响应也可以被缓存。通过一个 GET 请求就能够取到计算结果。GET 响应也包含一个缓存控制头，如果客户端紧接着重新执行这两次请求，那么它们都可由缓存进行响应。

```
1. REQUEST:
POST /CreditScore/ HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6
Content-Length: 198
Content-Type: text/x-vcard
Accept: application/x-www-form-urlencoded


BEGIN:VCARD
VERSION:2.1
N:Gump;Forest;;;;
FN:Forest Gump
ADR;HOME:;;42 Plantation St.;Baytown;LA;30314;US
LABEL;HOME;ENCODING=QUOTED-PRINTABLE:42 Plantation St.=0D=0A30314 Baytown=0D=0ALA US
END:VCARD


1. RESPONSE:
HTTP/1.1 201 Created
Server: xLightweb/2.6
Cache-Control: public, no-transform, max-age=300
Content-Length: 40
Content-Type: text/plain; charset=utf-8
Location: http://localhost/CreditScore/l00000001-l0000005c


the credit score resource has been created



2. REQUEST:
GET /CreditScore/l00000001-l0000005c HTTP/1.1
Host: localhost
User-Agent: xLightweb/2.6


2. RESPONSE:
HTTP/1.1 200 OK
Server: xLightweb/2.6
Content-Length: 31
Content-Type: application/x-www-form-urlencoded
Cache-Control: public, no-transform, max-age=300


scorecard=Excellent&points=92
```

**图 21： Non-CRUD HTTP POST 交互**

还有其他不同的实现方式。比如不返回 201 响应，而返回 301(Moved Permanently) 转发响应。该响应缺省是可缓存的。其他避免二次请求的方法是在 201 响应中增加一个新创建的 CreditScoreResource 资源的表示。

## 总结

大多数 SOA 架构（如 SOAP 或 CORBA）都试图映射如图 1 所示的类模型，或多或少是一对一的远程访问。通常，这些 SOA 架构的比较多地关注在编程语言对象的透明映射上，这种映射很容易理解，且易于跟踪。可是，它们把对分布性和扩展性等方面的关注排在第二位。

相反，REST 架构风格的最主要驱动是分布性和扩展性。RESTful HTTP 接口的设计是由网络因素而非编程语言的绑定驱动的。 RESTful HTTP 也没有试图去封装很那些难隐藏的因素，如网络延迟，网络健壮性以及网络带宽等。

RESTful HTTP 应用用一种直接的方式使用 HTTP 协议，而不需任何抽象层，也不存在 REST 指定的数据域，如错误域，安全令牌域等。RESTful HTTP 应用只使用 WEB 的固有能力。设计 RESTful HTTP 的接口意味着远程结构的设计者必须在 HTTP 协议上进行思考。这通常增加了开发周期中额外步骤。

然而，RESTful HTTP 使得应用程序实现具有高扩展性，更健壮。特别是为很大用户群提供 Web 应用的公司，如 WebMailing 或 SocialNetworking 的应用就能从 REST 架构风格中获益。通常，这些应用要更快更高地扩展，而且，这些公司通常在一些低预算的基础设施（基于广泛使用的标准组件和软件之上）上运行应用。

## 关于作者

Gregor Roth，xLightweb HTTP 库的作者。在 United Internet 组织担任软件架构师，该组织是最重要的欧洲因特网服务提供商，其产品有 GMX, 1&1, and Web.de 等。他感兴趣的领域包括软件和系统架构、企业架构管理、面向对象设计、分布式计算和开发方法论等。

## 参考文献

[Architectural Styles and the Design of Network-based Software Architectures](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)

[REST Eye for the SOA Guy ](http://steve.vinoski.net/pdf/IEEE-REST_Eye_for_the_SOA_Guy.pdf) 

[Presentation: Steve Vinoski on REST, Reuse and Serendipity](http://www.infoq.com/presentations/vinoski-rest-serendipity)

[A Brief Introduction to REST](http://www.infoq.com/articles/rest-introduction)

[Fallacies of Distributed Computing](http://en.wikipedia.org/wiki/Fallacies_of_Distributed_Computing)

[Server load balancing architectures](http://www.javaworld.com/javaworld/jw-10-2008/jw-10-load-balancing-1.html)

[Asynchronous HTTP and Comet architectures](http://www.javaworld.com/javaworld/jw-03-2008/jw-03-asynchhttp.html)

[JSON-lib](http://json-lib.sourceforge.net/)

[xLightweb](http://xlightweb.org/)

**查看英文原文**：[ RESTful HTTP in practic ](http://www.infoq.com/articles/designing-restful-http-apps-roth)。

**71.Restful好处**

(1)客户-服务器：客户-服务器约束背后的原则是分离关注点。通过分离用户接口和数据存储这两个关注点，改善了用户接口跨多个平台的可移植性；同时通过简化服务器组件，改善了系统的可伸缩性。
(2)无状态：通信在本质上是无状态的，改善了可见性、可靠性、可伸缩性.
(3)缓存：改善了网络效率减少一系列交互的平均延迟时间，来提高效率、可伸缩性和用户可觉察的性能。
(4)统一接口：REST架构风格区别于其他基于网络的架构风格的核心特征是，它强调组件之间要有一个统一的接口。

