# 爬虫小案例

## 1. 爬虫小案例 <a id="&#x722C;&#x866B;&#x5C0F;&#x6848;&#x4F8B;"></a>

### 1.1.1. 爬虫步骤 <a id="&#x722C;&#x866B;&#x6B65;&#x9AA4;"></a>

* 明确目标（确定在哪个网站搜索）
* 爬（爬下内容）
* 取（筛选想要的）
* 处理数据（按照你的想法去处理）

```text
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
    "regexp"
)


var (
    
    reQQEmail = `(\d+)@qq.com`
)


func GetEmail() {
    
    resp, err := http.Get("https://tieba.baidu.com/p/6051076813?red_tag=1573533731")
    HandleError(err, "http.Get url")
    defer resp.Body.Close()
    
    pageBytes, err := ioutil.ReadAll(resp.Body)
    HandleError(err, "ioutil.ReadAll")
    
    pageStr := string(pageBytes)
    
    
    re := regexp.MustCompile(reQQEmail)
    
    results := re.FindAllStringSubmatch(pageStr, -1)
    

    
    for _, result := range results {
        fmt.Println("email:", result[0])
        fmt.Println("qq:", result[1])
    }
}


func HandleError(err error, why string) {
    if err != nil {
        fmt.Println(why, err)
    }
}
func main() {
    GetEmail()
}
```

### 1.1.2. 正则表达式 <a id="&#x6B63;&#x5219;&#x8868;&#x8FBE;&#x5F0F;"></a>

