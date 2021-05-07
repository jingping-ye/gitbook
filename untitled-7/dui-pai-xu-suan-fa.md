# 堆排序算法

## 1. 堆排序算法 <a id="&#x5806;&#x6392;&#x5E8F;&#x7B97;&#x6CD5;"></a>

算法描述：首先建一个堆，然后调整堆，调整过程是将节点和子节点进行比较，将 其中最大的值变为父节点，递归调整调整次数lgn,最后将根节点和尾节点交换再n次 调整**O\(nlgn\)**.

### 算法步骤 <a id="&#x7B97;&#x6CD5;&#x6B65;&#x9AA4;"></a>

* 创建最大堆或者最小堆（我是最小堆）
* 调整堆
* 交换首尾节点\(为了维持一个完全二叉树才要进行收尾交换\)

```text
package sort

import "fmt"


func main() {
    arr := []int{1, 9, 10, 30, 2, 5, 45, 8, 63, 234, 12}
    fmt.Println(HeapSort(arr))
}
func HeapSortMax(arr []int, length int) []int {
    
    if length <= 1 {
        return arr
    }
    depth := length/2 - 1 
    for i := depth; i >= 0; i-- {
        topmax := i 
        leftchild := 2*i + 1
        rightchild := 2*i + 2
        if leftchild <= length-1 && arr[leftchild] > arr[topmax] { 
            topmax = leftchild
        }
        if rightchild <= length-1 && arr[rightchild] > arr[topmax] { 
            topmax = rightchild
        }
        if topmax != i {
            arr[i], arr[topmax] = arr[topmax], arr[i]
        }
    }
    return arr
}
func HeapSort(arr []int) []int {
    length := len(arr)
    for i := 0; i < length; i++ {
        lastlen := length - i
        HeapSortMax(arr, lastlen)
        if i < length {
            arr[0], arr[lastlen-1] = arr[lastlen-1], arr[0]
        }
    }
    return arr
}
```

