# 汉字转拼音

## 1. 汉字转拼音 <a id="&#x6C49;&#x5B57;&#x8F6C;&#x62FC;&#x97F3;"></a>

汉语拼音转换工具 Go 版。

### 1.1.1. 安装 <a id="&#x5B89;&#x88C5;"></a>

```text
    go get -u github.com/mozillazg/go-pinyin
```

安装CLI工具：

```text
    go get -u github.com/mozillazg/go-pinyin/cmd/pinyin
    $ pinyin 中国人
    zhōng guó rén
```

### 1.1.2. 用法 <a id="&#x7528;&#x6CD5;"></a>

```text
package main

import (
    "fmt"

    "github.com/mozillazg/go-pinyin"
)

func main() {
    hans := "中国人"

    
    a := pinyin.NewArgs()
    fmt.Println(pinyin.Pinyin(hans, a))
    

    
    a.Style = pinyin.Tone
    fmt.Println(pinyin.Pinyin(hans, a))
    

    
    a.Style = pinyin.Tone2
    fmt.Println(pinyin.Pinyin(hans, a))
    

    
    a = pinyin.NewArgs()
    a.Heteronym = true
    fmt.Println(pinyin.Pinyin(hans, a))
    
    a.Style = pinyin.Tone2
    fmt.Println(pinyin.Pinyin(hans, a))
    

    fmt.Println(pinyin.LazyPinyin(hans, pinyin.NewArgs()))
    

    fmt.Println(pinyin.Convert(hans, nil))
    

    fmt.Println(pinyin.LazyConvert(hans, nil))
    
}
```

