# 简介 · Go语言中文文档

## 1. 简介 <a id="&#x7B80;&#x4ECB;"></a>

### 1.1. 介绍 <a id="&#x4ECB;&#x7ECD;"></a>

* Gin是一个golang的微框架，封装比较优雅，API友好，源码注释比较明确，具有快速灵活，容错方便等特点
* 对于golang而言，web框架的依赖要远比Python，Java之类的要小。自身的`net/http`足够简单，性能也非常不错
* 借助框架开发，不仅可以省去很多常用的封装带来的时间，也有助于团队的编码风格和形成规范

### 1.2. 安装 <a id="&#x5B89;&#x88C5;"></a>

要安装Gin软件包，您需要安装Go并首先设置Go工作区。

1.首先需要安装Go（需要1.10+版本），然后可以使用下面的Go命令安装Gin。

> go get -u github.com/gin-gonic/gin

2.将其导入您的代码中：

> import "github.com/gin-gonic/gin"

3.（可选）导入net/http。例如，如果使用常量，则需要这样做http.StatusOK。

> import "net/http"

### 1.3. hello word <a id="hello-word"></a>

```text
package main

import (
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    
   r := gin.Default()
   
   
   r.GET("/", func(c *gin.Context) {
      c.String(http.StatusOK, "hello World!")
   })
   
   
   r.Run(":8000")
}
```

