# TCP编程

## 1. TCP编程 <a id="tcp&#x7F16;&#x7A0B;"></a>

### 1.1.1. Go语言实现TCP通信 <a id="go&#x8BED;&#x8A00;&#x5B9E;&#x73B0;tcp&#x901A;&#x4FE1;"></a>

#### TCP协议 <a id="tcp&#x534F;&#x8BAE;"></a>

TCP/IP\(Transmission Control Protocol/Internet Protocol\) 即传输控制协议/网间协议，是一种面向连接（连接导向）的、可靠的、基于字节流的传输层（Transport layer）通信协议，因为是面向连接的协议，数据像水流一样传输，会存在黏包问题。

#### TCP服务端 <a id="tcp&#x670D;&#x52A1;&#x7AEF;"></a>

一个TCP服务端可以同时连接很多个客户端，例如世界各地的用户使用自己电脑上的浏览器访问淘宝网。因为Go语言中创建多个goroutine实现并发非常方便和高效，所以我们可以每建立一次链接就创建一个goroutine去处理。

TCP服务端程序的处理流程：

```text
    1.监听端口
    2.接收客户端请求建立链接
    3.创建goroutine处理链接。
```

我们使用Go语言的net包实现的TCP服务端代码如下：

```text





func process(conn net.Conn) {
    defer conn.Close() 
    for {
        reader := bufio.NewReader(conn)
        var buf [128]byte
        n, err := reader.Read(buf[:]) 
        if err != nil {
            fmt.Println("read from client failed, err:", err)
            break
        }
        recvStr := string(buf[:n])
        fmt.Println("收到client端发来的数据：", recvStr)
        conn.Write([]byte(recvStr)) 
    }
}

func main() {
    listen, err := net.Listen("tcp", "127.0.0.1:20000")
    if err != nil {
        fmt.Println("listen failed, err:", err)
        return
    }
    for {
        conn, err := listen.Accept() 
        if err != nil {
            fmt.Println("accept failed, err:", err)
            continue
        }
        go process(conn) 
    }
}
```

将上面的代码保存之后编译成server或server.exe可执行文件。

#### TCP客户端 <a id="tcp&#x5BA2;&#x6237;&#x7AEF;"></a>

一个TCP客户端进行TCP通信的流程如下：

```text
    1.建立与服务端的链接
    2.进行数据收发
    3.关闭链接
```

使用Go语言的net包实现的TCP客户端代码如下：

```text



func main() {
    conn, err := net.Dial("tcp", "127.0.0.1:20000")
    if err != nil {
        fmt.Println("err :", err)
        return
    }
    defer conn.Close() 
    inputReader := bufio.NewReader(os.Stdin)
    for {
        input, _ := inputReader.ReadString('\n') 
        inputInfo := strings.Trim(input, "\r\n")
        if strings.ToUpper(inputInfo) == "Q" { 
            return
        }
        _, err = conn.Write([]byte(inputInfo)) 
        if err != nil {
            return
        }
        buf := [512]byte{}
        n, err := conn.Read(buf[:])
        if err != nil {
            fmt.Println("recv failed, err:", err)
            return
        }
        fmt.Println(string(buf[:n]))
    }
}
```

将上面的代码编译成client或client.exe可执行文件，先启动server端再启动client端，在client端输入任意内容回车之后就能够在server端看到client端发送的数据，从而实现TCP通信。

