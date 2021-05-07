# 认证

## 1. 认证 <a id="&#x8BA4;&#x8BC1;"></a>

gRPC默认内置了两种认证方式：

* SSL/TLS认证方式
* 基于Token的认证方式

同时，gRPC提供了接口用于扩展自定义认证方式

### 1.1. TLS认证示例 <a id="tls&#x8BA4;&#x8BC1;&#x793A;&#x4F8B;"></a>

这里直接扩展hello项目，实现TLS认证机制

首先需要准备证书，在hello目录新建keys目录用于存放证书文件。

#### 1.1.1. 证书制作 <a id="&#x8BC1;&#x4E66;&#x5236;&#x4F5C;"></a>

**制作私钥 \(.key\)**

```text

$ openssl genrsa -out server.key 2048



$ openssl ecparam -genkey -name secp384r1 -out server.key
```

**自签名公钥\(x509\) \(PEM-encodings .pem\|.crt\)**

```text
$ openssl req -new -x509 -sha256 -key server.key -out server.pem -days 3650
```

**自定义信息**

```text
-----
Country Name (2 letter code) [AU]:CN  //这个是中国的缩写
State or Province Name (full name) [Some-State]:XxXx  //省份
Locality Name (eg, city) []:XxXx  //城市
Organization Name (eg, company) [Internet Widgits Pty Ltd]:XX Co. Ltd  //公司名称
Organizational Unit Name (eg, section) []:Dev   //部门名称
Common Name (e.g. server FQDN or YOUR name) []:server name   //服务名称 例如www.topgoer.com
Email Address []:xxx@xxx.com  //邮箱地址
```

#### 1.1.2. 目录结构 <a id="&#x76EE;&#x5F55;&#x7ED3;&#x6784;"></a>

```text
|—— hello-tls/
    |—— client/
        |—— main.go   // 客户端
    |—— server/
        |—— main.go   // 服务端
|—— keys/                 // 证书目录
    |—— server.key
    |—— server.pem
|—— proto/
    |—— hello/
        |—— hello.proto   // proto描述文件
        |—— hello.pb.go   // proto编译后文件
```

#### 1.1.3. 示例代码 <a id="&#x793A;&#x4F8B;&#x4EE3;&#x7801;"></a>

`proto/helloworld.proto`及`proto/hello.pb.go`文件不需要改动

修改服务端代码：server/main.go

```text
package main

import (
    "fmt"
    "net"

    pb "github.com/jergoo/go-grpc-example/proto/hello"

    "golang.org/x/net/context"
    "google.golang.org/grpc"
    "google.golang.org/grpc/credentials" 
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
        grpclog.Fatalf("Failed to listen: %v", err)
    }

    
    creds, err := credentials.NewServerTLSFromFile("../../keys/server.pem", "../../keys/server.key")
    if err != nil {
        grpclog.Fatalf("Failed to generate credentials %v", err)
    }

    
    s := grpc.NewServer(grpc.Creds(creds))

    
    pb.RegisterHelloServer(s, HelloService)

    grpclog.Println("Listen on " + Address + " with TLS")

    s.Serve(listen)
}
```

运行：

```text
$ go run main.go

Listen on 127.0.0.1:50052 with TLS
```

服务端在实例化grpc Server时，可配置多种选项，TLS认证是其中之一

客户端添加TLS认证：client/main.go

```text
package main

import (
    pb "github.com/jergoo/go-grpc-example/proto/hello" 

    "golang.org/x/net/context"
    "google.golang.org/grpc"
    "google.golang.org/grpc/credentials" 
    "google.golang.org/grpc/grpclog"
)

const (
    
    Address = "127.0.0.1:50052"
)

func main() {
    
    creds, err := credentials.NewClientTLSFromFile("../../keys/server.pem", "server name")
    if err != nil {
        grpclog.Fatalf("Failed to create TLS credentials %v", err)
    }

    conn, err := grpc.Dial(Address, grpc.WithTransportCredentials(creds))
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
```

运行：

```text
$ go run main.go

Hello gRPC
```

客户端添加TLS认证的方式和服务端类似，在创建连接`Dial`时，同样可以配置多种选项，后面的示例中会看到更多的选项。

### 1.2. Token认证示例 <a id="token&#x8BA4;&#x8BC1;&#x793A;&#x4F8B;"></a>

再进一步，继续扩展hello-tls项目，实现TLS + Token认证机制

#### 1.2.1. 目录结构 <a id="&#x76EE;&#x5F55;&#x7ED3;&#x6784;_1"></a>

```text
|—— hello_token/
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

#### 1.2.2. 示例代码 <a id="&#x793A;&#x4F8B;&#x4EE3;&#x7801;_1"></a>

先修改客户端实现：client/main.go

```text
package main

import (
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
```

这里我们定义了一个`customCredential`结构，并实现了两个方法`GetRequestMetadata`和`RequireTransportSecurity`。这是gRPC提供的自定义认证方式，每次RPC调用都会传输认证信息。`customCredential`其实是实现了`grpc/credential`包内的`PerRPCCredentials`接口。每次调用，token信息会通过请求的metadata传输到服务端。下面具体看一下服务端如何获取metadata中的信息。

修改server/main.go中的SayHello方法：

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
    
    md, ok := metadata.FromContext(ctx)
    if !ok {
        return nil, grpc.Errorf(codes.Unauthenticated, "无Token认证信息")
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
        return nil, grpc.Errorf(codes.Unauthenticated, "Token认证信息无效: appid=%s, appkey=%s", appid, appkey)
    }

    resp := new(pb.HelloResponse)
    resp.Message = fmt.Sprintf("Hello %s.\nToken info: appid=%s,appkey=%s", in.Name, appid, appkey)

    return resp, nil
}

func main() {
    listen, err := net.Listen("tcp", Address)
    if err != nil {
        grpclog.Fatalf("failed to listen: %v", err)
    }

    
    creds, err := credentials.NewServerTLSFromFile("../../keys/server.pem", "../../keys/server.key")
    if err != nil {
        grpclog.Fatalf("Failed to generate credentials %v", err)
    }

    
    s := grpc.NewServer(grpc.Creds(creds))

    
    pb.RegisterHelloServer(s, HelloService)

    grpclog.Println("Listen on " + Address + " with TLS + Token")

    s.Serve(listen)
}
```

服务端可以从`context`中获取每次请求的metadata，从中读取客户端发送的token信息并验证有效性。

运行：

```text
$ go run main.go

Listen on 50052 with TLS + Token
```

运行客户端程序 client/main.go：

```text
$ go run main.go

// 认证成功结果
Hello gRPC
Token info: appid=101010,appkey=i am key

// 修改key验证认证失败结果：
rpc error: code = 16 desc = Token认证信息无效: appID=101010, appKey=i am not key
```

`google.golang.org/grpc/credentials/oauth`包已实现了用于Google API的oauth和jwt验证的方法，使用方法可以参考[官方文档](https://github.com/grpc/grpc-go/blob/master/Documentation/grpc-auth-support.md)。在实际应用中，我们可以根据自己的业务需求实现合适的验证方式。

