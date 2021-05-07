# 函数验证中间键

## 1. 函数验证中间键 <a id="&#x51FD;&#x6570;&#x9A8C;&#x8BC1;&#x4E2D;&#x95F4;&#x952E;"></a>

本章节介绍的是使用原生的手法自定义一个函数验证的中间键！

目录结构：

-common

--filter.go

-main.go

filter.go文件代码：

```text
package common

import (
    "net/http"
)


type FilterHandle func(rw http.ResponseWriter, req *http.Request) error


type Filter struct {
    
    filterMap map[string]FilterHandle
}


func NewFilter() *Filter {
    return &Filter{filterMap: make(map[string]FilterHandle)}
}


func (f *Filter) RegisterFilterUri(uri string, handler FilterHandle) {
    f.filterMap[uri] = handler
}


func (f *Filter) GetFilterHandle(uri string) FilterHandle {
    return f.filterMap[uri]
}


type WebHandle func(rw http.ResponseWriter, req *http.Request)


func (f *Filter) Handle(webHandle WebHandle) func(rw http.ResponseWriter, r *http.Request) {
    return func(rw http.ResponseWriter, r *http.Request) {
        for path, handle := range f.filterMap {
            if path == r.RequestURI {
                
                err := handle(rw, r)
                if err != nil {
                    rw.Write([]byte(err.Error()))
                    return
                }
                
                break
            }
        }
        
        webHandle(rw, r)
    }
}
```

main.go文件代码：

```text
package main

import (
    "fmt"
    "net/http"

    "github.com/student/1330/common"
)


func Auth(w http.ResponseWriter, r *http.Request) error {
    
    fmt.Println("我是验证层面的信息")
    return nil
}


func Check(w http.ResponseWriter, r *http.Request) {
    
    fmt.Println("执行check！")
}
func main() {
    
    filter := common.NewFilter()
    
    filter.RegisterFilterUri("/check", Auth)
    
    http.HandleFunc("/check", filter.Handle(Check))
    
    http.ListenAndServe(":8083", nil)
}
```

