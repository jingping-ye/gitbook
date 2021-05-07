# 解决中文乱码

## 1. 解决中文乱码 <a id="&#x89E3;&#x51B3;&#x4E2D;&#x6587;&#x4E71;&#x7801;"></a>

Go中实现的字符集转换库。

Mahonia是在Go中实现的字符集转换库。所有数据都被编译到可执行文件中；它不需要任何外部数据文件。

根据[http://code.google.com/p/mahonia/](http://code.google.com/p/mahonia/)

### 1.1.1. 安装 <a id="&#x5B89;&#x88C5;"></a>

```text
    go get github.com/axgle/mahonia
```

### 1.1.2. 小案例 <a id="&#x5C0F;&#x6848;&#x4F8B;"></a>

```text
package main

import "fmt"
import "github.com/axgle/mahonia"

func main() {
    enc := mahonia.NewEncoder("gbk")
    //converts a  string from UTF-8 to gbk encoding.
    fmt.Println(enc.ConvertString("hello,世界"))
}
```

