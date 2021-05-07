# 文件上传

## 1. 文件上传 <a id="&#x6587;&#x4EF6;&#x4E0A;&#x4F20;"></a>

```text
package main

import (
    "io"
    "io/ioutil"
    "log"
    "net/http"

    "github.com/julienschmidt/httprouter"
)

const (
    MAX_UPLOAD_SIZE = 1024 * 1024 * 20 
)

func main() {
    r := RegisterHandlers()

    http.ListenAndServe(":8080", r)
}


func RegisterHandlers() *httprouter.Router {
    router := httprouter.New()

    router.POST("/upload", uploadHandler)

    return router
}
func uploadHandler(w http.ResponseWriter, r *http.Request, p httprouter.Params) {
    r.Body = http.MaxBytesReader(w, r.Body, MAX_UPLOAD_SIZE)
    if err := r.ParseMultipartForm(MAX_UPLOAD_SIZE); err != nil {
        log.Printf("File is too big")
        return
    }
    file, headers, err := r.FormFile("file")
    if err != nil {
        log.Printf("Error when try to get file: %v", err)
        return
    }
    
    if headers.Header.Get("Content-Type") != "image/png" {
        log.Printf("只允许上传png图片")
        return
    }
    data, err := ioutil.ReadAll(file)
    if err != nil {
        log.Printf("Read file error: %v", err)
        return
    }
    fn := headers.Filename
    err = ioutil.WriteFile("./video/"+fn, data, 0666)
    if err != nil {
        log.Printf("Write file error: %v", err)
        return
    }
    w.WriteHeader(http.StatusCreated)
    io.WriteString(w, "Uploaded successfully")
}
```

此上传函数同样也适用于gin框架w http.ResponseWriter等同于c.Writer，`r *http.Request`等同于c.Request

