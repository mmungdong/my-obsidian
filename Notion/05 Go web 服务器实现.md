---
type: Post
status: Published
date: 2025-06-28
tags:
  - golang
  - miniblog
  - project
category: 编程学习
---
> [!important] 前言
> 
> 学习孔云飞大佬的 miniblog 项目笔记，项目地址：[https://github.com/onexstack/miniblog](https://github.com/onexstack/miniblog)
> 
> 具体知识详参引用文章，文章不做过多赘述，只概后续开发用到的知识，方便开发快速参考

  

- [[#1. 什么是 web 应用？]]
    - [[#1.1. web 应用，web 服务和 web 服务器的概念]]
    - [[#1.2. 如何实现一个 web 服务器]]
    - [[#1.3. 如何选择合适的通信协议和数据交换格式]]
    - [[#1.3. 如何选择一个优秀的 Web 框架]]
    - [[#1.4. miniblog 项目中实现的 Web 服务类型]]
- [[#2. gRPC 服务实现]]
    - [[#2.1. rpc 介绍]]
    - [[#2.2. gRPC 介绍]]
    - [[#2.3. Protocol Buffers 介绍]]
    - [[#2.4. miniblog 实现 gRPC 服务器]]
        - [[#2.4.1. 定义 gRPC 服务]]
        - [[#2.4.2. 生成客户端和服务器代码]]
        - [[#2.4.3. 实现 gRPC 服务端]]
- [[#🤗 总结归纳]]
- [[#📎 参考文章]]

# 1. 什么是 web 应用？

## 1.1. web 应用，web 服务和 web 服务器的概念

- **Web 服务：**Web 服务是一种通过网络提供的功能服务，其通信基于标准的 HTTP、RPC 或其他协议。Web 服务遵循客户端-服务器模型，客户端使用 Web 服务支持的协议向服务端发送请求，服务端处理请求后返回相应的数据包。在通信过程中，为了确保双方能够理解对方的信息，需要使用统一约定的通信协议和数据交换格式。
- **Web 应用：**Web 应用是基于 Web 技术开发的具体应用程序，它是 Web 服务的实现者，运行在 Web 服务器上。Web 应用可以利用 Web 服务实现特定的功能，例如通过远程 API 获取数据。
- **Web 服务器：**Web 服务器指提供 Web 服务的软件或硬件设备，是 Web 应用运行和管理所需的基础架构和环境。Web 服务器负责配置、监控和维护 Web 应用，同时在客户端与 Web 应用之间充当通信桥梁。客户端向 Web 服务器发送请求，服务器将请求转发到具体的 Web 应用进行处理，并将结果返回给客户端。

  

总结来看，Web 应用是基于 Web 技术开发的具体应用程序，其中包含了多个 Web 服务。Web 应用可以部署在 Web 服务器上，而 Web 服务器则为 Web 应用提供运行与管理所需的基础设施。

  

[![](https://raw.githubusercontent.com/mmungdong/image/main/notion/20250628022119291.png)](https://raw.githubusercontent.com/mmungdong/image/main/notion/20250628022119291.png)

  

## 1.2. 如何实现一个 web 服务器

  

为了高效开发一个 Web 服务，通常需要进行以下技术选型：

1. **通信协议：**根据具体的业务场景和需求选择合适的通信协议；
2. **数据交换格式：**根据具体的业务场景和需求选择适用的数据交换格式。在选择数据交换格式时，还需考虑通信协议的支持情况，因为不同的通信协议支持的数据交换格式可能不同；
3. **Web 框架：**由于 Web 服务通常包含多个 API 接口，选择合适的 Web 框架可以提高开发效率和代码复用率。我们可以选择从零自行设计开发一个框架，也可以直接使用业界成熟的开源 Web 框架。在选择 Web 框架时，需要考虑通信协议和数据交换格式，因为每个 Web 框架支持的通信协议和数据交换格式可能有所不同。一款优秀的 Web 框架通常能够满足所需的通信协议和数据交换格式。

  

## 1.3. **如何选择合适的通信协议和数据交换格式**

  

开发 Web 服务的第一步是根据业务场景和需求选择适用的通信协议与数据交换格式，二者的定义如下：

1. **通信协议：**通信协议是规定计算机或设备之间通信规则的协议，定义了数据传输的格式、传输方式、错误检测及纠正机制等。常见的通信协议包括 HTTP、RPC、WebSocket、TCP/IP、FTP 等。不同的通信协议支持的数据交换格式也会有所不同；
2. **数据交换格式：**数据交换格式（也称数据序列化格式）是为不同系统之间传输和解析数据制定的规范，定义了数据的结构、编码方式及解析方法。常见的数据交换格式包括 JSON、Protobuf、XML 等。

  

首先，我们需要根据业务场景和需求，选择合适的通信协议。在 Go 项目开发中，常用的通信协议包括 HTTP、RPC 和 WebSocket，其中使用最频繁的是 HTTP 和 RPC。在实际开发中，通常选择 REST API 接口规范来开发 API 接口，这些 API 接口的底层通信基于 HTTP 协议。而实现 RPC 通信时，则通常使用 gRPC 框架。gRPC 是由谷歌开源的一个 RPC 框架。

> [!important] 提示
> 
> RPC 也可以理解为一种通信协议，但它是基于其他协议（例如 TCP、UDP、HTTP）封装而成的通信协议。

  

接下来，根据所选的通信协议，选择最佳适配的数据交换格式。HTTP 和 RPC 各自有其推荐使用的数据交换格式，这可以视为事实上的标准。在无特殊需求的情况下，一般不需要改变这种适配关系：HTTP 协议通常采用 JSON 数据格式，而 RPC 通常采用 Protobuf 数据格式。

  

> [!important] HTTP 和 RPC 在不同的场景下各有适配。在企业应用开发中，通常会结合两种通信协议，共同构建一个高效的 Go 应用：
> 
> 1. **对外：**REST（基于 HTTP 协议）+JSON 的组合。由于 REST API 接口规范清晰直观，JSON 数据格式易于理解和使用，并且客户端和服务端通过 HTTP 协议通信时无需使用相同的编程语言，因此 REST+JSON 更适合用于对外提供 API 接口；
> 2. **对内：**gRPC（基于 RPC 协议）+Protobuf 的组合。由于 RPC 协议调用便捷、Protobuf 格式的数据传输效率更高，因此 gRPC+Protobuf 更适合用于对内提供高性能的 API 接口。

  

为了更好地开发 Web 服务，通常不会直接使用裸 HTTP 或 RPC 协议，而是基于这些协议封装一层框架来使用。因此，文中提到的“通信协议”实际上指的是协议在实际应用中的使用形态。

  

REST+JSON 和 RPC+Protobuf 这两种组合在企业级应用中应用广泛。二者并非相互取代，而是各自适用于不同的场景，相辅相成。在企业应用中，REST 与 RPC 的组合方式通常如图

[![](https://raw.githubusercontent.com/mmungdong/image/main/notion/20250628022826784.png)](https://raw.githubusercontent.com/mmungdong/image/main/notion/20250628022826784.png)

  

外部请求通过 REST+JSON 访问 Web 服务，Web 服务通过 RPC+Protobuf 访问应用内的其他服务。应用内服务间调用通过 RPC+Protobuf 来调用。

  

此外，很多 Go 应用采用了一种更灵活、更强大的构建方式：在一个 Web 服务器中同时实现 REST 接口和 RPC 接口。外部客户端调用 REST 接口，内部服务调用 RPC 接口，而 REST API 通过代理，将请求转发到内部的 RPC 接口。通过这种方式，只需实现一套 RPC 接口，就可以通过代理对外提供 REST 接口。例如，可以使用 grpc-gateway 将 HTTP 请求转换为 gRPC 请求。

  

## **1.3. 如何选择一个优秀的 Web 框架**

  

当前自己的 golang 技术栈选项：

- http: gin, echo
- rpc: grpc-go

  

## 1.4. **miniblog 项目中实现的 Web 服务类型**

  

miniblog 是一个小而美的项目，虽然项目不大，却同时实现了 HTTP 和 gRPC 两种 Web 服务类型，miniblog 具体的服务类型如图：

[![](https://raw.githubusercontent.com/mmungdong/image/main/notion/20250628023146515.png)](https://raw.githubusercontent.com/mmungdong/image/main/notion/20250628023146515.png)

  

miniblog 项目使用 Gin 框架实现了 HTTP 服务，使用 gRPC 框架实现了 gRPC 服务，使用 grpc-gateway 实现了 HTTP 反向代理服务，用来将 HTTP 服务转换为 gRPC 服务。

  

同时，miniblog 项目支持通过配置文件中的 tls.use-tls 配置项开启 TLS 认证。mb-apiserver 服务启动时，可通过配置文件中的 server-mode 配置项来配置启动的 Web 服务类型：

1. server-mode=gin：启动使用 Gin Web 框架开发的 HTTP 服务；
2. server-mode=grpc：启动使用 grpc+grpc-gateway 框架开发的 gRPC 服务，同时支持 HTTP 请求。在 mb-apiserver 接收到 HTTP 请求后，HTTP 反向代理服务，会将 HTTP 请求转换为 gRPC 请求，并转发给 gRPC 服务接口。

  

为什么 miniblog 项目会同时实现 HTTP 反向代理服务、gRPC 服务和 HTTP 服务：

1. H**TTP 反向代理服务+gRPC 服务：**在 Go 项目开发中，外部系统一般通过 HTTP 接口访问服务，而内部系统则基于性能和调用便捷性的考虑，更倾向于使用 RPC 接口通信。一般情况下，服务只需要对外提供一种类型的通信协议。例如，仅提供 gRPC 接口，外部系统如果需要访问可以访问 API 网关，请求在 API 网关层被转换为 gRPC 请求。但这种方式依赖于 API 网关基础设施，在企业应用开发中，有些服务因为种种原因（例如，企业没有 API 网关），并不会接入 API 网关，所以这在这种情况下，服务内置一个 HTTP 反向代理服务器，用于支持 HTTP 请求，并将请求自动转换为 gRPC 请求，以解决此类诉求。miniblog 服务的 HTTP 反向代理服务器+gRPC 服务的组合模式，既能满足外部系统的访问需求，又能满足内部服务之间的访问需求；
2. **HTTP 服务：**绝大多数企业应用通过 HTTP 接口对外提供服务，这类 HTTP 服务通常使用 Gin 框架开发。本课程中的 HTTP 服务实现仅用于展示如何使用 Gin 框架开发 HTTP 接口。

  

# 2. gRPC 服务实现

  

## 2.1. rpc 介绍

  

> RPC（Remote Procedure Call，远程过程调用）是一种计算机通信协议。该协议允许运行在一台计算机上的程序调用另一台计算机上的子程序，而开发者无需为这种交互编写额外的代码。

  

[![](https://raw.githubusercontent.com/mmungdong/image/main/notion/20250628023814532.png)](https://raw.githubusercontent.com/mmungdong/image/main/notion/20250628023814532.png)

  

RPC 调用的具体流程如下：

1. 客户端通过本地调用的方式调用客户端存根（Client Stub）；
2. 客户端存根将参数打包（也称为 Marshalling）成一个消息，并发送该消息；
3. 客户端所在的操作系统（OS）将消息发送到服务端；
4. 服务端接收到消息后，将消息传递给服务端存根（Server Stub）；
5. 服务端存根将消息解包（也称为 Unmarshalling），得到参数；
6. 服务端存根调用服务端的子程序（函数），完成处理后，将结果按照相反的步骤返回给客户端。

  

需要注意的是，Stub 负责处理参数和返回值的序列化（Serialization）、参数的打包与解包，以及网络层的通信。在 RPC 中，客户端的 Stub 通常被称为“Stub”，而服务端的 Stub 通常被称为“Skeleton”。

  

## 2.2. gRPC 介绍

  

gRPC 是由谷歌开发的一种高性能、开源且支持多种编程语言的通用 RPC 框架，基于 HTTP/2 协议开发，并默认采用 Protocol Buffers 作为数据序列化协议。gRPC 具有以下特性：

1. **语言中立：**支持多种编程语言，例如 Go、Java、C、C++、C\#、Node.js、PHP、Python、Ruby 等；
2. **基于 IDL 定义服务：**通过 IDL（Interface Definition Language）文件定义服务，并使用 proto3 工具生成指定语言的数据结构、服务端接口以及客户端存根。这种方法能够解耦服务端和客户端，实现客户端与服务端的并行开发；
3. **基于 HTTP/2 协议：**通信协议基于标准的 HTTP/2 设计，支持双向流、消息头压缩、单 TCP 的多路复用以及服务端推送等能力；
4. **支持 Protocol Buffer 序列化：**Protocol Buffer（简称 Protobuf）是一种与语言无关的高性能序列化框架，可以减少网络传输流量，提高通信效率。此外，Protobuf 语法简单且表达能力强，非常适合用于接口定义。

  

与许多其他 RPC 框架类似，gRPC 也通过 IDL 语言来定义接口（包括接口名称、传入参数和返回参数等）。在服务端，gRPC 服务实现了预定义的接口。在客户端，gRPC 存根提供了与服务端相同的方法。

  

## 2.3. **Protocol Buffers 介绍**

  

Protocol Buffers（简称 Protobuf）是由谷歌开发的一种用于对数据结构进行序列化的方法，可用于数据通信协议、数据存储格式等，也是一种灵活且高效的数据格式，与 XML 和 JSON 类似。由于 Protobuf 具有出色的传输性能，因此常被用于对数据传输性能要求较高的系统中。Protobuf 的主要特性如下：

1. **更快的数据传输速度：**Protobuf 在传输过程中会将数据序列化为二进制格式，相较于 XML 和 JSON 的文本传输格式，这种序列化方式能够显著减少 I/O 操作，从而提升数据传输的速度；
2. **跨平台多语言支持：**Protobuf 自带的编译工具 protoc 可以基于 Protobuf 定义文件生成多种语言的客户端或服务端代码，供程序直接调用，因此适用于多语言需求的场景；
3. **良好的扩展性和兼容性：**Protobuf 能够在不破坏或影响现有程序的基础上，更新已有的数据结构，提高系统的灵活性；
4. **基于 IDL 文件定义服务：**通过 proto3 工具可以生成特定语言的数据结构、服务端和客户端接口。

  

在 gRPC 框架中，Protocol Buffers 主要有以下四个作用：

  

**第一，可以用来定义数据结构。**举个例子，下面的代码定义了一个 LoginRequest 数据结构：

```Protobuf
// LoginRequest 表示登录请求
message LoginRequest {
    // username 表示用户名称
    string username = 1;
    // password 表示用户密码
    string password = 2;
}
```

  

**第二，可以用来定义服务接口。**下面的代码定义了一个 MiniBlog 服务：

```Protobuf
service MiniBlog {
   rpc Login(LoginRequest) returns (LoginResponse) {}
} 
```

  

**第三，可以通过 protobuf 序列化和反序列化，提升传输效率:**

使用 XML 或 JSON 编译数据时，虽然数据文本格式可读性更高，但在进行数据交换时，设备需要耗费大量的 CPU 资源进行 I/O 操作，从而影响整体传输速率。而 Protocol Buffers 不同于前者，它会将字符串序列化为二进制数据后再进行传输。这种二进制格式的字节数比 JSON 或 XML 少得多，因此传输速率更高。

  

**第四，Protobuf 是标准化的:**

我们可以基于标准的 Protobuf 文件生成多种编程语言的客户端、服务端代码。在 Go 项目开发中，可以基于这种标准化的语言开发多种 protoc 编译插件，从而大大提高开发效率。

  

## 2.4. **miniblog 实现 gRPC 服务器**

  

> 具体代码参见：[miniblog/pkg/api/apiserver/v1 at master · mmungdong/miniblog](https://github.com/mmungdong/miniblog/tree/master/pkg/api/apiserver/v1)

  

为了展示如何实现一个 gRPC 服务器，并展示如何通信，miniblog 模拟了一个场景：miniblog 配套一个运营系统，运营系统需要通过接口获取所有的用户，进行注册用户统计。为了提高内部接口通信的性能，运营系统通过 gRPC 接口访问 miniblog 的 API 接口。为此，miniblog 需要实现一个 gRPC 服务器。那么如何实现一个 gRPC 服务器呢？其实很简单，可以通过以下几步来实现：

1. 定义 gRPC 服务；
2. 生成客户端和服务器代码；
3. 实现 gRPC 服务端；
4. 实现 gRPC 客户端；
5. 测试 gRPC 服务。

  

grpc-go 官方仓库中提供了许多代码实现供参考，例如 [examples](https://github.com/grpc/grpc-go/tree/master/examples) 目录。gRPC 官方文档也包含了大量 gRPC 框架的使用教程。建议在学习后续内容之前，先根据官方的 [Quick start 文档](https://grpc.io/docs/languages/go/quickstart/)完成一次 gRPC 服务的创建和使用流程，这将有助于你更好地理解后续内容。

  

### 2.4.1. **定义 gRPC 服务**

  

我们需要编写.proto 格式的 Protobuf 文件来描述一个 gRPC 服务。服务内容包括以下部分：

1. **服务定义：**描述服务包含的 API 接口；
2. **请求和返回参数的定义：**服务定义了一系列 API 接口，每个 API 接口都需要指定请求参数和返回参数。

  

新建 [pkg/api/apiserver/v1/apiserver.proto](https://github.com/onexstack/miniblog/blob/feature/s09/pkg/api/apiserver/v1/apiserver.proto) 文件，其内容如下：

```Protobuf
syntax = "proto3"; // 告诉编译器此文件使用什么版本的语法

package v1;

import "google/protobuf/empty.proto";       // 导入空消息
import "apiserver/v1/healthz.proto";        // 健康检查消息定义

option go_package = "github.com/onexstack/miniblog/pkg/api/apiserver/v1;v1";

// MiniBlog 定义了一个 MiniBlog RPC 服务
service MiniBlog {
    // Healthz 健康检查
    rpc Healthz(google.protobuf.Empty) returns (HealthzResponse) {}
}
```

  

syntax 关键字可以指定当前使用的版本号，这里采用的是 proto3 版本

package 关键字用于指定生成的 .pb.go 文件所属的包名。

import 关键字用来导入其他 Protobuf 文件。

option 关键字用于对 .proto 文件进行配置，其中 go_package 是必需的配置项，其值必须设置为包的导入路径。

service 关键字用来定义一个 MiniBlog 服务，服务中包含了所有的 RPC 接口。

  

在 MiniBlog 服务中，使用 rpc 关键字定义服务的 API 接口。接口中包含了请求参数 google.protobuf.Empty 和返回参数 HealthzResponse。在上述 Protobuf 文件中，google.protobuf.Empty 是谷歌提供的一个特殊的 Protobuf 消息类型，其作用是表示一个“空消息”。它来自于谷歌的 Protocol Buffers 标准库，定义在 [google/protobuf/empty.proto](https://github.com/golang/protobuf/tree/master/ptypes/empty) 文件中。

  

gRPC 支持定义四种类型的服务方法。上述示例中定义的是简单模式的服务方法，也是 miniblog 使用的 gRPC 模式。以下是四种服务方法的具体介绍：

1. **简单模式（Simple RPC）：**这是最基本的 gRPC 调用形式。客户端发起一个请求，服务端返回一个响应。定义格式为 rpc SayHello (HelloRequest) returns (HelloReply) {}；
2. **服务端流模式（Server-side streaming RPC）：**客户端发送一个请求，服务端返回数据流，客户端从流中依次读取数据直到流结束。定义格式为 rpc SayHello (HelloRequest) returns (stream HelloReply) {}；
3. **客户端流模式（Client-side streaming RPC）：**客户端以数据流的形式连续发送多条消息至服务端，服务端在处理完所有数据之后返回一次响应。定义格式为 rpc SayHello (stream HelloRequest) returns (HelloReply) {}；
4. **双向数据流模式（Bidirectional streaming RPC）：**客户端和服务端可以同时以数据流的方式向对方发送消息，实现实时交互。定义格式为 rpc SayHello (stream HelloRequest) returns (stream HelloReply) {}。

  

在 apiserver.proto 文件中，定义了 [Healthz](https://github.com/onexstack/miniblog/blob/feature/s09/pkg/api/apiserver/v1/apiserver.proto#L20) 接口，还需要为这些接口定义请求参数和返回参数。考虑到代码未来的可维护性，这里建议将不同资源类型的请求参数定义保存在不同的文件中。在 Go 项目开发中，将不同资源类型相关的结构体定义和方法实现分别保存在不同的文件中，是一个好的开发习惯，代码按资源分别保存在不同的文件中，可以提高代码的维护效率。

  

同样，为了提高代码的可维护性，建议接口的请求参数和返回参数都定义成固定的格式：

1. **请求参数格式：**<接口名>Request，例如 LoginRequest；
2. **返回参数格式：**<接口名>Response，例如 LoginResponse。

  

根据上面的可维护性要求，新建 [pkg/api/apiserver/v1/healthz.proto](https://github.com/onexstack/miniblog/blob/feature/s09/pkg/api/apiserver/v1/healthz.proto) 文件，在文件中定义健康检查相关的请求参数，内容如下：

```Protobuf
// Healthz API 定义，包含健康检查响应的相关消息和状态
syntax = "proto3"; // 告诉编译器此文件使用什么版本的语法

package v1;

option go_package = "github.com/onexstack/miniblog/pkg/api/apiserver/v1";

// ServiceStatus 表示服务的健康状态
enum ServiceStatus {
    // Healthy 表示服务健康
    Healthy = 0;
    // Unhealthy 表示服务不健康
    Unhealthy = 1;
}

// HealthzResponse 表示健康检查的响应结构体
message HealthzResponse {
    // status 表示服务的健康状态
    ServiceStatus status = 1;

    // timestamp 表示请求的时间戳
    string timestamp = 2;

    // message 表示可选的状态消息，描述服务健康的更多信息
    string message = 3;
}
```

  

在 healthz.proto 文件中，使用 message 关键字定义消息类型（即接口参数）。消息类型由多个字段组成，每个字段包括字段类型和字段名称。位于等号（=）右侧的值并非字段默认值，而是数字标签，可理解为字段的唯一标识符（类似于数据库中的主键），不可重复。标识符用于在编译后的二进制消息格式中对字段进行识别。**一旦 Protobuf 消息投入使用，字段的标识符就不应再修改。**数字标签的取值范围为 [1, 536870911]，其中 19000 至 19999 为保留数字，不能使用。

  

在实际项目开发中，最常用的是 optional 和 repeated 关键字。Protobuf 更多的语法示例请参考 [pkg/api/apiserver/v1/example.proto](https://github.com/onexstack/miniblog/blob/feature/s09/pkg/api/apiserver/v1/example.proto) 文件，更多 Protobuf 语法请参考 Protobuf 的官方文档。  

### 2.4.2. **生成客户端和服务器代码**

  

编写好 Protobuf 文件后，需要使用 protoc 工具对 Protobuf 文件进行编译，以生成所需的客户端和服务端代码。由于在项目迭代过程中，Protobuf 文件可能会经常被修改并需要重新编译，为了提高开发效率和简化项目维护的复杂度，我们可以将编译操作定义为 [Makefile](https://github.com/onexstack/miniblog/blob/feature/s09/Makefile#L66) 中的一个目标。在 Makefile 文件中，添加以下代码：  

```Makefile
...
# Protobuf 文件存放路径
APIROOT=$(PROJ_ROOT_DIR)/pkg/api
...
protoc: # 编译 protobuf 文件.
    @echo "===========> Generate protobuf files"
    @protoc                                              \
        --proto_path=$(APIROOT)                          \
        --proto_path=$(PROJ_ROOT_DIR)/third_party/protobuf    \
        --go_out=paths=source_relative:$(APIROOT)        \
        --go-grpc_out=paths=source_relative:$(APIROOT)   \
        $(shell find $(APIROOT) -name *.proto)
```

  

上述 protoc 规则的命令中，protoc 是 Protocol Buffers 文件的编译器工具，用于编译 .proto 文件生成代码。需要先安装 protoc 命令后才能使用。protoc 通过插件机制实现对不同语言的支持。例如，使用 --xxx_out 参数时，protoc 会首先查询是否存在内置的 xxx 插件。如果没有内置的 xxx 插件，则会继续查询系统中是否存在名为 protoc-gen-xxx 的可执行程序。例如 --go_out 参数使用的插件名为 protoc-gen-go。

  

以下是 protoc 命令参数的说明：

1. -proto_path 或 -I：用于指定编译源码的搜索路径，类似于 C/C++中的头文件搜索路径，在构建 .proto 文件时，protoc 会在这些路径下查找所需的 Protobuf 文件及其依赖；
2. -go_out：用于生成与 gRPC 服务相关的 Go 代码，并配置生成文件的路径和文件结构。例如 --go_out=plugins=grpc,paths=import:.。主要参数包括 plugins 和 paths。分别表示生成 Go 代码所使用的插件，以及生成的 Go 代码的位置。这里我们使用到了 paths 参数，它支持以下两个选项：
3. import（默认值）：按照生成的 Go 代码包的全路径创建目录结构；
4. source_relative：表示生成的文件应保持与输入文件相对路径一致。假设 Protobuf 文件位于 pkg/api/apiserver/v1/example.proto，启用该选项后，生成的代码也会位于 pkg/api/apiserver/v1/目录。如果没有设置 paths=source_relative，默认情况下，生成的 Go 文件的路径可能与包含路径有直接关系，并不总是与输入文件相对路径保持一致。
5. -go-grpc_out：功能与 --go_out 类似，但该参数用于指定生成的 *_grpc.pb.go 文件的存放路径。

  

在 pkg/api/apiserver/v1/apiserver.proto 文件中，通过以下语句导入了 empty.proto 文件：

```Protobuf
import "google/protobuf/empty.proto";
```

  

因此，需要将 empty.proto 文件保存在匹配的路径下，并通过以下参数将其添加到 Protobuf 文件的搜索路径中：--proto_path=$(PROJ_ROOT_DIR)/third_party/protobuf。

  

由于 empty.proto 是第三方项目的文件，根据目录结构规范，应将其存放在项目根目录下的 third_party 目录中。

  

执行以下命令编译 Protobuf 文件：

```Shell
make protoc
```

  

述命令会在 [pkg/api/apiserver/v1/](https://github.com/onexstack/miniblog/tree/feature/s09/pkg/api/apiserver/v1) 目录下生成以下两类文件：

1. .pb.go：包含与 Protobuf 文件中定义的消息类型（使用 message 关键字）对应的 Go 语言结构体、枚举类型、以及与这些结构体相关的序列化、反序列化代码。主要功能是将 Protobuf 数据格式与 Go 语言中的结构体进行映射，并支持 Protobuf 协议的数据序列化与反序列化操作；
2. _grpc.pb.go：包含与 Protobuf 文件中定义的服务（使用 service 关键字）对应的 gRPC 服务代码。该文件会定义客户端和服务端用到的接口（interface），并包含注册服务的方法（如 RegisterService）。

  

> 💡 提示：  
> 由于编译 Protobuf 文件不是每次构建都需要执行的操作，因此未将 protoc 目标添加为 Makefile 中 all 目标的依赖项。

  

### 2.4.3. **实现 gRPC 服务端**

  

启动 gRPC 服务，需要指定一些核心配置，例如 gRPC 服务监听的端口。所以，需要先给应用添加 gRPC 服务配置。根据 miniblog 应用构建模型，需要先添加初始化配置，再添加运行时配置，之后根据运行时配置创建一个 gRPC 服务实例。代码实现如代码清单 7-1 所示（位于 [cmd/mb-apiserver/app/options/options.go](https://github.com/onexstack/miniblog/blob/feature/s09/cmd/mb-apiserver/app/options/options.go#L25) 文件中）。

  

TODO

  

# 🤗 总结归纳

总结文章的内容

# 📎 参考文章

- [12 | Web 服务器实现：什么是 Web 应用？](https://articles.zsxq.com/id_o96j60qppjpt.html)
- 引用文章

  

> [!important] 有关Notion安装或者使用上的问题，欢迎您在底部评论区留言，一起交流~