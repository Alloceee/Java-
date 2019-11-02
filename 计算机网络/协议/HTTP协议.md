# HTTP协议

HTTP协议是应用层协议，在TCP/IP协议接受到数据之后需要通过HTTP协议来解析才可以使用。

请求报文和响应报文：

请求报文

```http
请求行：方法（请求类型） URL HTTP版本
头部：键值对的属性

主体
```

响应报文

```http
请求行：HTTP版本 状态码 简短原因
头部：键值对的属性

主体
```



常见的状态码

- 200 OK 客户端请求成功
- 301 Moved Permanently （永久移除），请求的URL已移走。Response中应该包含一个Location URL,说明资源现在所处的位置
- 302 found 重定向
- 400 Bad Request 客户端请求有语法错误，不能被服务器解析
- 404 表示没有找到请求的资源
- 500 表示服务端内部错误

| **要方法** | **典型用法**                                                 | **典型状态码**                                               | **安全？** | **幂等？** |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------- | ---------- |
| **GET**    | - 获取表示- 变更时获取表示（缓存）                           | 200（OK） - 表示已在响应中发出204（无内容） - 资源有空表示301（Moved Permanently） - 资源的 URI 已被更新303（See Other） - 其他（如，负载均衡）304（not modified）- 资源未更改（缓存）400 （bad request）- 指代坏请求（如，参数错误）404 （not found）- 资源不存在406 （not acceptable）- 服务端不支持所需表示500 （internal server error）- 通用错误响应503 （Service Unavailable）- 服务端当前无法处理请求 | 是         | 是         |
| **DELETE** | - 删除资源                                                   | 200 （OK）- 资源已被删除301 （Moved Permanently）- 资源的 URI 已更改  303 （See Other）- 其他，如负载均衡400 （bad request）- 指代坏请求 t  404 （not found）- 资源不存在  409 （conflict）- 通用冲突500 （internal server error）- 通用错误响应  503 （Service Unavailable）- 服务端当前无法处理请求 | 否         | 是         |
| **PUT**    | - 用客户端管理的实例号创建一个资源- 通过替换的方式更新资源- 如果未被修改，则更新资源（乐观锁） | 200 （OK）- 如果已存在资源被更改  201 （created）- 如果新资源被创建301（Moved Permanently）- 资源的 URI 已更改303 （See Other）- 其他（如，负载均衡）400 （bad request）- 指代坏请求404 （not found）- 资源不存在406 （not acceptable）- 服务端不支持所需表示 /p>409 （conflict）- 通用冲突412 （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）415 （unsupported media type）- 接受到的表示不受支持500 （internal server error）- 通用错误响应503 （Service Unavailable）- 服务当前无法处理请求 | 否         | 是         |
| **POST**   | - 使用服务端管理的（自动产生）的实例号创建资源- 创建子资源- 部分更新资源- 如果没有被修改，则不过更新资源（乐观锁） | 200（OK）- 如果现有资源已被更改  201（created）- 如果新资源被创建  202（accepted）- 已接受处理请求但尚未完成（异步处理）301（Moved Permanently）- 资源的 URI 被更新  303（See Other）- 其他（如，负载均衡）400（bad request）- 指代坏请求  404 （not found）- 资源不存在  406 （not acceptable）- 服务端不支持所需表示  409 （conflict）- 通用冲突  412 （Precondition Failed）- 前置条件失败（如执行条件更新时的冲突）  415 （unsupported media type）- 接受到的表示不受支持500 （internal server error）- 通用错误响应  503 （Service Unavailable）- 服务当前无法处理请求 | 否         | 否         |