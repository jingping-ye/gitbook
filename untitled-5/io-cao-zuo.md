# IO操作

## 1. IO操作 <a id="io&#x64CD;&#x4F5C;"></a>

### 1.1.1. 输入输出的底层原理 <a id="&#x8F93;&#x5165;&#x8F93;&#x51FA;&#x7684;&#x5E95;&#x5C42;&#x539F;&#x7406;"></a>

* 终端其实是一个文件，相关实例如下：
  * `os.Stdin`：标准输入的文件实例，类型为`*File`
  * `os.Stdout`：标准输出的文件实例，类型为`*File`
  * `os.Stderr`：标准错误输出的文件实例，类型为`*File`

以文件的方式操作终端:

```text
package main

import "os"

func main() {
    var buf [16]byte
    os.Stdin.Read(buf[:])
    os.Stdin.WriteString(string(buf[:]))
}
```

### 1.1.2. 文件操作相关API <a id="&#x6587;&#x4EF6;&#x64CD;&#x4F5C;&#x76F8;&#x5173;api"></a>

* `func Create(name string) (file *File, err Error)`
  * 根据提供的文件名创建新的文件，返回一个文件对象，默认权限是0666
* `func NewFile(fd uintptr, name string) *File`
  * 根据文件描述符创建相应的文件，返回一个文件对象
* `func Open(name string) (file *File, err Error)`
  * 只读方式打开一个名称为name的文件
* `func OpenFile(name string, flag int, perm uint32) (file *File, err Error)`
  * 打开名称为name的文件，flag是打开的方式，只读、读写等，perm是权限
* `func (file *File) Write(b []byte) (n int, err Error)`
  * 写入byte类型的信息到文件
* `func (file *File) WriteAt(b []byte, off int64) (n int, err Error)`
  * 在指定位置开始写入byte类型的信息
* `func (file *File) WriteString(s string) (ret int, err Error)`
  * 写入string信息到文件
* `func (file *File) Read(b []byte) (n int, err Error)`
  * 读取数据到b中
* `func (file *File) ReadAt(b []byte, off int64) (n int, err Error)`
  * 从off开始读取数据到b中
* `func Remove(name string) Error`
  * 删除文件名为name的文件

### 1.1.3. 打开和关闭文件 <a id="&#x6253;&#x5F00;&#x548C;&#x5173;&#x95ED;&#x6587;&#x4EF6;"></a>

`os.Open()`函数能够打开一个文件，返回一个`*File`和一个`err`。对得到的文件实例调用close\(\)方法能够关闭文件。

```text
package main

import (
    "fmt"
    "os"
)

func main() {
    
    file, err := os.Open("./main.go")
    if err != nil {
        fmt.Println("open file failed!, err:", err)
        return
    }
    
    file.Close()
}
```

### 1.1.4. 写文件 <a id="&#x5199;&#x6587;&#x4EF6;"></a>

```text
package main

import (
    "fmt"
    "os"
)

func main() {
    
    file, err := os.Create("./xxx.txt")
    if err != nil {
        fmt.Println(err)
        return
    }
    defer file.Close()
    for i := 0; i < 5; i++ {
        file.WriteString("ab\n")
        file.Write([]byte("cd\n"))
    }
}
```

### 1.1.5. 读文件 <a id="&#x8BFB;&#x6587;&#x4EF6;"></a>

文件读取可以用file.Read\(\)和file.ReadAt\(\)，读到文件末尾会返回io.EOF的错误

```text
package main

import (
    "fmt"
    "io"
    "os"
)

func main() {
    
    file, err := os.Open("./xxx.txt")
    if err != nil {
        fmt.Println("open file err :", err)
        return
    }
    defer file.Close()
    
    var buf [128]byte
    var content []byte
    for {
        n, err := file.Read(buf[:])
        if err == io.EOF {
            
            break
        }
        if err != nil {
            fmt.Println("read file err ", err)
            return
        }
        content = append(content, buf[:n]...)
    }
    fmt.Println(string(content))
}
```

