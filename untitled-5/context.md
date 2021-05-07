# Context

## 1. Context <a id="context"></a>

在 Go http包的Server中，每一个请求在都有一个对应的 goroutine 去处理。请求处理函数通常会启动额外的 goroutine 用来访问后端服务，比如数据库和RPC服务。用来处理一个请求的 goroutine 通常需要访问一些与请求特定的数据，比如终端用户的身份认证信息、验证相关的token、请求的截止时间。 当一个请求被取消或超时时，所有用来处理该请求的 goroutine 都应该迅速退出，然后系统才能释放这些 goroutine 占用的资源。

### 1.1.1. 为什么需要Context <a id="&#x4E3A;&#x4EC0;&#x4E48;&#x9700;&#x8981;context"></a>

#### 基本示例 <a id="&#x57FA;&#x672C;&#x793A;&#x4F8B;"></a>

```text
package main

import (
    "fmt"
    "sync"

    "time"
)

var wg sync.WaitGroup



func worker() {
    for {
        fmt.Println("worker")
        time.Sleep(time.Second)
    }
    
    wg.Done()
}

func main() {
    wg.Add(1)
    go worker()
    
    wg.Wait()
    fmt.Println("over")
}
```

#### 全局变量方式 <a id="&#x5168;&#x5C40;&#x53D8;&#x91CF;&#x65B9;&#x5F0F;"></a>

```text
package main

import (
    "fmt"
    "sync"

    "time"
)

var wg sync.WaitGroup
var exit bool





func worker() {
    for {
        fmt.Println("worker")
        time.Sleep(time.Second)
        if exit {
            break
        }
    }
    wg.Done()
}

func main() {
    wg.Add(1)
    go worker()
    time.Sleep(time.Second * 3) 
    exit = true                 
    wg.Wait()
    fmt.Println("over")
}
```

#### 通道方式 <a id="&#x901A;&#x9053;&#x65B9;&#x5F0F;"></a>

```text
package main

import (
    "fmt"
    "sync"

    "time"
)

var wg sync.WaitGroup




func worker(exitChan chan struct{}) {
LOOP:
    for {
        fmt.Println("worker")
        time.Sleep(time.Second)
        select {
        case <-exitChan: 
            break LOOP
        default:
        }
    }
    wg.Done()
}

func main() {
    var exitChan = make(chan struct{})
    wg.Add(1)
    go worker(exitChan)
    time.Sleep(time.Second * 3) 
    exitChan <- struct{}{}      
    close(exitChan)
    wg.Wait()
    fmt.Println("over")
}
```

#### 官方版的方案 <a id="&#x5B98;&#x65B9;&#x7248;&#x7684;&#x65B9;&#x6848;"></a>

```text
package main

import (
    "context"
    "fmt"
    "sync"

    "time"
)

var wg sync.WaitGroup

func worker(ctx context.Context) {
LOOP:
    for {
        fmt.Println("worker")
        time.Sleep(time.Second)
        select {
        case <-ctx.Done(): 
            break LOOP
        default:
        }
    }
    wg.Done()
}

func main() {
    ctx, cancel := context.WithCancel(context.Background())
    wg.Add(1)
    go worker(ctx)
    time.Sleep(time.Second * 3)
    cancel() 
    wg.Wait()
    fmt.Println("over")
}
```

当子goroutine又开启另外一个goroutine时，只需要将ctx传入即可：

```text
package main

import (
    "context"
    "fmt"
    "sync"

    "time"
)

var wg sync.WaitGroup

func worker(ctx context.Context) {
    go worker2(ctx)
LOOP:
    for {
        fmt.Println("worker")
        time.Sleep(time.Second)
        select {
        case <-ctx.Done(): 
            break LOOP
        default:
        }
    }
    wg.Done()
}

func worker2(ctx context.Context) {
LOOP:
    for {
        fmt.Println("worker2")
        time.Sleep(time.Second)
        select {
        case <-ctx.Done(): 
            break LOOP
        default:
        }
    }
}
func main() {
    ctx, cancel := context.WithCancel(context.Background())
    wg.Add(1)
    go worker(ctx)
    time.Sleep(time.Second * 3)
    cancel() 
    wg.Wait()
    fmt.Println("over")
}
```

### 1.1.2. Context初识 <a id="context&#x521D;&#x8BC6;"></a>

Go1.7加入了一个新的标准库context，它定义了Context类型，专门用来简化 对于处理单个请求的多个 goroutine 之间与请求域的数据、取消信号、截止时间等相关操作，这些操作可能涉及多个 API 调用。

对服务器传入的请求应该创建上下文，而对服务器的传出调用应该接受上下文。它们之间的函数调用链必须传递上下文，或者可以使用WithCancel、WithDeadline、WithTimeout或WithValue创建的派生上下文。当一个上下文被取消时，它派生的所有上下文也被取消。

### 1.1.3. Context接口 <a id="context&#x63A5;&#x53E3;"></a>

context.Context是一个接口，该接口定义了四个需要实现的方法。具体签名如下：

