# RPC

## 1. RPC <a id="rpc"></a>

### 1.1.1. RPC简介 <a id="rpc&#x7B80;&#x4ECB;"></a>

* 远程过程调用（Remote Procedure Call，RPC）是一个计算机通信协议
* 该协议允许运行于一台计算机的程序调用另一台计算机的子程序，而程序员无需额外地为这个交互作用编程
* 如果涉及的软件采用面向对象编程，那么远程过程调用亦可称作远程调用或远程方法调用

### 1.1.2. 流行RPC框架的对比 <a id="&#x6D41;&#x884C;rpc&#x6846;&#x67B6;&#x7684;&#x5BF9;&#x6BD4;"></a>

### 1.1.3. golang中如何实现RPC <a id="golang&#x4E2D;&#x5982;&#x4F55;&#x5B9E;&#x73B0;rpc"></a>

* golang中实现RPC非常简单，官方提供了封装好的库，还有一些第三方的库
* golang官方的net/rpc库使用encoding/gob进行编解码，支持tcp和http数据传输方式，由于其他语言不支持gob编解码方式，所以golang的RPC只支持golang开发的服务器与客户端之间的交互
* 官方还提供了net/rpc/jsonrpc库实现RPC方法，jsonrpc采用JSON进行数据编解码，因而支持跨语言调用，目前jsonrpc库是基于tcp协议实现的，暂不支持http传输方式
* 例题：golang实现RPC程序，实现求矩形面积和周长

**服务端**

```text
package main

import (
    "log"
    "net/http"
    "net/rpc"
)



type Params struct {
    Width, Height int
}

type Rect struct{}


func (r *Rect) Area(p Params, ret *int) error {
    *ret = p.Height * p.Width
    return nil
}


func (r *Rect) Perimeter(p Params, ret *int) error {
    *ret = (p.Height + p.Width) * 2
    return nil
}


func main() {
    
    rect := new(Rect)
    
    rpc.Register(rect)
    
    rpc.HandleHTTP()
    
    err := http.ListenAndServe(":8000", nil)
    if err != nil {
        log.Panicln(err)
    }
}
```

**客户端**

```text
package main

import (
    "fmt"
    "log"
    "net/rpc"
)


type Params struct {
    Width, Height int
}


func main() {
    
    conn, err := rpc.DialHTTP("tcp", ":8000")
    if err != nil {
        log.Fatal(err)
    }
    
    
    ret := 0
    err2 := conn.Call("Rect.Area", Params{50, 100}, &ret)
    if err2 != nil {
        log.Fatal(err2)
    }
    fmt.Println("面积：", ret)
    
    err3 := conn.Call("Rect.Perimeter", Params{50, 100}, &ret)
    if err3 != nil {
        log.Fatal(err3)
    }
    fmt.Println("周长：", ret)
}
```

* golang写RPC程序，必须符合4个基本条件，不然RPC用不了
  * 结构体字段首字母要大写，可以别人调用
  * 函数名必须首字母大写
  * 函数第一参数是接收参数，第二个参数是返回给客户端的参数，必须是指针类型
  * 函数还必须有一个返回值error
* 练习：模仿前面例题，自己实现RPC程序，服务端接收2个参数，可以做乘法运算，也可以做商和余数的运算，客户端进行传参和访问，得到结果如下：

服务端代码：

```text
package main

import (
   "errors"
   "log"
   "net/http"
   "net/rpc"
)


type Arith struct{}


type ArithRequest struct {
   A, B int
}


type ArithResponse struct {
   
   Pro int
   
   Quo int
   
   Rem int
}


func (this *Arith) Multiply(req ArithRequest, res *ArithResponse) error {
   res.Pro = req.A * req.B
   return nil
}


func (this *Arith) Divide(req ArithRequest, res *ArithResponse) error {
   if req.B == 0 {
      return errors.New("除数不能为0")
   }
   
   res.Quo = req.A / req.B
   
   res.Rem = req.A % req.B
   return nil
}


func main() {
   
   rect := new(Arith)
   
   rpc.Register(rect)
   
   rpc.HandleHTTP()
   
   err := http.ListenAndServe(":8000", nil)
   if err != nil {
      log.Fatal(err)
   }
}
```

