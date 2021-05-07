# 基数排序算法

* [**1.** 基数排序算法](ji-shu-pai-xu-suan-fa.md#基数排序算法)

## 1. 基数排序算法 <a id="&#x57FA;&#x6570;&#x6392;&#x5E8F;&#x7B97;&#x6CD5;"></a>

算法描述：基数排序类似计数排序，需要额外的空间来记录对应的基数内的数据 额外的空间是有序的，最终时间复杂度**O\(nlogrm\)**,r是基数，r^m=n.当给定 特定的范围，计数排序又可以叫桶排序，当以10进制为基数时就是简单的桶排序

### 算法步骤 <a id="&#x7B97;&#x6CD5;&#x6B65;&#x9AA4;"></a>

* 从个位开始排序，从低到高进行递推
* 比较过程中如果遇到高位相同时，顺序不变

### 算法分两类 <a id="&#x7B97;&#x6CD5;&#x5206;&#x4E24;&#x7C7B;"></a>

1. 低位排序LSD
2. 高位排序MSD

```text
package sort

import "fmt"

func main() {
    var arr [3][]int
    myarr := []int{1, 2, 3, 1, 1, 2, 2, 2, 2, 2, 3}
    for i := 0; i < len(myarr); i++ {
        arr[myarr[i]-1] = append(arr[myarr[i]-1], myarr[i])
    }
    fmt.Println(arr)
}
```

