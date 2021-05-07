# 检查切片中是否存在元素

## 1. 检查切片中是否存在元素 <a id="&#x68C0;&#x67E5;&#x5207;&#x7247;&#x4E2D;&#x662F;&#x5426;&#x5B58;&#x5728;&#x5143;&#x7D20;"></a>

Go 的标准库中没有find或in\_array函数。它们很容易创建。在此示例中，我们创建了一个Find\(\)函数，用于在字符串切片中搜索所需的元素。

```text
package main

import (
    "fmt"
)

func main() {

    items := []string{"A", "1", "B", "2", "C", "3"}

    
    _, found := Find(items, "topgoer.com")
    if !found {
        fmt.Println("Value not found in slice")
    }

    
    k, found := Find(items, "B")
    if !found {
        fmt.Println("Value not found in slice")
    }
    fmt.Printf("B found at key: %d\n", k)
}


func Find(slice []string, val string) (int, bool) {
    for i, item := range slice {
        if item == val {
            return i, true
        }
    }
    return -1, false
}
```