客户端代码：

```text
package main

import (
   "fmt"
   "log"
   "net/rpc"
)

type ArithRequest struct {
   A, B int
}


type ArithResponse struct {
   
   Pro int
   
   Quo int
   
   Rem int
}

func main() {
   conn, err := rpc.DialHTTP("tcp", ":8000")
   if err != nil {
      log.Fatal(err)
   }
   req := ArithRequest{9, 2}
   var res ArithResponse
   err2 := conn.Call("Arith.Multiply", req, &res)
   if err2 != nil {
      log.Fatal(err2)
   }
   fmt.Printf("%d * %d = %d\n", req.A, req.B, res.Pro)
   err3 := conn.Call("Arith.Divide", req, &res)
   if err3 != nil {
      log.Fatal(err3)
   }
   fmt.Printf("%d / %d 商 %d，余数 = %d\n", req.A, req.B, res.Quo, res.Rem)
}
```

> > > 另外，net/rpc/jsonrpc库通过json格式编解码，支持跨语言调用

服务端代码：

```text
package main

import (
    "fmt"
    "log"
    "net"
    "net/rpc"
    "net/rpc/jsonrpc"
)

type Params struct {
    Width, Height int
}
type Rect struct {
}

func (r *Rect) Area(p Params, ret *int) error {
    *ret = p.Width * p.Height
    return nil
}
func (r *Rect) Perimeter(p Params, ret *int) error {
    *ret = (p.Height + p.Width) * 2
    return nil
}
func main() {
    rpc.Register(new(Rect))
    lis, err := net.Listen("tcp", ":8080")
    if err != nil {
        log.Panicln(err)
    }
    for {
        conn, err := lis.Accept()
        if err != nil {
            continue
        }
        go func(conn net.Conn) {
            fmt.Println("new client")
            jsonrpc.ServeConn(conn)
        }(conn)
    }
}
```

客户端代码：

```text
package main

import (
    "fmt"
    "log"
    "net/rpc/jsonrpc"
)

type Params struct {
    Width, Height int
}

func main() {
    conn, err := jsonrpc.Dial("tcp", ":8080")
    if err != nil {
        log.Panicln(err)
    }
    ret := 0
    err2 := conn.Call("Rect.Area", Params{50, 100}, &ret)
    if err2 != nil {
        log.Panicln(err2)
    }
    fmt.Println("面积：", ret)
    err3 := conn.Call("Rect.Perimeter", Params{50, 100}, &ret)
    if err3 != nil {
        log.Panicln(err3)
    }
    fmt.Println("周长：", ret)
}
```

### 1.1.4. RPC调用流程 <a id="rpc&#x8C03;&#x7528;&#x6D41;&#x7A0B;"></a>

* 微服务架构下数据交互一般是对内 RPC，对外 REST
* 将业务按功能模块拆分到各个微服务，具有提高项目协作效率、降低模块耦合度、提高系统可用性等优点，但是开发门槛比较高，比如 RPC 框架的使用、后期的服务监控等工作
* 一般情况下，我们会将功能代码在本地直接调用，微服务架构下，我们需要将这个函数作为单独的服务运行，客户端通过网络调用

### 1.1.5. 网络传输数据格式 <a id="&#x7F51;&#x7EDC;&#x4F20;&#x8F93;&#x6570;&#x636E;&#x683C;&#x5F0F;"></a>

* 两端要约定好数据包的格式
* 成熟的RPC框架会有自定义传输协议，这里网络传输格式定义如下，前面是固定长度消息头，后面是变长消息体
* 自己定义数据格式的读写

