# 内置Trace

## 1. 内置Trace <a id="&#x5185;&#x7F6E;trace"></a>

grpc内置了客户端和服务端的请求追踪，基于`golang.org/x/net/trace`包实现，默认是开启状态，可以查看事件和请求日志，对于基本的请求状态查看调试也是很有帮助的，客户端与服务端基本一致，这里以服务端开启trace server为例，修改hello项目服务端代码：

### 1.1. 目录结构 <a id="&#x76EE;&#x5F55;&#x7ED3;&#x6784;"></a>

```text
|—— hello_trace/
    |—— client/
        |—— main.go   // 客户端
    |—— server/
        |—— main.go   // 服务端
|—— proto/
    |—— hello/
        |—— hello.proto   // proto描述文件
        |—— hello.pb.go   // proto编译后文件
```

### 1.2. 示例代码 <a id="&#x793A;&#x4F8B;&#x4EE3;&#x7801;"></a>

```text
package main

import (
    "fmt"
    "net"
    "net/http"

    pb "github.com/jergoo/go-grpc-example/proto/hello" 

    "golang.org/x/net/context"
    "golang.org/x/net/trace"
    "google.golang.org/grpc"
    "google.golang.org/grpc/grpclog"
)

const (
    
    Address = "127.0.0.1:50052"
)


type helloService struct{}


var HelloService = helloService{}


func (h helloService) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloResponse, error) {
    resp := new(pb.HelloResponse)
    resp.Message = fmt.Sprintf("Hello %s.", in.Name)

    return resp, nil
}

func main() {
    listen, err := net.Listen("tcp", Address)
    if err != nil {
        grpclog.Fatalf("failed to listen: %v", err)
    }

    
    s := grpc.NewServer()

    
    pb.RegisterHelloServer(s, HelloService)

    
    go startTrace()

    grpclog.Println("Listen on " + Address)
    s.Serve(listen)
}

func startTrace() {
    trace.AuthRequest = func(req *http.Request) (any, sensitive bool) {
        return true, true
    }
    go http.ListenAndServe(":50051", nil)
    grpclog.Println("Trace listen on 50051")
}
```

这里我们开启一个http服务监听50051端口，用来查看grpc请求的trace信息

运行：

```text
$ go run main.go

Listen on 127.0.0.1:50052                                                       
Trace listen on 50051


```

### 1.3. 服务端事件查看 <a id="&#x670D;&#x52A1;&#x7AEF;&#x4E8B;&#x4EF6;&#x67E5;&#x770B;"></a>

访问：localhost:50051/debug/events，结果如图：

可以看到服务端注册的服务和服务正常启动的事件信息。

### 1.4. 请求日志信息查看 <a id="&#x8BF7;&#x6C42;&#x65E5;&#x5FD7;&#x4FE1;&#x606F;&#x67E5;&#x770B;"></a>

访问：localhost:50051/debug/requests，结果如图：

这里可以显示最近的请求状态，包括请求的服务、参数、耗时、响应，对于简单的状态查看还是很方便的，默认值显示最近10条记录。

