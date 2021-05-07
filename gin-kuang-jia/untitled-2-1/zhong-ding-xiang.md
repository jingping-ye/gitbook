# 重定向

* [**1.** 重定向](zhong-ding-xiang.md#重定向)

## 1. 重定向 <a id="&#x91CD;&#x5B9A;&#x5411;"></a>

```text
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.GET("/index", func(c *gin.Context) {
        c.Redirect(http.StatusMovedPermanently, "http://www.5lmh.com")
    })
    r.Run()
}
```

