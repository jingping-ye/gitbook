# 判断字符串开头

## 1. 判断字符串开头 <a id="&#x5224;&#x65AD;&#x5B57;&#x7B26;&#x4E32;&#x5F00;&#x5934;"></a>

在这篇文章中，我们研究如何检查给定的字符串是否以某些东西开头。这通常在编程中使用，并且有不同的方法可以实现相同的目标。

将strings包与HasPrefix功能一起使用-这可能是最简单/最好的解决方案。

```text
package main

import (
    "fmt"
    "strings"
)

func main() {

    myString := "www.topgoer.com"

    // Option 1: (Recommended)
    if strings.HasPrefix(myString, "www") {
        fmt.Println("Hello to you too")
    } else {
        fmt.Println("Goodbye")
    }
}
```