### 1.1.6. 拷贝文件 <a id="&#x62F7;&#x8D1D;&#x6587;&#x4EF6;"></a>

```text
package main

import (
    "fmt"
    "io"
    "os"
)

func main() {
    
    srcFile, err := os.Open("./xxx.txt")
    if err != nil {
        fmt.Println(err)
        return
    }
    
    dstFile, err2 := os.Create("./abc2.txt")
    if err2 != nil {
        fmt.Println(err2)
        return
    }
    
    buf := make([]byte, 1024)
    for {
        
        n, err := srcFile.Read(buf)
        if err == io.EOF {
            fmt.Println("读取完毕")
            break
        }
        if err != nil {
            fmt.Println(err)
            break
        }
        
        dstFile.Write(buf[:n])
    }
    srcFile.Close()
    dstFile.Close()
}
```

### 1.1.7. bufio <a id="bufio"></a>

* bufio包实现了带缓冲区的读写，是对文件读写的封装
* bufio缓冲写数据

| 模式 | 含义 |
| :--- | :--- |
| os.O\_WRONLY | 只写 |
| os.O\_CREATE | 创建文件 |
| os.O\_RDONLY | 只读 |
| os.O\_RDWR | 读写 |
| os.O\_TRUNC | 清空 |
| os.O\_APPEND | 追加 |

* bufio读数据

```text
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
)

func wr() {
    
    
    
    file, err := os.OpenFile("./xxx.txt", os.O_CREATE|os.O_WRONLY, 0666)
    if err != nil {
        return
    }
    defer file.Close()
    
    writer := bufio.NewWriter(file)
    for i := 0; i < 10; i++ {
        writer.WriteString("hello\n")
    }
    
    writer.Flush()
}

func re() {
    file, err := os.Open("./xxx.txt")
    if err != nil {
        return
    }
    defer file.Close()
    reader := bufio.NewReader(file)
    for {
        line, _, err := reader.ReadLine()
        if err == io.EOF {
            break
        }
        if err != nil {
            return
        }
        fmt.Println(string(line))
    }

}

func main() {
    re()
}
```

### 1.1.8. ioutil工具包 <a id="ioutil&#x5DE5;&#x5177;&#x5305;"></a>

* 工具包写文件
* 工具包读取文件

```text
package main

import (
   "fmt"
   "io/ioutil"
)

func wr() {
   err := ioutil.WriteFile("./yyy.txt", []byte("www.5lmh.com"), 0666)
   if err != nil {
      fmt.Println(err)
      return
   }
}

func re() {
   content, err := ioutil.ReadFile("./yyy.txt")
   if err != nil {
      fmt.Println(err)
      return
   }
   fmt.Println(string(content))
}

func main() {
   re()
}
```

### 1.1.9. 例子 <a id="&#x4F8B;&#x5B50;"></a>

#### 实现一个cat命令 <a id="&#x5B9E;&#x73B0;&#x4E00;&#x4E2A;cat&#x547D;&#x4EE4;"></a>

使用文件操作相关知识，模拟实现linux平台cat命令的功能。

```text
package main

import (
    "bufio"
    "flag"
    "fmt"
    "io"
    "os"
)


func cat(r *bufio.Reader) {
    for {
        buf, err := r.ReadBytes('\n') 
        if err == io.EOF {
            break
        }
        fmt.Fprintf(os.Stdout, "%s", buf)
    }
}

func main() {
    flag.Parse() 
    if flag.NArg() == 0 {
        
        cat(bufio.NewReader(os.Stdin))
    }
    
    for i := 0; i < flag.NArg(); i++ {
        f, err := os.Open(flag.Arg(i))
        if err != nil {
            fmt.Fprintf(os.Stdout, "reading from %s failed, err:%v\n", flag.Arg(i), err)
            continue
        }
        cat(bufio.NewReader(f))
    }
}
```

