# 带进度条大文件下载

## 1. 带进度条大文件下载 <a id="&#x5E26;&#x8FDB;&#x5EA6;&#x6761;&#x5927;&#x6587;&#x4EF6;&#x4E0B;&#x8F7D;"></a>

### 1.1.1. 普通文件下载 <a id="&#x666E;&#x901A;&#x6587;&#x4EF6;&#x4E0B;&#x8F7D;"></a>

本示例说明如何从网上将文件下载到本地计算机。通过io.Copy\(\)直接使用并传递响应主体，我们将数据流式传输到文件中，而不必将其全部加载到内存中-小文件不是问题，但下载大文件时会有所不同。

```text
package main

import (
    "io"
    "net/http"
    "os"
)

func main() {
    fileUrl := "http://topgoer.com/static/2/9.png"
    if err := DownloadFile("9.png", fileUrl); err != nil {
        panic(err)
    }
}


func DownloadFile(filepath string, url string) error {

    
    resp, err := http.Get(url)
    if err != nil {
        return err
    }
    defer resp.Body.Close()

    
    out, err := os.Create(filepath)
    if err != nil {
        return err
    }
    defer out.Close()

    
    _, err = io.Copy(out, resp.Body)
    return err
}
```

### 1.1.2. 带进度条的大文件下载 <a id="&#x5E26;&#x8FDB;&#x5EA6;&#x6761;&#x7684;&#x5927;&#x6587;&#x4EF6;&#x4E0B;&#x8F7D;"></a>

下面的示例是带有进度条的大文件下载，我们将响应主体传递到其中，io.Copy\(\)但是如果使用a，TeeReader则可以传递计数器来跟踪进度。在下载时，我们还将文件另存为临时文件，因此在完全下载文件之前，我们不会覆盖有效文件。

```text
package main

import (
    "fmt"
    "io"
    "net/http"
    "os"
    "strings"

    "github.com/dustin/go-humanize"
)

type WriteCounter struct {
    Total uint64
}

func (wc *WriteCounter) Write(p []byte) (int, error) {
    n := len(p)
    wc.Total += uint64(n)
    wc.PrintProgress()
    return n, nil
}

func (wc WriteCounter) PrintProgress() {
    fmt.Printf("\r%s", strings.Repeat(" ", 35))
    fmt.Printf("\rDownloading... %s complete", humanize.Bytes(wc.Total))
}

func main() {
    fmt.Println("Download Started")

    fileUrl := "http://topgoer.com/static/2/9.png"
    err := DownloadFile("9.png", fileUrl)
    if err != nil {
        panic(err)
    }

    fmt.Println("Download Finished")
}
func DownloadFile(filepath string, url string) error {
    out, err := os.Create(filepath + ".tmp")
    if err != nil {
        return err
    }
    resp, err := http.Get(url)
    if err != nil {
        out.Close()
        return err
    }
    defer resp.Body.Close()
    counter := &WriteCounter{}
    if _, err = io.Copy(out, io.TeeReader(resp.Body, counter)); err != nil {
        out.Close()
        return err
    }
    fmt.Print("\n")
    out.Close()
    if err = os.Rename(filepath+".tmp", filepath); err != nil {
        return err
    }
    return nil
}
```

