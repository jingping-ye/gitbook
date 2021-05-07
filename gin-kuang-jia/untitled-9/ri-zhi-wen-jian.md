# 日志文件

* [**1.** 日志文件](ri-zhi-wen-jian.md#日志文件)

## 1. 日志文件 <a id="&#x65E5;&#x5FD7;&#x6587;&#x4EF6;"></a>

```text
package main

import (
    "io"
    "os"

    "github.com/gin-gonic/gin"
)

func main() {
    gin.DisableConsoleColor()

    // Logging to a file.
    f, _ := os.Create("gin.log")
    gin.DefaultWriter = io.MultiWriter(f)

    // 如果需要同时将日志写入文件和控制台，请使用以下代码。
    // gin.DefaultWriter = io.MultiWriter(f, os.Stdout)
    r := gin.Default()
    r.GET("/ping", func(c *gin.Context) {
        c.String(200, "pong")
    })
    r.Run()
}
```

效果演示：