* 文档：[https://studygolang.com/pkgdoc](https://studygolang.com/pkgdoc)
* API
  * re := regexp.MustCompile\(reStr\)，传入正则表达式，得到正则表达式对象
  * ret := re.FindAllStringSubmatch\(srcStr,-1\)：用正则对象，获取页面页面，srcStr是页面内容，-1代表取全部
* 爬邮箱
* 方法抽取
* 爬超链接
* 爬手机号
  * [http://www.zhaohaowang.com/](http://www.zhaohaowang.com/) 如果连接失效了自己找一个有手机号的就好了
* 爬身份证号
  * [http://henan.qq.com/a/20171107/069413.htm](http://henan.qq.com/a/20171107/069413.htm) 如果连接失效了自己找一个就好了
* 爬图片链接

```text
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
    "regexp"
)

var (
    
    reEmail = `\w+@\w+\.\w+`
    
    
    
    
    reLinke  = `href="(https?://[\s\S]+?)"`
    rePhone  = `1[3456789]\d\s?\d{4}\s?\d{4}`
    reIdcard = `[123456789]\d{5}((19\d{2})|(20[01]\d))((0[1-9])|(1[012]))((0[1-9])|([12]\d)|(3[01]))\d{3}[\dXx]`
    reImg    = `https?://[^"]+?(\.((jpg)|(png)|(jpeg)|(gif)|(bmp)))`
)


func HandleError(err error, why string) {
    if err != nil {
        fmt.Println(why, err)
    }
}

func GetEmail2(url string) {
    pageStr := GetPageStr(url)
    re := regexp.MustCompile(reEmail)
    results := re.FindAllStringSubmatch(pageStr, -1)
    for _, result := range results {
        fmt.Println(result)
    }
}


func GetPageStr(url string) (pageStr string) {
    resp, err := http.Get(url)
    HandleError(err, "http.Get url")
    defer resp.Body.Close()
    
    pageBytes, err := ioutil.ReadAll(resp.Body)
    HandleError(err, "ioutil.ReadAll")
    
    pageStr = string(pageBytes)
    return pageStr
}

func main() {
    
    
    
    
    
    
    
    
    
    
}

func GetIdCard(url string) {
    pageStr := GetPageStr(url)
    re := regexp.MustCompile(reIdcard)
    results := re.FindAllStringSubmatch(pageStr, -1)
    for _, result := range results {
        fmt.Println(result)
    }
}


func GetLink(url string) {
    pageStr := GetPageStr(url)
    re := regexp.MustCompile(reLinke)
    results := re.FindAllStringSubmatch(pageStr, -1)
    for _, result := range results {
        fmt.Println(result[1])
    }
}


func GetPhone(url string) {
    pageStr := GetPageStr(url)
    re := regexp.MustCompile(rePhone)
    results := re.FindAllStringSubmatch(pageStr, -1)
    for _, result := range results {
        fmt.Println(result)
    }
}

func GetImg(url string) {
    pageStr := GetPageStr(url)
    re := regexp.MustCompile(reImg)
    results := re.FindAllStringSubmatch(pageStr, -1)
    for _, result := range results {
        fmt.Println(result[0])
    }
}
```

### 1.1.3. 并发爬取美图 <a id="&#x5E76;&#x53D1;&#x722C;&#x53D6;&#x7F8E;&#x56FE;"></a>

下面的两个是即将要爬的网站，如果网址失效自己换一个就好了

* [https://www.bizhizu.cn/shouji/tag-%E5%8F%AF%E7%88%B1/1.html](https://www.bizhizu.cn/shouji/tag-%E5%8F%AF%E7%88%B1/1.html)

```text
package main

import (
    "fmt"
    "io/ioutil"
    "net/http"
    "regexp"
    "strconv"
    "strings"
    "sync"
    "time"
)

func HandleError(err error, why string) {
    if err != nil {
        fmt.Println(why, err)
    }
}


func DownloadFile(url string, filename string) (ok bool) {
    resp, err := http.Get(url)
    HandleError(err, "http.get.url")
    defer resp.Body.Close()
    bytes, err := ioutil.ReadAll(resp.Body)
    HandleError(err, "resp.body")
    filename = "E:/topgoer.com/src/github.com/student/3.0/img/" + filename
    
    err = ioutil.WriteFile(filename, bytes, 0666)
    if err != nil {
        return false
    } else {
        return true
    }
}







var (
    
    chanImageUrls chan string
    waitGroup     sync.WaitGroup
    
    chanTask chan string
    reImg    = `https?://[^"]+?(\.((jpg)|(png)|(jpeg)|(gif)|(bmp)))`
)

func main() {
    
    

    
    chanImageUrls = make(chan string, 1000000)
    chanTask = make(chan string, 26)
    
    for i := 1; i < 27; i++ {
        waitGroup.Add(1)
        go getImgUrls("https://www.bizhizu.cn/shouji/tag-%E5%8F%AF%E7%88%B1/" + strconv.Itoa(i) + ".html")
    }
    
    waitGroup.Add(1)
    go CheckOK()
    
    for i := 0; i < 5; i++ {
        waitGroup.Add(1)
        go DownloadImg()
    }
    waitGroup.Wait()
}


func DownloadImg() {
    for url := range chanImageUrls {
        filename := GetFilenameFromUrl(url)
        ok := DownloadFile(url, filename)
        if ok {
            fmt.Printf("%s 下载成功\n", filename)
        } else {
            fmt.Printf("%s 下载失败\n", filename)
        }
    }
    waitGroup.Done()
}


func GetFilenameFromUrl(url string) (filename string) {
    
    lastIndex := strings.LastIndex(url, "/")
    
    filename = url[lastIndex+1:]
    
    timePrefix := strconv.Itoa(int(time.Now().UnixNano()))
    filename = timePrefix + "_" + filename
    return
}


func CheckOK() {
    var count int
    for {
        url := <-chanTask
        fmt.Printf("%s 完成了爬取任务\n", url)
        count++
        if count == 26 {
            close(chanImageUrls)
            break
        }
    }
    waitGroup.Done()
}



func getImgUrls(url string) {
    urls := getImgs(url)
    
    for _, url := range urls {
        chanImageUrls <- url
    }
    
    
    
    chanTask <- url
    waitGroup.Done()
}


func getImgs(url string) (urls []string) {
    pageStr := GetPageStr(url)
    re := regexp.MustCompile(reImg)
    results := re.FindAllStringSubmatch(pageStr, -1)
    fmt.Printf("共找到%d条结果\n", len(results))
    for _, result := range results {
        url := result[0]
        urls = append(urls, url)
    }
    return
}


func GetPageStr(url string) (pageStr string) {
    resp, err := http.Get(url)
    HandleError(err, "http.Get url")
    defer resp.Body.Close()
    
    pageBytes, err := ioutil.ReadAll(resp.Body)
    HandleError(err, "ioutil.ReadAll")
    
    pageStr = string(pageBytes)
    return pageStr
}
```

 作者：孙建超

