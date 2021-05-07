# 堆

## 1. 堆 <a id="&#x5806;"></a>

### 1.1.1. 堆：是一个完全二叉树，可以用数组来保存它。 <a id="&#x5806;&#xFF1A;&#x662F;&#x4E00;&#x4E2A;&#x5B8C;&#x5168;&#x4E8C;&#x53C9;&#x6811;&#xFF0C;&#x53EF;&#x4EE5;&#x7528;&#x6570;&#x7EC4;&#x6765;&#x4FDD;&#x5B58;&#x5B83;&#x3002;"></a>

#### 实现 <a id="&#x5B9E;&#x73B0;"></a>

* 创建堆：对顶的元素是最大或者最小的
* 调整堆：自下向上，或者自上而下进行调整，调整方式，就是交换
* 插入一个元素：将新元素插入堆底，自下上向上调整
* 删除堆顶：将堆底和堆顶元素调换，调整堆最后删除被移到堆底的元素

#### 总结 <a id="&#x603B;&#x7ED3;"></a>

* 所有的一起操作的都是为了保证堆是完全二叉树，这样保存是使用内存最少的，并且能实现快速排序和查找最新小值或者最大值。

#### 应用 <a id="&#x5E94;&#x7528;"></a>

* 优先级队列
  * 合并小文件
  * 高性能定时器
* TopK问题
  * 维护一个大小为K的小顶堆，如果新来的元素比它大，就删除堆顶插入这个元素，小于这个元素不做处理
* 中位数问题
  * 维护两个堆，一个大顶堆，一个小顶堆，可以是一半一半，或者某个特定百分比。在插入完成后调整比例。两个杯子相互倒水

```text
package heap

import (
    "fmt"
)

type Node struct {
    Value int
    Key   string
}

type Heap struct {
    list   []*Node
    length int
}


func CreateHeap() {
    arrList := []int{1, 2, 11, 3, 7, 8, 4, 5}
    var myHeap Heap
    myHeap.list = append(myHeap.list, &Node{})
    for _, value := range arrList {
        tmp := Node{}
        tmp.Value = value
        myHeap.InsertHeap(&tmp)
    }
    for {
        node := myHeap.GetTopHeap()
        fmt.Println(node)
    }
    myHeap.SortHeap(myHeap.list)
    heapShow(myHeap.list)
}


func (h *Heap) InsertHeap(one *Node) {
    h.list = append(h.list, one)
    length := len(h.list)
    h.length = length - 1
    h.AdjustHeap(h.length)
}


func (h *Heap) SortHeap(heaps []*Node) {
    length := len(heaps)
    length = length - 1
    if length == 1 {
        return
    }
    if length == 2 {
        h.AdjustHeap(length - 1)
    }
    for length > 0 {
        h.SliceNodeSwap(1, length)
        length--
        h.Heapfiy(length, 1)
    }
    
    minPos := 1
    maxPos := h.length
    for minPos < maxPos {
        h.SliceNodeSwap(minPos, maxPos)
        minPos++
        maxPos--
    }
}


func (h *Heap) AdjustHeap(length int) {
    if length < 1 {
        return
    }
    if length == 2 {
        if h.list[length].Value > h.list[length-1].Value {
            h.SliceNodeSwap(length, length-1)
        }
        return
    }
    i := length
    for i/2 > 0 && h.list[i].Value > h.list[i/2].Value {
        h.SliceNodeSwap(i, i/2)
        i = i / 2
    }
    return
}


func heapShow(heaps []*Node) {
    for one, value := range heaps {
        fmt.Println(one, value)
    }
}


func (h *Heap) SliceNodeSwap(i int, j int) {
    x := h.list[i]
    h.list[i] = h.list[j]
    h.list[j] = x
}


func (h *Heap) Heapfiy(length int, pos int) {
    for {
        maxPos := pos
        if pos*2 < length && h.list[pos].Value < h.list[pos*2].Value {
            maxPos = pos * 2
        }
        if pos*2+1 < length && h.list[maxPos].Value < h.list[pos*2+1].Value {
            maxPos = pos*2 + 1
        }
        if maxPos == pos {
            break
        }
        h.SliceNodeSwap(pos, maxPos)
        pos = maxPos
    }
}


func (h *Heap) GetTopHeap() *Node {
    if h.length == 0 {
        panic("Heap is empty")
    }
    top := h.list[1]
    
    h.SliceNodeSwap(1, len(h.list)-1)
    length := len(h.list) - 2
    fmt.Println(length)
    h.Heapfiy(length, 1)
    heapShow(h.list)
    h.list = append(h.list[:length+1], h.list[length+2:]...)
    h.length--
    return top

}
```

