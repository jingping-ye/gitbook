# GORM Dialects

## 1. GORM Dialects <a id="gorm-dialects"></a>

### 1.1. 编写一个新的 Dialect <a id="&#x7F16;&#x5199;&#x4E00;&#x4E2A;&#x65B0;&#x7684;-dialect"></a>

GORM 原生支持 sqlite, mysql, postgres 和 mssql。

你可以通过实现 [dialect interface](https://godoc.org/github.com/jinzhu/gorm#Dialect) 接口，来新增对某个新的数据库的支持。

有一些关系型数据库与 mysql 和 postgres 语法兼容，因此你可以直接使用这两个数据库的 dialect 。

### 1.2. Dialect 专属的数据类型 <a id="dialect-&#x4E13;&#x5C5E;&#x7684;&#x6570;&#x636E;&#x7C7B;&#x578B;"></a>

一些 SQL 语法的 dialects 支持他们自定义的，非标准的字段类型，如 PostgreSQL 中的 `jsonb` 字段类型。GORM 支持一些类似此种类型，下面是这些类型的简单实用范例。

#### 1.2.1. PostgreSQL <a id="postgresql"></a>

GORM 支持 PostgreSQL 专有的字段类型：

* jsonb
* hstore

以下这是 Model 的定义：

```text
import (
    "encoding/json"
    "github.com/jinzhu/gorm/dialects/postgres"
)

type Document struct {
    Metadata postgres.Jsonb
    Secrets  postgres.Hstore
    Body     string
    ID       int
}
```

你可以这样子使用 Model：

```text
password := "0654857340"
metadata := json.RawMessage(`{"is_archived": 0}`)
sampleDoc := Document{
  Body: "This is a test document",
  Metadata: postgres.Jsonb{ metadata },
  Secrets: postgres.Hstore{"password": &password},
}

// 将范例数据写入数据库
db.Create(&sampleDoc)

// 读取数据，来检测是否正确写入
resultDoc := Document{}
db.Where("id = ?", sampleDoc.ID).First(&resultDoc)

metadataIsEqual := reflect.DeepEqual(resultDoc.Metadata, sampleDoc.Metadata)
secretsIsEqual := reflect.DeepEqual(resultDoc.Secrets, resultDoc.Secrets)

// 应该打印 "true"
fmt.Println("Inserted fields are as expected:", metadataIsEqual && secretsIsEqual)
```

