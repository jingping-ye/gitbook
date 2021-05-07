# 图

## 1. 图 <a id="&#x56FE;"></a>

* 相对于树来说更复杂的非线性数据结构

### 1.1.1. 图分类 <a id="&#x56FE;&#x5206;&#x7C7B;"></a>

* 无向图
  * 表示顶点到顶点没有方向
* 有向图
  * 表示顶点到顶点有方向
    * 入度：有多少个顶点进入
    * 出度：有多少个顶点出去
    * 六度分割理论
* 带权图
  * 表示顶点到顶点有权重

### 1.1.2. 图的存储方式 <a id="&#x56FE;&#x7684;&#x5B58;&#x50A8;&#x65B9;&#x5F0F;"></a>

* 邻接矩阵：
  * 缺点：对于无向图来说，用邻接矩阵存储是浪费空间的，因为邻接矩阵是对称的，并且对于大量顶点需要跟多的空间来存储
  * 优点：直观，并且可以利用矩阵计算，求最短路径
* 邻接表：
* 如何实现微博的关注关系
  * 用邻接表来实现用户的关注的人
    * 类似链表链表对缓存不友好，可以通过其他方式优化比如hash,跳表，红黑二叉树等来实现快速查询，B+树
    * 可以通过其他存储方式落地，比如mysql建立表。
  * 用逆邻接表来实现用户的粉丝
* 搜索
  * 广度搜索：地毯式搜索
  * 深度搜索：深度优先式搜索

```text
package main

import "fmt"


type graph struct {
    vertex int
    list   map[int][]int
    found  bool
}

func main() {
    g := NewGraph(8)
    g.addVertex(1, 2)
    g.addVertex(1, 4)
    g.addVertex(2, 3)
    g.addVertex(2, 5)
    g.addVertex(4, 5)
    g.addVertex(3, 6)
    g.addVertex(5, 6)
    g.addVertex(5, 7)
    g.addVertex(6, 8)
    g.addVertex(7, 8)
    g.bfs(1, 7)
}


func NewGraph(v int) *graph {
    g := new(graph)
    g.vertex = v
    g.found = false
    g.list = map[int][]int{}
    i := 0
    for i < v {
        g.list[i] = make([]int, 0)
        i++
    }
    return g
}


func (g *graph) addVertex(t int, s int) {
    g.list[t] = push(g.list[t], s)
    g.list[s] = push(g.list[s], t)
}


func pop(list []int) (int, []int) {
    if len(list) > 0 {
        a := list[0]
        b := list[1:]
        return a, b
    } else {
        return -1, list
    }
}


func push(list []int, value int) []int {
    result := append(list, value)
    return result
}


func (g *graph) bfs(s int, t int) {
    if s == t {
        return
    }
    visited := make([]bool, g.vertex+1)
    var queue []int
    queue = append(queue, s)
    prev := make([]int, g.vertex+1)
    i := 0
    for i < len(prev) {
        prev[i] = -1
        i++
    }
    for len(queue) != 0 {
        var w int
        w, queue = pop(queue)
        for j := 0; j < len(g.list[w]); j++ {
            q := g.list[w][j]
            fmt.Println(q)
            if !visited[q] {
                prev[q] = w
                if q == t {
                    fmt.Println(prev)
                    printPath(prev, s, t)
                    return
                }
                visited[q] = true
                queue = append(queue, q)
            }
        }
    }
}


func (g *graph) dfs(s int, t int) {
    prev := make([]int, g.vertex+1)
    for i := 0; i < len(prev); i++ {
        prev[i] = -1
    }
    visit := make([]bool, g.vertex+1)
    g.recurDsf(s, t, prev, visit)
    fmt.Println(prev)
    printPath(prev, s, t)
}

func (g *graph) recurDsf(w int, t int, prev []int, visited []bool) {
    if g.found {
        return
    }
    if w == t {
        g.found = true
        return
    }
    visited[w] = true
    for i := 0; i < len(g.list[w]); i++ {
        q := g.list[w][i]
        if !visited[q] {
            prev[q] = w
            g.recurDsf(g.list[w][i], t, prev, visited)
        }
    }
}


func printPath(prev []int, s int, t int) {
    if prev[t] != -1 && s != t {
        printPath(prev, s, prev[t])
    }
    fmt.Println(t, "  ")
}
```

