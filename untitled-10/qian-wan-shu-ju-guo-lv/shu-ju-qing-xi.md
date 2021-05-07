# 数据清洗

## 1. 数据清洗 <a id="&#x6570;&#x636E;&#x6E05;&#x6D17;"></a>

读取清洗的效率还是挺快的

```text
package main

import (
    "bufio"
    "fmt"
    "io"
    "os"
    "strings"

    "github.com/axgle/mahonia"
)

func main() {
    
    file, _ := os.Open("./kaifang.txt")
    defer file.Close()
    
    goodFile, _ := os.OpenFile("./kaifang_good.txt", os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0666)
    defer goodFile.Close()
    
    badFile, _ := os.OpenFile("./kaifang_bad.txt", os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0666)
    defer badFile.Close()
    
    reader := bufio.NewReader(file)
    for {
        lineBytes, _, err := reader.ReadLine()
        if err == io.EOF {
            break
        }
        gbkStr := string(lineBytes)
        lineStr := ConvertEncoding(gbkStr, "GBK")
        
        fields := strings.Split(lineStr, ",")
        
        if len(fields) >= 2 && len(fields[1]) == 18 {
            goodFile.WriteString(lineStr + "\n")
            fmt.Println("Good:", lineStr)
        } else {
            badFile.WriteString(lineStr + "\n")
            fmt.Println("Bad:", lineStr)
        }
    }
}

func ConvertEncoding(srcStr string, encoding string) (dstStr string) {
    
    enc := mahonia.NewDecoder(encoding)
    
    utfStr := enc.ConvertString(srcStr)
    dstStr = utfStr
    return
}
```