```text
type Context interface {
    Deadline() (deadline time.Time, ok bool)
    Done() <-chan struct{}
    Err() error
    Value(key interface{}) interface{}
}
```

其中：

* Deadline方法需要返回当前Context被取消的时间，也就是完成工作的截止时间（deadline）；
* Done方法需要返回一个Channel，这个Channel会在当前工作完成或者上下文被取消之后关闭，多次调用Done方法会返回同一个Channel；
* Err方法会返回当前Context结束的原因，它只会在Done返回的Channel被关闭时才会返回非空的值；
  * 如果当前Context被取消就会返回Canceled错误；
  * 如果当前Context超时就会返回DeadlineExceeded错误；
* Value方法会从Context中返回键对应的值，对于同一个上下文来说，多次调用Value 并传入相同的Key会返回相同的结果，该方法仅用于传递跨API和进程间跟请求域的数据；

### 1.1.4. Background\(\)和TODO\(\) <a id="background&#x548C;todo"></a>

Go内置两个函数：Background\(\)和TODO\(\)，这两个函数分别返回一个实现了Context接口的background和todo。我们代码中最开始都是以这两个内置的上下文对象作为最顶层的partent context，衍生出更多的子上下文对象。

Background\(\)主要用于main函数、初始化以及测试代码中，作为Context这个树结构的最顶层的Context，也就是根Context。

TODO\(\)，它目前还不知道具体的使用场景，如果我们不知道该使用什么Context的时候，可以使用这个。

background和todo本质上都是emptyCtx结构体类型，是一个不可取消，没有设置截止时间，没有携带任何值的Context。

### 1.1.5. With系列函数 <a id="with&#x7CFB;&#x5217;&#x51FD;&#x6570;"></a>

此外，context包中还定义了四个With系列函数。

### 1.1.6. WithCancel <a id="withcancel"></a>

WithCancel的函数签名如下：

```text
    func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
```

WithCancel返回带有新Done通道的父节点的副本。当调用返回的cancel函数或当关闭父上下文的Done通道时，将关闭返回上下文的Done通道，无论先发生什么情况。

取消此上下文将释放与其关联的资源，因此代码应该在此上下文中运行的操作完成后立即调用cancel。

```text
func gen(ctx context.Context) <-chan int {
        dst := make(chan int)
        n := 1
        go func() {
            for {
                select {
                case <-ctx.Done():
                    return 
                case dst <- n:
                    n++
                }
            }
        }()
        return dst
    }
func main() {
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel() 

    for n := range gen(ctx) {
        fmt.Println(n)
        if n == 5 {
            break
        }
    }
}
```

上面的示例代码中，gen函数在单独的goroutine中生成整数并将它们发送到返回的通道。 gen的调用者在使用生成的整数之后需要取消上下文，以免gen启动的内部goroutine发生泄漏。

### 1.1.7. WithDeadline <a id="withdeadline"></a>

WithDeadline的函数签名如下：

```text
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
```

返回父上下文的副本，并将deadline调整为不迟于d。如果父上下文的deadline已经早于d，则WithDeadline\(parent, d\)在语义上等同于父上下文。当截止日过期时，当调用返回的cancel函数时，或者当父上下文的Done通道关闭时，返回上下文的Done通道将被关闭，以最先发生的情况为准。

取消此上下文将释放与其关联的资源，因此代码应该在此上下文中运行的操作完成后立即调用cancel。

```text
func main() {
    d := time.Now().Add(50 * time.Millisecond)
    ctx, cancel := context.WithDeadline(context.Background(), d)

    
    
    defer cancel()

    select {
    case <-time.After(1 * time.Second):
        fmt.Println("overslept")
    case <-ctx.Done():
        fmt.Println(ctx.Err())
    }
}
```

上面的代码中，定义了一个50毫秒之后过期的deadline，然后我们调用context.WithDeadline\(context.Background\(\), d\)得到一个上下文（ctx）和一个取消函数（cancel），然后使用一个select让主程序陷入等待：等待1秒后打印overslept退出或者等待ctx过期后退出。 因为ctx50秒后就过期，所以ctx.Done\(\)会先接收到值，上面的代码会打印ctx.Err\(\)取消原因。

### 1.1.8. WithTimeout <a id="withtimeout"></a>

WithTimeout的函数签名如下：

```text
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
```

WithTimeout返回WithDeadline\(parent, time.Now\(\).Add\(timeout\)\)。

取消此上下文将释放与其相关的资源，因此代码应该在此上下文中运行的操作完成后立即调用cancel，通常用于数据库或者网络连接的超时控制。具体示例如下：

```text
package main

import (
    "context"
    "fmt"
    "sync"

    "time"
)



var wg sync.WaitGroup

func worker(ctx context.Context) {
LOOP:
    for {
        fmt.Println("db connecting ...")
        time.Sleep(time.Millisecond * 10) 
        select {
        case <-ctx.Done(): 
            break LOOP
        default:
        }
    }
    fmt.Println("worker done!")
    wg.Done()
}

func main() {
    
    ctx, cancel := context.WithTimeout(context.Background(), time.Millisecond*50)
    wg.Add(1)
    go worker(ctx)
    time.Sleep(time.Second * 5)
    cancel() 
    wg.Wait()
    fmt.Println("over")
}
```

