# 压缩解压文件

## 1. 压缩解压文件 <a id="&#x538B;&#x7F29;&#x89E3;&#x538B;&#x6587;&#x4EF6;"></a>

### 1.1.1. 压缩文件 <a id="&#x538B;&#x7F29;&#x6587;&#x4EF6;"></a>

这次介绍的是压缩与解压文件，它的基本原理是创建初始的zip文件，然后循环遍历每个文件，并使用zip编写器将其添加到存档中，确保指定deflate方法以获得更好的压缩。

```text
package main

import (
    "archive/zip"
    "fmt"
    "io"
    "os"
)

func main() {

    
    files := []string{"1.txt", "2.txt"}
    output := "done.zip"

    if err := ZipFiles(output, files); err != nil {
        panic(err)
    }
    fmt.Println("Zipped File:", output)
}




func ZipFiles(filename string, files []string) error {

    newZipFile, err := os.Create(filename)
    if err != nil {
        return err
    }
    defer newZipFile.Close()

    zipWriter := zip.NewWriter(newZipFile)
    defer zipWriter.Close()

    
    for _, file := range files {
        if err = AddFileToZip(zipWriter, file); err != nil {
            return err
        }
    }
    return nil
}

func AddFileToZip(zipWriter *zip.Writer, filename string) error {

    fileToZip, err := os.Open(filename)
    if err != nil {
        return err
    }
    defer fileToZip.Close()

    
    info, err := fileToZip.Stat()
    if err != nil {
        return err
    }

    header, err := zip.FileInfoHeader(info)
    if err != nil {
        return err
    }

    
    
    header.Name = filename

    
    
    header.Method = zip.Deflate

    writer, err := zipWriter.CreateHeader(header)
    if err != nil {
        return err
    }
    _, err = io.Copy(writer, fileToZip)
    return err
}
```

### 1.1.2. 解压文件 <a id="&#x89E3;&#x538B;&#x6587;&#x4EF6;"></a>

下面的代码用于将zip存档文件解压缩到其组成文件中。因为可能有多个文件，所以它将在一个文件夹中创建文件（用第二个参数指定）。

```text
package main

import (
    "archive/zip"
    "fmt"
    "io"
    "log"
    "os"
    "path/filepath"
    "strings"
)

func main() {

    files, err := Unzip("done.zip", "output-folder")
    if err != nil {
        log.Fatal(err)
    }

    fmt.Println("Unzipped:\n" + strings.Join(files, "\n"))
}



func Unzip(src string, dest string) ([]string, error) {

    var filenames []string

    r, err := zip.OpenReader(src)
    if err != nil {
        return filenames, err
    }
    defer r.Close()

    for _, f := range r.File {

        
        fpath := filepath.Join(dest, f.Name)

        
        if !strings.HasPrefix(fpath, filepath.Clean(dest)+string(os.PathSeparator)) {
            return filenames, fmt.Errorf("%s: illegal file path", fpath)
        }

        filenames = append(filenames, fpath)

        if f.FileInfo().IsDir() {
            
            os.MkdirAll(fpath, os.ModePerm)
            continue
        }

        
        if err = os.MkdirAll(filepath.Dir(fpath), os.ModePerm); err != nil {
            return filenames, err
        }

        outFile, err := os.OpenFile(fpath, os.O_WRONLY|os.O_CREATE|os.O_TRUNC, f.Mode())
        if err != nil {
            return filenames, err
        }

        rc, err := f.Open()
        if err != nil {
            return filenames, err
        }

        _, err = io.Copy(outFile, rc)

        
        outFile.Close()
        rc.Close()

        if err != nil {
            return filenames, err
        }
    }
    return filenames, nil
}
```

