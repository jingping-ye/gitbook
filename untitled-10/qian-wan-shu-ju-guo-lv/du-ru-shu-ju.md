# 读入数据

## 1. 读入数据 <a id="&#x8BFB;&#x5165;&#x6570;&#x636E;"></a>

```text
package main

import (
    "bufio"
    "fmt"
    "io"
    "io/ioutil"
    "os"
    "strings"

    
    "github.com/axgle/mahonia"
)


func read() {
    contentBytes, err := ioutil.ReadFile("./kaifang.txt")
    if err != nil {
        fmt.Println("读入失败，", err)
    }
    contentStr := string(contentBytes)
    
    lineStrs := strings.Split(contentStr, "\n\r")
    for _, lineStr := range lineStrs {
        
        newStr := ConvertEncoding(lineStr, "GBK")
        fmt.Println(newStr)
    }
}


func read2() {
    file, _ := os.Open("./kaifang.txt")
    defer file.Close()
    
    reader := bufio.NewReader(file)
    for {
        lineBytes, _, err := reader.ReadLine()
        if err == io.EOF {
            break
        }
        gbkStr := string(lineBytes)
        utfStr := ConvertEncoding(gbkStr, "GBK")
        fmt.Println(utfStr)
    }
}





func ConvertEncoding(srcStr string, encoding string) (dstStr string) {
    
    enc := mahonia.NewDecoder(encoding)
    
    utfStr := enc.ConvertString(srcStr)
    dstStr = utfStr
    return
}

func main() {
    read2()
}
```

