# Logger

## 1. Logger <a id="logger"></a>

### 1.1.1. 介绍 <a id="&#x4ECB;&#x7ECD;"></a>

在许多Go语言项目中，我们需要一个好的日志记录器能够提供下面这些功能：

* 能够将事件记录到文件中，而不是应用程序控制台。
* 日志切割-能够根据文件大小、时间或间隔等来切割日志文件。
* 支持不同的日志级别。例如INFO，DEBUG，ERROR等。
* 能够打印基本信息，如调用文件/函数名和行号，日志时间等。

### 1.1.2. 默认的Go Logger <a id="&#x9ED8;&#x8BA4;&#x7684;go-logger"></a>

在介绍Uber-go的zap包之前，让我们先看看Go语言提供的基本日志功能。Go语言提供的默认日志包是 [https://golang.org/pkg/log/](https://golang.org/pkg/log/) 。

### 1.1.3. 实现Go Logger <a id="&#x5B9E;&#x73B0;go-logger"></a>

实现一个Go语言中的日志记录器非常简单——创建一个新的日志文件，然后设置它为日志的输出位置。

```text
package main

import (
    "fmt"
    "log"
    "os"
    "time"
)

func main() {
    
    logFile, err := os.Create("./" + time.Now().Format("20060102") + ".txt")
    if err != nil {
        fmt.Println(err)
    }
    
    
    
    
    loger := log.New(logFile, "test_", log.Ldate|log.Ltime|log.Lshortfile)
    
    fmt.Println(loger.Flags())

    
    loger.SetFlags(log.Ldate | log.Ltime | log.Lshortfile)

    
    fmt.Println(loger.Prefix())

    
    loger.SetPrefix("test_")

    
    loger.Output(2, "打印一条日志信息")

    
    

    
    

    
    

    
    
    

    
    fmt.Println(log.Flags())
    
    fmt.Printf(log.Prefix())
}
```

### 1.1.4. Go Logger的优势和劣势 <a id="go-logger&#x7684;&#x4F18;&#x52BF;&#x548C;&#x52A3;&#x52BF;"></a>

#### 优势 <a id="&#x4F18;&#x52BF;"></a>

它最大的优点是使用非常简单。我们可以设置任何io.Writer作为日志记录输出并向其发送要写入的日志。

#### 劣势 <a id="&#x52A3;&#x52BF;"></a>

* 仅限基本的日志级别
  * 只有一个Print选项。不支持INFO/DEBUG等多个级别。
* 对于错误日志，它有Fatal和Panic
  * Fatal日志通过调用os.Exit\(1\)来结束程序
  * Panic日志在写入日志消息之后抛出一个panic
  * 但是它缺少一个ERROR日志级别，这个级别可以在不抛出panic或退出程序的情况下记录错误
* 缺乏日志格式化的能力——例如记录调用者的函数名和行号，格式化日期和时间格式。等等。
* 不提供日志切割的能力。

