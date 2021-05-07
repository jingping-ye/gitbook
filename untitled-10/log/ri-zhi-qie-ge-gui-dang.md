# 日志切割归档

## 1. 日志切割归档 <a id="&#x65E5;&#x5FD7;&#x5207;&#x5272;&#x5F52;&#x6863;"></a>

### 1.1.1. 使用Lumberjack进行日志切割归档 <a id="&#x4F7F;&#x7528;lumberjack&#x8FDB;&#x884C;&#x65E5;&#x5FD7;&#x5207;&#x5272;&#x5F52;&#x6863;"></a>

这个日志程序中唯一缺少的就是日志切割归档功能。

> > > Zap本身不支持切割归档日志文件

为了添加日志切割归档功能，我们将使用第三方库[Lumberjack](https://github.com/natefinch/lumberjack)来实现。

### 1.1.2. 安装 <a id="&#x5B89;&#x88C5;"></a>

执行下面的命令安装Lumberjack

```text
    go get -u github.com/natefinch/lumberjack
```

### 1.1.3. zap logger中加入Lumberjack <a id="zap-logger&#x4E2D;&#x52A0;&#x5165;lumberjack"></a>

要在zap中加入Lumberjack支持，我们需要修改WriteSyncer代码。我们将按照下面的代码修改getLogWriter\(\)函数：

```text
func getLogWriter() zapcore.WriteSyncer {
    lumberJackLogger := &lumberjack.Logger{
        Filename:   "./test.log",
        MaxSize:    10,
        MaxBackups: 5,
        MaxAge:     30,
        Compress:   false,
    }
    return zapcore.AddSync(lumberJackLogger)
}
```

Lumberjack Logger采用以下属性作为输入:

* Filename: 日志文件的位置
* MaxSize：在进行切割之前，日志文件的最大大小（以MB为单位）
* MaxBackups：保留旧文件的最大个数
* MaxAges：保留旧文件的最大天数
* Compress：是否压缩/归档旧文件

### 1.1.4. 测试所有功能 <a id="&#x6D4B;&#x8BD5;&#x6240;&#x6709;&#x529F;&#x80FD;"></a>

最终，使用Zap/Lumberjack logger的完整示例代码如下：

```text
package main

import (
    "net/http"

    "github.com/natefinch/lumberjack"
    "go.uber.org/zap"
    "go.uber.org/zap/zapcore"
)

var sugarLogger *zap.SugaredLogger

func main() {
    InitLogger()
    defer sugarLogger.Sync()
    simpleHttpGet("www.topgoer.com")
    simpleHttpGet("http://www.topgoer.com")
}

func InitLogger() {
    writeSyncer := getLogWriter()
    encoder := getEncoder()
    core := zapcore.NewCore(encoder, writeSyncer, zapcore.DebugLevel)

    logger := zap.New(core, zap.AddCaller())
    sugarLogger = logger.Sugar()
}

func getEncoder() zapcore.Encoder {
    encoderConfig := zap.NewProductionEncoderConfig()
    encoderConfig.EncodeTime = zapcore.ISO8601TimeEncoder
    encoderConfig.EncodeLevel = zapcore.CapitalLevelEncoder
    return zapcore.NewConsoleEncoder(encoderConfig)
}

func getLogWriter() zapcore.WriteSyncer {
    lumberJackLogger := &lumberjack.Logger{
        Filename:   "./test.log",
        MaxSize:    1,
        MaxBackups: 5,
        MaxAge:     30,
        Compress:   false,
    }
    return zapcore.AddSync(lumberJackLogger)
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

