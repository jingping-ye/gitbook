# 结构体验证

## 1. 结构体验证 <a id="&#x7ED3;&#x6784;&#x4F53;&#x9A8C;&#x8BC1;"></a>

用gin框架的数据验证，可以不用解析数据，减少if else，会简洁许多。

```text
package main

import (
    "fmt"
    "time"

    "github.com/gin-gonic/gin"
)


type Person struct {
    
    Age      int       `form:"age" binding:"required,gt=10"`
    Name     string    `form:"name" binding:"required"`
    Birthday time.Time `form:"birthday" time_format:"2006-01-02" time_utc:"1"`
}

func main() {
    r := gin.Default()
    r.GET("/5lmh", func(c *gin.Context) {
        var person Person
        if err := c.ShouldBind(&person); err != nil {
            c.String(500, fmt.Sprint(err))
            return
        }
        c.String(200, fmt.Sprintf("%#v", person))
    })
    r.Run()
}
```

演示地址：

[http://localhost:8080/5lmh?age=11&name=枯藤&birthday=2006-01-02](http://localhost:8080/5lmh?age=11&name=%E6%9E%AF%E8%97%A4&birthday=2006-01-02)

