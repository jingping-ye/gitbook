# 上传多个文件

## 1. 上传多个文件 <a id="&#x4E0A;&#x4F20;&#x591A;&#x4E2A;&#x6587;&#x4EF6;"></a>

```text

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Documenttitle>
head>
<body>
    <form action="http://localhost:8000/upload" method="post" enctype="multipart/form-data">
          上传文件:<input type="file" name="files" multiple>
          <input type="submit" value="提交">
    form>
body>
html>
```

```text
package main

import (
   "github.com/gin-gonic/gin"
   "net/http"
   "fmt"
)



func main() {
   
   
   r := gin.Default()
   
   r.MaxMultipartMemory = 8 << 20
   r.POST("/upload", func(c *gin.Context) {
      form, err := c.MultipartForm()
      if err != nil {
         c.String(http.StatusBadRequest, fmt.Sprintf("get err %s", err.Error()))
      }
      
      files := form.File["files"]
      
      for _, file := range files {
         
         if err := c.SaveUploadedFile(file, file.Filename); err != nil {
            c.String(http.StatusBadRequest, fmt.Sprintf("upload err %s", err.Error()))
            return
         }
      }
      c.String(200, fmt.Sprintf("upload ok %d files", len(files)))
   })
   
   r.Run(":8000")
}
```

效果演示：

