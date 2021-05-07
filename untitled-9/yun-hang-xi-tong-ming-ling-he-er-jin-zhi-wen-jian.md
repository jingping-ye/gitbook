# 运行系统命令和二进制文件

## 1. 运行系统命令和二进制文件 <a id="&#x8FD0;&#x884C;&#x7CFB;&#x7EDF;&#x547D;&#x4EE4;&#x548C;&#x4E8C;&#x8FDB;&#x5236;&#x6587;&#x4EF6;"></a>

在Go中与其他语言一样，我们可以调用外部二进制文件。允许我们做各种各样的事情，但是在我们的示例中，我们只是通过调用副本（位于/usr/local/go/bin计算机上）来打印go版本。

包中的[Command](https://golang.org/pkg/os/exec/#Command)函数os/exec将允许我们执行此操作。它接受至少一个字符串-您要运行的命令/二进制文件的名称-后接任意数量的字符串作为其参数。

```text
package main

import (
    "fmt"
    "log"
    "os/exec"
)

func main() {

    // Print Go Version
    cmdOutput, err := exec.Command("go", "version").Output()
    if err != nil {
        log.Fatal(err)
    }

    fmt.Printf("%s", cmdOutput)
}
```

