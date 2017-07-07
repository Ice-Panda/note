# Channels

channels 使得 Django 能够用来处理除了 http 之外的其他协议,*(传统的 http 协议request/response)*,例如: Websocket 和 HTTP2.

##  第一章

Channels 的五个依赖包

- Channels:Django 的注入层
- Daphne: HTTP 和 Websocket 服务器 
- asgiref: 基础 ASGI 库,保存在内存中
- asgi_redis: Redis 信道 
- asgi_rabbitmq:RabbitMQ 信道
- asgi_ipc: POSIX IPC 信道

## 第二章

### Channels 是什么

Channels 在 Django 中添加新的一层,支持两个重要特性:

- 处理 WebSocket, 以类似于 view 的方式
- 后台任务,在同一个服务器下,作为 Django 的一部分

### 实现方式

Channels 将 Django 分成两个类型的进行

- 处理 HTTP 和 WebSockets
- 执行 views,websocket处理器以及后台任务(consumers)

他们通过 ASGI 协议通信, ASGI 类似于 WSGI, 运行在网络之上,并且支持更多的协议

Channels 不会将asyncio, gevent或者其他异步代码引入到 django 代码中.所有的业务逻辑以同步方式运行在 worker 进行或者线程中.

### 是否需要更改 Django 的运行方式

所有的东西都是可选的,如果你愿意,可以把 Django 的 WSGI 方式改为以下方式:

- 一个 ASGI 服务器,例如 Daphne
- Django worker 服务器,使用`mangge.py runworker`
- 可以将 ASGI 请求进行路由的东西,例如 Redis

即使运行的是 Channels, 他也会默认把所有的 HTTP 请求路由到 Django View系统

### Channels 带来了什么

额外的特性包括:

- 使得支持上千的 HTTP 长连接变得简单
- 向Wedsocket 提供支持完整的 session 和 auth 支持
- 为 Websocket 提供用户自动登录支持,他通过基础的网站 cookie
- 内置了有事件触发的复杂触发事件(聊天,实时博客等等)
- 对于 URL 提供了低层次 HTTP 控制
- 对于其他协议或者事件资源提供可扩展性(例如 WebRTC,原始 UDP,SMS)

### 是否可扩展

可以运行任何数量`协议服务器`(例如, HTTP 和 WebScoket) 和 `worker服务器`(运行 Django的代码)

ASGI 允许在这两个组件之间多个不同的信道层.他被设计用来支持快速简便的切分,就像使用分布式集群运行他们自己的协议和 worker 服务器

### 为什么不适用消息队列

Channels 被设计用来实现低延迟(目标是几毫秒),并且在保证传输的下高吞吐量.这个特性有些消息队列不支持.

一些特性例如, `guaranteed ordering of messages`,are opt-in as they incur a performance hit, 但使得他更像消息队列.

### Channels 概念





