# 快速排序算法

## 1. 快速排序算法 <a id="&#x5FEB;&#x901F;&#x6392;&#x5E8F;&#x7B97;&#x6CD5;"></a>

算法描述：是对插入算法的一种优化，利用对问题的二分化，实现递归完成快速排序 ，在所有算法中二分化是最常用的方式，将问题尽量的分成两种情况加以分析， 最终以形成类似树的方式加以利用，因为在比较模型中的算法中，最快的排序时间 负载度为 **O\(nlgn\)**.

### 算法步骤 <a id="&#x7B97;&#x6CD5;&#x6B65;&#x9AA4;"></a>

* 将数据根据一个值按照大小分成左右两边，左边小于此值，右边大于
* 将两边数据进行递归调用步骤1
* 将所有数据合并

```text
package sort

import "fmt"

func QuickSort(arr []int) []int {
    if len(arr) <= 1 {
        return arr
    }
    splitdata := arr[0]          
    low := make([]int, 0, 0)     
    hight := make([]int, 0, 0)   
    mid := make([]int, 0, 0)     
    mid = append(mid, splitdata) 
    for i := 1; i < len(arr); i++ {
        if arr[i] < splitdata {
            low = append(low, arr[i])
        } else if arr[i] > splitdata {
            hight = append(hight, arr[i])
        } else {
            mid = append(mid, arr[i])
        }
    }
    low, hight = QuickSort(low), QuickSort(hight)
    myarr := append(append(low, mid...), hight...)
    return myarr
}


func main() {
    arr := []int{1, 9, 10, 30, 2, 5, 45, 8, 63, 234, 12}
    fmt.Println(QuickSort(arr))
}
```

