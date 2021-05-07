# 选择排序算法

## 1. 选择排序算法 <a id="&#x9009;&#x62E9;&#x6392;&#x5E8F;&#x7B97;&#x6CD5;"></a>

算法描述：从未排序数据中选择最大或者最小的值和当前值交换 **O\(n^2\)**.

### 算法步骤 <a id="&#x7B97;&#x6CD5;&#x6B65;&#x9AA4;"></a>

* 选择一个数当最小值或者最大值，进行比较然后交换
* 循环向后查进行

```text
package sort

import "fmt"


func SelectMax(arr []int) int {
    length := len(arr)
    if length <= 1 {
        return arr[0]
    }
    max := arr[0]
    for i := 1; i < length; i++ {
        if arr[i] > max {
            max = arr[i]
        }
    }
    return max
}


func SelectSort(arr []int) []int {
    length := len(arr)
    if length <= 1 {
        return arr
    }
    for i := 1; i < length; i++ {
        min := i
        for j := i + 1; j < length; j++ {
            if arr[min] > arr[j] {
                min = j
            }
        }
        if i != min {
            arr[i], arr[min] = arr[min], arr[i]
        }
    }
    return arr
}


func main() {
    arr := []int{1, 9, 10, 30, 2, 5, 45, 8, 63, 234, 12}
    max := SelectMax(arr)
    selectsort := SelectSort(arr)
    fmt.Println(max)
    fmt.Println(selectsort)
}
```

