# 跳跃表

## 1. 跳跃表 <a id="&#x8DF3;&#x8DC3;&#x8868;"></a>

### 1.1.1. 定义 <a id="&#x5B9A;&#x4E49;"></a>

在一组数据中，按照一定规则对数据创建层级索引，通过自上至下的索引查询查找数据位置

### 1.1.2. 特点 <a id="&#x7279;&#x70B9;"></a>

* 二分查找针对数组，为了实现不使用针对数组二分查找，产生了跳跃表
* 跳跃表使用链表
* 有多级索引

### 1.1.3. 时间复杂度 <a id="&#x65F6;&#x95F4;&#x590D;&#x6742;&#x5EA6;"></a>

* 修改（增删改）时间复杂度 O\(lgn\)
* 查询时间复杂度O\(lgn\)

### 1.1.4. 空间复杂度 <a id="&#x7A7A;&#x95F4;&#x590D;&#x6742;&#x5EA6;"></a>

* 空间复杂度根据索引生成方法有关，例子中的大概是O\(n\)
* n/2+n/4+....+4+2 等比求和 Sn=\(a1-anq\)/1-q O\(n-2\) 约等于 O\(n\)

```text
package skipList

import "fmt"

type Node struct {
    Data      int
    NextPoint *Node
    PrePoint  *Node
    NextLevel *Node
}

type LinkedList struct {
    Head    *Node
    Current *Node
    Tail    *Node
    Length  int
    Level   int
}

type SkipList struct {
    List        LinkedList
    FirstIndex  LinkedList
    SecondIndex LinkedList
    
}

func InitSkipList() {
    data := []int{11, 12, 13, 19, 21, 31, 33, 42, 51, 62}
    sl := SkipList{}
    sl.initSkip(data)
    
    sl.add(11)
    showSkipList(sl)
}

func showSkipLinkedList(link LinkedList, name int) {
    var currentNode *Node
    currentNode = link.Head
    for {
        i := 1
        fmt.Print(name, "-Node:", currentNode.Data)
        if currentNode.NextPoint == nil {
            break
        } else {
            currentNode = currentNode.NextPoint
        }
        if name == 1 {
            fmt.Print("-------->")
        } else if name == 2 {
            for i <= 3 {
                fmt.Print("-------->")
                i++
            }
        } else {
            for i <= 7 {
                fmt.Print("-------->")
                i++
            }
        }
    }
    fmt.Println("")
}

func (sl *SkipList) initSkip(list []int) {
    sl.List = LinkedList{}
    sl.FirstIndex = LinkedList{}
    sl.SecondIndex = LinkedList{}
    var currentNode *Node
    for i := 0; i < len(list); i++ {
        currentNode = new(Node)
        currentNode.Data = list[i]
        addNode(sl, currentNode)
        
    }
    showSkipList(*sl)
}
func (sl *SkipList) find(x int) {
    var current *Node
    current = sl.SecondIndex.Head
    if current.Data == x {
        fmt.Println(current.Data)
        return
    }
    if x < current.Data {
        panic("No exist in skipList")
        return
    }
    for {
        if x > current.Data {
            fmt.Println(current.Data)
            current = current.NextPoint
        } else if x < current.Data {
            
            fmt.Println(current.Data)
            current = current.PrePoint.NextLevel.NextPoint
        } else {
            fmt.Println(current.Data)
            return
        }
    }
}
func (sl *SkipList) add(x int) {
    var current *Node
    current = sl.SecondIndex.Head
    if current.Data == x {
        panic("Had existed in skipList")
        return
    }
    if x < current.Data {
        newNode2 := new(Node)
        newNode2.Data = x
        newNode2.NextPoint = sl.SecondIndex.Head
        sl.SecondIndex.Head.PrePoint = newNode2
        sl.SecondIndex.Head = newNode2
        newNode1 := new(Node)
        newNode1.Data = x
        newNode1.NextPoint = sl.FirstIndex.Head
        sl.FirstIndex.Head.PrePoint = newNode1
        sl.FirstIndex.Head = newNode1
        newNode := new(Node)
        newNode.Data = x
        newNode.NextPoint = sl.List.Head
        sl.SecondIndex.Head.PrePoint = newNode
        sl.List.Head = newNode
        return
    }
    for {
        if x > current.Data {
            if current.NextPoint == nil {
                if current.NextLevel != nil {
                    current = current.NextLevel
                } else {
                    
                    newNode := new(Node)
                    newNode.Data = x
                    current.NextPoint = newNode
                    newNode.PrePoint = current
                    return
                }
            } else {
                fmt.Println(current.Data)
                current = current.NextPoint
            }

        } else if x < current.Data {
            
            fmt.Println(current.Data)
            if current.PrePoint.NextLevel != nil {
                current = current.PrePoint.NextLevel.NextPoint
            } else {
                
                newNode := new(Node)
                newNode.Data = x
                current.PrePoint.NextPoint = newNode
                newNode.NextPoint = current
                current.PrePoint = newNode
                return
            }
        } else {
            fmt.Println(current.Data)
            return
        }
    }
}
func showSkipList(sl SkipList) {
    showSkipLinkedList(sl.SecondIndex, 3)
    fmt.Println("")
    showSkipLinkedList(sl.FirstIndex, 2)
    fmt.Println("")
    showSkipLinkedList(sl.List, 1)
}
func addNode(skipList *SkipList, node *Node) {
    insertToLink(&skipList.List, node)
    if skipList.FirstIndex.Length == 0 || ((skipList.List.Length-1)%2 == 0 && skipList.List.Length > 2) {
        newNode := new(Node)
        newNode.Data = node.Data
        newNode.NextLevel = node
        insertToLink(&skipList.FirstIndex, newNode)
        if skipList.SecondIndex.Length == 0 || ((skipList.FirstIndex.Length-1)%2 == 0 && skipList.FirstIndex.Length > 2) {
            newNode2 := new(Node)
            newNode2.Data = node.Data
            newNode2.NextLevel = newNode
            insertToLink(&skipList.SecondIndex, newNode2)
        }
    }
}

func insertToLink(link *LinkedList, node *Node) {
    if link.Head == nil {
        link.Head = node
        link.Tail = node
        link.Current = node
    } else {
        link.Tail.NextPoint = node
        node.PrePoint = link.Tail
        link.Tail = node
    }
    link.Length++
}
```

