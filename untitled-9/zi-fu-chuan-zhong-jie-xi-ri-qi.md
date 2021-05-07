# 字符串中解析日期

## 1. 字符串中解析日期 <a id="&#x5B57;&#x7B26;&#x4E32;&#x4E2D;&#x89E3;&#x6790;&#x65E5;&#x671F;"></a>

关于Unix时间，我们几乎在文章中谈到了这一点-但在这篇文章中，我们研究了如何获取字符串格式的缩写日期并将其转换为所需格式的有意义的日期。这使用了两个重要的功能，Parse和Format的时间包内。

解析功能很有趣，因为与某些编程语言不同，解析日期时不需要使用字母指定日期（例如Ymd）。相反，您使用实时格式-这次实际上是：2006-01-02T15:04:05+07:00。

```text
package main

import (
    "fmt"
    "time"
)

func main() {

    
    myDateString := "2020-01-08 04:35"
    fmt.Println("My Starting Date:\t", myDateString)

    
    myDate, err := time.Parse("2006-01-02 15:04", myDateString)
    if err != nil {
        panic(err)
    }

    
    fmt.Println("My Date Reformatted:\t", myDate.Format(time.RFC822))

    
    fmt.Println("Just The Date:\t\t", myDate.Format("2006-01-02"))
}
```

