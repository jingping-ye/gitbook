# markdown解析库

## 1. markdown解析库 <a id="markdown&#x89E3;&#x6790;&#x5E93;"></a>

Blackfriday是在Go中实现的Markdown处理器。您可以安全地输入用户提供的数据，速度快，支持通用扩展（表，智能标点符号替换等），并且对于所有utf-8（unicode）都是安全的输入。

当前支持HTML输出以及Smartypants扩展。

### 1.1.1. 使用 <a id="&#x4F7F;&#x7528;"></a>

首先当然要引入：

```text
    import github.com/russross/blackfriday
```

然后

```text
    output := blackfriday.MarkdownBasic(input)
```

这里input是`[]byte`类型，可以将markdown类型的字符串强转为`[]byte`，即`input = []byte(string)`

如果想过滤不信任的内容，使用以下方法：

代码：

```text
package main

import (
    "fmt"

    "github.com/microcosm-cc/bluemonday"
    "github.com/russross/blackfriday"
)

func main() {
    input := []byte("### topgoer.com是个不错的go文档网站")
    unsafe := blackfriday.MarkdownCommon(input)
    html := bluemonday.UGCPolicy().SanitizeBytes(unsafe)
    fmt.Println(string(html))
}
```

基本上就这些操作

我的使用方法是在添加新文章时，将表单提交的数据直接通过上面的方法转换后，将markdown和转换后的内容都存储到数据库中

不过我在前端渲染时，又出现了问题，就是转换后的内容中的html标签会直接显示在网页上，为避免这种状况，我使用了自定义模板函数

```text
    // 定义模板函数
    func unescaped(x string) interface{} { return template.HTML(x)}

    // 注册模板函数
    t := template.New("post.html")
    t = t.Funcs(template.FuncMap{"unescaped": unescaped})
    t, _ = t.ParseFiles("templates/post.html")
    t.Execute(w, post)

    // 使用模板函数

    {{ .Content|unescaped }}
```

