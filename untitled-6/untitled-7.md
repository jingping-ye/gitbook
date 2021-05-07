# Go Micro接口详解 · Go语言中文文档

## 1. Go Micro接口详解 <a id="go-micro&#x63A5;&#x53E3;&#x8BE6;&#x89E3;"></a>

### 1.1.1. Transort通信接口 <a id="transort&#x901A;&#x4FE1;&#x63A5;&#x53E3;"></a>

通信相关接口

```text
type Socket interface {
   Recv(*Message) error
   Send(*Message) error
   Close() error
}

type Client interface {
   Socket
}

type Listener interface {
   Addr() string
   Close() error
   Accept(func(Socket)) error
}

type Transport interface {
   Dial(addr string, opts ...DialOption) (Client, error)
   Listen(addr string, opts ...ListenOption) (Listener, error)
   String() string
}
```

### 1.1.2. Codec编码接口 <a id="codec&#x7F16;&#x7801;&#x63A5;&#x53E3;"></a>

编解码，底层也是protobuf

```text
type Codec interface {
   ReadHeader(*Message, MessageType) error
   ReadBody(interface{}) error
   Write(*Message, interface{}) error
   Close() error
   String() string
}
```

### 1.1.3. Registry注册接口 <a id="registry&#x6CE8;&#x518C;&#x63A5;&#x53E3;"></a>

服务注册发现的实现：etcd、consul、mdns、kube-DNS、zk

```text
type Registry interface {
   Register(*Service, ...RegisterOption) error
   Deregister(*Service) error
   GetService(string) ([]*Service, error)
   ListServices() ([]*Service, error)
   Watch(...WatchOption) (Watcher, error)
   String() string
   Options() Options
}
```

### 1.1.4. Selector负载均衡 <a id="selector&#x8D1F;&#x8F7D;&#x5747;&#x8861;"></a>

根据不同算法请求主机列表

```text
type Selector interface {
   Init(opts ...Option) error
   Options() Options
   
   Select(service string, opts ...SelectOption) (Next, error)
   
   Mark(service string, node *registry.Node, err error)
   
   Reset(service string)
   
   Close() error
   
   String() string
}
```

### 1.1.5. Broker发布订阅接口 <a id="broker&#x53D1;&#x5E03;&#x8BA2;&#x9605;&#x63A5;&#x53E3;"></a>

pull push watch

```text
type Broker interface {
   Options() Options
   Address() string
   Connect() error
   Disconnect() error
   Init(...Option) error
   Publish(string, *Message, ...PublishOption) error
   Subscribe(string, Handler, ...SubscribeOption) (Subscriber, error)
   String() string
}
```

### 1.1.6. Client客户端接口 <a id="client&#x5BA2;&#x6237;&#x7AEF;&#x63A5;&#x53E3;"></a>

```text
type Client interface {
   Init(...Option) error
   Options() Options
   NewMessage(topic string, msg interface{}, opts ...MessageOption) Message
   NewRequest(service, method string, req interface{}, reqOpts ...RequestOption) Request
   Call(ctx context.Context, req Request, rsp interface{}, opts ...CallOption) error
   Stream(ctx context.Context, req Request, opts ...CallOption) (Stream, error)
   Publish(ctx context.Context, msg Message, opts ...PublishOption) error
   String() string
}
```

### 1.1.7. Server服务端接口 <a id="server&#x670D;&#x52A1;&#x7AEF;&#x63A5;&#x53E3;"></a>

```text
type Server interface {
   Options() Options
   Init(...Option) error
   Handle(Handler) error
   NewHandler(interface{}, ...HandlerOption) Handler
   NewSubscriber(string, interface{}, ...SubscriberOption) Subscriber
   Subscribe(Subscriber) error
   Register() error
   Deregister() error
   Start() error
   Stop() error
   String() string
}
```

### 1.1.8. Serveice接口 <a id="serveice&#x63A5;&#x53E3;"></a>

```text
type Service interface {
   Init(...Option)
   Options() Options
   Client() client.Client
   Server() server.Server
   Run() error
   String() string
}
```