```text
package rpc

import (
    "encoding/binary"
    "io"
    "net"
)




type Session struct {
    conn net.Conn
}


func NewSession(conn net.Conn) *Session {
    return &Session{conn: conn}
}


func (s *Session) Write(data []byte) error {
    
    
    buf := make([]byte, 4+len(data))
    
    binary.BigEndian.PutUint32(buf[:4], uint32(len(data)))
    
    copy(buf[4:], data)
    _, err := s.conn.Write(buf)
    if err != nil {
        return err
    }
    return nil
}


func (s *Session) Read() ([]byte, error) {
    
    header := make([]byte, 4)
    
    _, err := io.ReadFull(s.conn, header)
    if err != nil {
        return nil, err
    }
    
    dataLen := binary.BigEndian.Uint32(header)
    data := make([]byte, dataLen)
    _, err = io.ReadFull(s.conn, data)
    if err != nil {
        return nil, err
    }
    return data, nil
}
```

**测试类**

```text
package rpc

import (
    "fmt"
    "net"
    "sync"
    "testing"
)

func TestSession_ReadWriter(t *testing.T) {
    
    addr := "127.0.0.1:8000"
    my_data := "hello"
    
    wg := sync.WaitGroup{}
    wg.Add(2)
    
    go func() {
        defer wg.Done()
        lis, err := net.Listen("tcp", addr)
        if err != nil {
            t.Fatal(err)
        }
        conn, _ := lis.Accept()
        s := Session{conn: conn}
        err = s.Write([]byte(my_data))
        if err != nil {
            t.Fatal(err)
        }
    }()

    
    go func() {
        defer wg.Done()
        conn, err := net.Dial("tcp", addr)
        if err != nil {
            t.Fatal(err)
        }
        s := Session{conn: conn}
        data, err := s.Read()
        if err != nil {
            t.Fatal(err)
        }
        
        if string(data) != my_data {
            t.Fatal(err)
        }
        fmt.Println(string(data))
    }()
    wg.Wait()
}
```

**编码解码**

```text
package rpc

import (
    "bytes"
    "encoding/gob"
)


type RPCData struct {
    
    Name string
    
    Args []interface{}
}


func encode(data RPCData) ([]byte, error) {
    
    var buf bytes.Buffer
    bufEnc := gob.NewEncoder(&buf)
    
    if err := bufEnc.Encode(data); err != nil {
        return nil, err
    }
    return buf.Bytes(), nil
}


func decode(b []byte) (RPCData, error) {
    buf := bytes.NewBuffer(b)
    
    bufDec := gob.NewDecoder(buf)
    
    var data RPCData
    if err := bufDec.Decode(&data); err != nil {
        return data, err
    }
    return data, nil
}
```

### 1.1.6. 实现RPC服务端 <a id="&#x5B9E;&#x73B0;rpc&#x670D;&#x52A1;&#x7AEF;"></a>

* 服务端接收到的数据需要包括什么？
  * 调用的函数名、参数列表，还有一个返回值error类型
* 服务端需要解决的问题是什么？
  * Map维护客户端传来调用函数，服务端知道去调谁
* 服务端的核心功能有哪些？
  * 维护函数map
  * 客户端传来的东西进行解析
  * 函数的返回值打包，传给客户端

```text
package rpc

import (
    "fmt"
    "net"
    "reflect"
)


type Server struct {
    
    addr string
    
    funcs map[string]reflect.Value
}


func NewServer(addr string) *Server {
    return &Server{addr: addr, funcs: make(map[string]reflect.Value)}
}



func (s *Server) Register(rpcName string, f interface{}) {
    
    
    if _, ok := s.funcs[rpcName]; ok {
        return
    }
    
    fVal := reflect.ValueOf(f)
    s.funcs[rpcName] = fVal
}


func (s *Server) Run() {
    
    lis, err := net.Listen("tcp", s.addr)
    if err != nil {
        fmt.Printf("监听 %s err :%v", s.addr, err)
        return
    }
    for {
        
        conn, err := lis.Accept()
        if err != nil {
            return
        }
        serSession := NewSession(conn)
        
        b, err := serSession.Read()
        if err != nil {
            return
        }
        
        rpcData, err := decode(b)
        if err != nil {
            return
        }
        
        f, ok := s.funcs[rpcData.Name]
        if !ok {
            fmt.Println("函数 %s 不存在", rpcData.Name)
            return
        }
        
        inArgs := make([]reflect.Value, 0, len(rpcData.Args))
        for _, arg := range rpcData.Args {
            inArgs = append(inArgs, reflect.ValueOf(arg))
        }
        
        
        out := f.Call(inArgs)
        
        outArgs := make([]interface{}, 0, len(out))
        for _, o := range out {
            outArgs = append(outArgs, o.Interface())
        }
        
        respRPCData := RPCData{rpcData.Name, outArgs}
        bytes, err := encode(respRPCData)
        if err != nil {
            return
        }
        
        err = serSession.Write(bytes)
        if err != nil {
            return
        }
    }
}
```

