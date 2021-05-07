# base64

## 1. base64 <a id="base64"></a>

Base64是网络上最常见的用于传输8Bit字节代码的编码方式之一，可用于在HTTP环境下传递较长的标识信息。Go 的 encoding/base64 提供了对base64的编解码操作。

```text
package main

import (
    "encoding/base64"
    "fmt"
    "log"
)

func main() {

    str := "www.5lmh.com"
    fmt.Printf("string : %v\n", str)

    input := []byte(str)
    fmt.Printf("[]byte : %v\n", input)

    
    encodeString := base64.StdEncoding.EncodeToString(input)
    fmt.Printf("encode base64 : %v\n", encodeString)

    
    decodeBytes, err := base64.StdEncoding.DecodeString(encodeString)
    if err != nil {
        log.Fatalln(err)
    }
    fmt.Printf("decode base64 : %v\n", string(decodeBytes))

    fmt.Println()

    
    urlencode := base64.URLEncoding.EncodeToString([]byte(input))
    fmt.Printf("urlencode : %v\n", urlencode)
    
    urldecode, err := base64.URLEncoding.DecodeString(urlencode)
    if err != nil {
        log.Fatalln(err)
    }
    fmt.Printf("urldecode : %v\n", string(urldecode))
}
```

输出结果：

```text
    string : www.5lmh.com
    []byte : [119 119 119 46 53 108 109 104 46 99 111 109]
    encode base64 : d3d3LjVsbWguY29t
    decode base64 : www.5lmh.com

    urlencode : d3d3LjVsbWguY29t
    urldecode : www.5lmh.com
```

