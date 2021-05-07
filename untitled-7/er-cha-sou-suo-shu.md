# 二叉搜索树

## 1. 二叉搜索树 <a id="&#x4E8C;&#x53C9;&#x641C;&#x7D22;&#x6811;"></a>

算法描述：二叉搜索树，是按照一个节点的左子叶小于节点的值，右子叶大于节点的值 搜索二叉树的，插入和搜索时间复杂都是**O\(lgn\)**.

### 1.1.1. 算法步骤 <a id="&#x7B97;&#x6CD5;&#x6B65;&#x9AA4;"></a>

* 选取根节点
* 按照算法描述构建树

### 1.1.2. 算法分析 <a id="&#x7B97;&#x6CD5;&#x5206;&#x6790;"></a>

* 如果想树的搜索深度尽量浅，就改把中间值作为根节点,数据尽量也不要是有序的 否则会形成单边树，或者就是一个链表，修改和查找的时间复杂都变为**O\(n\)**

### 1.1.3. B+树在数据库中的应用 <a id="b&#x6811;&#x5728;&#x6570;&#x636E;&#x5E93;&#x4E2D;&#x7684;&#x5E94;&#x7528;"></a>

#### 思路总结 <a id="&#x601D;&#x8DEF;&#x603B;&#x7ED3;"></a>

1. 解决问题前先定义清楚问题
   * 对问题进行假设，限定解决问题的范围
   * select from a where id=1 主键搜索
   * select from a where id&gt;19 范围搜索
   * 非功能性需求
     * 安全
     * 性能
     * 用户体验
   * 由于我们想学习算法和数据结构，所以我们只从性能上进行讨论
     * 执行效率
     * 存储空间
2. 尝试用学习过的数据结构解决问题
   * 散列表：
     * 查询效率O\(1\)但是不支持范围查询
   * 平衡二叉树：
     * 可以快速查询O\(lgn\)，中序遍历是有序数组单不支持范围查询
   * 跳表
     * 查询数据O\(lgn\)，并可以支持范围查询
     * 虽然满足条件但有更好的方法，减少内存使用
3. 优化
   * 使用跳表虽然可以实现需求，但是我们有更好的数据结构，二叉查找树。二叉查找树和跳表类似查询速度O\(lgn\)
   * 遇到问题：内存消耗巨大
     * 如果数据量巨大的二叉树，比如1亿条数据，就要有1亿个节点，如果一个节点占用16字节，那占用内存1g,10个表就是10gb
   * 解决问题：为了减少内存消耗，只能通过将数据放在硬盘中
   * 遇到分体：读取磁盘中的节点IO消耗很大
   * 解决问题：所以尽量降低树的高度，减少io操作
   * 遇到问题：如何降低树的高度
   * 解决问题：从二叉树，变为N叉树
   * 遇到问题：是不是N越大越好呢?
   * 解决问题：cup的缓存是以也为单位的，如果数据超过一页，需要分页处理所以为了尽量减少io操作，节点数据尽量不要超过一页（4KB）

### 1.1.4. 结论 <a id="&#x7ED3;&#x8BBA;"></a>

综上所述：使用某种算法是要全面考虑到实际的问题，根据实际的问题进行假设，选择合适的算法，结合软硬件效率综合得出一个合理方案。

```text
package main

import (
    "fmt"
    "math"
)

type Node struct {
    value  int
    left   *Node
    right  *Node
    parent *Node
}

type Tree struct {
    root   *Node
    length int
}

func main() {
    creatTree()
}

func creatTree() {
    arrList := []int{14, 2, 5, 7, 23, 35, 12, 17, 31}
    myTree := Tree{}
    for i := 0; i < len(arrList); i++ {
        myTree = insertNode(myTree, arrList[i])
        myTree.length++
    }
    fmt.Println(myTree)
    
    TreeHeight(myTree)
}
func TreeHeight(tree Tree) {
    var hl = 1
    if tree.root.left != nil {
        hl = heightMax(tree.root.left, hl)
    }
    var hr = 1
    if tree.root.right != nil {
        hr = heightMax(tree.root.left, hr)
    }
    fmt.Println(hl, hr)
    fmt.Println("Tree height is ", int(math.Max(float64(hl), float64(hr))))
}

func heightMax(node *Node, h int) int {
    var hL = h
    var hR = h
    if node.left == nil && node.right == nil {
        fmt.Println(node)
        return h
    }
    if node.left != nil {
        h++
        hL = heightMax(node.left, h)
    }
    if node.right != nil {
        h++
        hR = heightMax(node.right, h)
    }
    return int(math.Max(float64(hL), float64(hR)))
}


func LDR(tree Tree) {
    readList := make(map[int]int)
    i := 0
    var currentNode *Node
    currentNode = tree.root
    for {
        
        if i == tree.length {
            
            break
        }
        if currentNode.left == nil {
            if readList[currentNode.value] == 1 {
                if readList[currentNode.right.value] == 1 {
                    currentNode = currentNode.parent
                    continue
                } else {
                    currentNode = currentNode.right
                    continue
                }
            } else {
                fmt.Println(currentNode.value)
                readList[currentNode.value] = 1
                i++
                if currentNode.right == nil {
                    currentNode = currentNode.parent
                    continue
                } else {
                    if readList[currentNode.right.value] == 1 {
                        currentNode = currentNode.parent
                        continue
                    } else {
                        currentNode = currentNode.right
                        continue
                    }
                }
            }
        } else {
            if readList[currentNode.left.value] == 1 {
                if readList[currentNode.value] == 1 {
                    currentNode = currentNode.right
                    continue
                } else {
                    fmt.Println(currentNode.value)
                    readList[currentNode.value] = 1
                    i++
                    if currentNode.right == nil {
                        currentNode = currentNode.parent
                        continue
                    } else {
                        if readList[currentNode.right.value] == 1 {
                            currentNode = currentNode.parent
                            continue
                        } else {
                            currentNode = currentNode.right
                            continue
                        }

                    }
                }
            } else {
                currentNode = currentNode.left
                continue
            }

        }

    }
}

func insertNode(tree Tree, insertValue int) Tree {
    var currentNode *Node
    var tmp *Node
    i := 0
    if tree.length == 0 {
        currentNode = new(Node)
        currentNode.value = insertValue
        tree.root = currentNode
        return tree
    } else {
        currentNode = tree.root
    }
    for {
        
        if currentNode.value < insertValue {
            
            if currentNode.right == nil {
                tmp = new(Node)
                tmp.value = insertValue
                currentNode.right = tmp
                tmp.parent = currentNode
                break
            } else {
                currentNode = currentNode.right
                continue
            }
        } else {
            if currentNode.left == nil {
                tmp = new(Node)
                tmp.value = insertValue
                currentNode.left = tmp
                tmp.parent = currentNode
                break
            } else {
                currentNode = currentNode.left
                continue
            }
        }
        i++
    }
    return tree
}
```

