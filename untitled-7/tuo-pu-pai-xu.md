# 拓扑排序

## 1. 拓扑排序 <a id="&#x62D3;&#x6251;&#x6392;&#x5E8F;"></a>

#### 定义 <a id="&#x5B9A;&#x4E49;"></a>

对于一些有前后依赖关系的排序算法，是利用有向无环图进行实现，通过局部依赖关系确定全局顺序的算法

#### 应用场景 <a id="&#x5E94;&#x7528;&#x573A;&#x666F;"></a>

* 编译有序依赖的文件

### 1.1.1. 两种拓扑算法 <a id="&#x4E24;&#x79CD;&#x62D3;&#x6251;&#x7B97;&#x6CD5;"></a>

#### Kahn算法 <a id="kahn&#x7B97;&#x6CD5;"></a>

* 算法逻辑
* 利用贪心算法，如果两个顶点，顶点b依赖于顶点a,就将a指向b,当一个顶点的入度为零，将这个顶点就是最优排序点， 并且将顶点从图中移除，将可达顶点的入度减一。

#### DFS算法 <a id="dfs&#x7B97;&#x6CD5;"></a>

1.使用深度算法，产生逆向邻接表先输出其他依赖，最后输出自己。

```text
package main

import (
    "fmt"
)


type graph struct {
    vertex int           
    list   map[int][]int 
}


func (g *graph) addVertex(t int, s int) {
    g.list[t] = push(g.list[t], s)
}

func main() {
    g := NewGraph(8)
    g.addVertex(2, 1)
    g.addVertex(3, 1)
    g.addVertex(7, 1)
    g.addVertex(4, 2)
    g.addVertex(5, 2)
    g.addVertex(8, 7)
    g.DfsSort()
}


func NewGraph(v int) *graph {
    g := new(graph)
    g.vertex = v
    g.list = map[int][]int{}
    i := 0
    for i < v {
        g.list[i] = make([]int, 0)
        i++
    }
    return g
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


func (g *graph) KhanSort() {
    var inDegree = make(map[int]int)
    var queue []int
    for i := 1; i <= g.vertex; i++ {
        for _, m := range g.list[i] {
            inDegree[m]++
        }
    }
    for i := 1; i <= g.vertex; i++ {

        if inDegree[i] == 0 {
            queue = push(queue, i)
        }
    }
    for len(queue) > 0 {
        var now int
        now, queue = pop(queue)
        fmt.Println("->", now)
        for _, k := range g.list[now] {
            inDegree[k]--
            if inDegree[k] == 0 {
                queue = push(queue, k)
            }
        }
    }
}

func (g *graph) DfsSort() {
    inverseList := make(map[int][]int)
    
    for i := 1; i <= g.vertex; i++ {
        for _, k := range g.list[i] {
            inverseList[k] = append(inverseList[k], i)
        }
    }
    visited := make([]bool, g.vertex+1)
    visited[0] = true
    for i := 1; i <= g.vertex; i++ {
        if visited[i] == false {
            visited[i] = true
            dfs(i, inverseList, visited)
        }
    }

}

func dfs(vertex int, inverseList map[int][]int, visited []bool) {
    for _, w := range inverseList[vertex] {
        if visited[w] == true {
            continue
        } else {
            visited[w] = true
            dfs(w, inverseList, visited)
        }
    }
    fmt.Println("->", vertex)
}
```

