# 拦截器

## 1. 拦截器 <a id="&#x62E6;&#x622A;&#x5668;"></a>

grpc服务端和客户端都提供了interceptor功能，功能类似middleware，很适合在这里处理验证、日志等流程。

在自定义Token认证的示例中，认证信息是由每个服务中的方法处理并认证的，如果有大量的接口方法，这种姿势就太不优雅了，每个接口实现都要先处理认证信息。这个时候interceptor就可以用来解决了这个问题，在请求被转到具体接口之前处理认证信息，一处认证，到处无忧。 在客户端，我们增加一个请求日志，记录请求相关的参数和耗时等等。修改hello\_token项目实现：

### 1.1. 目录结构 <a id="&#x76EE;&#x5F55;&#x7ED3;&#x6784;"></a>

```text
|—— hello_interceptor/
    |—— client/
        |—— main.go   // 客户端
    |—— server/
        |—— main.go   // 服务端
|—— keys/             // 证书目录
    |—— server.key
    |—— server.pem
|—— proto/
    |—— hello/
        |—— hello.proto   // proto描述文件
        |—— hello.pb.go   // proto编译后文件
```

### 1.2. 示例代码 <a id="&#x793A;&#x4F8B;&#x4EE3;&#x7801;"></a>

**Step 1. 服务端interceptor：**

> hello\_interceptor/server/main.go

```text
package main

import (
    "fmt"
    "net"

    pb "github.com/jergoo/go-grpc-example/proto/hello"

    "golang.org/x/net/context"
    "google.golang.org/grpc"
    "google.golang.org/grpc/codes"       
    "google.golang.org/grpc/credentials" 
    "google.golang.org/grpc/grpclog"
    "google.golang.org/grpc/metadata" 
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
        grpclog.Fatalf("Failed to listen: %v", err)
    }

    var opts []grpc.ServerOption

    
    creds, err := credentials.NewServerTLSFromFile("../../keys/server.pem", "../../keys/server.key")
    if err != nil {
        grpclog.Fatalf("Failed to generate credentials %v", err)
    }

    opts = append(opts, grpc.Creds(creds))

    
    opts = append(opts, grpc.UnaryInterceptor(interceptor))

    
    s := grpc.NewServer(opts...)

    
    pb.RegisterHelloServer(s, HelloService)

    grpclog.Println("Listen on " + Address + " with TLS + Token + Interceptor")

    s.Serve(listen)
}


func auth(ctx context.Context) error {
    md, ok := metadata.FromContext(ctx)
    if !ok {
        return grpc.Errorf(codes.Unauthenticated, "无Token认证信息")
    }

    var (
        appid  string
        appkey string
    )

    if val, ok := md["appid"]; ok {
        appid = val[0]
    }

    if val, ok := md["appkey"]; ok {
        appkey = val[0]
    }

    if appid != "101010" || appkey != "i am key" {
        return grpc.Errorf(codes.Unauthenticated, "Token认证信息无效: appid=%s, appkey=%s", appid, appkey)
    }

    return nil
}


func interceptor(ctx context.Context, req interface{}, info *grpc.UnaryServerInfo, handler grpc.UnaryHandler) (interface{}, error) {
    err := auth(ctx)
    if err != nil {
        return nil, err
    }
    
    return handler(ctx, req)
}
```

**Step 2. 实现客户端interceptor：**

> hello\_intercepror/client/main.go

```text
package main

import (
    "time"

    pb "github.com/jergoo/go-grpc-example/proto/hello" 

    "golang.org/x/net/context"
    "google.golang.org/grpc"
    "google.golang.org/grpc/credentials" 
    "google.golang.org/grpc/grpclog"
)

const (
    
    Address = "127.0.0.1:50052"

    
    OpenTLS = true
)


type customCredential struct{}


func (c customCredential) GetRequestMetadata(ctx context.Context, uri ...string) (map[string]string, error) {
    return map[string]string{
        "appid":  "101010",
        "appkey": "i am key",
    }, nil
}


func (c customCredential) RequireTransportSecurity() bool {
    return OpenTLS
}

func main() {
    var err error
    var opts []grpc.DialOption

    if OpenTLS {
        
        creds, err := credentials.NewClientTLSFromFile("../../keys/server.pem", "server name")
        if err != nil {
            grpclog.Fatalf("Failed to create TLS credentials %v", err)
        }
        opts = append(opts, grpc.WithTransportCredentials(creds))
    } else {
        opts = append(opts, grpc.WithInsecure())
    }

    
    opts = append(opts, grpc.WithPerRPCCredentials(new(customCredential)))
    
    opts = append(opts, grpc.WithUnaryInterceptor(interceptor))

    conn, err := grpc.Dial(Address, opts...)
    if err != nil {
        grpclog.Fatalln(err)
    }
    defer conn.Close()

    
    c := pb.NewHelloClient(conn)

    
    req := &pb.HelloRequest{Name: "gRPC"}
    res, err := c.SayHello(context.Background(), req)
    if err != nil {
        grpclog.Fatalln(err)
    }

    grpclog.Println(res.Message)
}


func interceptor(ctx context.Context, method string, req, reply interface{}, cc *grpc.ClientConn, invoker grpc.UnaryInvoker, opts ...grpc.CallOption) error {
    start := time.Now()
    err := invoker(ctx, method, req, reply, cc, opts...)
    grpclog.Printf("method=%s req=%v rep=%v duration=%s error=%v\n", method, req, reply, time.Since(start), err)
    return err
}
```

### 1.3. 运行结果 <a id="&#x8FD0;&#x884C;&#x7ED3;&#x679C;"></a>

```text
$ cd hello_inteceptor/server && go run main.go
Listen on 127.0.0.1:50052 with TLS + Token + Interceptor
```

```text
$ cd hello_inteceptor/client && go run main.go
method=/hello.Hello/SayHello req=name:"gRPC"  rep=message:"Hello gRPC."  duration=33.879699ms error=<nil>

Hello gRPC.
```

**项目推荐：** [go-grpc-middleware](https://github.com/grpc-ecosystem/go-grpc-middleware)

这个项目对interceptor进行了封装，支持多个拦截器的链式组装，对于需要多种处理的地方使用起来会更方便些。