### 1.1.7. 实现RPC客户端 <a id="&#x5B9E;&#x73B0;rpc&#x5BA2;&#x6237;&#x7AEF;"></a>

* 客户端只有函数原型，使用reflect.MakeFunc\(\) 可以完成原型到函数的调用
* reflect.MakeFunc\(\)是Client从函数原型到网络调用的关键

```text
package rpc

import (
    "net"
    "reflect"
)


type Client struct {
    conn net.Conn
}


func NewClient(conn net.Conn) *Client {
    return &Client{conn: conn}
}






func (c *Client) callRPC(rpcName string, fPtr interface{}) {
    
    fn := reflect.ValueOf(fPtr).Elem()
    
    f := func(args []reflect.Value) []reflect.Value {
        
        inArgs := make([]interface{}, 0, len(args))
        for _, arg := range args {
            inArgs = append(inArgs, arg.Interface())
        }
        
        cliSession := NewSession(c.conn)
        
        reqRPC := RPCData{Name: rpcName, Args: inArgs}
        b, err := encode(reqRPC)
        if err != nil {
            panic(err)
        }
        
        err = cliSession.Write(b)
        if err != nil {
            panic(err)
        }
        
        respBytes, err := cliSession.Read()
        if err != nil {
            panic(err)
        }
        
        respRPC, err := decode(respBytes)
        if err != nil {
            panic(err)
        }
        
        outArgs := make([]reflect.Value, 0, len(respRPC.Args))
        for i, arg := range respRPC.Args {
            
            if arg == nil {
                
                
                outArgs = append(outArgs, reflect.Zero(fn.Type().Out(i)))
                continue
            }
            outArgs = append(outArgs, reflect.ValueOf(arg))
        }
        return outArgs
    }
    
    
    
    
    v := reflect.MakeFunc(fn.Type(), f)
    
    fn.Set(v)
}
```

### 1.1.8. 实现RPC通信测试 <a id="&#x5B9E;&#x73B0;rpc&#x901A;&#x4FE1;&#x6D4B;&#x8BD5;"></a>

* 给服务端注册一个查询用户的方法，客户端使用RPC方式调用

```text
package rpc

import (
    "encoding/gob"
    "fmt"
    "net"
    "testing"
)




type User struct {
    Name string
    Age  int
}


func queryUser(uid int) (User, error) {
    user := make(map[int]User)
    
    user[0] = User{"zs", 20}
    user[1] = User{"ls", 21}
    user[2] = User{"ww", 22}
    
    if u, ok := user[uid]; ok {
        return u, nil
    }
    return User{}, fmt.Errorf("%d err", uid)
}

func TestRPC(t *testing.T) {
    
    gob.Register(User{})
    addr := "127.0.0.1:8000"
    
    srv := NewServer(addr)
    
    srv.Register("queryUser", queryUser)
    
    go srv.Run()
    
    conn, err := net.Dial("tcp", addr)
    if err != nil {
        fmt.Println("err")
    }
    
    cli := NewClient(conn)
    
    var query func(int) (User, error)
    cli.callRPC("queryUser", &query)
    
    u, err := query(1)
    if err != nil {
        fmt.Println("err")
    }
    fmt.Println(u)
}
```

