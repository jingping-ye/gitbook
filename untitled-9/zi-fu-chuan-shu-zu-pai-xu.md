# 字符串数组排序

## 1. 字符串数组排序 <a id="&#x5B57;&#x7B26;&#x4E32;&#x6570;&#x7EC4;&#x6392;&#x5E8F;"></a>

当数字存储为字符串时，这是编程中的一个问题-因为作为字符串，当按字母顺序排序时，它们将从头到尾按每个数字排列。例如，在处理带编号的文件名时，您可能会遇到此问题，这些文件名将被视为字符串，但是我们可能需要按顺序处理它们。

在下面的示例中，我们同时使用sort和strconv包对数组进行排序，并将字符串转换为比较函数中的数字。转换为整数可以为我们提供可靠的排序。

```text
package main 

import (
    "fmt"
    "sort"
    "strconv"
)

func main() {

    myList := []string{"1", "10", "11", "2", "3", "4", "5", "6", "7", "8", "9"}

    fmt.Printf("Before: %v\n", myList)

    
    sort.Slice(myList, func(i, j int) bool {
        numA, _ := strconv.Atoi(myList[i])
        numB, _ := strconv.Atoi(myList[j])
        return numA < numB
    })

    fmt.Printf("After: %v\n", myList)
}
```

