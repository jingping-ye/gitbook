# 上传单个文件

## 1. 上传单个文件 <a id="&#x4E0A;&#x4F20;&#x5355;&#x4E2A;&#x6587;&#x4EF6;"></a>

* multipart/form-data格式用于文件上传
* gin文件上传与原生的net/http方法类似，不同在于gin把原生的request封装到c.Request中

```text

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Documenttitle>
head>
<body>
    <form action="http://localhost:8080/upload" method="post" enctype="multipart/form-data">
          上传文件:<input type="file" name="file" >
          <input type="submit" value="提交">
    form>
body>
html>
```

```text
package main

import (
    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    
    r.MaxMultipartMemory = 8 << 20
    r.POST("/upload", func(c *gin.Context) {
        file, err := c.FormFile("file")
        if err != nil {
            c.String(500, "上传图片出错")
        }
        
        c.SaveUploadedFile(file, file.Filename)
        c.String(http.StatusOK, file.Filename)
    })
    r.Run()
}
```

效果演示：

### 1.1.1. 上传特定文件 <a id="&#x4E0A;&#x4F20;&#x7279;&#x5B9A;&#x6587;&#x4EF6;"></a>

有的用户上传文件需要限制上传文件的类型以及上传文件的大小，但是gin框架暂时没有这些函数\(也有可能是我没找到\)，因此基于原生的函数写法自己写了一个可以限制大小以及文件类型的上传函数

```text
package main

import (
    "fmt"
    "log"
    "net/http"

    "github.com/gin-gonic/gin"
)

func main() {
    r := gin.Default()
    r.POST("/upload", func(c *gin.Context) {
        _, headers, err := c.Request.FormFile("file")
        if err != nil {
            log.Printf("Error when try to get file: %v", err)
        }
        
        if headers.Size > 1024*1024*2 {
            fmt.Println("文件太大了")
            return
        }
        
        if headers.Header.Get("Content-Type") != "image/png" {
            fmt.Println("只允许上传png图片")
            return
        }
        c.SaveUploadedFile(headers, "./video/"+headers.Filename)
        c.String(http.StatusOK, headers.Filename)
    })
    r.Run()
}
```