### 1.1.9. WithValue <a id="withvalue"></a>

WithValue函数能够将请求作用域的数据与 Context 对象建立关系。声明如下：

```text
    func WithValue(parent Context, key, val interface{}) Context
```

WithValue返回父节点的副本，其中与key关联的值为val。

仅对API和进程间传递请求域的数据使用上下文值，而不是使用它来传递可选参数给函数。

所提供的键必须是可比较的，并且不应该是string类型或任何其他内置类型，以避免使用上下文在包之间发生冲突。WithValue的用户应该为键定义自己的类型。为了避免在分配给interface{}时进行分配，上下文键通常具有具体类型struct{}。或者，导出的上下文关键变量的静态类型应该是指针或接口。

```text
package main

import (
    "context"
    "fmt"
    "sync"

    "time"
)



type TraceCode string

var wg sync.WaitGroup

func worker(ctx context.Context) {
    key := TraceCode("TRACE_CODE")
    traceCode, ok := ctx.Value(key).(string) 
    if !ok {
        fmt.Println("invalid trace code")
    }
LOOP:
    for {
        fmt.Printf("worker, trace code:%s\n", traceCode)
        time.Sleep(time.Millisecond * 10) 
        select {
        case <-ctx.Done(): 
            break LOOP
        default:
        }
    }
    fmt.Println("worker done!")
    wg.Done()
}

func main() {
    
    ctx, cancel := context.WithTimeout(context.Background(), time.Millisecond*50)
    
    ctx = context.WithValue(ctx, TraceCode("TRACE_CODE"), "12512312234")
    wg.Add(1)
    go worker(ctx)
    time.Sleep(time.Second * 5)
    cancel() 
    wg.Wait()
    fmt.Println("over")
}
```

### 1.1.10. 使用Context的注意事项 <a id="&#x4F7F;&#x7528;context&#x7684;&#x6CE8;&#x610F;&#x4E8B;&#x9879;"></a>

* 推荐以参数的方式显示传递Context
* 以Context作为参数的函数方法，应该把Context作为第一个参数。
* 给一个函数方法传递Context的时候，不要传递nil，如果不知道传递什么，就使用context.TODO\(\)
* Context的Value相关方法应该传递请求域的必要数据，不应该用于传递可选参数
* Context是线程安全的，可以放心的在多个goroutine中传递

### 1.1.11. 客户端超时取消示例 <a id="&#x5BA2;&#x6237;&#x7AEF;&#x8D85;&#x65F6;&#x53D6;&#x6D88;&#x793A;&#x4F8B;"></a>

调用服务端API时如何在客户端实现超时控制？

### 1.1.12. server端 <a id="server&#x7AEF;"></a>

```text

package main

import (
    "fmt"
    "math/rand"
    "net/http"

    "time"
)



func indexHandler(w http.ResponseWriter, r *http.Request) {
    number := rand.Intn(2)
    if number == 0 {
        time.Sleep(time.Second * 10) 
        fmt.Fprintf(w, "slow response")
        return
    }
    fmt.Fprint(w, "quick response")
}

func main() {
    http.HandleFunc("/", indexHandler)
    err := http.ListenAndServe(":8000", nil)
    if err != nil {
        panic(err)
    }
}
```

client端

```text

package main

import (
    "context"
    "fmt"
    "io/ioutil"
    "net/http"
    "sync"
    "time"
)



type respData struct {
    resp *http.Response
    err  error
}

func doCall(ctx context.Context) {
    transport := http.Transport{
       
       
       DisableKeepAlives: true,     }
    client := http.Client{
        Transport: &transport,
    }

    respChan := make(chan *respData, 1)
    req, err := http.NewRequest("GET", "http://127.0.0.1:8000/", nil)
    if err != nil {
        fmt.Printf("new requestg failed, err:%v\n", err)
        return
    }
    req = req.WithContext(ctx) 
    var wg sync.WaitGroup
    wg.Add(1)
    defer wg.Wait()
    go func() {
        resp, err := client.Do(req)
        fmt.Printf("client.do resp:%v, err:%v\n", resp, err)
        rd := &respData{
            resp: resp,
            err:  err,
        }
        respChan <- rd
        wg.Done()
    }()

    select {
    case <-ctx.Done():
        
        fmt.Println("call api timeout")
    case result := <-respChan:
        fmt.Println("call server api success")
        if result.err != nil {
            fmt.Printf("call server api failed, err:%v\n", result.err)
            return
        }
        defer result.resp.Body.Close()
        data, _ := ioutil.ReadAll(result.resp.Body)
        fmt.Printf("resp:%v\n", string(data))
    }
}

func main() {
    
    ctx, cancel := context.WithTimeout(context.Background(), time.Millisecond*100)
    defer cancel() 
    doCall(ctx)
}
```

