# 数据格式

## 1. 数据格式 <a id="&#x6570;&#x636E;&#x683C;&#x5F0F;"></a>

### 1.1.1. 数据格式介绍 <a id="&#x6570;&#x636E;&#x683C;&#x5F0F;&#x4ECB;&#x7ECD;"></a>

* 是系统中数据交互不可缺少的内容
* 这里主要介绍JSON、XML、MSGPack

### 1.1.2. JSON <a id="json"></a>

* json是完全独立于语言的文本格式，是k-v的形式 name:zs
* 应用场景：前后端交互，系统间数据交互
* json使用go语言内置的encoding/json 标准库
* 编码json使用json.Marshal\(\)函数可以对一组数据进行JSON格式的编码

```text
    func Marshal(v interface{}) ([]byte, error)
```

示例过结构体生成json

```text
package main

import (
    "encoding/json"
    "fmt"
)

type Person struct {
    Name  string
    Hobby string
}

func main() {
    p := Person{"5lmh.com", "女"}
    
    b, err := json.Marshal(p)
    if err != nil {
        fmt.Println("json err ", err)
    }
    fmt.Println(string(b))

    
    b, err = json.MarshalIndent(p, "", "     ")
    if err != nil {
        fmt.Println("json err ", err)
    }
    fmt.Println(string(b))
}
```

#### struct tag <a id="struct-tag"></a>

```text
    type Person struct {
        //"-"是忽略的意思
        Name  string `json:"-"`
        Hobby string `json:"hobby" `
    }
```

示例通过map生成json

```text
package main

import (
    "encoding/json"
    "fmt"
)

func main() {
    student := make(map[string]interface{})
    student["name"] = "5lmh.com"
    student["age"] = 18
    student["sex"] = "man"
    b, err := json.Marshal(student)
    if err != nil {
        fmt.Println(err)
    }
    fmt.Println(b)
}
```

* 解码json使用json.Unmarshal\(\)函数可以对一组数据进行JSON格式的解码

```text
    func Unmarshal(data []byte, v interface{}) error
```

示例解析到结构体

```text
package main

import (
    "encoding/json"
    "fmt"
)

type Person struct {
    Age       int    `json:"age,string"`
    Name      string `json:"name"`
    Niubility bool   `json:"niubility"`
}

func main() {
    
    b := []byte(`{"age":"18","name":"5lmh.com","marry":false}`)
    var p Person
    err := json.Unmarshal(b, &p)
    if err != nil {
        fmt.Println(err)
    }
    fmt.Println(p)
}
```

示例解析到interface

```text
package main

import (
    "encoding/json"
    "fmt"
)

func main() {
    
    
    b := []byte(`{"age":1.3,"name":"5lmh.com","marry":false}`)

    
    var i interface{}
    err := json.Unmarshal(b, &i)
    if err != nil {
        fmt.Println(err)
    }
    
    fmt.Println(i)
    
    m := i.(map[string]interface{})
    for k, v := range m {
        switch vv := v.(type) {
        case float64:
            fmt.Println(k, "是float64类型", vv)
        case string:
            fmt.Println(k, "是string类型", vv)
        default:
            fmt.Println("其他")
        }
    }
}
```

### 1.1.3. XML <a id="xml"></a>

* 是可扩展标记语言，包含声明、根标签、子元素和属性
* 应用场景：配置文件以及webService

示例：

```text
    
    <servers version="1">
        <server>
            <serverName>Shanghai_VPNserverName>
            <serverIP>127.0.0.1serverIP>
        server>
        <server>
            <serverName>Beijing_VPNserverName>
            <serverIP>127.0.0.2serverIP>
        server>
    servers>
```

```text
package main

import (
   "encoding/xml"
   "fmt"
   "io/ioutil"
)


type Server struct {
   ServerName string `xml:"serverName"`
   ServerIP   string `xml:"serverIP"`
}

type Servers struct {
   Name    xml.Name `xml:"servers"`
   Version int   `xml:"version"`
   Servers []Server `xml:"server"`
}

func main() {
   data, err := ioutil.ReadFile("D:/my.xml")
   if err != nil {
      fmt.Println(err)
      return
   }
   var servers Servers
   err = xml.Unmarshal(data, &servers)
   if err != nil {
      fmt.Println(err)
      return
   }
   fmt.Printf("xml: %#v\n", servers)
}
```

### 1.1.4. MSGPack <a id="msgpack"></a>

* MSGPack是二进制的json，性能更快，更省空间
* 需要安装第三方包：go get -u github.com/vmihailenco/msgpack

```text
package main

import (
   "fmt"
   "github.com/vmihailenco/msgpack"
   "io/ioutil"
   "math/rand"
)

type Person struct {
   Name string
   Age  int
   Sex  string
}


func writerJson(filename string) (err error) {
   var persons []*Person
   
   for i := 0; i < 10; i++ {
      p := &Person{
         Name: fmt.Sprintf("name%d", i),
         Age:  rand.Intn(100),
         Sex:  "male",
      }
      persons = append(persons, p)
   }
   
   data, err := msgpack.Marshal(persons)
   if err != nil {
      fmt.Println(err)
      return
   }
   err = ioutil.WriteFile(filename, data, 0666)
   if err != nil {
      fmt.Println(err)
      return
   }
   return
}


func readJson(filename string) (err error) {
   var persons []*Person
   
   data, err := ioutil.ReadFile(filename)
   if err != nil {
      fmt.Println(err)
      return
   }
   
   err = msgpack.Unmarshal(data, &persons)
   if err != nil {
      fmt.Println(err)
      return
   }
   for _, v := range persons {
      fmt.Printf("%#v\n", v)
   }
   return
}

func main() {
   
   
   
   
   
   err := readJson("D:/person.dat")
   if err != nil {
      fmt.Println(err)
      return
   }
}
```

