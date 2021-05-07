# 页面静态化

## 1. 页面静态化 <a id="&#x9875;&#x9762;&#x9759;&#x6001;&#x5316;"></a>

### 1.1.1. 什么是页面静态化： <a id="&#x4EC0;&#x4E48;&#x662F;&#x9875;&#x9762;&#x9759;&#x6001;&#x5316;&#xFF1A;"></a>

简 单的说，我们如果访问一个链接 ,服务器对应的模块会处理这个请求，转到对应的go方法，最后生成我们想要看到的数据。这其中的缺点是显而易见的：因为每次请求服务器都会进行处理，如 果有太多的高并发请求，那么就会加重应用服务器的压力，弄不好就把服务器 搞down 掉了。那么如何去避免呢？如果我们把请求后的结果保存成一个 html 文件，然后每次用户都去访问 ,这样应用服务器的压力不就减少了？

那么静态页面从哪里来呢？总不能让我们每个页面都手动处理吧？这里就牵涉到我们要讲解的内容了，静态页面生成方案… 我们需要的是自动的生成静态页面，当用户访问 ,会自动生成html文件 ,然后显示给用户。

**为了路由方便我用的gin框架但是不管用在什么框架上面都是一样的**

项目目录：

project

-tem

--index.html

-main.go

main.go文件代码：

```text
package main

import (
    "fmt"
    "net/http"
    "os"
    "path/filepath"
    "text/template"

    "github.com/gin-gonic/gin"
)

type Product struct {
    Id   int64  `json:"id"` 
    Name string `json:"name"`
}


var allproduct []*Product = []*Product{
    {1, "苹果手机"},
    {2, "苹果电脑"},
    {3, "苹果耳机"},
}
var (
    
    htmlOutPath = "./tem"
    
    templatePath = "./tem"
)

func main() {
    r := gin.Default()
    r.LoadHTMLGlob("tem/*")
    r.GET("/index", func(c *gin.Context) {
        GetGenerateHtml()
        c.HTML(http.StatusOK, "index.html", gin.H{"allproduct": allproduct})
    })
    r.GET("/index2", func(c *gin.Context) {
        c.HTML(http.StatusOK, "htmlindex.html", gin.H{})
    })
    r.Run()
}


func GetGenerateHtml() {
    //1.获取模版
    contenstTmp, err := template.ParseFiles(filepath.Join(templatePath, "index.html"))
    if err != nil {
        fmt.Println("获取模版文件失败")
    }
    //2.获取html生成路径
    fileName := filepath.Join(htmlOutPath, "htmlindex.html")
    //4.生成静态文件
    generateStaticHtml(contenstTmp, fileName, gin.H{"allproduct": allproduct})
}

//生成静态文件
func generateStaticHtml(template *template.Template, fileName string, product map[string]interface{}) {
    //1.判断静态文件是否存在
    if exist(fileName) {
        err := os.Remove(fileName)
        if err != nil {
            fmt.Println("移除文件失败")
        }
    }
    //2.生成静态文件
    file, err := os.OpenFile(fileName, os.O_CREATE|os.O_WRONLY, os.ModePerm)
    if err != nil {
        fmt.Println("打开文件失败")
    }
    defer file.Close()
    template.Execute(file, &product)
}

//判断文件是否存在
func exist(fileName string) bool {
    _, err := os.Stat(fileName)
    return err == nil || os.IsExist(err)
}
```

tem/index.html文件代码：

```text
{{define "index.html"}}

<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>商品列表页title>
head>
<tbody>

<div><a href="#">商品列表页a>div>
<table border="1">
    <thead>
    <tr>
        <th>IDth>
        <th>商品名称th>
    tr>
    thead>
    <tbody>
    {{range .allproduct}}
    <tr>
        <td>{{.Id}}td>
        <td>{{.Name}}td>
    tr>
    {{end}}
    tbody>
table>
html>
{{end}}
```

