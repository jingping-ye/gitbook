# Zap Logger

## 1. Zap Logger <a id="zap-logger"></a>

### 1.1.1. Uber-go Zap <a id="uber-go-zap"></a>

[Zap](https://github.com/uber-go/zap)是非常快的、结构化的，分日志级别的Go日志库。

### 1.1.2. 为什么选择Uber-go zap <a id="&#x4E3A;&#x4EC0;&#x4E48;&#x9009;&#x62E9;uber-go-zap"></a>

* 它同时提供了结构化日志记录和printf风格的日志记录
* 它非常的快

根据Uber-go Zap的文档，它的性能比类似的结构化日志包更好——也比标准库更快。 以下是Zap发布的基准测试信息

记录一条消息和10个字段:

记录一个静态字符串，没有任何上下文或printf风格的模板：

### 1.1.3. 安装 <a id="&#x5B89;&#x88C5;"></a>

运行下面的命令安装zap

```text
    go get -u go.uber.org/zap
```

### 1.1.4. 配置Zap Logger <a id="&#x914D;&#x7F6E;zap-logger"></a>

Zap提供了两种类型的日志记录器—Sugared Logger和Logger。

在性能很好但不是很关键的上下文中，使用SugaredLogger。它比其他结构化日志记录包快4-10倍，并且支持结构化和printf风格的日志记录。

在每一微秒和每一次内存分配都很重要的上下文中，使用Logger。它甚至比SugaredLogger更快，内存分配次数也更少，但它只支持强类型的结构化日志记录。

### 1.1.5. Logger <a id="logger"></a>

* 通过调用zap.NewProduction\(\)/zap.NewDevelopment\(\)或者zap.Example\(\)创建一个Logger。
* 上面的每一个函数都将创建一个logger。唯一的区别在于它将记录的信息不同。例如production logger默认记录调用函数信息、日期和时间等。
* 通过Logger调用Info/Error等。
* 默认情况下日志都会打印到应用程序的console界面。

```text
package main

import (
    "net/http"

    "go.uber.org/zap"
)

var logger *zap.Logger

func main() {
    InitLogger()
    defer logger.Sync()
    simpleHttpGet("www.5lmh.com")
    simpleHttpGet("http://www.google.com")
}

func InitLogger() {
    logger, _ = zap.NewProduction()
}

func simpleHttpGet(url string) {
    resp, err := http.Get(url)
    if err != nil {
        logger.Error(
            "Error fetching url..",
            zap.String("url", url),
            zap.Error(err))
    } else {
        logger.Info("Success..",
            zap.String("statusCode", resp.Status),
            zap.String("url", url))
        resp.Body.Close()
    }
}
```

在上面的代码中，我们首先创建了一个Logger，然后使用Info/ Error等Logger方法记录消息。

日志记录器方法的语法是这样的：

```text
    func (log *Logger) MethodXXX(msg string, fields ...Field)
```

其中MethodXXX是一个可变参数函数，可以是Info / Error/ Debug / Panic等。每个方法都接受一个消息字符串和任意数量的zapcore.Field场参数。

每个zapcore.Field其实就是一组键值对参数。

我们执行上面的代码会得到如下输出结果：

```text
{"level":"error","ts":1573180648.858149,"caller":"ce2/main.go:25","msg":"Error fetching url..","url":"www.5lmh.com","error":"Get www.5lmh.com: unsupported protocol scheme \"\"","stacktrace":"main.simpleHttpGet\n\te:/goproject/src/github.com/student/log/ce2/main.go:25\nmain.main\n\te:/goproject/src/github.com/student/log/ce2/main.go:14\nruntime.main\n\tE:/go/src/runtime/proc.go:200"}

{"level":"error","ts":1573180669.9273467,"caller":"ce2/main.go:25","msg":"Error fetching url..","url":"http://www.google.com","error":"Get http://www.google.com: dial tcp 31.13.72.54:80: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.","stacktrace":"main.simpleHttpGet\n\te:/goproject/src/github.com/student/log/ce2/main.go:25\nmain.main\n\te:/goproject/src/github.com/student/log/ce2/main.go:15\nruntime.main\n\tE:/go/src/runtime/proc.go:200"}
```

### 1.1.6. Sugared Logger <a id="sugared-logger"></a>

现在让我们使用Sugared Logger来实现相同的功能。

* 大部分的实现基本都相同。
* 惟一的区别是，我们通过调用主logger的. Sugar\(\)方法来获取一个SugaredLogger。
* 然后使用SugaredLogger以printf格式记录语句

下面是修改过后使用SugaredLogger代替Logger的代码：

```text
package main

import (
    "net/http"

    "go.uber.org/zap"
)

var sugarLogger *zap.SugaredLogger

func main() {
    InitLogger()
    defer sugarLogger.Sync()
    simpleHttpGet("www.5lmh.com")
    simpleHttpGet("http://www.google.com")
}

func InitLogger() {
    logger, _ := zap.NewProduction()
    sugarLogger = logger.Sugar()
}

func simpleHttpGet(url string) {
    sugarLogger.Debugf("Trying to hit GET request for %s", url)
    resp, err := http.Get(url)
    if err != nil {
        sugarLogger.Errorf("Error fetching URL %s : Error = %s", url, err)
    } else {
        sugarLogger.Infof("Success! statusCode = %s for URL %s", resp.Status, url)
        resp.Body.Close()
    }
}
```

```text
{"level":"error","ts":1573180998.3522997,"caller":"ce3/main.go:27","msg":"Error fetching URL www.5lmh.com : Error = Get www.5lmh.com: unsupported protocol scheme \"\"","stacktrace":"main.simpleHttpGet\n\te:/goproject/src/github.com/student/log/ce3/main.go:27\nmain.main\n\te:/goproject/src/github.com/student/log/ce3/main.go:14\nruntime.main\n\tE:/go/src/runtime/proc.go:200"}

{"level":"error","ts":1573181019.3677258,"caller":"ce3/main.go:27","msg":"Error fetching URL http://www.google.com : Error = Get http://www.google.com: dial tcp 67.228.37.26:80: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.","stacktrace":"main.simpleHttpGet\n\te:/goproject/src/github.com/student/log/ce3/main.go:27\nmain.main\n\te:/goproject/src/github.com/student/log/ce3/main.go:15\nruntime.main\n\tE:/go/src/runtime/proc.go:200"}
```

你应该注意到的了，到目前为止这两个logger都打印输出JSON结构格式。

在本博客的后面部分，我们将更详细地讨论SugaredLogger，并了解如何进一步配置它。

### 1.1.7. 定制logger <a id="&#x5B9A;&#x5236;logger"></a>

#### 将日志写入文件而不是终端 <a id="&#x5C06;&#x65E5;&#x5FD7;&#x5199;&#x5165;&#x6587;&#x4EF6;&#x800C;&#x4E0D;&#x662F;&#x7EC8;&#x7AEF;"></a>

* 我们要做的第一个更改是把日志写入文件，而不是打印到应用程序控制台。

```text
    func New(core zapcore.Core, options ...Option) *Logger
```

zapcore.Core需要三个配置——Encoder，WriteSyncer，LogLevel。

1.Encoder:编码器\(如何写入日志\)。我们将使用开箱即用的NewJSONEncoder\(\)，并使用预先设置的ProductionEncoderConfig\(\)。

```text
   zapcore.NewJSONEncoder(zap.NewProductionEncoderConfig())
```

2.WriterSyncer ：指定日志将写到哪里去。我们使用zapcore.AddSync\(\)函数并且将打开的文件句柄传进去。

```text
   file, _ := os.Create("./test.log")
   writeSyncer := zapcore.AddSync(file)
```

3.Log Level：哪种级别的日志将被写入。

我们将修改上述部分中的Logger代码，并重写InitLogger\(\)方法。其余方法—main\(\) /SimpleHttpGet\(\)保持不变。

```text
package main

import (
    "net/http"
    "os"

    "go.uber.org/zap"
    "go.uber.org/zap/zapcore"
)

var sugarLogger *zap.SugaredLogger

func main() {
    InitLogger()
    defer sugarLogger.Sync()
    simpleHttpGet("www.5lmh.com")
    simpleHttpGet("http://www.google.com")
}

func InitLogger() {
    writeSyncer := getLogWriter()
    encoder := getEncoder()
    core := zapcore.NewCore(encoder, writeSyncer, zapcore.DebugLevel)

    logger := zap.New(core)
    sugarLogger = logger.Sugar()
}

func getEncoder() zapcore.Encoder {
    return zapcore.NewJSONEncoder(zap.NewProductionEncoderConfig())
}

func getLogWriter() zapcore.WriteSyncer {
    
    file, _ := os.Create("./test.log")
    return zapcore.AddSync(file)
}

func simpleHttpGet(url string) {
    sugarLogger.Debugf("Trying to hit GET request for %s", url)
    resp, err := http.Get(url)
    if err != nil {
        sugarLogger.Errorf("Error fetching URL %s : Error = %s", url, err)
    } else {
        sugarLogger.Infof("Success! statusCode = %s for URL %s", resp.Status, url)
        resp.Body.Close()
    }
}
```

当使用这些修改过的logger配置调用上述部分的main\(\)函数时，以下输出将打印在文件——test.log中。

```text
{"level":"debug","ts":1573181732.5292294,"msg":"Trying to hit GET request for www.5lmh.com"}
{"level":"error","ts":1573181732.5292294,"msg":"Error fetching URL www.5lmh.com : Error = Get www.5lmh.com: unsupported protocol scheme \"\""}
{"level":"debug","ts":1573181732.5292294,"msg":"Trying to hit GET request for http://www.google.com"}
{"level":"error","ts":1573181753.564804,"msg":"Error fetching URL http://www.google.com : Error = Get http://www.google.com: dial tcp 66.220.149.32:80: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond."}
```

#### 将JSON Encoder更改为普通的Log Encoder <a id="&#x5C06;json-encoder&#x66F4;&#x6539;&#x4E3A;&#x666E;&#x901A;&#x7684;log-encoder"></a>

现在，我们希望将编码器从JSON Encoder更改为普通Encoder。为此，我们需要将NewJSONEncoder\(\)更改为NewConsoleEncoder\(\)。

```text
    return zapcore.NewConsoleEncoder(zap.NewProductionEncoderConfig())
```

当使用这些修改过的logger配置调用上述部分的main\(\)函数时，以下输出将打印在文件——test.log中。

```text
1.573181811861697e+09    debug    Trying to hit GET request for www.5lmh.com
1.5731818118626883e+09    error    Error fetching URL www.5lmh.com : Error = Get www.5lmh.com: unsupported protocol scheme ""
1.5731818118626883e+09    debug    Trying to hit GET request for http://www.google.com
1.5731818329012108e+09    error    Error fetching URL http://www.google.com : Error = Get http://www.google.com: dial tcp 216.58.200.228:80: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.
```

#### 更改时间编码并添加调用者详细信息 <a id="&#x66F4;&#x6539;&#x65F6;&#x95F4;&#x7F16;&#x7801;&#x5E76;&#x6DFB;&#x52A0;&#x8C03;&#x7528;&#x8005;&#x8BE6;&#x7EC6;&#x4FE1;&#x606F;"></a>

鉴于我们对配置所做的更改，有下面两个问题：

* 时间是以非人类可读的方式展示，例如1.572161051846623e+09
* 调用方函数的详细信息没有显示在日志中 我们要做的第一件事是覆盖默认的ProductionConfig\(\)，并进行以下更改:

修改时间编码器

* 在日志文件中使用大写字母记录日志级别

```text
func getEncoder() zapcore.Encoder {
    encoderConfig := zap.NewProductionEncoderConfig()
    encoderConfig.EncodeTime = zapcore.ISO8601TimeEncoder
    encoderConfig.EncodeLevel = zapcore.CapitalLevelEncoder
    return zapcore.NewConsoleEncoder(encoderConfig)
}
```

接下来，我们将修改zap logger代码，添加将调用函数信息记录到日志中的功能。为此，我们将在zap.New\(..\)函数中添加一个Option。

```text
    logger := zap.New(core, zap.AddCaller())
```

