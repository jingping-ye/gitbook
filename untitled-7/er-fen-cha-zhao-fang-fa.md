# 二分查找方法

## 1. 二分查找方法 <a id="&#x4E8C;&#x5206;&#x67E5;&#x627E;&#x65B9;&#x6CD5;"></a>

算法描述：在一组有序数组中，将数组一分为二，将要查询的元素和分割点进行比较，分为三种情况

* 相等直接返回
* 元素大于分割点，在分割点右侧继续查找
* 元素小于分割点，在分割点左侧继续查找

时间复杂： **O\(lgn\)**.

### 要求 <a id="&#x8981;&#x6C42;"></a>

* 必须是有序的数组，并能支持随机访问

### 变形 <a id="&#x53D8;&#x5F62;"></a>

* 查找第一个值等于给定的
  * 在相等的时候做处理，向前查
* 查找最后一个值等于给定的值
  * 在相等的时候做处理，向后查
* 查找第一个大于等于给定的值
  * 判断边界减1
* 查找最后一个小于等于给定的值
  * 判断边界加1

### 实际应用 <a id="&#x5B9E;&#x9645;&#x5E94;&#x7528;"></a>

* 用户ip区间段查询
* 用于相似度查询

```text
package sort

import "fmt"

func bin_search(arr []int, finddata int) int {
    low := 0
    high := len(arr) - 1
    for low <= high {
        mid := (low + high) / 2
        fmt.Println(mid)
        if arr[mid] > finddata {
            high = mid - 1
        } else if arr[mid] < finddata {
            low = mid + 1
        } else {
            return mid
        }
    }
    return -1
}

func main() {
    arr := make([]int, 1024*1024, 1024*1024)
    for i := 0; i < 1024*1024; i++ {
        arr[i] = i + 1
    }
    id := bin_search(arr, 1024)
    if id != -1 {
        fmt.Println(id, arr[id])
    } else {
        fmt.Println("没有找到数据")
    }
}
```

